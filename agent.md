# agent.md — Typora Themes（Sun / Moon / Claude）

> 面向 AI agent 的项目说明。改任何东西前先读本文件。

## 项目概况

- Typora 主题仓库，含**一组同排版三主题**：**Moon**（暗色）+ **Sun**（亮色·金）+ **Claude**（亮色·珊瑚）。
- 远程：`https://github.com/ShenZiLi/typora-theme`（public，分支 `main`）。
- 本地：Typora 主题目录（`~/Library/Application Support/abnerworks.Typora/themes`）。

## 🔒 核心铁律：三主题是镜像关系

**Moon / Sun / Claude 除颜色外，字体、间距、行高、选择器、mermaid 块必须逐字一致。** 任何修改都不得只改一侧。

维护方向（已固化）：
- **`moon.css` 是唯一手改源文件**（暗色）。
- **`sun.css` / `claude.css` 是派生文件**——都由 moon.css 用下方脚本生成（各自换一套 `:root` 基底色），**禁止手工改两份亮色文件的排版/字体/间距**。
- 代码高亮：`Moon/codeblock.xydark.css` 为暗色源；`Sun/codeblock.xylight.css` 与 `Claude/codeblock.xylight.css` 是其亮色镜像（结构逐行一致、仅色值不同）。目前两份亮色镜像同内容（GitHub light 中性配色），可各自演化——**结构改动三份都要同步**。

### 文件地图

```
moon.css                     暗色主题（唯一手改源）
sun.css                      亮色主题·金（派生自 moon.css，勿手改排版）
claude.css                   亮色主题·Claude 珊瑚（派生自 moon.css，勿手改排版）
Moon/codeblock.xydark.css    CodeMirror 暗色高亮（镜像源）
Sun/codeblock.xylight.css    CodeMirror 亮色高亮（xydark 的亮色镜像）
Claude/codeblock.xylight.css CodeMirror 亮色高亮（同上，初始同 Sun，可各自演化）
Moon/FiraMono-Regular.ttf    遗留未用（@font-face 已注释）
Moon/FiraMono-Bold.ttf       遗留未用
Readme.md
agent.md
```

## SOP-A：改排版/字体/间距 → 只改 moon.css，重新派生 sun.css 与 claude.css

moon.css 配色全走 `:root` CSS 变量，正文规则零硬编码色——这是可派生的前提。改完 moon.css 后执行：

```bash
cd "~/Library/Application Support/abnerworks.Typora/themes" && python3 << 'PYEOF'
import re

moon = open('moon.css', encoding='utf-8').read()

# Sun 亮色基底 = 白底 + 金强调（GitHub 语义）
SUN = {
  '--primary-color': '#b58900', '--primary-color-rgb': '181, 137, 0',
  '--bg-color': '#ffffff', '--bg-color-dark': '#f5f6f7',
  '--dark-trait': '#d0d7de', '--light-trait-100': '#f0f2f4',
  '--light-trait-200': '#e2e5e8', '--light-trait-300': '#8b949e',
  '--light-trait-400': '#6e7781', '--text-color': '#1d1d1f',
  '--text-color-secondary': '#6e6e73', '--text-highlight-color': '#1d1d1f',
  '--select-text-bg-color': 'rgba(0, 122, 255, 0.22)', '--code-color': '#a626a4',
}
# Claude 亮色基底 = 奶油画布 + 珊瑚强调（awesome-design-md/claude DESIGN.md）
# canvas #faf9f5 / primary #cc785c / ink #141413 / hairline #e6dfd8
CLAUDE = {
  '--primary-color': '#cc785c', '--primary-color-rgb': '204, 120, 92',
  '--bg-color': '#faf9f5', '--bg-color-dark': '#efe9de',
  '--dark-trait': '#e6dfd8', '--light-trait-100': '#f5f0e8',
  '--light-trait-200': '#ebe6df', '--light-trait-300': '#8e8b82',
  '--light-trait-400': '#6c6a64', '--text-color': '#141413',
  '--text-color-secondary': '#6c6a64', '--text-highlight-color': '#141413',
  '--select-text-bg-color': 'rgba(204, 120, 92, 0.22)', '--code-color': '#a9583e',
}

def derive(palette, import_line, header):
    """moon.css 全文复制，仅替换 :root 基底值；结构逐字保留。"""
    out, i = [], 0
    while True:
        st = moon.find(':root {', i)
        if st == -1:
            out.append(moon[i:]); break
        d = 0; j = st
        while j < len(moon):
            if moon[j] == '{': d += 1
            elif moon[j] == '}':
                d -= 1
                if d == 0: break
            j += 1
        block = moon[st:j+1]
        for k, v in palette.items():
            block = re.sub(r'(?m)^(\s*' + re.escape(k) + r'\s*:\s*)[^;]+;',
                           lambda m, k=k, v=v: m.group(1) + v + ';', block)
        out += [moon[i:st], block]; i = j + 1
    body = ''.join(out)
    body = body.replace("@import './Moon/codeblock.xydark.css';", import_line)
    return header + body

HEADER_SUN = ("/* ============================================================\n"
              "   Sun (Light) · Typora Theme\n"
              "   与 Moon 主题排版/字体/间距完全一致，仅配色为亮色。\n"
              "   派生自 Moon.css，两文件请同步维护。\n"
              "   ============================================================ */\n")
HEADER_CLAUDE = ("/* ============================================================\n"
                 "   Claude (Light) · Typora Theme — Claude 亮色（awesome-design-md/claude）\n"
                 "   与 Moon 主题排版/字体/间距完全一致，仅配色为亮色。\n"
                 "   派生自 Moon.css，两文件请同步维护。\n"
                 "   ============================================================ */\n")
open('sun.css', 'w', encoding='utf-8').write(
    derive(SUN, "@import './Sun/codeblock.xylight.css';", HEADER_SUN))
open('claude.css', 'w', encoding='utf-8').write(
    derive(CLAUDE, "@import './Claude/codeblock.xylight.css';", HEADER_CLAUDE))
print('sun.css + claude.css regenerated')
PYEOF
```

⚠️ `:root` 块扫描的 break 只能放在 `}` 分支内（`if d == 0: break` 不能写成无分支的紧凑循环，否则从 `:` 起第一轮就 break，palette 全部落空 → 亮色文件复制成暗色 :root）。此 bug 曾真实发生（Sun 一度渲染成暗色），靠下方 palette 断言兜底。

**改颜色**（仅想调某色）：直接改对应 palette dict 后重新派生（两份亮色一起生成），或手动同步改 sun.css / claude.css / moon.css 三个 `:root` 的同名行（见对照表）。**改字体/间距**：只改 moon.css，重新派生即可，禁止手改 sun.css / claude.css。

### 基底色对照表（:root 叶子值）

| 变量 | Moon（暗） | Sun（亮 · 白底金） | Claude（亮 · 奶油珊瑚） | 语义 |
|---|---|---|---|---|
| `--primary-color` | `#fcba03` 金 | `#b58900` 金 | `#cc785c` 珊瑚 | 主强调 |
| `--primary-color-rgb` | `252, 186, 3` | `181, 137, 0` | `204, 120, 92` | 主强调 rgb |
| `--bg-color` | `#292929` | `#ffffff` | `#faf9f5` 奶油 | 画布 |
| `--bg-color-dark` | `#1f1f1f` | `#f5f6f7` | `#efe9de` | 面板/侧栏 |
| `--dark-trait` | `#141414` | `#d0d7de` | `#e6dfd8` | 最深描边（hairline） |
| `--light-trait-100` | `#373737` | `#f0f2f4` | `#f5f0e8` | hover 底 |
| `--light-trait-200` | `#545454` | `#e2e5e8` | `#ebe6df` | 表格边/分隔 |
| `--light-trait-300` | `#777` | `#8b949e` | `#8e8b82` | 次级描边 |
| `--light-trait-400` | `#8c8c8c` | `#6e7781` | `#6c6a64` | heading 符 |
| `--text-color` | `#f8f8f2` | `#1d1d1f` | `#141413` 暖墨 | 正文 |
| `--text-color-secondary` | `#d2d3d3` | `#6e6e73` | `#6c6a64` | 次级文字 |
| `--text-highlight-color` | `#fff` | `#1d1d1f` | `#141413` | 高亮文字 |
| `--select-text-bg-color` | `rgba(255,255,255,.2)` | `rgba(0,122,255,.22)` | `rgba(204,120,92,.22)` | 选中底 |
| `--code-color` | `#f3b3f8` | `#a626a4` | `#a9583e` | 行内代码 |

其余 :root 键全为 `var()` 派生，自动跟随，无需三写。正文区（:root 之外）三份逐字一致，含 `.md-diagram-panel`（mermaid）块——mermaid 为 **Apple 亮色画布卡**（`#f5f5f7` 底 + 白卡节点 + Action Blue `#0066cc` 强调 + 序号 marker 修复），三主题相同、不改色。

## SOP-B：改代码高亮 → 同步镜像

结构改动（增删选择器/规则）需同时落到三个文件：`Moon/codeblock.xydark.css`（源）+ `Sun/codeblock.xylight.css` + `Claude/codeblock.xylight.css`。只改配色时用脚本从 xydark 镜像到两份亮色（dark→light 映射，GitHub dark→light 语义）：

```bash
python3 << 'PYEOF'
import shutil
# Claude 版初始与 Sun 同源；若 Claude 需要珊瑚系语法色，在此处为 Claude 单独维护映射表。
shutil.copy('Sun/codeblock.xylight.css', 'Claude/codeblock.xylight.css')
print('Sun xylight -> Claude xylight synced')
PYEOF
```

亮色镜像生成参考映射（dark → light，GitHub 语义）：`#fff→#1f2328`、`#b8bfc6→#24292f`、`#6699CC/#EC5F67→#cf222e`、`#99C794→#1a7f37`、`#65737E→#6e7781`、`#C594C5→#8250df`、`#d8d5d5→#afb8c1`、`#d3d3ef→#0969da`、`#b7b3b3→#57606a`、`#f3b3f8→#8250df`、`#5FB3B3→#57606a`、`#84B6CB→#0550ae`、`#F99157→#0550ae`、`#9FBAD5→#0969da`、`#1cc685→#1a7f37`、`#4a89dc→#cfe3ff`。

## 校验（每次改完必跑）

```bash
# 1) 三主题结构一致（排除 header/@import/:root 值后应为 0 差异）
python3 -c "
import re
def norm(p, skip=0):
    lines = open(p).read().splitlines()[skip:]
    k = re.compile(r'^\s*--(?:primary-color|primary-color-rgb|bg-color|bg-color-dark|dark-trait|light-trait-\d+|text-color|text-color-secondary|text-highlight-color|select-text-bg-color|code-color):')
    out = []
    for ln in lines:
        if ln.startswith('@import'): out.append('@IMPORT'); continue
        if k.match(ln): out.append(ln.split(':')[0].strip() + ': <VAL>'); continue
        out.append(ln.rstrip())
    return out
m, s, c = norm('moon.css'), norm('sun.css', 5), norm('claude.css', 5)
assert len(m) == len(s) == len(c), (len(m), len(s), len(c))
assert not [(i,x,y) for i,(x,y) in enumerate(zip(m,s)) if x!=y], 'sun 结构不一致'
assert not [(i,x,y) for i,(x,y) in enumerate(zip(m,c)) if x!=y], 'claude 结构不一致'
print('三主题结构一致 OK')
"
# 2) palette 生效断言（结构 diff 跳过 :root 值，此处兜底防派生失效；改 palette 时同步更新锚点）
python3 -c "
def root_of(p):
    t = open(p).read(); i = t.find(':root {'); return t[i:t.find('}', i)+1]
sr, cr = root_of('sun.css'), root_of('claude.css')
assert '--bg-color: #ffffff' in sr and '--text-color: #1d1d1f' in sr and '--primary-color: #b58900' in sr, 'sun palette NOT applied'
assert '--bg-color: #faf9f5' in cr and '--text-color: #141413' in cr and '--primary-color: #cc785c' in cr, 'claude palette NOT applied'
print('palette OK (sun 白底金 / claude 奶油珊瑚)')
"
# 3) codeblock 镜像结构 diff（dark 源 vs 两份亮色，色值归一后应一致）
diff <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Moon/codeblock.xydark.css) \
     <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Sun/codeblock.xylight.css) && echo 'mirror OK'
diff <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Moon/codeblock.xydark.css) \
     <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Claude/codeblock.xylight.css) && echo 'mirror OK'
# 4) 括号配平
python3 -c "s=open('moon.css').read(); print(s.count('{'), s.count('}'))"
```

## Typora 平台约束（踩过的坑，勿再犯）

1. **主题 css 文件名必须小写开头**（`sun.css`/`moon.css`/`claude.css`）。大写开头（`Sun.css`）Typora 扫描器不识别 → 主题菜单消失/空。显示名 = 去扩展名首字母大写（sun→Sun、moon→Moon、claude→Claude）。
2. **改名/新增主题后如不可见，检查 plist**：`~/Library/Preferences/abnerworks.Typora.plist` 按显示名记忆当前主题（`theme`=亮色、`darkTheme`=暗色、`useDarkTheme`）。若当前选中主题指向已删除/改名文件必须**先完全退出 Typora** 再 `defaults write`（运行中写会被退出时内存旧值覆盖）。新增主题无需写 plist（菜单实时扫描）。
3. 主题仍不可见时清缓存：退出后删 `~/Library/WebKit/abnerworks.Typora` 与 `~/Library/Caches/abnerworks.Typora`。
4. APFS 大小写不敏感盘上改名用两步 `git mv A.css tmp.css && git mv tmp.css a.css`。
5. 文件末尾追加内容前先确认有换行，否则会拼行（曾把 `.DS_Store` 拼成 `.workbuddy.DS_Store`）。

## 设计基线（勿随意偏离）

- 正文字体：`"HONOR Sans Design Regular"` 本地字体栈（未装则回退系统栈），三主题一致。
- 等宽字体：`'JetBrains Mono', 'HONOR Sans Design Regular', 'Zconsola', monospace`。
- mermaid：Apple 亮色画布卡（单一 Action Blue `#0066cc` 强调、卡片无阴影），暗色正文里嵌亮卡是设计意图。
- CSS 语法用 python 原子替换或重写，慎用多次 Edit（曾出现同文件连续 Edit 漏落盘）。

## Git 约定

- 每次改动：`git add -A && git commit -m "<英文简短描述>" && git push origin main`。
- `.gitignore` 已含 `.idea` / `.workbuddy` / `.DS_Store`；`.workbuddy/` 下是记忆日志，不入库。
- 改 moon.css 排版后提交需同时含重新生成的 sun.css 与 claude.css（一条提交，不留中间态）。
