# ChaiChaiEmbyTV Quest 3 输入问题交接说明

> **2026-07-04 更新（v28）**：Quest 3 实机反馈"点进度条不跳、拖动可以"——
> v27 的条带判定与 x 几何是像素比例基准，实机分辨率/密度不同导致错位。
> v28 全部 dp 化：条带=距底 75-135dp，x 几何 = 37.714/38.095dp pad +
> 5.9429dp/字符，运行时乘 DisplayMetrics.density。模拟器回归 -1px。
> 已上传 release（universal `d7ed3149…`、arm64 `0d046504…`）并更新 body。
> 实机若仍不准：抓 QuestWake 日志看点击走 tap 还是 drag seek abs=true/false
> 定位（详见 work/release-notes-v28.md）。release 上 v22/v25/v27 旧资产
> 待用户手动清理（delete-asset 被会话权限拦截）。


> **2026-07-03 更新 4（v27）**：进度条可直接点击/拖动（底部条带 y∈0.66h-0.84h
> 内按下=绝对定位模式）；拖动实时 seek（500ms 节流+UP 精确落点）；新增
> 9924 键（seek 后注入，仅 `sd1.d0(k)` 刷新控制栏计时，拖动中控制栏不再
> 消失）。进度条几何是**动态**的（时间文字位数挤压弹性布局，用户发现）：
> `L=(0.043574+0.006866*nl)*vw, R=(0.95598-0.006866*nr)*vw`，nl/nr 由
> questTimeChars(位置/时长) 算（4/5/7/8 字符）。同格式区域点击→knob 偏差
> 0px；跨位数边界 seek 有 ±18px 布局重排残差（固有）。教训：getX()=view
> 本地坐标；DisplayMetrics.widthPixels=窗口宽≠物理屏宽；标定用"seek 日志
> pos+knob 像素扫描"自校准。APK：
> `outputs/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v27.apk`
> (SHA256 `39c4c5916cd3972c1433cfeae2418594da1171ff22be37519a030c2d8c729dfe`)，
> 已复制 `~/Downloads/`。release 上仍是 v25。详见 `work/release-notes-v27.md`。

> **2026-07-03 更新 3（v26）**：修复①播放页进度条无法拖动（Compose 进度条
> 不消费指针拖动，事件穿透到 PlayerView）——PlayerView.onTouchEvent 接管
> 手势：|dx|>100px 的水平拖动 → 相对 seek（全屏宽=全片长），小位移仍走
> tap→9923→toggle；②剧集列表倒序——`p80` 的 `sort_descending` prefs 默认值
> true→false（app 内有切换入口 w40，切换后持久化）。模拟器双向 seek、tap、
> 集数升序均验证通过。APK：
> `outputs/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v26.apk`
> (SHA256 `ff4d0dab6706b12c0d50d5c99ba2eb48d6443d24610fc5c0b2d0053b196329b2`)，
> 已复制 `~/Downloads/`，release 尚未上传 v26。详见 `work/release-notes-v26.md`。

> **2026-07-03 更新 2（v25）**：v24 触屏验证通过但用户鼠标实测仍复现——
> `ua1->a` 是"焦点在控制栏元素上"状态，鼠标 hover 按钮后 a=true，
> W/X 整条链路被 block（原生设计：焦点在按钮时确认键交给按钮）。
> v25 改 `ua1`：9923 不再 remap 走 W/X，KeyDown 直接
> `setPlayWhenReady(!x)` + `sd1.d0(ua1->k)` 刷时间戳（k 驱动控制栏显示，
> 已实测），KeyUp 消费。模拟器 V1-V3 通过（含焦点在控制栏的场景）。
> APK：`outputs/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v25.apk`
> (SHA256 `e4961bdd9ec3cdd703c99cbe51aa349014ce540da1a3df3d70a8f0ea9e636d7d`)，
> 已复制到 `~/Downloads/`。v23/v24 均有缺陷勿发布。详见
> `work/release-notes-v25.md`。待用户鼠标/Quest 3 实机确认。

> **2026-07-03 更新（v24）**：修复"点击画面暂停后无法再次点击画面恢复播放"，
> 并修复 v23 引入的 2.0x 倍速卡死 regression。要点：`sd1.W/X` 是确认键
> down/up 状态机（W=标记按下+唤醒+启动长按倍速检测，X=取消检测+短按 toggle
> 播放/暂停）；v23 吞掉 9923 KeyUp 导致 X 不执行。v24：① ua1 恢复 9923 纯
> remap（down/up 都→23）；② PlayerView 新增 onTouchEvent + `questTapOnVideo`
> 静态标志（触摸真正落到视频层才置位）；③ MainActivity 注入加该标志门槛
> （点控制栏按钮不再误注入）。模拟器端到端验证通过（T1-T6，见
> `work/release-notes-v24.md`），待 Quest 3 实机确认。APK：
> `outputs/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v24.apk`
> (SHA256 `4573f56261840b4592bbb1e989a2d6c806fd3cd0b8294ed33ad54bf8a6b42e44`)，
> 已复制到 `~/Downloads/`。注意：v23 的 apk（outputs 里）有已知 regression，勿发布。
> 另：`ua1->j` 不是"控制栏可见"而是"确认键按下中"标志；控制栏可见性疑似由
> 交互时间戳派生，ua1 里没有直接可用的可见性状态。
> 以下 v22 及更早的交接内容仅作历史参考。

> **2026-07-02 更新（v22）**：根因已在模拟器上用真实 Emby 服务器端到端定位并修复。
> 播放页唤醒只挂在 Compose onPreviewKeyEvent 的 keyCode 23/66 上，没有任何
> tap 唤醒逻辑；Quest 手柄点击是指针事件所以永远唤不醒。v22 做了三处修改：
> ① PlayerView 增加 questAttached 静态标志；② MainActivity.dispatchTouchEvent
> 在播放页把短 tap 转成自定义 keyCode 9923 注入；③ ua1 把 96/85/62/9923 重映射为 23。
> 模拟器验证 touch/mouse/BUTTON_A 均能唤醒控制栏，非播放页无影响。
> 详见 `work/release-notes-v22.md`。APK：
> `outputs/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v22.apk`
> (SHA256 `2dead7e1e8170e66d69ba1dbab86bc4a96468268a8e08cbba4fa07d361a66a76`)。
> v21-diag 起保留 QuestWake 四层日志，Quest 实机可 `adb logcat -s QuestWake` 定位。
> 待 Quest 3 实机确认。以下为 v20 时期的历史交接内容。

## 当前目标

用户希望让 `ChaiChaiEmbyTV-0.3.0-alpha6` 在 Meta Quest 3 上作为普通安卓应用使用。当前大部分点击和输入问题已经能用，剩余核心问题是：

- 播放页中，播放控制栏自动隐藏后，Quest 3 手柄无论按什么键都无法唤醒控制栏。
- 用户已明确说这个问题先不继续在当前会话里处理，准备交给另一个 AI。

## GitHub Release

仓库：

https://github.com/ji1360677347/ChaiChaiEmbyTV

当前 release：

https://github.com/ji1360677347/ChaiChaiEmbyTV/releases/tag/v0.3.0-alpha6-quest3-fix

目前 release 资产已清理，只保留最新版：

https://github.com/ji1360677347/ChaiChaiEmbyTV/releases/download/v0.3.0-alpha6-quest3-fix/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v20.apk

SHA256：

`83682ed756ea4dcf0d64bad1692834284622522dd94536d5fcc8e0b6bdfa4031`

## 本地工作区

当前工作目录：

`/Users/lwj/Documents/Codex/2026-06-30/users-lwj-downloads-chaichaiembytv-0-3`

主要目录和工具：

- 解包目录：`work/decoded-fullres-v18`
- Apktool：`work/tools/apktool.jar`
- JDK：`work/tools/jdk-21.0.11+10/Contents/Home`
- Android build tools：`work/android-sdk/build-tools/35.0.0`
- Debug keystore：`work/quest-emby-native/debug.keystore`
- 输出 APK：`outputs/ChaiChaiEmbyTV-0.3.0-alpha6-player-wake-v20.apk`

包名：

`com.dh.myembyapp.quest3regular2`

## 已经解决的问题

### 1. 普通按钮 / 勾选 / 保存等点击问题

早期版本修过 TV Material 组件对鼠标/Quest 点击不友好的问题。

关键补丁：

`work/decoded-fullres-v18/smali_classes4/androidx/tv/material3/SurfaceClickableUtilsKt.smali`

处理方式：

- 将 TV Material clickables 包一层 Compose foundation `ClickableKt.clickable-O2vRcR0`
- 目标是让 Quest 3 手柄点击和安卓模拟器鼠标点击可以触发普通按钮、勾选框、保存按钮等。

用户反馈后来已可用。

### 2. 配置页文字框无法调起键盘

用户反馈：点击文字框只出现焦点框，不能编辑，也不弹键盘。

关键补丁：

`work/decoded-fullres-v18/smali_classes4/h02.smali`

处理方式：

- 这是 `TvConfirmEditableBasicTextField` / `TvConfirmEditableOutlinedTextField` 的 focus handler。
- 原逻辑 focus gain 不进入编辑态，只在 focus loss 时退出编辑。
- 已改为 focus gain 时：
  - 将编辑态 `MutableState` 设置为 `Boolean.TRUE`
  - 调用 `SoftwareKeyboardController.show()`
- 模拟器验证过点击添加服务器页的输入框后：
  - `mInputShown=true`
  - `mIsInputViewShown=true`
  - `inputType=0x8001`

用户后续也确认输入框问题基本解决。

## 当前剩余问题

播放时控制栏隐藏后，Quest 3 手柄无法唤醒播放控制栏。

用户最后反馈：

> 仍然没有解决，但先不管了，删掉之前的所有版本，只保留最新版

也就是说 v20 仍没有修好播放控制栏唤醒。

## 已经尝试过但没有最终解决的方向

### v17 / v18：Activity 全局事件桥

关键文件：

`work/decoded-fullres-v18/smali_classes4/com/dh/myembyapp/MainActivity.smali`

已添加 / 修改过：

- `questDispatchCenterTap()Z`
- `questShowKeyboardIfEditing()Z`
- `questIsAcceptingText()Z`
- `dispatchTouchEvent`
- `dispatchKeyEvent`
- `dispatchGenericMotionEvent`
- `MainActivity$QuestShowKeyboardRunnable`

尝试策略：

- 在 Activity 层把 Quest / 鼠标 / 遥控事件转换成合成中心点击。
- 对 `DPAD_CENTER`、`ENTER`、`SPACE`、`MEDIA_PLAY_PAUSE`、`BUTTON_A`、`MENU` 等按键做额外 synthetic tap。
- 避免输入框编辑时误触发 synthetic tap。

结果：

- 对文字框和一些按钮曾有帮助。
- 对播放页隐藏控制栏唤醒无效。

推断：

- 播放页可能不是 Activity 层拿不到事件这么简单。
- 或者合成中心点击没有落到 Compose 控制层的正确状态机上。

### v20：Media3 PlayerView 不截留按键

关键文件：

`work/decoded-fullres-v18/smali_classes4/wa1.smali`

这里创建嵌入式播放器：

- `PlayerScreen$lambda$105$createEmbeddedPlayerView(...)`
- 返回 `androidx.media3.ui.PlayerView`
- 调用了 `setUseController(false)`，说明原生 Media3 controller 关闭，ChaiChai 用 Compose 自绘控制栏。

已改：

- 创建 `PlayerView` 后设置：
  - `setFocusable(false)`
  - `setFocusableInTouchMode(false)`
  - `setClickable(false)`

关键文件：

`work/decoded-fullres-v18/smali_classes3/androidx/media3/ui/PlayerView.smali`

已改：

- 在 `PlayerView.dispatchKeyEvent(KeyEvent)` 中，如果 `useController=false`，对以下按键直接返回 `false`，避免被原生 `PlayerView` 消费：
  - `DPAD_CENTER` 23
  - `ENTER` 66
  - `SPACE` 62
  - `MEDIA_PLAY_PAUSE` 85
  - `BUTTON_A` 96
  - `MENU` 82

结果：

- APK 可安装、可启动。
- 但用户在 Quest 3 实机反馈播放控制栏仍无法唤醒。

## 播放页相关定位

播放器界面主要在：

`work/decoded-fullres-v18/smali_classes4/sd1.smali`

相关符号：

- `PlayerScreen`
- `ModernPlayerControls`
- `PlayerScreen$lambda$105$handlePlayerRootPreviewKeyEvent`

根按键处理 wrapper：

`work/decoded-fullres-v18/smali_classes4/ua1.smali`

对应函数签名字符串：

`PlayerScreen$lambda$105$handlePlayerRootPreviewKeyEvent(...;Landroid/view/KeyEvent;)Z`

读到的关键逻辑：

- `DPAD_CENTER` / `ENTER` 的 KeyDown 进入：
  - `Lsd1;->W(...)Z`
- KeyUp 进入：
  - `Lsd1;->X(...)Z`

`sd1.W(...)` 开头逻辑很关键：

- 如果控制栏隐藏，会把某个控制栏显示状态 `MutableState` 设为 `TRUE`
- 并更新时间戳状态
- 返回 `true`

相关工具函数：

`Lsd1;->d0(Landroidx/compose/runtime/MutableState;)V`

作用：

- 将当前 `System.currentTimeMillis()` 写入某个 `MutableState<Long>`
- 很可能用于刷新 / 延长控制栏显示时间。

## 建议下一个 AI 优先调查方向

### 方向 1：确认 Quest 3 按键到底有没有进入 `ua1.invoke`

现在最大的未知点是：

- Quest 3 手柄按键是否进入了 Compose 的 `onPreviewKeyEvent`
- 如果进入，是否走到了 `sd1.W(...)`
- 如果没有进入，是哪个 View / Surface / Activity 层吞掉了

建议加临时 logcat 日志：

- 在 `MainActivity.dispatchKeyEvent`
- 在 `androidx.media3.ui.PlayerView.dispatchKeyEvent`
- 在 `ua1.invoke`
- 在 `sd1.W(...)`

日志 tag 建议统一：

`QuestWake`

至少打印：

- keyCode
- action
- `useController`
- 是否进入 `sd1.W`
- `sd1.W` 返回值

这样能快速判断事件断在哪一层。

### 方向 2：不要继续盲目 synthetic tap

之前多轮 synthetic tap 对播放页无效，可能因为：

- Compose 控制栏显示不靠屏幕点击坐标，而靠内部 state
- 合成 touch 没有触发播放器根的 pointer input
- 事件被 video surface / PlayerView / Compose AndroidView 边界吃掉

更建议直接找控制栏显示状态 `MutableState`，在实际收到 Quest 按键的最早入口处调用对应显示逻辑。

### 方向 3：查 `AndroidView` 嵌入 PlayerView 的焦点关系

`wa1.smali` 创建 `PlayerView` 后交给 Compose `AndroidView` 嵌入。即使 `PlayerView` 自身 `setFocusable(false)`，子 View / SurfaceView / TextureView 仍可能拿焦点或输入通道。

建议查：

- `getVideoSurfaceView()`
- 是否是 `SurfaceView` 或 `TextureView`
- 创建后是否也要对 surface 子 View 设置 `focusable=false`
- 是否需要 `descendantFocusability=FOCUS_BLOCK_DESCENDANTS`

### 方向 4：查播放页是否进入沉浸式 / 系统焦点异常

Quest 3 上播放页可能进入特殊窗口/沉浸式状态。建议确认：

- Activity 是否仍拿到 `dispatchKeyEvent`
- 窗口 focus 是否在 app
- 是否被系统 media key 机制截走
- Quest 手柄按键 keyCode 是否真的是预期的 23 / 96 / 66

## 重新构建命令

```bash
set -e
ROOT="$PWD"
export JAVA_HOME="$ROOT/work/tools/jdk-21.0.11+10/Contents/Home"
export PATH="$JAVA_HOME/bin:$PATH"
APKTOOL="$ROOT/work/tools/apktool.jar"
UNSIGNED="$ROOT/outputs/ChaiChaiEmbyTV-0.3.0-alpha6-next-unsigned.apk"
ALIGNED="$ROOT/outputs/ChaiChaiEmbyTV-0.3.0-alpha6-next-aligned.apk"
SIGNED="$ROOT/outputs/ChaiChaiEmbyTV-0.3.0-alpha6-next.apk"
rm -f "$UNSIGNED" "$ALIGNED" "$SIGNED"
"$JAVA_HOME/bin/java" -jar "$APKTOOL" b work/decoded-fullres-v18 -o "$UNSIGNED"
"$ROOT/work/android-sdk/build-tools/35.0.0/zipalign" -p -f 4 "$UNSIGNED" "$ALIGNED"
"$ROOT/work/android-sdk/build-tools/35.0.0/apksigner" sign \
  --ks "$ROOT/work/quest-emby-native/debug.keystore" \
  --ks-pass pass:android \
  --key-pass pass:android \
  --ks-key-alias androiddebugkey \
  --out "$SIGNED" "$ALIGNED"
"$ROOT/work/android-sdk/build-tools/35.0.0/apksigner" verify --verbose "$SIGNED"
sha256sum "$SIGNED"
```

## 模拟器安装 / 启动命令

```bash
ADB="$HOME/Library/Android/sdk/platform-tools/adb"
APK="outputs/ChaiChaiEmbyTV-0.3.0-alpha6-next.apk"
$ADB install -r "$APK"
$ADB shell pm clear com.dh.myembyapp.quest3regular2
$ADB shell monkey -p com.dh.myembyapp.quest3regular2 1
$ADB shell pidof com.dh.myembyapp.quest3regular2
```

## 发布 / 清理 release 资产

上传新 APK：

```bash
gh release upload v0.3.0-alpha6-quest3-fix outputs/NEW.apk \
  --repo ji1360677347/ChaiChaiEmbyTV \
  --clobber
```

查看资产：

```bash
gh release view v0.3.0-alpha6-quest3-fix \
  --repo ji1360677347/ChaiChaiEmbyTV \
  --json assets,url,name,tagName
```

只保留最新版资产时，注意不要误删目标 APK。

## 注意事项

- 不要再上传 36KB 那种临时 WebView / 空壳 APK。用户已经明确不接受，目标必须基于原 ChaiChai APK。
- 当前 release 已只保留 v20，旧资产都删了。
- 用户在 Quest 3 实机测试才是最终标准；模拟器只能验证安装、启动、输入框、无崩溃。
- 播放页控制栏问题必须做日志定位，不建议继续凭感觉叠 synthetic tap。
