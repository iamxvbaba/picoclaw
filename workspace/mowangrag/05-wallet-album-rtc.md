# 05 Wallet Album RTC

## Wallet And Payment Domain

钱包相关能力被拆成两层：

- `p-red/lib-red-new`
  - 传统钱包、红包、转账、站内信、默往币、默往钱包、密聊开通
- `p-red/m-wallet-pay`
  - 会员、靓号、收益、提现、邀请、超级群权益

这不是一个“余额页”，而是一整套社交经济系统。

## Wallet Surface

`lib-red-new` 关键页面：

- `UserWalletActivity`
  - 钱包首页
- `WalletActivity`
  - 默往钱包
- `MoCoinActivity`
  - 默往币容器页
- `RechargeActivity` / `WithdrawActivity`
- `WalletRechargeActivity` / `WalletWithdrawActivity`
- `NewTransferActivity`
  - 转账
- `NewReceiptDetailActivity`
- 账单与明细：
  - `BillListActivity`
  - `NewBillListActivity`
  - `BalanceActivity`
  - `BillDetailActivity`
  - `NewBillDetailActivity`
- 红包：
  - `CreateSingleCoinsRedActivity`
  - `CreateGroupCoinsRedActivity`
  - `NewCoinsOpenDialogActivity`
  - `NewSingleCoinsRedInfoActivity`
  - `NewGroupCoinsRedInfoActivity`
  - `NewWalletRedDetailActivity`
  - `UnreceivedGroupCoinsRedActivity`
- 支付密码：
  - `WalletSettingActivity`
  - `SetPayPwdActivity`
  - `FindPwdActivity`
- 实名与绑卡：
  - `RealNameAuthActivity`
  - `AuthWalletActivity`
  - `MyBankActivity`
  - `BankCardSelectorActivity`
- 密聊开通：
  - `SecretChatOpenActivity`
  - `SecretChatIntroActivity`
- 站内信：
  - `MailListActivity`
  - `MailDetailActivity`

## Wallet Provider Surface

`m-wallet-pay` 暴露 `I_RED_API` -> `RedProvider`：

- 给用户增加会员经验
- 拉取并缓存单个头像挂件素材

`lib-red-new` 还额外暴露 `I_MCOIN_APP` -> `MCoinProviderImpl`

说明钱包模块不仅是 UI，也承担跨模块资产能力。

## Wallet Payment / VIP / Pretty Number

`m-wallet-pay` 的业务主题：

- 会员：
  - `VipActivity`
  - `VipLevelRankingActivity`
  - `VipPrivilegeActivity`
  - `VipCouponsActivity`
  - `VipRecordsActivity`
  - `VipPayResultActivity`
- 邀请裂变：
  - `VipInviteActivity`
  - `VipReceiveActivity`
  - `VipReceiveRecordActivity`
  - `VipInviteListActivity`
- 收益：
  - `IncomeActivity`
- 提现：
  - `WithdrawActivity`
  - `BindBankCardActivity`
  - `WithdrawRecordActivity`
  - `WithdrawRuleActivity`
- 靓号：
  - `LiangCenterActivity`
  - `LiangDetailActivity`
  - `MyLiangActivity`
  - `LiangSearchActivity`
  - `LiangTagListActivity`
- 超级群权益：
  - `SuperGroupActivity`
  - `SuperGroupPayResultActivity`

## Encrypted Album

加密相册不是 UI 壳，而是完整独立域：

- 我的相册：`MyAlbumActivitiy`
- 某个加密相册：`EncryptedAlbumActivity`
- 相册详情：`AlbumDetailActivity`
- 相册控制：`AlbumControlActivity`
- 相册设置：`AlbumSettingActivity`
- TA 的相册：`TaAlbumActivity`
- 相册密码：
  - `AlbumPwdActivity`
  - `AlbumVerifyActivity`
- 相册空间：
  - `AlbumSpaceActivity`
  - `AlbumExpansionRecordActivity`
- 朋友圈相册聚合：`AllPhotoActivity`
- 媒体查看器：
  - `MediaPagerActivity`
  - `MomentMediaPagerActivity`

Lifecycle：

- 登录后初始化相册加密数据库
- 把未完成上传改成失败态
- 拉取相册空间信息
- 退出登录时清本地空间统计并清上传任务

## Album Storage

相册本地库使用 WCDB 加密：

- 路径：`CacheHelper.instance.getPath()/album.db`
- 密码：
  - `md5(userId)` 后反转
  - 再取 `substring(3, 9)`

表结构：

- `TABLE_ALBUM`
- `TABLE_FILE`
- `TABLE_GROUP_ALBUM`

代码锚点：

- `life/p-user/m-encrypt-album/src/main/java/com/mostone/encryptalbum/AlbumLifecycle.kt`
- `life/p-user/m-encrypt-album/src/main/java/com/mostone/encryptalbum/db/DBManager.kt`
- `life/p-user/m-encrypt-album/src/main/java/com/mostone/encryptalbum/db/core/AlbumDBHelper.kt`

## RTC

RTC 由 `p-rtc` 系列模块承载，UI 入口在 `m-rtc-ui`：

- `RtcSingleVoiceActivity`
  - 单人语音
- `VideoCallActivity`
  - 单人视频
- `RtcGroupVoiceActivity`
  - 群语音
- 前台服务：
  - `RtcService`
  - `RtcGroupVoiceService`
  - `ThirdMusicService`
- 电话状态广播：
  - `PhoneCallReceiver`

## RTC Provider

跨模块入口是 `I_CALL_CALL` -> `RtcMsgProvider`：

- 接收单聊视频/语音邀请
- 接收群语音邀请
- 处理取消、拒绝、忙线、接通、进入房间、结束、响铃状态
- 判断当前是否正在 RTC
- 判断某个 Activity 是否 RTC 页面
- 向外暴露 RTC 生命周期 callback 挂载点

运行时交互：

- `IMLifecycle.onAgreePolicy` 会 `RtcStarer.init(RtcCallManager())`
- `MainActivity` 注册 `RtcLifecycleListener` 和 `RtcFloatWindowVisibilityListener`
- 屏幕解锁时 `IMLifecycle.onScreenPwdChange(false)` 会尝试恢复通话页

代码锚点：

- `life/p-rtc/m-rtc-ui/src/main/java/com/mostone/module/rtcui/provider/RtcMsgProvider.kt`
- `life/p-rtc/m-rtc-ui/src/main/AndroidManifest.xml`

