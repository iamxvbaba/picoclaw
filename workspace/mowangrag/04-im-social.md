# 04 IM Social

## Scope

这部分覆盖：

- IM 核心
- 联系人与群
- 群发助手
- 分享回流
- 朋友圈
- 探索内容

相关模块：

- `p-im/lib-im-basic`
- `p-im/lib-db`
- `p-im/lib-im`
- `p-im/lib-im-widgets`
- `p-im/m-im`
- `p-im/m-contacts`
- `p-im/m-share`
- `p-im/m-group-send`
- `p-circle-of-friends/lib-moment-db`
- `p-circle-of-friends/m-circle-of-friends`
- `p-explore/m-explore`

## IM Core Model

`IMLifecycle` 是 IM 域运行时入口。

登录后核心动作：

- `IMManager.instance.setUserId(userId)`
  - 初始化本地缓存、数据库、好友/群缓存、消息发送队列
- 初始化浮窗
- 初始化新长连接 `SocketClient`
- 连接成功后拉：
  - 离线消息
  - 客服离线消息
  - 官方账号通知
  - 存钱罐离线文本
- 初始化：
  - 上传能力
  - 钱包消息监听
  - 语音翻译

退出登录后会清：

- 自定义表情
- 收藏辅助状态
- IM 登录状态与会话缓存
- 钱包状态
- 群口令
- RTC
- 浮窗
- 上传初始化状态

代码锚点：

- `life/p-im/m-im/src/main/java/com/mostone/module/im/IMLifecycle.kt`
- `life/p-im/lib-im/src/main/java/com/mostone/lib/im/IMManager.kt`

## IM Feature Surface

`m-im` 公开或关键页面包括：

- `ChatActivity`
  - 普通聊天
- `SecretChatActivity`
  - 密聊聊天页
- `SecretConversationActivity`
  - 私密会话设置/说明链路
- `SecretChatListActivity`
  - 密聊列表
- `ContactInfoActivity`
- `NewContactInfoActivity`
- `SearchActivity`
- `SearchGroupsActivity`
- `BlackListActivity`
- `RetrieveFriendActivity`
- `ComplaintListActivity`
- `FavoriteActivity`
- `WebActivity`
- `WebLoginActivity`
- `PcLoginActivity`
- `CustomerServiceChatActivity`
- `TeenagerActivity`
- 群相关：
  - 群详情、群管理、群成员管理、群公告、群搜索、群机器人、群定时消息等

可见代码说明这是一个“重社交 IM”，不是单纯消息收发 SDK。

## Provider Surface For IM

`m-im` 通过 ARouter 暴露大量跨模块能力：

- `I_IM_API` -> `IMProvider`
  - 群头像、Vip 图标、群信息、好友/联系人查询、邮件箱本地缓存、转发消息等
- `I_IM_MSG_CACHE` -> `IMMsgCaChe`
- `I_IM_SENDER` -> `IMGeneralSender`
- `I_IM_PUSH` -> Push Provider
- `I_IM_SERVICE` -> IM 服务接口
- `I_IM_WEB_OPEN` -> Web 打开能力
- `I_IM_UI` -> UI Provider
- `I_IM_MIN_AIDE`
- `I_IM_AUTH`
- `I_IM_SECRET_CHAT_NOTIFY`
- `I_IM_OUT`

这说明其他业务模块访问 IM 时，优先通过 Provider，而不是直接依赖 IM 内部实现。

代码锚点：

- `life/p-im/m-im/src/main/java/com/mostone/module/im/provider/IMProvider.kt`
- `life/p-framework/lib-api/src/main/java/com/mostone/lib/api/arouter/AroProviderApi.kt`

## Contacts And Groups

`m-contacts` 覆盖：

- 联系人主 Tab：`ContactFragment`
- 新朋友：`NewFriendsActivity`
- 添加好友/群：`AddFriendOrGroupActivity`
- 搜索好友：`SearchFriendsActivity`
- 搜索国外人：`SearchForeignActivity` / `ForeignResultActivity`
- 我的群聊：`GroupActivity`
- 群搜索：`GroupSearchActivity`
- 好友分组：
  - `FriendGroupListActivity`
  - `CreateFriendGroupActivity`
  - `SetFriendGroupActivity`

跨模块 Provider：

- `IMContactsProvider`
  - 对外提供“设置好友分组”的 Intent 生成能力

## Group Send Assistant

`m-group-send` 是独立业务域，不是聊天页的小功能：

- `GroupAssistantActivity`
  - 群发助手首页
- `GroupCardBuyActivity`
  - 群发卡购买页
- 生命周期入口：`GSLifecycle`

## Share And External Intent Entry

`m-share` 是重要外部入口模块：

- `SendFileReceiveActivity`
  - 接收系统 `SEND` / `SEND_MULTIPLE`
- `ShareActivity`
  - deep link `mostone://life.com/share`
- `AuthActivity`
  - deep link `mostone://life.com/auth`
- `NotifiEventActivity`
  - deep link `mostone://life.com/notice`

这意味着 App 可以作为外部分享目标，也能被外部链接回流唤起。

## Circle Of Friends

朋友圈模块包含：

- `CircleOfFriendsActivity`
  - 朋友圈主页
- `TaSocialCircleActivity`
  - TA 的朋友圈
- `PostActivity`
  - 发动态
- `DynamicMessageActivity`
  - 动态消息
- `DynamicDetailActivity`
- `DynamicViolationActivity`
- `CircleMediaDetailActivity`
- `PostMediaDetailActivity`

生命周期：

- 登录后初始化朋友圈数据库
- 恢复今日发帖数
- 恢复上次浏览位置
- 拉取“谁拉黑了我”列表

Provider：

- `I_MOMENT_API` -> `MomentProvider`
  - 未读动态消息、违规缓存、动态消息推送等

数据库：

- `lib-moment-db` 使用 Room + WCDB 加密

## Explore Domain

探索不是一个单一 feed，而是内容工具集合：

- `ExploreFragment`
- 资讯 Web：`NewsWebActivity`
- 运势：`YunshiActivity`
- 股票：`StockActivity`
- 淘宝：`TaoBaoListActivity`
- 普通新闻：`NormalNewsActivity`
- 时尚资讯：`FashionNewsActivity`
- 热门美图：`HotBeautyActivity`
- 我的事件提醒：`MyTransActivity`
- 新增事件提醒：`AddTransActivity`
- 附近人：
  - `NearByUsersActivity`
  - `NearByHelloUsersActivity`

Lifecycle：

- 登录后初始化附近人角标信息

Provider：

- `I_EXPLORE_NEAR_BY`

## Existing Deep Dives Worth Using

IM/密聊相关已有深度文档，回答这类问题时应优先使用：

- `life/docs/会话可见消息已读与阅后即焚实现说明.md`
- `life/docs/单聊密聊已读与阅后即焚流程说明.md`
- `life/docs/密聊离线消息拉取功能实施文档.md`

这三篇分别覆盖：

- 可见区曝光即已读与倒计时
- 单聊/密聊的已读与阅后即焚规则矩阵
- 密聊离线消息拉取与历史时间存储兼容策略

