# 自动复活助手 · AutoReviveHelper

死了自动喊「999 + 地图名 + 坐标」，没人来救就按间隔一直重喊，长时间无人理会自动长按「返回副本入口」的确定，别人拉你秒接，被救活自动道谢。

推荐用在**博兹雅战线**和**新月岛**——野外大地图、单人能做、躺了没人盯血条，正是它派得上用场的地方。

---

## 怎么装（推荐）

进游戏，聊天框输入：

```
/xlplugins
```

点顶部「设置」→ 切到「实验性功能」→ 找到「第三方插件源 / 自定义插件仓库」→ 点「+」新增一条，填：

```
https://raw.githubusercontent.com/arcnapan/AutoReviveHelper/main/pluginmaster.json
```

保存后回到插件列表点「刷新」，就能看到「自动复活助手」，勾上启用即可。

> 加不上或者下载失败，多半是国内直连 `raw.githubusercontent.com` 被掐。可以挂上代理再试，或者用下面的手动方式。

## 手动装（不联网也能装）

把本仓库的 `latest.zip` 下载下来，解压到：

```
%APPDATA%\XIVLauncherCN\installedPlugins\AutoReviveHelper\1.1.0.0\
```

重启游戏，`/xlplugins` 里启用。

## 用起来

| 命令 | 作用 |
| --- | --- |
| `/autorevive` | 打开设置窗口 |
| `/autorevive on` | 快速开启 |
| `/autorevive off` | 快速关闭 |

## 设置项

| 配置 | 默认 | 说明 |
| --- | --- | --- |
| 总开关 | 开 | 关掉后插件完全不动作 |
| 死亡喊话 | 开 | 死了自动喊 |
| 自动接受复活 | 开 | 别人拉你自动点「是」 |
| 复活道谢 | 开 | 站起来自动说谢谢 |
| 频道 | `/p` | 可选 `/p` `/sh` `/y` `/a` |
| 喊话模板 | `999 {map} (X:{x} Y:{y}) 救救我` | 支持 `{map}` `{x}` `{y}` |
| 道谢模板 | `谢谢！！！` | 同上 |
| **喊话间隔** | **1 分钟** | 死亡期间每隔这么久重喊；0 = 只喊一次 |
| **超时返回** | **5 分钟** | 这么久没人救就自动长按返回入口；0 = 永不 |
| **超时后自动长按确定** | **开** | 关掉则只提醒不动手 |
| **模拟长按时长** | **1.5 秒** | 兜底硬推进度条用，一般不用改 |
| 调试日志 | 关 | 打开后聊天框会打印每一步判断 |

## 长按返回是怎么做的

`AddonSelectYesno` 里那个「确定」按钮是个长按组件 `AtkComponentHoldButton`（槽位 `AtkComponentHoldButton278`）。

插件的做法是：往它注入 `AtkEventType.MouseDown`，每帧推进 `Progress.CurrentValue`，
到 `TargetValue` 就注入 `MouseUp` + `FireCallbackInt(1)`。
如果 `HoldPressSeconds` 秒过去官方进度条还没走完，就把 `CurrentValue` 直接顶到 `TargetValue` 兜底。

弹窗里另一个按钮是【保留】——那等于继续等人救，插件**绝不碰它**。

## 已知限制

1. 长按这条没法在所有机器上预先验证。第一次用建议开调试日志，看聊天框有没有打出「死亡已超过 X 分钟无人救助，准备返回入口」。
2. 某些版本的长按组件可能不是 `AtkComponentHoldButton278`（还有 `280` / `288`），真遇到没反应就把 `dalamud.log` 发出来。
3. 坐标用的是兜底换算（世界坐标压成 0~100 百分制），多数野外地图量级一致，但不保证和地图读数完全相符。

## 文件说明

- `pluginmaster.json` —— 插件源清单，Dalamud 读这个
- `repo.json` —— 同一份内容的备用文件名（部分老版本认这个）
- `latest.zip` —— 插件本体包

## 发版流程

```powershell
cd D:\DEV\AutoReviveHelper\AutoReviveHelper
dotnet build -c Release -p:DalamudLibPath="$env:APPDATA\XIVLauncherCN\addon\Hooks\dev\"
```

产物在 `bin\Release\AutoReviveHelper\latest.zip`，复制到本目录，然后：
- 改 `pluginmaster.json` 和 `repo.json` 里的 `AssemblyVersion`
- `git add -A` + `git add -f latest.zip`（`.gitignore` 会挡 zip，必须 `-f`）
- `git commit` + `git push`

## 当前版本

**1.1.0.0** —— 喊话间隔 X 分钟 + 超时 Y 分钟自动长按返回副本入口
