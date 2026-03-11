# 03 Login User Settings

## Login Domain Scope

这部分横跨三个模块：

- `p-login/m-login`
  - 登录、验证码、密码登录、注册完善、邀请码、协议页
- `p-user/m-user`
  - 个人资料、状态、帮助中心、实名认证入口
- `p-independent/m-setting`
  - 设置、通知、安全、密码、锁屏密码、申诉、版本与反馈

## Login Flow

### Entry

- 启动页通过 `AroUtils.goLoginActivity()` 进入 `LoginActivity`
- 登录主路由：`AroConstants.ARO_LOGIN_MAIN`

### LoginActivity 结构

`LoginActivity` 自己不是一个单表单页面，而是多个登录 Fragment 容器：

- `LoginPhoneFragment`
  - 短信/手机号登录
- `LoginCodeFragment2`
  - 验证码逻辑
- `LoginPwdFragment`
  - 密码登录
- `LoginSecondPwdFragment`
  - 二次登录

补充状态：

- 支持切换账号
- 支持解冻后重登提示
- 打开即检查版本
- 初始化敏感词，供注册完善昵称时校验

代码锚点：

- `life/p-login/m-login/src/main/java/com/mostone/module/login/ui/login/LoginActivity.kt`

## Supported Login Methods

从 `LoginApi` / `LoginService` 能看出登录能力包括：

- 短信验证码登录：`user/newLogin`
- 密码登录：`user/loginByPwd`
- Token 自动登录：`user/loginByToken`
- 微信登录：`user/loginByWC`
- 抖音登录：`user/loginByDY`
- 易盾登录：`user/loginByDun`
- 秒验一键登录：当前仍通过 `loginByDun` 这套 JSON 服务通道承载

登录请求会携带：

- push/deviceId
- brand
- phoneModel
- 渠道 source
- regionId
- `ad_from` 设备信息 JSON

代码锚点：

- `life/p-login/m-login/src/main/java/com/mostone/module/login/http/LoginApi.kt`
- `life/p-login/m-login/src/main/java/com/mostone/module/login/http/LoginService.kt`

## Registration Completion

如果账号登录后资料未完善，启动页不会放行进入主界面，而是：

- `UserInfoUtils.instance.outLogin()`
- 跳回登录

完善资料主页面：

- `RegisterInfoActivity`
- 邀请码页：`InviteCodeActivity`

这说明“登录成功”和“可进入主业务”不是同一个条件。

## User Center

`m-user` 负责“我的”页和用户资产展示，关键页面包括：

- `UserFragment`
  - Tab 的“我的”
- `PersonalActivity`
  - 个人资料编辑
- `NickNameActivity`
- `AvatarActivity`
- `QAccountActivity`
- `HelpCenterActivity`
- `ProblemActivity`
- `ProblemDetailActivity`
- `AddAuthActivity`
  - 实名认证入口
- `AlbumEntryActivity`
  - 我的页进入加密相册
- 用户状态：
  - `UserSetStatusActivity`
  - `FriendStatusActivity`
  - `MineStatusNotifyActivity`
  - `UserStatusLikeInfoPopupActivity`

Provider：

- `UserStatusProvider` 暴露 `/user/status`

代码锚点：

- `life/p-user/m-user/src/main/AndroidManifest.xml`
- `life/p-user/m-user/src/main/java/com/mostone/module/user/UserLifecycle.kt`
- `life/p-user/m-user/src/main/java/com/mostone/module/user/UserStatusProvider.kt`

## Settings And Safety

设置域不只是通用偏好，而是账号安全中心。

关键页面：

- `SettingActivity`
- `PrivacySetActivity`
- `NoticeSetActivity`
- `NoticeChatSetActivity`
- `NoticeCallSetActivity`
- `PwdActivity`
- `ForgetPwdActivity`
- `SafeActivity`
- `SafeCenterActivity`
- `SafeVerifyActivity`
- `FreezeAccountActivity`
- `CancellationActivity`
- `AppealActivity`
- `AccountBannedDetailActivity`
- `VersionListActivity`
- `VersionDetailActivity`
- `UploadErrorJournalActivity`
- `AboutActivity`
- `FeedBackActivity`

### Screen Password Subsystem

锁屏密码能力集中在 `m-setting`：

- `ScreenPwdActivity`
- `ScreenPwdChangeActivity`
- `ScreenPwdGuideActivity`
- `AutoLogoutActivity`
- `ScreenPwdProvider`

运行时特性：

- `MainActivity` 注册 `ScreenBroadcastReceiver`
- 前后台切换时有 `ScreenPwdForegroundListener`
- `AppLifeCycleManager.onScreenPwdChange` 会通知模块
- `ScreenPwdProvider` 能清空指纹和自动登出配置

代码锚点：

- `life/p-independent/m-setting/src/main/AndroidManifest.xml`
- `life/p-independent/m-setting/src/main/java/com/mostone/module/setting/provider/ScreenPwdProvider.kt`

## Country / Region / Web Helpers

这些辅助能力也在登录/设置域：

- `CountryListActivity`
  - 国家地区选择
- `ProtocolWebActivity`
  - 协议/网页承载
- `WebDefActivity`
  - 简易 Web 容器
- `LoginVerifyWebActivity`
  - 登录验证网页

## Config Fetching

登录域还负责不少“全局配置”：

- `ConfigSocketApi.indexMoneyUsable`
  - 首页/钱包可用性配置
- `ConfigSocketApi.getIpArea`
  - IP 区域
- `ConfigSocketApi.getAgreement`
  - 协议文本
- `ConfigSocketApi.getAppConfigSwitch`
  - 各类开关配置

这类配置走 Socket API，不是传统 Retrofit HTTP。

代码锚点：

- `life/p-login/m-login/src/main/java/com/mostone/module/login/http/ConfigSocketApi.kt`

