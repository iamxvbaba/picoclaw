# 01 Project Overview

## Build Facts

- 宿主 App：`life/life-master/app`
- `applicationId`：`com.mostone.life`
- `compileSdkVersion`：34
- `minSdkVersion`：24
- `targetSdkVersion`：34
- 当前版本常量：`3.59.1` / `10012`
- Flavor：
  - `cn`：友盟渠道占位符 `local`
  - `gg`：友盟渠道占位符 `google`

代码锚点：

- `life/config/bs-plugin/src/main/java/com/mostone/config/Dep.java`
- `life/life-master/app/build.gradle`

## Module Assembly Model

这个工程不是一个根 Gradle 多模块直接 `include` 的传统结构，而是：

1. `life-master/settings.gradle` 读取 `life/config/include.json`
2. 只有本地存在 `.include/<project>.ib` 和 `.include/<module>.ib` 时，才 `includeBuild("../<project>")`
3. 否则宿主依赖 `Dep` 中的 `latest.integration` 坐标

这意味着：

- `life/` 同时支持“源码联调模式”和“二进制依赖模式”
- 代码仓里能看到所有模块源码，但本地实际是否源码参与构建，要看 `.include/*.ib`

代码锚点：

- `life/life-master/settings.gradle`
- `life/config/include.json`

## Product Skeleton

主产品是“社交 IM + 内容发现 + 钱包支付 + 相册/音视频能力”的组合体。

主页底部 Tab 定义：

- 会话
- 通讯录
- 探索
- 我的

代码锚点：

- `life/life-master/app/src/main/java/com/mostone/life/ui/MainTab.kt`
- `life/life-master/app/src/main/java/com/mostone/life/ui/MainActivity.kt`

## Module Map

### App shell

- `life-master/app`
  - 宿主壳、启动页、主页面、微信回调、Google/FCM 入口

### Base / Framework

- `p-base/lib-base`
  - Application 基类、ARouter 工具、HttpCons、BaseApi/BaseSocketApi、通用缓存、基础生命周期
- `p-base/lib-imageload`
  - 图片加载、MinIO 防盗链辅助
- `p-base/lib-widget`
  - 通用 UI 组件
- `p-framework/lib-api`
  - Provider 接口定义、跨模块 API 契约
- `p-framework/lib-framework`
  - MVP/MVVM 基础设施
- `p-framework/lib-socket`
  - 新长连接能力
- `p-framework/lib-old-socket`
  - 旧 IM WebSocket 能力

### Common / Utility

- `p-common/lib-utils`
  - 通用工具
- `p-common/lib-umeng`
  - 友盟统计/分享
- `p-common/lib-badge`
  - 桌面角标
- `p-common/lib-qr-code`
  - 二维码
- `p-common/lib-silicompressor`
  - 视频压缩
- `p-common/mars_xlog_sdk`
  - 日志

### Login / Account / Security

- `p-login/m-login`
  - 登录、注册完善、邀请码、协议页、设备标识、配置拉取
- `p-independent/m-setting`
  - 设置、通知、密码、申诉、安全中心、锁屏密码、自动登出
- `p-independent/m-real-auth`
  - 实名能力支撑

### IM / Social

- `p-im/lib-im-basic`
  - IM 基础模型与枚举
- `p-im/lib-db`
  - IM 本地数据库
- `p-im/lib-im`
  - IM 核心管理、离线消息、消息发送接收、Provider
- `p-im/lib-im-widgets`
  - IM UI 组件
- `p-im/m-im`
  - 聊天、群、密聊、收藏、投诉、青少年模式
- `p-im/m-contacts`
  - 通讯录、好友、群、分组
- `p-im/m-share`
  - 系统分享回流、外部分享/授权/通知深链入口
- `p-im/m-group-send`
  - 群发助手
- `p-circle-of-friends/lib-moment-db`
  - 朋友圈 DB
- `p-circle-of-friends/m-circle-of-friends`
  - 朋友圈主业务
- `p-explore/m-explore`
  - 探索、新闻、运势、股票、淘宝、附近人、事件提醒

### Wallet / Payment / User Asset

- `p-red/lib-red-new`
  - 钱包、红包、转账、默往币、默往钱包、密聊开通、站内信
- `p-red/m-wallet-pay`
  - 会员、邀请、收入、提现、靓号、超级群权益
- `p-user/m-user`
  - 个人中心、帮助中心、状态、实名认证入口、相册入口
- `p-user/m-encrypt-album`
  - 加密相册与空间

### Media / RTC / Platform

- `p-rtc/m-rtc-core`
  - RTC 核心
- `p-rtc/m-rtc-feature`
  - RTC 特性层
- `p-rtc/m-rtc-gvoice`
  - GVoice 能力
- `p-rtc/m-agora`
  - Agora 能力
- `p-rtc/m-rtc-ui`
  - 通话 UI、RTC Provider、RTC 前台服务
- `p-album/*`
  - 图片裁剪、图片编辑、视频裁剪、相册选择、视频工具
- `p-record-and-photograph/*`
  - 拍摄、录制、媒体处理
- `p-dev-plat/m-open`
  - 开放平台认证/支付 Web
- `p-dev-plat/m-ad-common`
  - 广告公共层
- `p-dev-plat/m-ad-pangle`
  - 穿山甲广告
- `p-dev-plat/m-ad-lingye`
  - 灵叶广告

## Flavor And Channel Differences

- `gg` flavor 单独配置 `libs_gg`，并排除：
  - 腾讯 TBS
  - 高德地图依赖
- `gg` 走 Google/Firebase 能力
- 国内机型优先走厂商推送
- `channel/gg` 里有 Google 渠道专用 `AdjustUtils`

代码锚点：

- `life/life-master/app/build.gradle`
- `life/life-master/app/channel/gg/src/com/mostone/life/google/AdjustUtils.kt`

## Main External Entry Points

- Launcher：`SplashActivity`
- Deep link：`mostone://life.com/app`
- Blank deep link：`mostone://life.com/blank`
- 分享深链：`mostone://life.com/share`
- 授权深链：`mostone://life.com/auth`
- 通知事件深链：`mostone://life.com/notice`
- 微信登录/支付回调：`WXEntryActivity` / `WXPayEntryActivity`

代码锚点：

- `life/life-master/app/src/main/AndroidManifest.xml`
- `life/p-im/m-share/src/main/AndroidManifest.xml`

