# 运动世界校园 · GKD 规则

> 本仓库**只发布一份规则**：针对 `com.zjwh.android_wh_physicalfitness`
> **7.3.93（versionCode 407）** 的 GKD 本地订阅「应用规则」。
> 不含 APK、不含修改版客户端、不含启动守卫工具、不含分析数据集。

## 文件

| 文件 | 用途 |
|---|---|
| `com.zjwh.android_wh_physicalfitness.json5` | 规则本体，JSON5，整段复制即可 |
| `README.md` / `LICENSE` / `NOTICE.md` / `.gitignore` | 仓库必要文件 |

## 导入

```
GKD → 订阅 → 本地订阅 → 应用规则 → + → 粘贴整段 JSON5 → 保存
```

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
| 100 | 开屏「跳过」文本兜底 | `matchTime 10000`、`actionMaximum 1`、`resetMatch: app`；`excludeActivityIds` 排除跑步 / 成绩 / 考试 / 定位页 |
| 101 | 全屏 / 插屏广告关闭按钮 | `activityIds` 白名单 14 项（startWith 匹配），`actionCd 1500`；只点 `iv_close` / `ad_close` / `dialog_close` |
| 102 | 视频广告返回键兜底 | `enable: false` **默认禁用**；需要时在 GKD 界面里打开这一组，不要手工改 JSON5 |

组 100 与组 101 是**按文本 / 按资源 ID 互补**，不是同一条规则写两遍。激励视频 / 奖励页、
`mvi.run` / `mvi.sport` / `RunPlanActivity` 均不在匹配范围内；无坐标点击、无跨应用全局规则，
组 102 未启用时不会按返回键。

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

1. 只启用本规则的组 100 / 101（组 102 保持禁用），冷启动 3 次查看 GKD 触发记录。
2. 完整走一遍「开屏 → 首页 → 开始跑步 → 结束 → 成绩上传」，确认计时、轨迹、上传正常。
3. 无误后再导入上游订阅（记得关闭上面 3 组），重复第 1、2 步。
4. **关闭 GKD 的无障碍开关即完全回退**，不改包、不 root、无残留。

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
