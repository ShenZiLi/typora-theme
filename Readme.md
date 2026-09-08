<p align="center">
  <img src="https://img.shields.io/github/stars/ShenZiLi/typora-theme?style=flat-square&label=Stars&color=b58900" alt="Stars">
  <img src="https://img.shields.io/github/license/ShenZiLi/typora-theme?style=flat-square&color=8b949e" alt="License">
  <img src="https://img.shields.io/badge/Typora-0.11%2B-1d1d1f?style=flat-square" alt="Typora">
</p>

<h1 align="center">🌙☀️ Typora Theme · Sun & Moon</h1>

<p align="center">
  <b>一套排版，两种心境。</b><br>
  Sun（亮）与 Moon（暗）是一组<em>镜像主题</em>——字体、间距、图表样式完全一致，仅配色不同。<br>
  白天用 Sun，夜里用 Moon，行文体验始终如一。
</p>

---

## ✨ 特性

- 🌗 **双子镜像**：Sun / Moon 共享同一套排版体系（字体、行高、间距、选择器逐字一致），切换主题不会打乱你的阅读节奏
- 📊 **亮色图表**：内建 Mermaid 亮色画布（Apple 风格：白卡节点 + Action Blue 强调）——即使在暗色主题中，图表也保持清晰可读
- 🔤 **字体统一**：正文 HONOR Sans Design Regular，代码 JetBrains Mono / Zconsola，行内代码与代码块观感一致
- 🎨 **GitHub 语义配色**：代码块语法高亮采用 GitHub 经典色（暗色 xydark / 亮色 xylight 镜像）
- 🧱 **零配置**：纯 CSS，无插件依赖，放进主题文件夹即可用

## 🖼 预览

> 截图待补充。可将预览图放入 `img/` 目录后在此引用，例如：
>
> ```markdown
> | Sun（亮） | Moon（暗） |
> | --- | --- |
> | ![sun](img/sun.png) | ![moon](img/moon.png) |
> ```

## 📦 主题一览

| 主题 | 模式 | 文件 | 说明 |
| --- | --- | --- | --- |
| **Sun** | ☀️ 亮色 | `sun.css` + `Sun/` | 奶油米色画布，珊瑚强调（Claude 风格 `#cc785c`） |
| **Moon** | 🌙 暗色 | `moon.css` + `Moon/` | 深底浅字，金色强调（`#fcba03`） |

## 🚀 安装

1. 从本仓库下载 `sun.css` + `Sun/`（亮色）或 `moon.css` + `Moon/`（暗色），建议两个都要以随时切换
2. 打开 Typora：**偏好设置 → 外观 → 打开主题文件夹**
3. 将下载的 `.css` 文件与同名文件夹一并放入该目录
4. 重启 Typora，在 **主题** 菜单中选择 **Sun** 或 **Moon**

> 💡 字体提示：为保证最佳效果，建议本地安装 [HONOR Sans Design](https://www.honor.com/cn/)（未安装时自动回退系统字体栈，不影响使用）。

## 🔧 开发 / 同步维护

Sun 与 Moon 的排版由 `moon.css` 单一维护、自动派生 `sun.css`（仅 `:root` 基底色不同）。修改排版时请遵循 `agent.md` 中的同步契约（SOP-A / SOP-B），并运行其中的校验脚本确认两主题保持镜像。

## 📄 更新日志

- **2026-09-08**：Sun/Moon 双子主题发布 —— 由 Typora 默认主题（Github / Notes Dark）改造：统一字体、排版镜像同步、内建 Apple 风格亮色 Mermaid、GitHub 语义代码高亮

## 🤝 致谢

- 基于 Typora 官方默认主题 [typora/typora-default-themes](https://github.com/typora/typora-default-themes)（Github / Notes Dark）改造
- [Typora Themes 官方站](https://theme.typoraio.cn/) 提供的主题分发平台

## 📜 License

[MIT](LICENSE) © [ShenZiLi](https://github.com/ShenZiLi)
