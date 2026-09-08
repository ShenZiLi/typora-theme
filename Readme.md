<p align="center">
  <img src="https://img.shields.io/github/stars/ShenZiLi/typora-theme?style=flat-square&label=Stars&color=b58900" alt="Stars">
  <img src="https://img.shields.io/github/license/ShenZiLi/typora-theme?style=flat-square&color=8b949e" alt="License">
  <img src="https://img.shields.io/badge/Typora-0.11%2B-1d1d1f?style=flat-square" alt="Typora">
</p>

<h1 align="center">🌙☀️ Typora Theme · Moon / Sun / Claude</h1>

<p align="center">
  <b>一套基底，三种心境。</b><br>
  Moon（暗 · 金）· Sun（亮 · 白底金）· Claude（亮 · 奶油珊瑚）共享同一套正文排版体系。<br>
  Moon 与 Sun 是<em>严格镜像</em>（仅配色不同）；Claude 在基底之上叠加 Claude 设计语言——<br>
  衬线标题、深色代码卡、珊瑚点缀，行文体验既有连贯性又各具性格。
</p>

---

## ✨ 特性

- 🌗 **Moon / Sun 镜像**：共享同一套排版体系（字体、行高、间距、选择器逐字一致），切换不打乱阅读节奏
- 🎨 **Claude 特色主题**：基底同源之上叠加完整 Claude 设计语言（参考 awesome-design-md/claude）——**衬线标题**（400 字重不粗体）、**深色代码卡**（奶油 × 深墨 `#181715` 节奏）、**珊瑚**链接与引用、Claude 奶油色 Mermaid
- 📊 **亮色图表**：内建 Mermaid 亮色画布——Moon/Sun 为 Apple 风格（白卡 + Action Blue），Claude 为奶油卡 + 珊瑚强调；即使正文是暗色，图表也自成亮底
- 🔤 **字体统一**：正文 HONOR Sans Design Regular，代码 JetBrains Mono / Zconsola；Claude 主题标题额外采用衬线（Cormorant Garamond / 宋体系）
- 🎨 **语义代码高亮**：Moon/Sun 用 GitHub 经典色（暗 xydark / 亮 xylight 镜像）；Claude 用深卡 one-dark 语法（暖 cream 文字）
- 🧱 **零配置**：纯 CSS，无插件依赖，放进主题文件夹即可用

## 🖼 预览

> 截图待补充。可将预览图放入 `img/` 目录后在此引用，例如：
>
> ```markdown
> | Moon（暗） | Sun（亮） | Claude（奶油珊瑚） |
> | --- | --- | --- |
> | ![moon](img/moon.png) | ![sun](img/sun.png) | ![claude](img/claude.png) |
> ```

## 📦 主题一览

| 主题 | 模式 | 文件 | 说明 |
| --- | --- | --- | --- |
| **Moon** | 🌙 暗色 | `moon.css` + `Moon/` | 深底浅字，金色强调（`#fcba03`） |
| **Sun** | ☀️ 亮色 | `sun.css` + `Sun/` | 白底墨字，金色强调（`#b58900`） |
| **Claude** | 🎨 亮色 | `claude.css` + `Claude/` | 奶油米色画布 + 珊瑚强调（`#cc785c`）；衬线标题、深色代码卡、Claude 奶油 Mermaid |

## 🚀 安装

1. 从本仓库下载 `.css` 文件与同名文件夹：`sun.css` + `Sun/`、`claude.css` + `Claude/`（亮色）或 `moon.css` + `Moon/`（暗色），建议都装以便随时切换
2. 打开 Typora：**偏好设置 → 外观 → 打开主题文件夹**
3. 将下载的 `.css` 文件与同名文件夹一并放入该目录
4. 重启 Typora，在 **主题** 菜单中选择 **Moon**、**Sun** 或 **Claude**

> 💡 字体提示：为保证最佳效果，建议本地安装 [HONOR Sans Design](https://www.honor.com/cn/)（未安装时自动回退系统字体栈，不影响使用）；Claude 主题衬线标题自动使用系统宋体（Songti SC 等）。

## 🔧 开发 / 同步维护

- **Moon ↔ Sun**：排版由 `moon.css` 单一维护、SOP-A 自动派生 `sun.css`（仅 `:root` 基底色不同），保持严格镜像。
- **Claude**：基底与 Moon 同源（SOP-C 重建），尾部拼接 `Claude/character.css` 特色层——特色改动只编辑该文件，不与 Moon/Sun 镜像。
- 修改后运行 `agent.md` 中的校验脚本（镜像一致性、palette 断言、括号配平）。

## 📄 更新日志

- **2026-09-08**：Moon / Sun / Claude 三主题发布 —— 由 Typora 默认主题（Notes Dark / Github）改造：统一字体、Moon/Sun 排版镜像、Apple 风格亮色 Mermaid、GitHub 语义代码高亮。Sun 白底金；Claude 奶油珊瑚（Claude 设计语言：衬线标题 / 深色代码卡 / 珊瑚 Mermaid，独立特色层 `Claude/character.css`）

## 🤝 致谢

- 基于 Typora 官方默认主题 [typora/typora-default-themes](https://github.com/typora/typora-default-themes)（Github / Notes Dark）改造
- [Typora Themes 官方站](https://theme.typoraio.cn/) 提供的主题分发平台
- Claude 特色层的设计 token 取自 [VoltAgent/awesome-design-md](https://github.com/VoltAgent/awesome-design-md) 的 `claude/DESIGN.md`

## 📜 License

[MIT](LICENSE) © [ShenZiLi](https://github.com/ShenZiLi)
