## Electron

### 修改默认安装路径
```nsh
!macro customHeader
  !system "echo '' > ${BUILD_RESOURCES_DIR}/customHeader"
!macroend

!macro preInit
  SetRegView 64
  ; 检查 D 盘是否存在
  IfFileExists "D:\*" 0 D_DRIVE_NOT_EXISTS_64
  WriteRegExpandStr HKLM "${INSTALL_REGISTRY_KEY}" InstallLocation "D:\MyApp"
  WriteRegExpandStr HKCU "${INSTALL_REGISTRY_KEY}" InstallLocation "D:\MyApp"
  Goto DONE_64
  D_DRIVE_NOT_EXISTS_64:
  ; 如果 D 盘不存在，跳过写入
  DONE_64:

  SetRegView 32
  ; 检查 D 盘是否存在
  IfFileExists "D:\*" 0 D_DRIVE_NOT_EXISTS_32
  WriteRegExpandStr HKLM "${INSTALL_REGISTRY_KEY}" InstallLocation "D:\MyApp"
  WriteRegExpandStr HKCU "${INSTALL_REGISTRY_KEY}" InstallLocation "D:\MyApp"
  Goto DONE_32
  D_DRIVE_NOT_EXISTS_32:
  ; 如果 D 盘不存在，跳过写入
  DONE_32:
!macroend

!macro customInit
  !system "echo '' > ${BUILD_RESOURCES_DIR}/customInit"
!macroend

!macro customInstall
  !system "echo '' > ${BUILD_RESOURCES_DIR}/customInstall"
!macroend

!macro customInstallMode
  # set $isForceMachineInstall or $isForceCurrentInstall
  # to enforce one or the other modes.
!macroend

!macro customWelcomePage
  # Welcome Page is not added by default for installer.
  !insertMacro MUI_PAGE_WELCOME
!macroend

!macro customUnWelcomePage
  !define MUI_WELCOMEPAGE_TITLE "custom title for uninstaller welcome page"
  !define MUI_WELCOMEPAGE_TEXT "custom text for uninstaller welcome page $\r$\n more"
  !insertmacro MUI_UNPAGE_WELCOME
!macroend
```
**https://juejin.cn/post/7018110903144955934**    
**https://blog.csdn.net/m0_51687040/article/details/137086491**   

### 打包后出现性能问题
问题描述： 应用直接通过浏览器进行访问不会掉帧，但是打包之后，进行复杂的操作出现明显掉帧，打开任务管理器发现进行复杂操作时，CPU很快就跑满了，但是GPU没有怎么调度，猜测是存在死循环导致CPU重复计算，当CPU算不过来时，没办法及时调度GPU进行渲染，所以就出现了掉帧。

#### 1.衡量数值
引入[stats.js](https://github.com/mrdoob/stats.js)可以直观看到FPS变化，在网页端打开基本稳定60FPS，在应用中打开，当CPU跑满后进行操作基本会掉到15FPS左右。

#### 2.排查
首先是怀疑Electron对于打包后的应用可能存在内存限制，但是翻阅官方文档将与性能相关的配置都尝试了一遍无果。       

怀疑可能是硬件加速没开，但是对比调用关闭硬件加速的API后前后两次信息发现结果无异。   
```javascript
const { app } = require('electron');

// 检查是否禁用了硬件加速
const isHardwareAccelerationDisabled = app.disableHardwareAcceleration;

// 硬件加速是否启用
const isHardwareAccelerationEnabled = !isHardwareAccelerationDisabled;

console.log(`Hardware acceleration enabled: ${isHardwareAccelerationEnabled}`);

const basicGPUInfo = await app.getGPUInfo('basic');
console.log('Basic GPU Info:', basicGPUInfo);

const completeGPUInfo = await app.getGPUInfo('complete');
console.log('Complete GPU Info:', completeGPUInfo);
```

可以通过访问chrome://gpu查看内核的硬件加速状态：   
```javascript
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true,
    },
  });

  mainWindow.loadURL('chrome://gpu');
```
对比内核的gpu信息发现，webGL都显示硬件加速(hardware acceleration)状态，唯一的区别就是内核版本不同，考虑是内核差异引起。    
拉了最新版的Electron跑之前的项目发现并没有这个问题，所以这个时候断定是内核引起的。   
但是查看Electron-egg相关的源码发现，是框架本身将硬件加速关闭了：
```javascript
// /node_modules/ee-core/blob/main/ee-core/electron/app/index.js
const { app } = require('electron');
...

/**
 * CoreElectronApp (框架封装的electron app对象)
 */
const CoreElectronApp = {

  /**
   * 创建electron应用
   */
  async create() {
    const { CoreApp } = EE;

    ...

    if (CoreApp.config.hardGpu.enable == false) {
      app.disableHardwareAcceleration();
    }

    return app;
  },

  /**
   * 退出app
   */
  quit() {
    app.quit();
  }
}

module.exports = CoreElectronApp;
```
**可以看到当`CoreApp.config.hardGpu.enable`这个配置为`false`时默认是禁用硬件加速的，而这个属性在初始化时默认就为`false`，所以也就导致了这个问题，关于`hardGpu`的配置在文档中也没有体现，这是比较坑的地方。**

#### 3.修复
```javascript
//  /electron/config/config.default.js

module.exports = (appInfo) => {
  ...
  config.hardGpu = {
    enable: true
  }
}
```

#### 4.总结
这个问题时Elctron-egg这个框架一些初始化配置引起的，通过配置`hardGpu`选项可修复该问题