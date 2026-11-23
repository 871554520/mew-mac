# Mewgenics on Mac / iPhone

## 结论先说

| 目标 | 可行性 |
| :--- | :--- |
| Mac 上跑 | ✅ Wine 封装，`.app` 双击即开 |
| iPhone 上跑（本地） | ❌ iOS 没有 Wine 类兼容层：需要 JIT、`fork/exec`、桌面 OpenGL，三项都不可能 |
| iPhone 上玩（串流） | ✅ Mac 当主机 + Moonlight，今天就能玩 |

## 一、Mac 主机

1. 产物：Actions → `Build macOS app` → 下载 `Mewgenics-mac.zip`，解出一个 `Mewgenics.app`。
2. 装 Wine：`brew install --cask --no-quarantine wine-stable`（或 CrossOver / Whisky）。
3. 用 [Keka](https://www.keka.io/) 打开 `Mewgenics.exe`（4.18 GB 的 RAR5 自解压），解出 `Mewgenics/` 目录。
4. 双击 `.app`，首次会让你指定游戏目录；或把游戏目录放到 `~/Documents/Mewgenics`。
5. 日志：`~/Library/Logs/Mewgenics.log`。

引擎是 Win32 + OpenGL 3.2 core，Wine 的 `wgl` 直接转 macOS 的 NSOpenGL，不需要 DXVK。

## 二、iPhone 串流

1. Mac 上装 [Sunshine](https://github.com/LizardByte/Sunshine/releases)：`brew install --cask sunshine`，或下 dmg。
2. 浏览器开 `https://localhost:47990` 配一次账号密码。
3. Sunshine 里新增应用 `Mewgenics`，命令填：
   `open -a /Applications/Mewgenics.app`
4. iPhone App Store 装 **Moonlight**，同一局域网自动发现 Mac，配对码对上即连。
5. 想要手柄手感，蓝牙手柄直连 iPhone。

外网也能用：Sunshine 支持 UPnP 或 Tailscale 组网，Moonlight 填 Tailscale IP。

## 三、为什么不给 iPhone 出 IPA

Mewgenics 本体是 x86-64 Windows PE，依赖：
- 动态生成代码（JIT）——iOS 不允许 App 运行时生成可执行代码
- 创建子进程（`fork/exec`）——iOS 沙盒不允许
- 桌面 OpenGL 3.2 core（`wglGetProcAddress`）——iOS 只有 OpenGL ES / Metal

所谓"给 iOS 出个兼容层 IPA"，等于要重写 Wine 的核心三件套再去适配 iOS 沙盒，这比移植游戏本身还大，公开领域没有可用实现。
