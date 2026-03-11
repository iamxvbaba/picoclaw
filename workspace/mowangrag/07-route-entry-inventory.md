# 07 Route Entry Inventory

## Notes

- 这里优先列“公开路由”和“外部入口”
- 不列全部内部 Activity
- Provider 路由单独列出，因为该项目跨模块调用大量依赖 Provider

## App And Login Routes

- `/app/main`
  - `MainActivity`
- `/login/main`
  - `LoginActivity`
- `/login/web_def`
  - `WebDefActivity`
- `/login/code_erro`
  - `ErroReasonActivity`
- `/login/register_info`
  - `RegisterInfoActivity`
- `/login/protocol`
  - `ProtocolWebActivity`
- `/setting/appeal`
  - `AppealActivity`
- `/setting/country_list`
  - `CountryListActivity`
- `/setting/setting`
  - `SettingActivity`
- `/setting/modify_pwd`
  - `PwdActivity`
- `/setting/banned_detail`
  - `AccountBannedDetailActivity`
- `/qrcode/qr_scan`
  - `QRCodeScanActivity`

## IM / Contacts Routes

- `/im/chat`
  - `ChatActivity`
- `/im/web`
  - `WebActivity`
- `/im/web_login`
  - `WebLoginActivity`
- `/im/pc_login`
  - `PcLoginActivity`
- `/im/private_cvsn`
  - `SecretConversationActivity`
- `/im/secret_chat_list`
  - `SecretChatListActivity`
- `/im/complaint`
  - `ComplaintListActivity`
- `/im/retrieve_friend`
  - `RetrieveFriendActivity`
- `/im/kf_chat`
  - `CustomerServiceChatActivity`
- `/im/new_contact_info`
  - `NewContactInfoActivity`
- `/im/contact_info`
  - `ContactInfoActivity`
- `/im/search_contacts`
  - `SearchActivity`
- `/im/search_group`
  - `SearchGroupsActivity`
- `/im/favorite`
  - `FavoriteActivity`
- `/im/scan_result`
  - `ScanReActivity`
- `/im/media`
  - `ChatImageAndVideoActivity`
- `/im/destroy`
  - `ChatImageDestroyActivity`
- `/im/group_red_enable_member`
  - `GroupRedEnableMemberActivity`
- `/im/greet_add`
  - `GreetAddFriendActivity`
- `/contacts/add_friends`
  - `AddFriendOrGroupActivity`
- `/contacts/my_group`
  - `GroupActivity`
- `/group_send/main`
  - `GroupAssistantActivity`
- `/group_send/buy`
  - `GroupCardBuyActivity`
- `/teenager/`
  - `TeenagerActivity`
- `/im/group_robot`
  - `GroupRobotActivity`

## Circle / Explore Routes

- `/circle/main`
  - `CircleOfFriendsActivity`
- `/circle/ta`
  - `TaSocialCircleActivity`
- `/news/web`
  - `NewsWebActivity`
- `/news/yunshi`
  - `YunshiActivity`
- `/news/stock`
  - `StockActivity`
- `/news/mytrans`
  - `MyTransActivity`
- `/news/looktrans`
  - `AddTransActivity`
- `/news/normal_news`
  - `NormalNewsActivity`
- `/news/fashion_news`
  - `FashionNewsActivity`
- `/news/taobao_list`
  - `TaoBaoListActivity`
- `/news/hot_beauty`
  - `HotBeautyActivity`

## Wallet / User / Album Routes

- `/wallet/main`
  - `UserWalletActivity`
- `/wallet/my_coin`
  - `MoCoinActivity`
- `/wallet/transfer`
  - `NewTransferActivity`
- `/wallet/unreceived_group_coins_red`
  - `UnreceivedGroupCoinsRedActivity`
- `/mo/wallet/`
  - `WalletActivity`
- `/wp/vip_rank`
  - `VipLevelRankingActivity`
- `/wp/vip`
  - `VipActivity`
- `/wp/invite`
  - `VipInviteActivity`
- `/wp/income`
  - `IncomeActivity`
- `/wp/user_liang`
  - `LiangCenterActivity`
- `/wp/user_liang_my`
  - `MyLiangActivity`
- `/wallet/secret_chat_open`
  - `SecretChatOpenActivity`
- `/user/personal`
  - `PersonalActivity`
- `/user/problem`
  - `ProblemActivity`
- `/user/problem_detail`
  - `ProblemDetailActivity`
- `/user/help_center`
  - `HelpCenterActivity`
- `/user/real_auth`
  - `AddAuthActivity`
- `/encrypt_album/my_album`
  - `MyAlbumActivitiy`
- `/encrypt_album/album`
  - `EncryptedAlbumActivity`
- `/encrypt_album/setting`
  - `AlbumSettingActivity`
- `/encrypt_album/ta_album`
  - `TaAlbumActivity`
- `/encrypt_album/number_pwd`
  - `AlbumPwdActivity`
- `/encrypt_album/moment_album`
  - `AllPhotoActivity`

## Provider Routes

### lib-api ProviderCons

- `/i_base/api`
  - `BaseProvider`
- `/i_red/api`
  - `RedProvider`
- `/i_im/api`
  - `IMProvider`
- `/I_MOMENT/api`
  - `MomentProvider`
- `/i_call/call`
  - `RtcMsgProvider`
- `/i_login/app`
  - `LoginAppLifecycle`
- `/i_app/app`
  - `MainLifecycle`
- `/i_im/app`
  - `IMLifecycle`
- `/i_user/app`
  - `UserLifecycle`
- `/I_MOMENT/app`
  - `MomentLifecycle`
- `/i_base/app`
  - `BaseLifecycle`
- `/i_gs/app`
  - `GSLifecycle`

### lib-base ProviderCons

- `/i_set/app`
  - `SetLifecycle`
- `/i_set/screen_pwd`
  - `ScreenPwdProvider`
- `/i_news/app`
  - `NewsLifecycle`
- `/i_album/app`
  - `AlbumLifecycle`
- `/i_ad/pangolin`
  - `AdPangolinLifecycle`
- `/i_ad/lingye`
  - `AdLingYeLifecycle`
- `/i_contacts/contacts`
  - `IMContactsProvider`
- `/i_im/msg`
  - `IMMsgCaChe`
- `/i_im/service`
  - `IMService`
- `/i_im/minaide`
  - `IMMinAide`
- `/i_im/auth`
  - `IAuth`
- `/i_im/webOpen`
  - `IWebOpen`
- `/i_im/sender`
  - `IMGeneralSender`
- `/i_im/im_push`
  - `PushProvider`
- `/i_im/im_ui`
  - `UIProvider`
- `/explore/near_by`
  - `NearByProvider`
- `/user/status`
  - `UserStatusProvider`
- `/i_mcoin/app`
  - `MCoinProviderImpl`

## Manifest External Entry Points

### App Deep Links

- `mostone://life.com/app`
  - `SplashActivity`
- `mostone://life.com/blank`
  - `BlankActivity`
- `um.5b3088238f4a9d6ae5000022://`
  - `SplashActivity`

### Share / Auth / Notice Deep Links

- `mostone://life.com/share`
  - `ShareActivity`
- `mostone://life.com/auth`
  - `AuthActivity`
- `mostone://life.com/notice`
  - `NotifiEventActivity`

### System Share Entry

- `android.intent.action.SEND`
  - `SendFileReceiveActivity`
- `android.intent.action.SEND_MULTIPLE`
  - `SendFileReceiveActivity`

### WeChat / Firebase

- `WXEntryActivity`
- `WXPayEntryActivity`
- `MyFirebaseMessagingService`

## Route Access Rules

- 某些页面的 `@Route` 带 `extras = AroConstants.ICR_LOGIN`
- 这类页面会被 `AroInterceptor` 做登录校验
- 典型例子：
  - `SettingActivity`
  - `BlackListActivity`
  - `ConversationFragment` 路由场景
  - 相册相关部分页面

代码锚点：

- `life/p-base/lib-base/src/main/java/com/mostone/lib/arouter/AroInterceptor.kt`
- `life/p-base/lib-base/src/main/java/com/mostone/lib/arouter/AroConstants.kt`
