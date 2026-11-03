# AutoReviveHelper 私有插件源

这是 AutoReviveHelper 的自建 Dalamud 插件源，格式是 `pluginmaster.json`（国服 / 国际服 Dalamud 都认这个格式）。

## 怎么加进游戏

1. 进游戏后按 `/xlplugins`，点「设置」
2. 切到「实验性功能」页，在「第三方插件源」里点「+」新增一条
3. 填这个地址：
   ```
   https://raw.githubusercontent.com/your-name/AutoReviveHelper/main/pluginmaster.json
   ```
4. 保存后回到插件列表，点「刷新」就能看到「自动复活助手」

## 本地调试用（先把仓库推上去之前）

还没推 GitHub 时，直接把 `latest.zip` 丢到：

```
%APPDATA%\XIVLauncherCN\installedPlugins\AutoReviveHelper\1.0.0.0\
```

把 zip 解压进去就行，重启游戏后生效。

## 文件说明

- `pluginmaster.json` —— 源清单，Dalamud 读这个
- `repo.json` —— 同一份内容的备用文件名（部分老版本认这个）
- `latest.zip` —— 插件本体包

## 发版流程

改完代码后：

```powershell
cd D:\DEV\AutoReviveHelper\AutoReviveHelper
dotnet build -c Release -p:DalamudLibPath="C:\Users\acer\AppData\Roaming\XIVLauncherCN\addon\Hooks\dev\"
```

产物在 `bin\Release\AutoReviveHelper\latest.zip`，复制到本目录，然后：
- 有新版本时，把 `pluginmaster.json` 里 `AssemblyVersion` 和两个 `DownloadLink*` 的版本号一起改掉
