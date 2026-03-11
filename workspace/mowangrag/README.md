# Life Android RAG Index

## Purpose

这组文档面向 RAG/AI 产品，不追求“阅读体验最顺”，而追求：

- 能快速定位 `life/` Android 项目的功能边界、运行链路和数据流
- 每份文档都可以独立切块检索
- 关键结论都带代码锚点，方便继续追源码

## Project Summary

`life/` 是一个模块化 Android 应用，产品形态不是单一 IM，而是以即时通讯为核心，叠加：

- 登录与账号体系
- 通讯录、群、群发、分享回流
- 朋友圈、探索内容、附近/资讯工具
- 钱包、红包、转账、默往币、会员、靓号、密聊开通
- 个人中心、帮助中心、账户安全、锁屏密码
- 加密相册与相册空间管理
- 单聊音视频、群语音
- 推送、广告、地图、统计、崩溃、WebView、开放平台

主宿主工程是 `life/life-master/app`，核心导航页是 4 个主 Tab：

- 会话 `ConversationFragment`
- 通讯录 `ContactFragment`
- 探索 `ExploreFragment`
- 我的 `UserFragment`

代码锚点：

- `life/life-master/app/src/main/java/com/mostone/life/ui/MainActivity.kt`
- `life/life-master/app/src/main/java/com/mostone/life/ui/MainTab.kt`

## Recommended Ingestion Order

1. `01-project-overview.md`
2. `02-startup-runtime.md`
3. `03-login-user-settings.md`
4. `04-im-social.md`
5. `05-wallet-album-rtc.md`
6. `06-data-network-infra.md`
7. `07-route-entry-inventory.md`

## Document Map

- `01-project-overview.md`
  - 工程装配方式、版本、Flavor、模块地图
- `02-startup-runtime.md`
  - Application、隐私弹窗、生命周期分发、主页面运行时逻辑
- `03-login-user-settings.md`
  - 启动登录、注册完善、个人中心、设置、安全、锁屏密码
- `04-im-social.md`
  - IM、联系人、群、群发、分享、朋友圈、探索
- `05-wallet-album-rtc.md`
  - 钱包/红包/会员/靓号/默往币、加密相册、RTC
- `06-data-network-infra.md`
  - HTTP/Socket、数据库、缓存、推送、广告、第三方 SDK
- `07-route-entry-inventory.md`
  - 公开路由、ARouter Provider、Deep Link、系统分享入口

## Existing Deep Dives Already in Repo

这些文档不是本次新增，但已经很适合纳入 RAG：

- `life/docs/会话可见消息已读与阅后即焚实现说明.md`
- `life/docs/单聊密聊已读与阅后即焚流程说明.md`
- `life/docs/密聊离线消息拉取功能实施文档.md`
- `life/docs/ScreenProtectionHelper.md`

建议把这 4 篇与 `life/docs/rag/` 一起入库，因为它们覆盖了通用总览文档不该塞太深的实现细节。

## Retrieval Hints

- 问“App 启动为什么会先弹隐私/广告/锁屏密码”：
  - 查 `02-startup-runtime.md`
- 问“某个业务在哪个模块、从哪个入口进”：
  - 先查 `01-project-overview.md`
  - 再查 `07-route-entry-inventory.md`
- 问“聊天、密聊、群、联系人、朋友圈怎么串起来”：
  - 查 `04-im-social.md`
- 问“钱包、会员、靓号、默往币、RTC、加密相册”：
  - 查 `05-wallet-album-rtc.md`
- 问“网络、Socket、数据库、推送、三方 SDK”：
  - 查 `06-data-network-infra.md`

