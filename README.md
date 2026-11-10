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

国内访问不稳时用 fastly 节点（实测与 `main` 同步）：

```
https://fastly.jsdelivr.net/gh/Hancard/SportsCampus-GKD-Rules@main/subscription.json5
```

> **用哪个链接导入，后续就一直用哪个链接自动更新**（`version 6` 起）。此前订阅文件里
> 硬编码了 `updateUrl` 指向 raw，导致用 fastly 导入的用户在 GKD 自动检查更新时被
> 强制回落到大概率不通的 raw，表现是「仓库明明更新了，广告照旧」。官方
> [RawSubscription](https://gkd.li/api/interfaces/RawSubscription) 明确：`updateUrl`
> 缺失时「使用添加订阅时填写的链接」，故已移除该字段。若你导入的是 `version 5` 或
> 更早的订阅，建议删掉后按上面的链接重新导入一次。

> ⚠️ **不要用 `cdn.jsdelivr.net` 节点导入订阅。** 该节点对 `@main` 有很长的缓存周期，
> 2026-11-09 实测仍返回 **`version 1`**（加 `?v=3` 查询参数也无法绕过），会拿到带
> `[clickable=true]`、组 102 默认禁用的最旧规则，表现就是「仓库明明更新了、广告照旧」。
> `fastly.jsdelivr.net`、`github.com/.../raw/main/` 与固定 commit SHA 的 CDN 链接均实测返回当前版本。
> **导入后请在 GKD 订阅页核对版本号是否为 `version: 6`**；不是就换 raw / fastly 链接重新导入。

导入后 GKD 会显示订阅名「运动世界校园 7.3.93 补充规则」与 **3 个规则组（100/101/102）**。
后续更新规则无需重新粘贴，GKD 会按 `version` 自动拉取（当前 `version: 6`）。
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

本规则是**补充**：组 100 按文本匹配开屏「跳过」，组 101 按资源 ID 匹配广告容器里的
关闭按钮，两者选择器不重叠，不会命中同一节点。主规则仍建议用持续维护的
[`Lin-arm/GKD_subscription`](https://github.com/Lin-arm/GKD_subscription)，任选一个订阅地址：

```
https://gkd667.vv.ax/gkd.json5
https://gkd-subscription-667.pages.dev/gkd.json5
https://raw.githubusercontent.com/Lin-arm/GKD_subscription/main/dist/gkd.json5
```

> 不要使用已归档停更的 `gkd-kit/subscription`：其中的 Activity 名属于重构前的旧结构
> （`activity.SplashActivity` / `ui.AdActivity`），在 7.3.93 上会全部失配。

导入订阅后，**必须关闭以下 3 组「便利类」规则**（它们会动到定位与运动模式，
关闭后不影响任何广告跳过）。按名称识别，key 对应当前 v628 版订阅：

| 规则组 | 必须关闭的原因 |
|---|---|
| `13 权限提示-不开启可选权限` | 会点「暂不开启，继续跑步」；若缺的是定位权限，轨迹失效 |
| `14 权限提示-定位权限` | 会在「定位服务未开启」弹窗上点「取消」 |
| `15 功能类-运动页面自动切换至校园跑` | 自动切换运动模式，成绩按模式校验 |

如果上游订阅已经能跳掉全部广告，**不要导入本仓库规则**，避免两个规则源重复点击。

## 规则组

| key | 行为 | 约束 |
|---|---|---|
| 100 | 开屏「跳过」文本兜底 | `matchTime 10000`、`actionMaximum 1`、`resetMatch: app`；`excludeActivityIds` 41 项覆盖本包 136/140 个业务 Activity，只留 4 个广告宿主 |
| 101 | 全屏 / 插屏广告关闭按钮 | `activityIds` 白名单 24 项（startWith 匹配），`actionCd 1500`；**单条 rule** 用 `\|\|` 连接已核实的 `vid` 与 `desc*="关闭"`，避免同一按钮被点两次 |
| 102 | 广告标签返回键兜底 | **默认启用**；出现「广告」标签且前两组读不到节点时按返回键；`matchTime 8000`、`actionMaximum 2`、`resetMatch: app`、`actionCd 2500` 限流 |

组 100 与组 101 是**按文本 / 按资源 ID 与 contentDescription 互补**，不是同一条规则写两遍。激励视频 / 奖励页、
`mvi.run` / `mvi.sport` / `RunPlanActivity` 均不在匹配范围内；无硬编码屏幕坐标、无跨应用全局规则。
组 102 只在广告容器白名单内按返回键，**不会作用于跑步页**；若实测发现首页被误退出，
在 GKD 界面里单独关掉组 102 即可。

### 京东 JAD 互动插屏（2026-11-09 两轮真机截图反馈修复）

截图里的广告由多层组成：白色「京东 JD / 电脑 11.11 高潮期来袭」Dialog、60 元 PLUS 专享券
卡片与「领券购买」、一个大的黑色圆形白 X、右下角「广告」标签与小 X、底部「上滑或点击按钮
查看详情」手势引导。对应 APK 内的 `assets/jad_slideup.json`。

**第一轮**（`version 2`）修了两处：关闭按钮 `vid` 没覆盖（`jad_interstitial_close` /
`jd_lijin_close`），以及京东 / 天目 / AdGain 的透明与插屏容器不在 `activityIds` 白名单。
**当时的** 9 个 `vid` 全部用脚本核实为本包 `resources.arsc` 中 **type=id 的真实资源名**
（4865 个 id 里逐个查得）；`jad_interstitial_close_view` / `jad_back_close` 只是 layout
名而非 id，写进 `vid` 会永不触发，故未采用。9 个的规模本身在第三轮之二被证明远远不够，
现为 34 个。

**第二轮**（`version 3`）复核了模板与官方 schema，又发现两个确凿缺陷：

1. **`[clickable=true]` 是错误约束（本轮主因）**。GKD 官方 `RawRuleProps` 写明：
   `action` 默认值 `"click"` 会**自动判断** —— 节点 clickable 用 `clickNode`，否则降级
   `clickCenter`；`clickNode` 未被应用接收时也会回落 `clickCenter`。京东这类自定义关闭
   View 常把点击交给父容器或 `onTouch`，`isClickable()` 返回 false，加上这个条件会把它们
   整批筛掉。已改为 `[visibleToUser=true]`，只排除不可见节点。同时补一条
   `[desc*="关闭"]` 兜底，覆盖没有 `vid`、只有 contentDescription 的关闭按钮。
2. **返回键兜底被默认禁用，等于没有兜底**。组 102 原本 `enable: false`，当关闭按钮
   读不到节点时三层规则全部落空。现改为默认启用，并用 `matchTime 8000` /
   `actionMaximum 2` / `resetMatch: app` 限流（`actionCd` 当时误设为 `8000`，
   见第三轮修复，现为 `2500`）。

另外核实：`assets/jad_slideup.json` 是 **Lottie 动画**（`v: 5.6.10`、`nm: 向上划动`），
只负责画手势引导，内部**没有任何 close 字段**——说明关闭按钮是 SDK 的原生 View，
用 `vid` 匹配的方向本身没错，问题出在 `clickable` 条件。

> 仍是**静态推断**：截图看不到节点树，无法确认 X 按钮的 `vid` / `desc` 及承载
> `activityId`。若更新到 `version 6` 后该广告仍未跳过，请抓 GKD 快照
> （`i.gkd.li/i/...`，先脱敏）并附上触发记录里的 `activityId`，按真实节点收窄选择器。

### 第三轮之一：官方 API 语义复核与参数自相矛盾（2026-11-10，`version 4`）

逐条对照 [GKD 官方 API](https://gkd.li/api/interfaces/RawCommonProps) 与
[查询优化](https://gkd.li/guide/optimize) 后，修掉三个脚本此前查不出的缺陷：

1. **组 102 的 `actionMaximum: 2` 是死配置**。此前 `actionCd` 与 `matchTime` 都取
   `8000`，且 `resetMatch: 'app'`。官方定义 `matchTime` 是「规则参与查询屏幕节点时，
   等待一段时间后休眠此规则」，`actionCd` 是「执行 action 最小间隔」——第一次返回键
   后规则进入 8 秒冷却，而 8 秒匹配窗口同时耗尽，第二次永远不可能执行。注释与文档
   声称「每次进入 App 最多按 2 次」与实现不符。现把 `actionCd` 降为 `2500`，让两次
   返回键在 8 秒窗口内真正可达。
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
   现补齐到 **34 个 `vid`**，逐个用脚本核对为真实 `type=id` 资源名。
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

## 版本核对

规则中的 `activityIds` 与 `vid` 已逐项对照 7.3.93 的完整 `AndroidManifest.xml`
（330 个 activity / activity-alias）与 `resources.arsc`：`iv_close`、`ad_close`、
`dialog_close`、`fl_native`、`ksad_ad_dislike` 等存在；旧订阅用的
`layoutContainer`、`ad_flag_source_layout` 已消失。

本包的穿山甲壳是 `com.byazt.cef.Stub_*`（7 个），**不是**常见的
`com.bytedance.sdk.openadsdk.stub.activity.Stub_Standard_Portrait_Activity`——后者在本包
manifest 里不存在，写成它会导致规则永不触发。

**其他版本必须重新核对组件名与资源 ID 后再使用。**
本项目基于静态分析，规则**未经真机效果验证**；欢迎提交脱敏后的 GKD 快照
（`i.gkd.li/i/...`）或误伤报告。

## 验证与回退

1. **先核对规则版本**：远程订阅在 GKD 订阅页应显示 `version: 6`；本地粘贴的确认末尾有
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

每次改动后先走一遍完整跑步流程，确认计时 / 轨迹 / 成绩正常，再叠加上游订阅。

## 真机排障

| 现象 | 优先检查 | 处理 |
|---|---|---|
| 导入报 `Unclosed array at index ...` / `Unclosed object` / `Expecting ',' delimiter` | **粘贴内容被截断**，末尾缺 `}` `]` `}` | 改用方式 A 订阅链接；或重新全选复制，确认末尾有 `// === END OF FILE ===`。不要用聊天记录里的旧版文本 |
| 导入成功但只有 2 个规则组 | 复制的是修复前的旧版本（没有组 102） | 从 `main` 分支重新取文件；旧版还含本包不存在的 `openadsdk.stub`，规则不会触发 |
| 仓库已更新但广告照旧、规则内容没变 | 用 `cdn.jsdelivr.net` 导入，订阅被缓存停在 `version 1` | 在 GKD 订阅页核对版本号；改用 raw 或 fastly 链接重新导入，`?v=` 参数不能绕过缓存 |
| 京东 60 元券 /「上滑查看详情」弹窗没跳掉 | 仍是 `version 1/2`：缺京东 `vid`，或末尾 `[clickable=true]` 把非 clickable 的关闭按钮整批筛掉 | 确认订阅已刷新到 `version 6`（或在 GKD 里看规则组 101 是否含「关闭」兜底）；仍无效就抓快照看真实 `vid` / `activityId` |
| 关闭按钮被连点两下、反而打开了广告落地页 | 旧版组 101 把 `vid` 与 `desc*="关闭"` 拆成两条 rule，同一按钮被命中两次 | 确认已升到 `version 4` 或更新；两条已合并为单条 rule。仍复现就关掉上游订阅排查双规则源 |
| 返回键兜底只按了一次就没动静 | 旧版 `actionCd` 与 `matchTime` 同为 8000，第二次永远排不进窗口 | 确认已升到 `version 4` 或更新，`actionCd` 应为 2500 |
| 首页被自动退出 | 组 102 的返回键没被广告浮层消费 | 在 GKD 界面单独关掉组 102，组 100 / 101 继续生效；附快照提 issue |
| 冷启动完全没有触发记录 | 无障碍被 vivo 回收、规则未保存成功 | 查 GKD 服务状态、电池白名单、多任务锁定；重新粘贴一次 |
| 有触发记录但广告还在 | 命中的是旧节点，或广告页不在白名单 | 看触发记录里的 `activityId`，把脱敏快照发 issue |
| 首页正常按钮被点 | 组 101 白名单误命中业务节点 | 先关组 101，确认是否发生在 `mvi.home.HomeActivity` |
| 一次开屏连点两下 | 两个规则源同时生效 | 二选一：只留上游订阅，或只留本规则 |
| 跑步途中出现自动点击 | **立刻关闭 GKD 无障碍开关** | 附触发记录提 issue，核对 `excludeActivityIds` 是否覆盖该页面 |

抓快照：GKD → 快照 / 触发记录 → 分享得到 `i.gkd.li/i/...` 链接。
**先脱敏**（账号、学校、位置、成绩），不要贴原始截图或完整 logcat。

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
