# 原生游戏库验证记录

本次改造面向已有快捷方式的查找、启动和再次启动。默认图标网格；设置顶部可切换紧凑列表并保存选择。应用首页、底部导航、搜索、容器筛选、收藏、最近启动及浏览状态恢复沿用现有 Java/XML 与运行协议。

## 构建与代码验证

- JDK 17、Android SDK 35、NDK 24.0.8215888、CMake 3.22.1。
- 在 `app` 目录执行 `sh ./gradlew -Dorg.gradle.jvmargs=-Xmx4g --max-workers=2 :app:assembleDebug` 成功。单独打包，避免 Lint 失败提前结束组合任务。
- `:app:testDebugUnitTest`：5 项通过，0 失败，覆盖容器身份隔离、目录移动、重命名、复制/删除及容器 ID 重用。
- `:app:lintDebug` 仍失败：保留上游既有 `targetSdkVersion 28`，触发 `ExpiredTargetSdkVersion`。未降低检查标准；当前报告无其他 Error/Fatal，仍包含上游及原生可访问性等 Warning，不宣称整个旧 App 全面合规。
- APK 位于 `app/app/build/outputs/apk/debug/app-debug.apk`；大小及 SHA-256 见 `build-proof.json`。

## 原生交互证据

独立 AVD `winlator-ui-review`、Android API 30、ARM64、Pixel 5 显示规格。测试快捷方式、容器和最近启动元数据均为明确的 UI 夹具。应用交付包不包含这些夹具。

`native-smoke-results.json` 记录布局切换与进程重启、真实文件名搜索、收藏和搜索条件持久化、无结果恢复、目录持久化及返回游戏库。`native-edge-results.json` 记录大字体下的最近区、横屏导航、跨目录同名项目辨识、损坏文件不阻断加载及损坏项目的修复提示。

截图文件分别覆盖网格、列表、设置、收藏、无结果、深色/大字体、横屏，以及最近区、同名目录与损坏快捷方式。

## 验证边界

- 未启动真实 Windows 游戏，也未验证真实运行退出引发的 App 重启。浏览状态验证通过模拟器进程重启完成；最近区验证使用合成元数据，不代表真实运行成功。
- 未验证物理设备、完整 TalkBack 流程或旧容器迁移的实机兼容性。
- UI 改造已对齐 App fork 的 `3981d86`，保留上游外接鼠标修复。主仓库改用自己的 App 子模块 fork，固定改版提交以便复现。
- 首轮独立设计审查给出 `fix`，列出横屏、异常加载、目录辨识、最近区字体适配、启用图标对比度五项问题；整批修正后，同一审查者逐项评分均为 `resolved`，终审 disposition 为 `ship`。该结论仅覆盖五项修复评分，不代表全产品或真实游戏运行验收。
