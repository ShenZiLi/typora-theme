# agent.md — Typora Themes（Sun / Moon 双子主题）

> 面向 AI agent 的项目说明。改任何东西前先读本文件。

## 项目概况

- Typora 主题仓库，只含**一组双子主题**：**Sun**（亮色）+ **Moon**（暗色）。
- 远程：`https://github.com/ShenZiLi/typora-theme`（public，分支 `main`）。
- 本地：Typora 主题目录（`~/Library/Application Support/abnerworks.Typora/themes`）。

## 🔒 核心铁律：Sun/Moon 是镜像关系

**Sun 与 Moon 除颜色外，字体、间距、行高、选择器、mermaid 块必须逐字一致。** 任何修改都不得只改一侧。

维护方向（已固化）：
- **`moon.css` 是唯一手改源文件**（暗色）。
- **`sun.css` 是派生文件**——由 moon.css 用下方脚本生成（换 :root 基底色），**禁止手工改 sun.css 的排版/字体/间距**。
- 代码高亮 `codeblock.xydark.css` / `codeblock.xylight.css` 互为镜像（结构逐行一致、仅色值不同），改一侧必须同步另一侧。

### 文件地图

```
sun.css                      亮色主题（派生自 moon.css，勿手改排版）
moon.css                     暗色主题（唯一手改源）
Sun/codeblock.xylight.css    CodeMirror 亮色高亮（codeblock.xydark 的亮色镜像）
Moon/codeblock.xydark.css    CodeMirror 暗色高亮（镜像源）
Moon/FiraMono-Regular.ttf    遗留未用（@font-face 已注释）
Moon/FiraMono-Bold.ttf       遗留未用
Readme.md
agent.md
```

## SOP-A：改排版/字体/间距 → 只改 moon.css，重新派生 sun.css

moon.css 配色全走 `:root` CSS 变量，正文规则零硬编码色——这是可派生的前提。改完 moon.css 后执行：

```bash
cd "~/Library/Application Support/abnerworks.Typora/themes" && python3 << 'PYEOF'
import re
moon = open('moon.css', encoding='utf-8').read()

# Sun 亮色基底（只替换叶子值；var() 派生值自动跟随）
palette = {
  '--primary-color': '#b58900',
  '--primary-color-rgb': '181, 137, 0',
  '--bg-color': '#ffffff',
  '--bg-color-dark': '#f5f6f7',
  '--dark-trait': '#d0d7de',
  '--light-trait-100': '#f0f2f4',
  '--light-trait-200': '#e2e5e8',
  '--light-trait-300': '#8b949e',
  '--light-trait-400': '#6e7781',
  '--text-color': '#1d1d1f',
  '--text-color-secondary': '#6e6e73',
  '--text-highlight-color': '#1d1d1f',
  '--select-text-bg-color': 'rgba(0, 122, 255, 0.22)',
  '--code-color': '#a626a4',
}

out, i = [], 0
while True:
    st = moon.find(':root {', i)
    if st == -1:
        out.append(moon[i:]); break
    d = 0; j = st
    while j < len(moon):
        d += moon[j] == '{'; d -= moon[j] == '}'
        if d == 0: break
        j += 1
    block = moon[st:j+1]
    for k, v in palette.items():
        block = re.sub(r'(?m)^(\s*' + re.escape(k) + r'\s*:\s*)[^;]+;',
                       lambda m, k=k, v=v: m.group(1) + v + ';', block)
    out += [moon[i:st], block]; i = j + 1
sun = ''.join(out)
sun = sun.replace("@import './Moon/codeblock.xydark.css';",
                  "@import './Sun/codeblock.xylight.css';")
header = "/* Sun (Light) · Typora Theme — 派生自 moon.css，请勿手改排版 */\n"
open('sun.css', 'w', encoding='utf-8').write(header + sun)
print('sun.css regenerated')
PYEOF
```

**改颜色**（仅想调某色）：直接在 moon.css 与 sun.css 的 `:root` 基底里各改对应行（两文件各一份，见下表），或改 palette 后重新派生。**改字体/间距**：只改 moon.css，重新派生即可，禁止手改 sun.css。

### 基底色对照表（:root 叶子值）

| 变量 | Moon（暗） | Sun（亮） | 语义 |
|---|---|---|---|
| `--primary-color` | `#fcba03` 金 | `#b58900` 深金 | 主强调 |
| `--primary-color-rgb` | `252, 186, 3` | `181, 137, 0` | 主强调 rgb |
| `--bg-color` | `#292929` | `#ffffff` | 画布 |
| `--bg-color-dark` | `#1f1f1f` | `#f5f6f7` | 面板/侧栏 |
| `--dark-trait` | `#141414` | `#d0d7de` | 最深描边 |
| `--light-trait-100` | `#373737` | `#f0f2f4` | hover 底 |
| `--light-trait-200` | `#545454` | `#e2e5e8` | 表格边/分隔 |
| `--light-trait-300` | `#777` | `#8b949e` | 次级描边 |
| `--light-trait-400` | `#8c8c8c` | `#6e7781` | heading 符 |
| `--text-color` | `#f8f8f2` | `#1d1d1f` | 正文 |
| `--text-color-secondary` | `#d2d3d3` | `#6e6e73` | 次级文字 |
| `--text-highlight-color` | `#fff` | `#1d1d1f` | 高亮文字 |
| `--select-text-bg-color` | `rgba(255,255,255,.2)` | `rgba(0,122,255,.22)` | 选中底 |
| `--code-color` | `#f3b3f8` | `#a626a4` | 行内代码 |

其余 :root 键全为 `var()` 派生，自动跟随，无需双写。正文区（:root 之外）moon/sun 逐字一致，含 `.md-diagram-panel`（mermaid）块——mermaid 为 **Apple 亮色画布卡**（`#f5f5f7` 底 + 白卡节点 + Action Blue `#0066cc` 强调 + 序号 marker 修复），两主题相同、不改色。

## SOP-B：改代码高亮 → 同步镜像

结构改动（增删选择器/规则）需同时落到两个文件。只改配色时用脚本镜像（dark→light 映射，GitHub dark→light 语义）：

```bash
python3 << 'PYEOF'
p = 'Sun/codeblock.xylight.css'
s = open(p, encoding='utf-8').read()
cm = {  # dark -> light（只替换色值 token，结构不变）
  '#fff': '#1f2328', '#b8bfc6': '#24292f',
  '#6699CC': '#cf222e', '#EC5F67': '#cf222e',
  '#99C794': '#1a7f37', '#65737E': '#6e7781',
  '#C594C5': '#8250df', '#d8d5d5': '#afb8c1',
  '#d3d3ef': '#0969da', '#b7b3b3': '#57606a',
  '#f3b3f8': '#8250df', '#5FB3B3': '#57606a',
  '#84B6CB': '#0550ae', '#F99157': '#0550ae',
  '#9FBAD5': '#0969da', '#1cc685': '#1a7f37',
  '#4a89dc': '#cfe3ff',
}
for a, b in cm.items(): s = s.replace(a, b)
open(p, 'w', encoding='utf-8').write(s)
print('mirrored')
PYEOF
```

注意映射的 token 顺序：`#EC5F67` 两处（tag/negative/string-2）、`#99C794` 多处（string/quote/positive）、`#cf222e` 是它们的**统一亮色目标**；`#4a89dc`（选中底）→ `#cfe3ff`。反向（亮→暗）同理反查表。

## 校验（每次改完必跑）

```bash
# 1) sun 与 moon 结构一致性（排除 header/@import/:root 值后应为 0 差异）
python3 -c "
import re
m = open('moon.css').read().splitlines()
s = open('sun.css').read().splitlines()[5:]   # 跳过 header
assert len(m) == len(s), (len(m), len(s))
k = re.compile(r'^\s*--(?:primary-color|primary-color-rgb|bg-color|bg-color-dark|dark-trait|light-trait-\d+|text-color|text-color-secondary|text-highlight-color|select-text-bg-color|code-color):')
real = [(i, x, y) for i, (x, y) in enumerate(zip(m, s))
        if not (x.startswith('@import') and y.startswith('@import'))
        and not (k.match(x) and k.match(y) and x.split(':')[0] == y.split(':')[0])
        and x != y]
print('real diffs:', len(real)); [print(i, x, '||', y) for i, x, y in real[:10]]
assert not real
"
# 2) codeblock 镜像结构 diff
diff <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Moon/codeblock.xydark.css) \
     <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Sun/codeblock.xylight.css) && echo 'mirror OK'
# 3) 括号配平
python3 -c "s=open('moon.css').read(); print(s.count('{'), s.count('}'))"
```

## Typora 平台约束（踩过的坑，勿再犯）

1. **主题 css 文件名必须小写开头**（`sun.css`/`moon.css`）。大写开头（`Sun.css`）Typora 扫描器不识别 → 主题菜单消失/空。显示名 = 去扩展名首字母大写（sun→Sun、moon→Moon）。
2. **改名主题后必须同步 plist**：`~/Library/Preferences/abnerworks.Typora.plist` 按显示名记忆当前主题（`theme`=亮色、`darkTheme`=暗色、`useDarkTheme`）。改名后需**先完全退出 Typora** 再 `defaults write abnerworks.Typora theme Sun` / `darkTheme Moon`（运行中写会被退出时内存旧值覆盖）。
3. 主题仍不可见时清缓存：退出后删 `~/Library/WebKit/abnerworks.Typora` 与 `~/Library/Caches/abnerworks.Typora`。
4. APFS 大小写不敏感盘上改名用两步 `git mv A.css tmp.css && git mv tmp.css a.css`。
5. 文件末尾追加内容前先确认有换行，否则会拼行（曾把 `.DS_Store` 拼成 `.workbuddy.DS_Store`）。

## 设计基线（勿随意偏离）

- 正文字体：`"HONOR Sans Design Regular"` 本地字体栈（未装则回退系统栈），两主题一致。
- 等宽字体：`'JetBrains Mono', 'HONOR Sans Design Regular', 'Zconsola', monospace`。
- mermaid：Apple 亮色画布卡（单一 Action Blue `#0066cc` 强调、卡片无阴影），暗色正文里嵌亮卡是设计意图。
- CSS 语法用 python 原子替换或重写，慎用多次 Edit（曾出现同文件连续 Edit 漏落盘）。

## Git 约定

- 每次改动：`git add -A && git commit -m "<英文简短描述>" && git push origin main`。
- `.gitignore` 已含 `.idea` / `.workbuddy` / `.DS_Store`；`.workbuddy/` 下是记忆日志，不入库。
- 改 moon.css 排版后提交需同时含重新生成的 sun.css（一条提交，不留中间态）。
