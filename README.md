# 运动世界校园 · GKD 规则

> 本仓库**只发布一份规则**：针对 `com.zjwh.android_wh_physicalfitness`
> **7.3.93（versionCode 407）** 的 GKD 本地订阅「应用规则」。
> 不含 APK、不含修改版客户端、不含启动守卫工具、不含分析数据集。

## 文件

| 文件 | 用途 |
|---|---|
| `com.zjwh.android_wh_physicalfitness.json5` | 本地「应用规则」本体，JSON5，整段复制粘贴 |
| `subscription.json5` | 远程订阅文件，内容同上，供 GKD 通过 URL 直接导入 |
| `README.md` / `LICENSE` / `NOTICE.md` / `.gitignore` | 仓库必要文件 |

> **两个文件二选一**：要么加远程订阅，要么本地粘贴应用规则，**不要同时启用**，
> 否则同一个广告会被两组相同规则重复点击。

## 导入

### 方式 A（推荐）：远程订阅，不用复制文本

```
GKD → 订阅 → + → 粘贴 URL → 确认导入
```

```
https://raw.githubusercontent.com/Hancard/SportsCampus-GKD-Rules/main/subscription.json5
```

GitHub 网页入口（**不是独立备用**）：

```
https://github.com/Hancard/SportsCampus-GKD-Rules/raw/main/subscription.json5
```

> 这个路径最终会 **302 到 `raw.githubusercontent.com`**，只是入口域名不同，
> 并不能绕开 raw 域名不通的问题。2026-11-12 本机实测：该 `github.com` 链接
> 连续 3 次代理返回 `CONNECT tunnel failed, response 502`（`http=000`），
> 而 `raw.githubusercontent.com` 稳定 `200`。当时 `version 8/9` 能同步只是
> 因为两者本来就是同一份文件。**若 raw 确实打不开，请改用方式 B 本地粘贴**，
> 或临时用 jsDelivr 节点但务必核对 `version`（可能滞后数小时到一天）。

> **用哪个链接导入，后续就一直用哪个链接自动更新**（`version 6` 起）。此前订阅文件里
> 硬编码了 `updateUrl` 指向 raw，导致用备用链接导入的用户在 GKD 自动检查更新时被
> 强制覆盖成 raw（在国内部分网络下可能不通），表现是「仓库明明更新了，广告照旧」。官方
> [RawSubscription](https://gkd.li/api/interfaces/RawSubscription) 明确：`updateUrl`
> 缺失时「使用添加订阅时填写的链接」，故已移除该字段。**若你此前是用
> `*.jsdelivr.net` 任何节点导入的，建议删掉该订阅、改用上面的
> `raw.githubusercontent.com` 链接重新导入**：
> 缓存节点每次发版后都会重新滞后数小时到一天（见「第四轮之三」三时点实测），
> 滞后期间 GKD 一直拿到旧规则。

> ⚠️ **不推荐用任何 `*.jsdelivr.net` 节点导入订阅**（`cdn.jsdelivr.net` /
> `fastly.jsdelivr.net` / `gcore.jsdelivr.net` 都一样）：它们对 `@main` 有不可控的
> 长缓存，`cdn.jsdelivr.net` 曾长期停在 **`version 1`**；`version 8` 发布后
> `fastly` / `gcore` 滞后约 18 小时才追上，而 `version 9` 一推上去又立刻停在
> `version 8`（三时点实测见「第四轮之三」）。滞后期间拿到的旧规则缺第四轮时序
> 修复与京东 smile 关闭按钮，表现正是「仓库明明更新了、几秒后的弹窗广告照旧」。
> `raw.gitmirror.com` 则 TLS 握手失败，同样不可用。
> **导入后请在 GKD 订阅页核对版本号是否为 `version: 9`**；不是就换上面的
`raw.githubusercontent.com` 链接重新导入（`github.com` 入口只是同一文件的 302 跳板，
不能作为另一条独立线路）。

导入后 GKD 会显示订阅名「运动世界校园 7.3.93 补充规则」与 **3 个规则组（100/101/102）**。
后续更新规则无需重新粘贴，GKD 会按 `version` 自动拉取（当前 `version: 9`）。
若之前已导入过本订阅，在订阅页手动刷新一次即可拿到最新规则。

### 方式 B：本地粘贴应用规则

```
GKD → 订阅 → 本地订阅 → 应用规则 → + → 粘贴整段 JSON5 → 保存
```

建议从 <https://raw.githubusercontent.com/Hancard/SportsCampus-GKD-Rules/main/com.zjwh.android_wh_physicalfitness.json5>
全选复制，不要用聊天窗口或历史消息里的旧文本。**粘贴前必须能看到文件最后的
`// === END OF FILE ===` 哨兵行**；看不到就说明复制被截断，GKD 会报
`Unclosed array` / `Unclosed object`。

**只给 GKD 无障碍权限**，绝不要给运动世界校园（该 App 自带无障碍服务、设备管理器与
针对 vivo 的权限引导页）。vivo / OriginOS 上把 GKD 加入电池白名单、允许后台高耗电、
允许自启动与关联启动、锁住多任务卡片，否则无障碍会被系统反复回收。

## 与上游订阅的关系

本规则是**补充**：组 100 按文本匹配开屏「跳过」，组 101 按资源 ID /
contentDescription 匹配广告容器里的关闭按钮。两者是**职责互补而非节点互斥**：
若某个跳过按钮同时带 `text*="跳过"` 与组 101 白名单里的 skip 类 `vid`，两组可能
各点一次（见「真机排障」）。主规则仍建议用持续维护的
[`Lin-arm/GKD_subscription`](https://github.com/Lin-arm/GKD_subscription)，任选一个订阅地址：

```
https://gkd667.vv.ax/gkd.json5
https://gkd-subscription-667.pages.dev/gkd.json5
https://raw.githubusercontent.com/Lin-arm/GKD_subscription/main/dist/gkd.json5
```

> 不要使用已归档停更的 `gkd-kit/subscription`：其中的 Activity 名属于重构前的旧结构
> （`activity.SplashActivity` / `ui.AdActivity`），在 7.3.93 上会全部失配。

导入订阅后，**必须关闭以下 3 组「便利类」规则**（它们会动到定位与运动模式，
关闭后不影响任何广告跳过）。按名称识别，key 对应 2026-11-13 实测的
**v631** 版订阅（版本号会变，不要只记数字）：

| 规则组 | 必须关闭的原因 |
|---|---|
| `13 权限提示-不开启可选权限` | 会点「暂不开启，继续跑步」；若缺的是定位权限，轨迹失效 |
| `14 权限提示-定位权限` | 会在「定位服务未开启」弹窗上点「取消」 |
| `15 功能类-运动页面自动切换至校园跑` | 自动切换运动模式，成绩按模式校验 |

如果上游订阅已经能跳掉全部广告，**不要导入本仓库规则**，避免两个规则源重复点击。

## 其它 App 的广告（支付宝等）

本订阅的 `subscription.json5` 里 `apps` **只有**
`com.zjwh.android_wh_physicalfitness` 一个应用。GKD 的应用规则按包名隔离，
所以它对支付宝（`com.eg.android.AlipayGphone`）或其它任何 App 的广告
**完全不会生效 —— 这是设计使然，不是规则失效，也不是 bug**。

静态复核（7.3.93 APK，2026-11-13）：dex 中 `alipay` 命中 532 处，全部属于
支付宝支付 / 授权 SDK 与实名核验链路；manifest 里 6 个
`com.alipay.sdk.app.*` 是 `H5PayActivity` / `H5AuthActivity` /
`PayResultActivity` 等付款授权页；中文文案是「跑步开始前支付宝认证查询超时」
「支付宝人脸比对」「跳转支付宝信用分核验」「是否完成了支付宝人脸校验？」。
**没有支付宝广告 SDK，「支付宝红包」dex 命中 0 次**。因此往本订阅里加
支付宝广告规则既没有静态依据，还可能误伤防作弊实名核验（成绩上传前置条件）——
本仓库不会这么做。

**要拦支付宝 App 自己的广告，导入上面「与上游订阅的关系」里的上游订阅即可。**
2026-11-13 实测其 `v631` 版含支付宝 **44 个规则组**，例如：

| 组 | 作用 |
|---|---|
| `13 全屏广告-弹窗广告` | 支付宝弹窗广告主力组（推荐广告标准层） |
| `24 开屏广告-小程序开屏广告` | 小程序开屏 |
| `14 全屏广告-支付后的推荐弹窗` | 支付完成后的推荐弹窗 |
| `21 局部广告-卡片广告` / `19 分段广告-服务消息页面卡片广告` | 信息流卡片 |
| `26 局部广告-直播悬浮窗` / `33 局部广告-底部横幅广告` | 悬浮与横幅 |

这些组带 `snapshotUrls` / `exampleUrls` 真机快照，且**都没有 `enable:false`、
也不引用订阅分类**，导入后默认启用（订阅里另几处
`{id:'com.eg.android.AlipayGphone',enable:false}` 只是把它排除出
「更新提示 / 青少年模式」等*全局组*，不影响应用自身的广告规则）。

> ⚠️ **别关错 `key 13`**：本 README 要求关闭的 `13 权限提示-不开启可选权限`
> 是**运动世界校园**这个 app 下的组；支付宝 app 下的 `13 全屏广告-弹窗广告`
> 恰恰是拦支付宝弹窗广告的主力组。GKD 里规则组按应用分开显示，
> **按组名称识别，不要只按数字关**。

若已导入上游订阅仍拦不住：先确认 GKD 里「支付宝」应用没有被整体关闭、
`13 全屏广告-弹窗广告` 处于开启状态；再看「触发记录」有无命中，
没有就按下方「真机排障 → 抓快照」提供 `activityId` 与脱敏快照。

## 规则组

| key | 行为 | 约束 |
|---|---|---|
| 100 | 开屏 / 首页弹窗「跳过」文本兜底 | `matchTime 20000`、`actionMaximum 2`、`resetMatch: activity`、`actionCd 1500`；`excludeActivityIds` 41 项覆盖本包 136/140 个业务 Activity，只留 4 个广告宿主 |
| 101 | 全屏 / 插屏广告关闭按钮 | `activityIds` 白名单 24 项（startWith 匹配），`actionCd 1500`；**单条 rule** 用 `\|\|` 连接已核实的 `vid` 与 `desc*="关闭"`，避免同一按钮被点两次 |
| 102 | 广告标签 / 京东互动引导语返回键兜底 | **默认启用**；前两组读不到节点时按返回键；白名单 **23 项**（组 101 为 24 项，本组另有 `excludeActivityIds` 优先排除启动入口 `SplashActivity`）；`matchTime 20000`、`actionMaximum 3`、`resetMatch: activity`、`actionCd 2500` 限流 |

组 100 与组 101 是**按文本 / 按资源 ID 与 contentDescription 互补**，不是同一条规则写两遍；
但二者没有做节点级互斥，详见「已知限制」。激励视频 / 奖励页、
`mvi.run` / `mvi.sport` / `RunPlanActivity` 均不在匹配范围内；无硬编码屏幕坐标、无跨应用全局规则。
组 102 只在广告容器白名单内按返回键，**不会作用于跑步页**；`excludeActivityIds` 单独排除
`SplashActivity`，因为在启动入口按返回键会直接退出 App。返回键即使命中广告素材文案
（「立即领取」等）也不会打开落地页，这是它比点击兜底更安全的地方。若实测发现首页被
误退出，在 GKD 界面里单独关掉组 102 即可。

### 京东 JAD 互动插屏（2026-11-09 两轮真机截图反馈修复）

截图里的广告由多层组成：白色「京东 JD / 电脑 11.11 高潮期来袭」Dialog、60 元 PLUS 专享券
卡片与「领券购买」、一个大的黑色圆形白 X、右下角「广告」标签与小 X、底部「上滑或点击按钮
查看详情」手势引导。对应 APK 内的 `assets/jad_slideup.json`。

**第一轮**（`version 2`）修了两处：关闭按钮 `vid` 没覆盖（`jad_interstitial_close` /
`jd_lijin_close`），以及京东 / 天目 / AdGain 的透明与插屏容器不在 `activityIds` 白名单。
**当时的** 9 个 `vid` 全部用脚本核实为本包 `resources.arsc` 中 **type=id 的真实资源名**
（4865 个 id 里逐个查得）；`jad_interstitial_close_view` / `jad_back_close` 只是 layout
名而非 id，写进 `vid` 会永不触发，故未采用。9 个的规模本身在第三轮之二被证明远远不够，
现为 39 个。

**第二轮**（`version 3`）复核了模板与官方 schema，又发现两个确凿缺陷：

1. **`[clickable=true]` 是错误约束（本轮主因）**。GKD 官方 `RawRuleProps` 写明：
   `action` 默认值 `"click"` 会**自动判断** —— 节点 clickable 用 `clickNode`，否则降级
   `clickCenter`；`clickNode` 未被应用接收时也会回落 `clickCenter`。京东这类自定义关闭
   View 常把点击交给父容器或 `onTouch`，`isClickable()` 返回 false，加上这个条件会把它们
   整批筛掉。已改为 `[visibleToUser=true]`，只排除不可见节点。同时补一条
   `[desc*="关闭"]` 兜底，覆盖没有 `vid`、只有 contentDescription 的关闭按钮。
2. **返回键兜底被默认禁用，等于没有兜底**。组 102 原本 `enable: false`，当关闭按钮
   读不到节点时三层规则全部落空。当时改为默认启用，并一度用 `matchTime 8000` /
   `actionMaximum 2` / `resetMatch: app` 限流（`actionCd` 当时误设为 `8000`，
   见第三轮修复）；这些时序参数已在第四轮全部替换，当前值见「规则组」表。

另外核实：`assets/jad_slideup.json` 是 **Lottie 动画**（`v: 5.6.10`、`nm: 向上划动`），
只负责画手势引导，内部**没有任何 close 字段**——说明关闭按钮是 SDK 的原生 View，
用 `vid` 匹配的方向本身没错，问题出在 `clickable` 条件。

> 仍是**静态推断**：截图看不到节点树，无法确认 X 按钮的 `vid` / `desc` 及承载
> `activityId`。若升级到当前版本后该广告仍未跳过，请抓 GKD 快照
> （`i.gkd.li/i/...`，先脱敏）并附上触发记录里的 `activityId`，按真实节点收窄选择器。

### 第三轮之一：官方 API 语义复核与参数自相矛盾（2026-11-10，`version 4`）

逐条对照 [GKD 官方 API](https://gkd.li/api/interfaces/RawCommonProps) 与
[查询优化](https://gkd.li/guide/optimize) 后，修掉三个脚本此前查不出的缺陷：

1. **组 102 的 `actionMaximum: 2` 是死配置**。此前 `actionCd` 与 `matchTime` 都取
   `8000`，且 `resetMatch: 'app'`。官方定义 `matchTime` 是「规则参与查询屏幕节点时，
   等待一段时间后休眠此规则」，`actionCd` 是「执行 action 最小间隔」——第一次返回键
   后规则进入 8 秒冷却，而 8 秒匹配窗口同时耗尽，第二次永远不可能执行。注释与文档
   声称「每次进入 App 最多按 2 次」与实现不符。当时把 `actionCd` 降为 `2500`，
   让两次返回键在 8 秒窗口内真正可达；第四轮又把窗口放宽到 20000。
2. **组 101 两条 rule 会重复点击同一个关闭按钮**。原来按 `vid` 与按
   `desc*="关闭"` 拆成 key 0 / key 1，一个按钮若同时有 `vid` 和 `desc`，两条 rule
   各点一次，第二下可能落在广告素材上打开落地页。现合并为一条 rule 用 `||` 连接。
3. **组 101 的 `fastQuery: true` 是无效配置**。官方规定快速查询只接受
   `id` / `vid` / `text` 系列作为 `||` 分支，`desc*="关闭"` 不符合；「只要有一个分支
   不符合, 整个 `||` 表达式就必须停用全局快速查询」。合并后的选择器刻意不写
   `fastQuery`，走全树遍历，正确性优先。

同时核实：`RawAppGroup` 确实继承 `RawAppRuleProps`，所以 `activityIds` /
`excludeActivityIds` 写在**组级**是合法的；`desc`、`visibleToUser`、`text.length`
都是官方 [节点属性](https://gkd.li/guide/node) 中的真实字段，选择器语法无误。

### 第三轮之二：反查发现真正的漏拦主因（2026-11-10，`version 5`）

把 `resources.arsc` 的 **4865 个 `type=id` 资源名**和 manifest 的 **330 个
activity** 全量反查一遍后，发现之前的规则有两个覆盖型缺陷——这才是「规则看着对、
广告照样弹」的主要原因，前面几轮只修了语法和参数：

1. **关闭按钮 `vid` 只写了 9 个，但本包实际打包了 7 套广告 SDK**。反查到 207 个
   含 `close` / `skip` / `dismiss` 的资源名，其中大量是各 SDK 的**主**关闭按钮：
   天目 `tianmu_iv_close` / `tianmu_tv_close`、快手 `ksad_close_btn` /
   `ksad_interstitial_auto_close`、京东 `jad_banner_close` / `jad_feed_close`、
   AdGain `adgain_interstitial_close_text`、MIMO `mimo_interstitial_close_img`、
   HiAd `hiad_banner_close_button` 等。原白名单里只有 `tianmu_suspend_iv_close`
   （悬浮广告专用）和 `ksad_interstitial_close_outer`，主插屏的关闭按钮全部落空。
   当时补齐到 **34 个 `vid`**（第四轮之二再扩到 39 个），逐个用脚本核对为真实
   `type=id` 资源名。
2. **广告容器白名单漏了整条链路**。manifest 里明确存在百度
   `com.baidu.mobads.sdk.api.AppActivity` / `BdShellActivity`、穿山甲
   `com.bytedance.msdk.api.activity.TTTransparentActivity` /
   `TTDelegateActivity`、快手 `KsFullScreenVideoActivity`，这些都不在原 19 项里。
   组 101 白名单补到 **24 项**，组 102 补 3 个纯广告容器。

补的时候刻意守三条边界：只收广告 SDK 专属前缀（不收 `btn_close` / `img_close` /
`tv_close` 这类业务首页常见通用名，否则 `.mvi.home.HomeActivity` 在白名单内会误点
首页控件）；不收任何 `*_reward_*` 激励视频资源（用户可能想领奖励）；不把广告落地页
WebView 或任何 `mvi/run`、`mvi/sport` 业务页面放进白名单。所有新增容器名都对照
完整 manifest 逐项核实存在，红线检查（不含 `reward`、不含跑步/成绩页）仍然通过。

> 注意 `desc*="关闭"` 兜底仍然保留：没有 `vid` 的自定义关闭 View 靠它兜住。
> 但它是全树遍历、无快速查询优化；若真机发现首页正常按钮被点，先关组 101 排查。

### 第三轮之三：红线反查与自动更新源（2026-11-10，`version 6`）

1. **组 100 的 `excludeActivityIds` 严重不足**。该组没有 `activityIds` 白名单
   （否则会漏掉开屏宿主），而是靠黑名单挡住业务页面；但原来只有 10 个前缀，
   按完整 manifest 逐路径核对，本包 **140 个自有 Activity 里只盖住 36 个**。
   `.mvi.me`（39 个）、`.mvi.tice`、`.mvi.teachplan`、`.mvi.setting`、`.mvi.extra`
   等成绩 / 体测 / 教学 / 设置页面全部暴露在 `[text*="跳过"]` 下，与 README 承诺的
   「绝不能被自动点击」不符。现补到 **41 个前缀，覆盖 136/140**；剩下未排除的
   4 个恰好是必须保留的广告链路：`SplashActivity`、`AdActivity`、`HomeActivity`、
   `KwadHandlerActivity`（快手桥接，刻意不排除以免漏掉开屏跳过）。另外单独排除了
   `SystemUpgradeActivity`（版本升级弹窗，非广告）。每个前缀都用脚本确认在
   manifest 中至少命中一个真实 Activity。
2. **订阅里硬编码的 `updateUrl` 会让备用链接导入失效**，详见上方「方式 A」的说明。
   已移除该字段，改为沿用导入时填写的链接。

### 第四轮：为什么「开屏能拦、几秒后的弹窗拦不住」（2026-11-10，`version 7`）

真机反馈：开屏广告与固定式 banner 都能正常跳过，唯独**进入 App 几秒后弹出、必须
手动关掉才能继续使用**的模态弹窗（大多是京东）拦不住。这轮没有再补 `vid` —— 前两类
广告能拦已经证明按 `vid` 点击是通的，问题出在**时序与容器白名单**。

静态侧先确认「开屏」与「首页弹窗」确实是两个独立广告位：dex 里 `indexScreen` /
`index_screen`、`returnIndexScreen` / `return_index_screen`、`expiredOpenScreen` /
`expired_open_screen`、`runScreen`、`minePageAd` 各自独立，与早期从 `XYZAdConfigBean`
读到的服务端配置字段一致。首页弹窗挂在 `.mvi.home.HomeActivity`，开屏在
`.mvi.splash.SplashActivity`。

1. **组 100 开屏点过一次就整次会话休眠（主因）**。官方
   [RawCommonProps](https://gkd.li/api/interfaces/rawcommonprops) 原文：
   `actionMaximum` 是「规则的 action 被执行的最大次数，达到最大次数时，休眠此规则」；
   `resetMatch: 'app'` 是「重新进入 app 时，重置规则」。原配置 `actionMaximum: 1` +
   `resetMatch: "app"`，开屏「跳过」点完即休眠，而首页弹窗是同一次进入 App 里几秒后
   才出现的第二个广告位 —— 它的「跳过」按钮永远等不到规则醒来。改为官方默认的
   `resetMatch: "activity"`（Activity 刷新时重置）、`actionMaximum: 2`、
   `matchTime: 20000`，补 `actionCd: 1500` 防同一广告内连点。
2. **组 102 的返回键兜底窗口已经过期**。`matchTime: 8000` 从进入 App 起算，
   `resetMatch: "app"` 同会话不重置；弹窗几秒后才弹、关闭按钮还可能再延迟，
   8 秒窗口耗尽后规则休眠。改 `matchTime: 20000`、`resetMatch: "activity"`、
   `actionMaximum: 3`（`actionCd 2500` 保持，仍满足 `actionCd < matchTime`）。
3. **组 102 白名单缺的 14 项恰好都是弹窗类容器**。组 101 有 24 项，组 102 只有
   10 项；缺的包括 `DialogProxyActivity`、`TransparentActivity`、
   `com.qq.e.ads.DialogActivity`、`AdActivity`、`JADWebViewActivity` 等 —— 正是
   「几秒后弹出的模态弹窗」的宿主。关闭按钮读不到节点时，兜底在这些页面上根本不
   参与匹配。当时与组 101 对齐补齐到 24 项，并单独用 `excludeActivityIds` 排除
   `SplashActivity`（启动入口按返回键会退出 App）；第五轮删掉被排除项遮蔽的
   同名冗余条目，现为 23 项。
4. **触发条件是精确匹配，把京东弹窗文案全漏了**。原来是
   `[text="广告" || desc="广告"]`，而京东互动插屏的屏幕文案是「摇一摇查看广告」
   「上滑查看更多内容」「滑动查看详情」「立即领取」（dex 中「广告」x533、
   「摇一摇」x7、「上滑」x7、「查看详情」x11，均为实证），精确匹配全部漏掉。
   改为包含匹配并补入这些引导语。因为本组 action 是 `back` 而非 `click`，命中
   素材文案不会点开广告落地页 —— 这是刻意选返回键做兜底的原因。

> 边界：`indexScreen` 等字段只说明广告位存在，**不能证明弹窗的真实 `activityId`
> 与关闭按钮的节点属性**。`assets/jad_slideup.json` 是 Lottie 动画，内部没有任何
> close 字段，关闭按钮是 SDK 原生 View；它究竟带 `vid`、`text` 还是什么都没有，
> 只有真机快照能确定。若升到 `version 9` 后仍漏，请按下方「真机排障 → 抓快照」
> 提供 `activityId` 与节点树，据此收窄选择器。

### 第四轮之二：京东「笑脸 / 礼金」弹窗的关闭按钮漏项（2026-11-10，`version 8`）

批次 1 修完时序后，把 `resources.arsc` 的 **4865 个 `type=id` 资源名**按
`close` / `skip` / `dismiss` 全量反查，得到 60 个候选、其中 38 个此前未收录。
逐个按「广告 SDK 专属前缀 / 非 reward / 非关闭区域 / 非业务通用名」筛完，
发现一组**确凿漏项**：

| 新增 `vid` | 归属 |
|---|---|
| `iv_jd_home_smile_close` | 京东首页「笑脸」弹窗 |
| `iv_jd_smill_close` | 京东笑脸弹窗（SDK 内拼写为 smill） |
| `iv_jdwm_smile_close` | 京东万盟笑脸弹窗 |
| `jad_video_skip_btn` | 京东视频广告跳过 |
| `mimo_interstitial_endpage_close_img` | MIMO 插屏结束页 |

前三个与已收录的 `jd_lijin_close` / `jdwm_close` 同属
`jd_home_smile_background` / `jd_home_small_layout` / `jd_lijin_big` 资源族
（dex 字符串上下文实证），正是「进入 App 几秒后、必须手动关掉的京东弹窗」
那一族。此前 34 个 `vid` 里只有 `jd_close` / `jd_lijin_close` / `jdwm_close`，
恰好漏掉了 smile 系列的主关闭按钮。

同时补 3 个**纯文本关闭信号** `text="关闭"` / `text="×"` / `text="✕"`：
组 100 只匹配 `text*="跳过"`，组 101 原本只匹配 `vid` 与 `desc*="关闭"`，
一个只暴露 text、既无 `vid` 也无 `desc` 的自定义关闭 View 会三层全落空。
这三个信号仍合并在组 101 的**同一条 rule** 里，同一节点只会被点一次。

顺带复核了此前的判定：`jad_interstitial_close_view`、`jad_back_close`、
`jad_skip_btn` 确实**不在** 4865 个 `type=id` 名字里（只是 layout / string 名），
写进 `vid` 会永不触发，继续保持排除。也刻意没收 `ad_close_area` /
`ad_close_delay_area` 这类「关闭区域」——它们往往是整块遮罩，点击等于点广告
素材会打开落地页；以及所有 `ksad_*_reward_*` 激励视频资源。

> 仍然要强调：`vid` 是资源名，不等于运行时节点一定带这个 id。京东互动广告用
> `Resources.getIdentifier` 动态取 id，关闭 View 也可能完全无 id。若升到
> `version 9` 后弹窗还在，请按下面「真机排障 → 抓快照」把 `activityId` 与
> 节点树发来 —— 这是唯一能终结静态推断的办法。

### 第四轮之三 / 第五轮复核：备用订阅链接的三时点实测

`version 8` 推上去后逐个实测导入链接，发现一个会直接让前面两批修复落空的
交付问题；`version 9` 推送前后又各测一次，确认这是**周期性滞后**而非一次性故障：

| 链接 | 11-10 16:xx（v8 刚发布） | 11-11 10:xx（v9 推送前） | 11-11 10:xx（v9 推送后） |
|---|---|---|---|
| `raw.githubusercontent.com/.../main/subscription.json5` | `version 8` | `version 8` | **`version 9`** |
| `github.com/.../raw/main/subscription.json5` | `version 8` | `version 8` | **`version 9`** |
| `fastly.jsdelivr.net/gh/...@main/...` | `version 6` | `version 8` | `version 8` |
| `gcore.jsdelivr.net/gh/...@main/...` | `version 6` | `version 8` | `version 8` |
| `cdn.jsdelivr.net/gh/...@main/...` | 未测（曾长期停在 `version 1`） | `version 8` | `version 8` |
| `raw.gitmirror.com/...` | TLS 握手失败 | 未复测 | 未复测 |

结论：jsDelivr 全系节点**会**追上 `main`，但每次发版后重新滞后数小时到一天
（v8 滞后约 18 小时，v9 推送后一刻钟内仍停在 v8）；`purge.jsdelivr.net`
主动刷新返回 `finished` 后也不会立刻同步。所以准确的说法是**延迟不可控**，
而不是「永远不会追上」——但仍不能作为首选导入链接。用 jsdelivr 导入过的用户
请**删掉订阅重新导入**。`37dff21`（换链接）规则本体未变，`version` 保持 8；
`version 9` 来自第五轮批次 1 的规则清理。

> 2026-11-12 第六轮更正：上表里 `github.com/.../raw/main/` 之所以「三时点
> 始终同步」，是因为它 **302 到 `raw.githubusercontent.com`**，本来就是同一份
> 文件，不是独立线路。本机代理实测该域名连续 3 次 `502`（`http=000`），
> 同期 raw 稳定 `200`。因此它**不能当作 raw 不通时的兜底**；真正的兜底是
> 方式 B 本地粘贴。README 与订阅头里原先「国内备用」的措辞已一并纠正。

### 第五轮：官方语义复核与文档自洽（2026-11-11，`version 9`）

这一轮没有新的真机反馈，改为把前几轮「凭印象写下的官方语义」逐条回查，
并对全量静态数据做交叉验证（`_probe/91_audit_round5.py`：330 个 manifest
Activity、4865 个 `type=id` 名、订阅与规则 groups 深度比对、每个 rule 的选择器
分支合规性）：

| 复核项 | 官方依据 | 结论 / 处置 |
|---|---|---|
| `resetMatch` 合法值 | `RawCommonProps`：`"activity" \| "match" \| "app"`，默认 `activity`；且「刷新 activity 并不代表 activityId 变化」 | 第四轮改用 `activity` 成立 |
| `action: "back"` | `RawRuleProps` 的 action 枚举明确含 `back` | 组 102 兜底动作合法 |
| `excludeActivityIds` 优先级 | `RawAppRuleProps`：「优先级高于 activityIds」 | 组 102 声明 24 项却只有 23 项生效，删掉被遮蔽的同名冗余项 |
| `fastQuery` 格式 | `optimize#rule-format` 允许 `[id=]` / `[vid=]` / `[text=]` / `[text^=]` / `[text*=]` / `[text$=]` 及其 `\|\|` 组合；「只要有一个分支不符合，整个 `\|\|` 表达式就必须停用全局快速查询」 | 组 100 的 `[text*="跳过"]` **合规**，`fastQuery:true` 有效；组 101 因 `desc*="关闭"` 必须全树遍历，刻意不开 |
| 组间节点互斥 | GKD 无跨组互斥机制 | README 原先两处「不会命中同一节点」的断言不成立，已改为职责互补说明并补排障归因 |

另修正一批把历史参数写成现在时的注释与段落（组 102 注释里 16 处
`matchTime 8000` / `actionMaximum 2` / `resetMatch app`），以及「版本核对」
章节把只在 arsc 出现、并未写进规则的 `fl_native` / `ksad_ad_dislike`
误列为规则 `vid` 的表述。规则行为只变了一处（删冗余项），故 `version` 升到 9。

### 第六轮：变异测试与官方 schema 复核（2026-11-12，`version 9`）

这一轮没有新的真机反馈，改为**反过来检验校验脚本本身**：不再继续堆断言，
而是注入已知会破坏行为的改动，看既有 251 项检查能不能真的拦住。

| 轮次 | 做法 | 结果 |
|---|---|---|
| 第一轮（`_probe/95_mutation_test.py`） | 14 个变异只改主规则文件 | 全部「被捕获」，但**多数是假阳性** —— 唯一失败的几乎都是「订阅 groups 与本地规则完全一致」。正常发布一定会重新生成订阅，那条断言届时必然通过 |
| 第二轮（`_probe/96_mutation_resync.py`） | 16 个变异按真实流程「改规则 → 重生成订阅 → 全量校验」 | **3 个真实漏洞**：以下破坏性改动全部以 `rc=0` 通过 |

3 个漏洞都落在组 101（它常驻首页、没有窗口限流，改坏的后果最隐蔽）：

| 变异 | 若被误改的后果 | 修复 |
|---|---|---|
| 给组 101 加 `matchTime: 8000` | 重现第四轮「进入 App 几秒后的弹窗拦不住」——规则在弹窗出现前就休眠 | 断言：组 101 不得出现 `matchTime` / `actionMaximum` / `resetMatch` |
| 删掉组 101 的 `actionCd: 1500` | 同一广告可能被连点，第二下容易落在素材上打开落地页（官方默认仅 1000ms） | 断言：`actionCd` 必须为 1500 |
| 删掉选择器末尾 `[visibleToUser=true]` | 连不可见节点一起点，误伤范围扩大到整棵节点树 | 断言：选择器必须以 `[visibleToUser=true]` 结尾 |

其余 13 个变异（删 `desc*="关闭"`、删组 100 的 `fastQuery`、两组同步删掉
`HomeActivity` / 京东 JAD 容器、删组 102 的 `excludeActivityIds`、把
`matchTime` 改回 8000、`action` 改成 `click`、`resetMatch` 改回 `app`、
混入跑步页 / 通用 `btn_close`、拆成两条 rule、删 EOF 哨兵、把广告宿主
加进排除列表等）都被既有断言捕获。修复后重跑第二轮得到
`UNCAUGHT_AFTER_RESYNC=0`，校验项从 251 增至 258。

> 校验脚本在 `_probe/`、**不在本仓库**（仓库按约定只发布规则），所以这三条
> 不变量同时写进了规则文件组 101 的注释和下面「修改规则」章节 —— 那是仓库内
> 唯一的防线。

官方 schema 同步复核（把前几轮凭印象写下的部分逐条回查）：

| 复核项 | 官方依据 | 结论 |
|---|---|---|
| 组级 `actionCd` / `matchTime` 是否合法 | `RawGroupProps` 继承 `RawCommonProps`；`fastQuery` 文档写「**此规则下**的所有选择器」 | 合法，组级参数无需下放到 rule |
| `actionCd` 默认值 | `RawCommonProps`：默认 `1000` ms | 显式写 `1500` 有意义，删掉会退回更短的冷却 |
| 订阅 `id` | `RawSubscription`：「新旧订阅文件 id 不一致则更新失败」「负数 id 被 GKD 内部使用」 | 固定正数 `248879535`，不得改动 |
| `categories` / `globalGroups` / `apps[].name` | `RawSubscription` 三者均可选；`RawApp.name` 可选（未安装时的显示名） | 现有字段写法合法 |
| `updateUrl` 缺失 | `RawSubscription`：「使用添加订阅时填写的链接」 | 第三轮之三的移除成立 |

另外纠正了备用链接的不实陈述：`github.com/.../raw/main/` 最终 302 到
`raw.githubusercontent.com`，不是独立线路；2026-11-12 本机实测该域名连续
3 次代理 502（`http=000`），同期 raw 稳定 200。详见「导入」与「第四轮之三」。

## 版本核对

规则中的 `activityIds` 与 39 个 `vid` 已逐项对照 7.3.93 的完整
`AndroidManifest.xml`（330 个 activity / activity-alias）与 `resources.arsc`
（4865 个 `type=id` 名）：`iv_close`、`ad_close`、`dialog_close`、
`jad_interstitial_close`、`tianmu_iv_close` 等均在规则白名单内且确实存在。
`fl_native`、`ksad_ad_dislike` 只是早期探针在 arsc 里查到过的名字，**并未写进
规则**；旧订阅用的 `layoutContainer`、`ad_flag_source_layout` 已消失。

本包的穿山甲壳是 `com.byazt.cef.Stub_*`（7 个），**不是**常见的
`com.bytedance.sdk.openadsdk.stub.activity.Stub_Standard_Portrait_Activity`——后者在本包
manifest 里不存在，写成它会导致规则永不触发。

**其他版本必须重新核对组件名与资源 ID 后再使用。**
本项目基于静态分析，规则**未经真机效果验证**；欢迎提交脱敏后的 GKD 快照
（`i.gkd.li/i/...`）或误伤报告。

## 验证与回退

1. **先核对规则版本**：远程订阅在 GKD 订阅页应显示 `version: 9`；本地粘贴的确认末尾有
   `// === END OF FILE ===` 且组 101 的单条规则里同时含 `vid=` 与 `desc*="关闭"`。
   版本不对，后面测了也白测。
2. 三个组保持默认启用，冷启动 3 次查看 GKD 触发记录；若发现首页被自动退出，
   先单独关掉组 102（其余两组继续生效）。
3. 完整走一遍「开屏 → 首页 → 开始跑步 → 结束 → 成绩上传」，确认计时、轨迹、上传正常。
4. 无误后再导入上游订阅（记得关闭上面 3 组），重复第 2、3 步。
5. **关闭 GKD 的无障碍开关即完全回退**，不改包、不 root、无残留。

## 修改规则

改 `com.zjwh.android_wh_physicalfitness.json5` 后：

- **方式 A**：同步更新 `subscription.json5` 里的 `apps[0].groups`（两处内容必须一致），
  并把顶层 `version` **+1**，否则 GKD 不会拉取更新；
- **方式 B**：重新整段粘贴到 GKD 本地订阅（GKD 不会读取本仓库）。

**必须保持的不变量**（第六轮变异测试证明，违反后既有校验可能全部放过）：

| 组 | 不变量 | 违反后果 |
|---|---|---|
| 101 | 不得加 `matchTime` / `actionMaximum` / `resetMatch` | 首页延迟弹窗在规则休眠后才出现，重新漏拦 |
| 101 | 保留 `actionCd: 1500` | 同一广告连点，可能点开落地页 |
| 101 | 选择器末尾保留 `[visibleToUser=true]`、保持**单条 rule** | 误点不可见节点 / 同一按钮被点两次 |
| 100 | 保留 `fastQuery: true`、`resetMatch: "activity"`、`actionMaximum: 2` | 开屏退回全树遍历 / 第二个广告位永不触发 |
| 102 | 保留 `action: "back"`、`excludeActivityIds` 排除启动入口 | 命中素材跳落地页 / 按返回键直接退出 App |
| 全部 | `activityIds` 不含任何 `*_reward_*`、跑步 / 成绩页 | 触碰红线 |

改完请重新生成订阅（规则注释不会进订阅，但 `groups` 必须与主规则逐字段一致）
并让 `version` 递增；本地粘贴方式不受 `version` 影响。

每次改动后先走一遍完整跑步流程，确认计时 / 轨迹 / 成绩正常，再叠加上游订阅。

## 真机排障

| 现象 | 优先检查 | 处理 |
|---|---|---|
| 支付宝 App 的开屏 / 弹窗广告拦不住 | 本订阅按包名隔离，`apps` 只含运动世界校园 | 不是 bug；导入上游订阅（含支付宝 44 组），见「其它 App 的广告」；注意别误关支付宝 `13 全屏广告-弹窗广告` |
| 导入报 `Unclosed array at index ...` / `Unclosed object` / `Expecting ',' delimiter` | **粘贴内容被截断**，末尾缺 `}` `]` `}` | 改用方式 A 订阅链接；或重新全选复制，确认末尾有 `// === END OF FILE ===`。不要用聊天记录里的旧版文本 |
| 导入成功但只有 2 个规则组 | 复制的是修复前的旧版本（没有组 102） | 从 `main` 分支重新取文件；旧版还含本包不存在的 `openadsdk.stub`，规则不会触发 |
| 仓库已更新但广告照旧、规则内容没变 | 用 `*.jsdelivr.net` 任一节点导入，每次发版后缓存滞后数小时到一天 | 在 GKD 订阅页核对版本号；**删掉订阅**，改用 `raw.githubusercontent.com` 重新导入，`?v=` 参数不能绕过缓存 |
| 主链接打不开，换 `github.com/.../raw/main/` 也打不开 | 该路径最终仍 302 到 `raw.githubusercontent.com`，不是独立线路（2026-11-12 本机实测 `github.com` 3/3 次代理 502） | 改用**方式 B 本地粘贴**；或临时用 jsDelivr 节点并核对 `version` |
| 订阅页停在旧 `version`、几秒后弹窗仍拦不住 | 缓存节点还没追上 `main`（v9 发布后仍停在 `version 8`） | 同上，删掉重新导入；`version 6` 及更早不含第四轮的时序修复 |
| 京东 60 元券 /「上滑查看详情」弹窗没跳掉 | 仍是 `version 1/2`：缺京东 `vid`，或末尾 `[clickable=true]` 把非 clickable 的关闭按钮整批筛掉 | 确认订阅已刷新到 `version 9`（或在 GKD 里看规则组 101 是否含「关闭」兜底）；仍无效就抓快照看真实 `vid` / `activityId` |
| **开屏 / 固定式能拦，进入 App 几秒后的弹窗拦不住** | `version 6` 及更早：组 100 开屏点过一次后整次会话休眠、组 102 的 8 秒窗口已耗尽且白名单缺 Dialog 类容器 | 升到 `version 9`；核对订阅页版本号，仍漏就抓快照确认弹窗的 `activityId` 与关闭节点 |
| 关闭按钮被连点两下、反而打开了广告落地页 | 旧版组 101 把 `vid` 与 `desc*="关闭"` 拆成两条 rule，同一按钮被命中两次 | 确认已升到 `version 4` 或更新；两条已合并为单条 rule。仍复现就关掉上游订阅排查双规则源 |
| 返回键兜底只按了一次就没动静 | 旧版 `actionCd` 与 `matchTime` 同为 8000，第二次永远排不进窗口 | 确认已升到 `version 4` 或更新，`actionCd` 应为 2500 |
| 首页被自动退出 | 组 102 的返回键没被广告浮层消费 | 在 GKD 界面单独关掉组 102，组 100 / 101 继续生效；附快照提 issue |
| 冷启动完全没有触发记录 | 无障碍被 vivo 回收、规则未保存成功 | 查 GKD 服务状态、电池白名单、多任务锁定；重新粘贴一次 |
| 有触发记录但广告还在 | 命中的是旧节点，或广告页不在白名单 | 看触发记录里的 `activityId`，把脱敏快照发 issue |
| 首页正常按钮被点 | 组 101 白名单误命中业务节点 | 先关组 101，确认是否发生在 `mvi.home.HomeActivity` |
| 一次开屏连点两下 | 两个规则源同时生效；或同一跳过按钮同时命中组 100 的 `text*="跳过"` 与组 101 的 skip 类 `vid` | 先二选一（只留上游订阅或只留本规则）；仍复现就临时关组 101 确认是否跨组重叠 |
| 跑步途中出现自动点击 | **立刻关闭 GKD 无障碍开关** | 附触发记录提 issue，核对 `excludeActivityIds` 是否覆盖该页面 |

抓快照（判断漏拦时最关键的一步）：

1. 等弹窗出现时，先看 GKD →「触发记录」：这一刻有没有规则命中、命中的
   `activityId` 是什么。**没有任何记录**说明该页面根本不在 `activityIds`
   白名单里，或规则已因 `matchTime` / `actionMaximum` 休眠。
2. GKD →「快照」（或触发记录里的「查看快照」）→ 分享，得到 `i.gkd.li/i/...`
   链接。务必在**弹窗还在屏幕上**时抓，抓早了节点树里没有广告层。
3. 在快照里找右上角的 X / 关闭按钮，看它的 `vid`、`text`、`desc`、
   `visibleToUser` 与 `clickable`。若三者全空，说明是纯绘制的 View，
   只能靠 `back` 或按快照里的相对层级收窄，不能靠 `vid` 继续猜。
4. **先脱敏**（账号、学校、位置、成绩），不要贴原始截图或完整 logcat。
   把链接 + 触发记录里的 `activityId` 一起发 issue，就能按真实节点定规则。

## 已知限制

- 规则来自对一份 7.3.93 样本的静态分析，**未经真机验证**；不同渠道包、加固版本或
  App 更新都会改变组件名与资源 ID，需重新核对。
- `activityIds` / `excludeActivityIds` 是 **startWith** 匹配，前缀过短会扩大命中范围。
  本规则刻意使用全名，只有 `com.byazt.cef.Stub_` 用前缀（覆盖 7 个穿山甲壳 Activity）。
- 上游订阅持续更新，`key 13 / 14 / 15` 可能变动，**按规则组名称识别**，不要只记数字。
- 广告由服务端开关控制，样式随时变化；本规则只处理「能读到节点的跳过 / 关闭」，
  不处理预加载缓存素材本身，也不修改任何网络请求。
- 需要 GKD 本体与无障碍权限（撰写时为 v1.13.0）。Android 13+ 侧载安装时，
  无障碍开关可能被「受限设置」置灰，需要在系统应用信息里手动允许。
- 组 100 的 `[text*="跳过"]` 与组 101 白名单里的 skip 类 `vid`
  （`jad_splash_skip_btn`、`hiad_btn_skip`、`mimo_interstitial_skip` 等）**没有做
  节点级互斥**：同一个开屏跳过按钮若同时带文本与资源 ID，可能被两组各点一次。
  真机出现「一次开屏连点两下」时，除排查双规则源外也要考虑此项。目前刻意保留
  这些 `vid` —— 部分 SDK 的跳过按钮只暴露 `vid` 而没有文本，删掉会漏拦。
- 组 101 刻意**不加 `matchTime` / `actionMaximum` 限流**：它常驻于
  `.mvi.home.HomeActivity`，而首页弹窗可能在进入 App 后任意时刻出现，加窗口会
  重现第四轮的「几秒后弹窗拦不住」。代价是这条 43 分支规则在首页持续全树遍历
  （`desc*="关闭"` 分支不符合官方快速查询格式），耗电与误点风险由
  `actionCd 1500`、`visibleToUser=true` 与只收 SDK 专属 `vid` 前缀共同约束。
  若真机发现首页正常控件被点，先单独关组 101。

## 红线

- 不要用 `pm disable` 禁用 `mvi.splash.SplashActivity`（启动入口）或任何 `mvi/run`、
  `mvi/sport` 组件。**规则在 `activityIds` 里匹配某个页面 ≠ 禁用该组件**：前者只是读取
  该页面上的广告节点，后者会让应用起不来。
- 不要屏蔽业务 / 定位域名，不要改包、不要改写 `api/v70360/ad/config` 响应：
  该 App 有加固、设备指纹与成绩上报完整性校验，风险是成绩作废或账号异常。
- 服务端存在 `WhiteTeacher` 免广告白名单；学校统一账号可走管理员正规渠道，零风险。
- 不要上传任何 APK 或反编译产物到本仓库；贡献规则前请先阅读 `NOTICE.md`。

## 许可与免责

规则文件采用 [MIT](LICENSE)。本项目与运动世界校园及其开发者、GKD、
上游订阅作者均无关联、无授权、无背书；详见 [`NOTICE.md`](NOTICE.md)。
使用规则可能影响账号或跑步成绩，风险自负；不构成法律意见，也不保证去广告效果。
