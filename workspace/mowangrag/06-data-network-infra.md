# 06 Data Network Infra

## Two Network Stacks Coexist

这个项目至少有三条数据通路：

- Retrofit HTTP
- 通用长连接 `SocketClient`
- IM 专用旧 WebSocket `IMWebSocket`

不能把它误解成“全站都是 Retrofit”。

## HTTP Layer

基础类：

- `BaseApi<T>`
  - 基于 `HttpCons.getHttpAddr()`
  - 提供表单提交和 JSON 提交两种 service 构造

典型使用：

- `LoginApi` -> `LoginService`

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/http/BaseApi.kt`

## Socket Layer

基础类：

- `BaseSocketApi`

能力分两类：

- `sendSocketTask`
  - 走新普通长连接 `SocketClient`
- `sendIMSocketTask`
  - 走 IM 专用链路，通过 IM 缓存/发送能力转发

细节：

- 默认 `cmdOP` 由子类指定
- `cmdOP == 252` 的数据会先 Base64 解码再 Gson 解析
- 解析失败会上报友盟错误

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/http/BaseSocketApi.kt`

## Dynamic Domain Strategy

`HttpCons` 不是静态常量表，而是动态域名管理器：

- 启动时可向服务端请求最新服务地址
- 失败时回退到本地缓存地址
- 某些错误码或网络异常累计后会切换地址
- 按业务区分多种域名：
  - 短连接
  - 离线接口
  - 支付宝
  - 钱包
  - 开放平台
  - 首信易
  - 联运
  - 统计
  - 长链接口

静态文件域名也分流：

- 图片
- 视频
- 音频
- 文件传输
- 加密相册

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/cons/HttpCons.kt`

## Local Storage

### Shared Preferences / File Cache

- `SPUtils`
  - 轻量配置、隐私协议、推送 token、锁屏密码配置等
- `CacheHelper`
  - 大量业务缓存、离线拉取时间、敏感词、群公告等
- `SDCacheHelper`
  - 磁盘缓存、挂件资源等

### IM DB

IM 本地库使用 WCDB 加密 SQLite：

- 路径：`CacheHelper.instance.getPath()/msg.db`
- 密码：
  - `md5(userId).substring(0, 6)`
- 典型表：
  - 会话
  - 消息
  - 好友
  - 群
  - 群成员
  - 站内信
  - at 消息
  - 稍后处理/重要的事
  - 客服消息

代码锚点：

- `life/p-im/lib-db/src/main/java/com/mostone/lib/db/core/DBManager.kt`
- `life/p-im/lib-db/src/main/java/com/mostone/lib/db/core/DBHelper.kt`

### Moment DB

朋友圈库使用 Room + WCDB 加密：

- 路径：`<internalAppData>/IMMsg/<md5(userId)>/moment`
- 密码：`md5(userId).dropLast(8)`
- 实体：
  - `BeanMomentMsg`
  - `BeanDBMoment`

### Album DB

相册库也是 WCDB 加密，规则见 `05-wallet-album-rtc.md`。

## Push Strategy

推送策略是“国内厂商优先，谷歌兜底”：

1. 先检测 ROM / 厂商：
   - 魅族
   - 华为
   - 小米
   - OPPO
   - vivo
2. 若都不可用，再反射加载 `GooglePushProvider`
3. `MainActivity` 如果发现 pushId 为空，会补一次初始化

Google / FCM 相关：

- `GooglePushProvider`
- `MyFirebaseMessagingService`

代码锚点：

- `life/p-independent/lib-push/src/main/java/com/mostone/lib/push/PushApp.kt`
- `life/life-master/app/src/main/java/com/mostone/life/google/GooglePushProvider.kt`
- `life/life-master/app/src/main/java/com/mostone/life/google/MyFirebaseMessagingService.kt`

## Third-Party SDKs

### Initialized After Privacy Agreement

- 友盟
- Bugly
- X5 WebView
- 腾讯地图 / 腾讯定位
- 抖音 SDK
- 推送
- OAID
- RTC Starter
- 广告 SDK
- Google 渠道 Adjust

### Other Integrations Visible In Code

- Firebase Messaging / Analytics
- Huawei AGConnect
- 微信登录/支付
- 穿山甲广告
- 灵叶广告
- 联运开放平台 `MUnionUtil`

## Ads

广告模块是运行时按类存在性动态接入：

- 穿山甲：`AdPangolinLifecycle`
  - `AdManager.init(PangleAdMaker(), PangleAdId)`
  - `GMAdManagerHolder.init(app)`
- 灵叶：`AdLingYeLifecycle`
  - `AdManager.init(LingYeAdMaker(), LingYeIdMaker())`
  - `LingYeManager.init(app)`
  - 登录后上报 userId

开屏广告最终在 `SplashActivity` 决定是否展示。

## Screen Protection

屏幕保护能力有单独专项文档：

- `life/docs/ScreenProtectionHelper.md`

它覆盖：

- `FLAG_SECURE`
- 截屏检测
- 录屏检测
- Activity/Fragment 自动生命周期监听

