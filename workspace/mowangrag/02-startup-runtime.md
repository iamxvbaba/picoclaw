# 02 Startup And Runtime

## Startup Chain

完整启动主线：

1. `BaseApp.attachBaseContext`
   - `MultiDex.install`
   - Debug 下打开 ARouter log/debug
   - `ARouter.init`
   - `AppLifeCycleManager.attachBaseContext`
2. `BaseApp.onCreate`
   - 初始化 `ToastUtil`、`Utils`、`SPUtils`、`SDCacheHelper`
   - 同步触发 `AppLifeCycleManager.onCreate`
   - 初始化语言
   - 异步判断隐私政策是否已同意，再触发 `AppLifeCycleManager.onAgreePolicy`
3. `SplashActivity`
   - 隐私弹窗
   - 默认域名拉取
   - 判断登录态、资料是否完整、是否会员、是否需要开屏广告
   - 跳 `MainActivity` 或 `LoginActivity`
4. `MainActivity`
   - 初始化 push、网络重连、敏感词、前后台监听、浮窗监听、附近人上报等

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/app/BaseApp.kt`
- `life/life-master/app/src/main/java/com/mostone/life/ui/SplashActivity.kt`
- `life/life-master/app/src/main/java/com/mostone/life/ui/MainActivity.kt`

## Privacy Gate

这个项目的隐私同意开关语义是反的：

- `SPUtils[UsCons.SP_PRIVACY_AGREE, true] == true`
  - 表示还要弹隐私弹窗
- 用户点击同意后写入 `false`
  - 同时记录 `SP_PRIVACY_VERSION`
  - 然后触发 `AppLifeCycleManager.onAgreePolicy`

这意味着，后续读到 `SP_PRIVACY_AGREE` 时，不能按变量名直觉理解成“true 已同意”。

代码锚点：

- `life/life-master/app/src/main/java/com/mostone/life/ui/SplashActivity.kt`

## Splash Logic

启动页不是单纯闪屏，实际承担业务分流：

- 若不是任务根并且是 Launcher 重进，直接 `finish()`，避免桌面重复打开
- 先弹隐私协议
- 拉默认服务域名 `HttpCons.getDefaultAddr`
- 若已登录：
  - 资料完整：
    - 会员：直接进主页面
    - 非会员：先走开屏广告策略
  - 资料不完整：
    - 强制退出登录
    - 跳登录页
- 若启用了锁屏密码：
  - 先启动 `MainActivity`
  - 再立刻跳 `ScreenPwdActivity`

额外细节：

- 会员用户跳过开屏广告
- 开屏广告依赖 `AdCons` 配置和 `AdManager`

## Lifecycle Dispatch Model

模块初始化不是宿主硬编码依赖调用，而是通过 `AppLifeCycleManager` 聚合 `IAppLifecycle/IAppLifecycle2`：

- 固定模块：
  - base
  - app
  - login
  - im
  - album
  - moment
  - user
  - news
  - setting
  - group send
- 动态广告模块：
  - 运行时检测类是否存在
  - 有穿山甲类就挂 `I_PANGOLIN_AD_APP`
  - 有灵叶类就挂 `I_LINYE_AD_APP`

分发生命周期：

- `attachBaseContext`
- `onCreate`
- `onAgreePolicy`
- `onAccountLogin`
- `onAccountOutLogin`
- `onScreenPwdChange`
- `onIMSocketConnectSuss`

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/app/AppLifeCycleManager.kt`

## Per-Module Runtime Hooks

### BaseLifecycle

- 隐私同意后初始化：
  - 友盟
  - Bugly
  - X5 WebView
  - 腾讯定位/腾讯地图
  - 抖音 SDK
- `onCreate` 里预初始化友盟、视频缓存代理

### MainLifecycle

- 隐私同意后初始化 Google 渠道的 Adjust

### LoginAppLifecycle

- 隐私同意后初始化推送
- 延迟获取 OAID
- 登录后初始化联运游戏能力

### IMLifecycle

- `onCreate` 初始化 IMManager、音频
- `onAgreePolicy` 初始化 RTC starter
- `onAccountLogin`：
  - 设置 IM userId
  - 初始化浮窗
  - 初始化通用长连接 `SocketClient`
  - 登录成功回调后拉离线消息/客服离线/官方账号通知/存钱罐离线文本
  - 初始化上传、钱包监听、语音转写
- `onAccountOutLogin`：
  - 断开 IM、清缓存、清浮窗、结束 RTC、重置钱包/上传状态

### UserLifecycle

- 登录后延迟拉取 `user/userinfo`
- IM socket 首次成功连接时给新用户加会员经验

### MomentLifecycle

- 登录后初始化朋友圈数据库
- 恢复今日发帖数、上次浏览位置、拉黑名单数据

### AlbumLifecycle

- 登录后初始化加密相册 DB、修正未完成上传状态、拉空间信息

## MainActivity Runtime Responsibilities

`MainActivity` 是真正的运行时中枢，不只是 tab 容器：

- 如果 pushId 为空，主动补初始化推送
- 注册网络状态监听，支撑长连接重连
- 初始化聊天敏感词和朋友圈敏感词
- 注册锁屏/亮屏广播
- 启用摇一摇锁屏
- 登录后刷新用户资料，必要时弹部分封禁提示
- 初始化我的状态
- 未开启青少年模式时弹提示
- 上报附近人
- 注册前后台监听：
  - 浮窗
  - 长连接重连
  - 相册密码前后台
  - AppForeground
  - RTC 生命周期
  - 锁屏密码前后台

代码锚点：

- `life/life-master/app/src/main/java/com/mostone/life/ui/MainActivity.kt`

## Navigation Model

统一跳转依赖 ARouter + `AroUtils`：

- `AroUtils` 封装跳登录、跳主页面、跳 Web、跳相册、跳钱包、跳扫码等
- `AroInterceptor` 只拦截 `extra == AroConstants.ICR_LOGIN` 的路由
- 被打上 `extras = AroConstants.ICR_LOGIN` 的 `@Route` 页面，未登录会先跳登录页

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/arouter/AroUtils.kt`
- `life/p-base/lib-base/src/main/java/com/mostone/lib/arouter/AroInterceptor.kt`

