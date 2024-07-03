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
