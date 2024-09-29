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

#### 5.关于禁用硬件加速后CPU很快就跑满的解释
禁用硬件加速后进行复杂操作时，CPU很快跑满并且出现掉帧的现象可以通过以下几个方面来解释：

##### 硬件加速的作用
硬件加速是指利用计算机的硬件（如GPU）来执行一些通常由CPU处理的计算任务。这些任务包括图形渲染、视频解码和其他计算密集型操作。硬件加速的主要好处是可以减轻CPU的负担，并且提高性能和响应速度。

##### 禁用硬件加速的影响
1. **CPU负担增加**: 当硬件加速被禁用后，原本由GPU处理的任务转移到了CPU上。这意味着CPU需要承担额外的计算负担，这通常会导致CPU使用率迅速上升。

2. **性能瓶颈**: CPU和GPU的架构不同，GPU专为并行处理设计，适合大规模的并行计算任务（如图形渲染）。而CPU更适合串行处理任务。将这些并行任务转移到CPU上会导致性能下降，因为CPU并不擅长处理这些任务。

3. **掉帧现象**: 图形渲染和视频解码等操作对实时性要求较高。当CPU无法在规定时间内完成这些任务时，就会出现掉帧现象（即无法及时刷新屏幕上的图像）。

##### 具体过程
1. **任务转移**: 禁用硬件加速后，所有的图形渲染和相关的计算任务都由CPU处理。
2. **CPU资源消耗**: CPU需要处理额外的图形渲染任务，同时还要执行其他系统和应用程序的任务。这会导致CPU资源迅速耗尽。
3. **处理延迟**: 由于CPU的资源有限，当其满负荷运行时，处理任务的速度会减慢。这种延迟会影响到图形渲染的帧率，导致掉帧。

##### 解决方法
- **启用硬件加速**: 如果可能的话，重新启用硬件加速功能，这样可以让GPU分担一部分计算任务，减轻CPU的负担。
- **优化代码**: 尽量优化代码，减少CPU密集型操作的次数，或者将这些操作放在异步任务中处理。
- **升级硬件**: 如果条件允许，升级硬件配置，尤其是CPU和GPU的性能。


### Electron-Egg实现多开以及命令行打开应用时携带参数
egg内部限制死了只能单开
```javascript
//  node_modules/ee-core/electron/app/index.js
const CoreElectronApp = {

  /**
   * 创建electron应用
   */
  async create() {
    const { CoreApp } = EE;

    const gotTheLock = app.requestSingleInstanceLock();
    if (!gotTheLock) {
      app.quit();
    }
    ...
```
这里把`gotTheLock`这一块注释掉就可以了。   

如果外部通过命令行启动exe文件，可以直接在后面拼接参数后，通过`process.argv`获取到
```javascript
//  外部通过    demo.exe  --params1=value1  --params2=value2

console.log(process.argv)   //  ['--params1=value1', '--params2=value2']
```