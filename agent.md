# agent.md — Typora Themes（Moon / Sun / Claude）

> 面向 AI agent 的项目说明。改任何东西前先读本文件。

## 项目概况

- Typora 主题仓库，含**一组同排版三主题**：**Moon**（暗色）+ **Sun**（亮色·金）+ **Claude**（亮色·珊瑚，带独立特色层）。
- 远程：`https://github.com/ShenZiLi/typora-theme`（public，分支 `main`）。
- 本地：Typora 主题目录（`~/Library/Application Support/abnerworks.Typora/themes`）。

## 🔒 核心契约：Moon/Sun 镜像，Claude 基底同源 + 特色解绑

**Moon 与 Sun 除颜色外，字体、间距、行高、选择器、mermaid 块必须逐字一致**（镜像关系，任何修改不得只改一侧）。

**Claude 是"基底同源 + 特色层解绑"**：
- 基底（`:root` palette 前的全部规则）与 Moon 逐字同源——保证正文排版可用、可随 Moon 演进。
- **尾部追加 `Claude/character.css` 特色层**（独立手改文件），**允许且鼓励与 Moon/Sun 不一致**：衬线标题（400）、珊瑚链接/引用、深色代码卡（#181715，cream↔dark 节奏）、Claude 奶油 mermaid（取代基底的 Apple 蓝卡）。
- 因此 claude.css ≠ 镜像文件，结构 = header(6) + moon 基底 + character 层。

维护方向（已固化）：
- **`moon.css` 是唯一手改源文件**（暗色）。
- **`sun.css` 派生**：由 moon.css + SOP-A 生成（换金 palette），禁止手改 sun.css 排版。
- **`claude.css` 重建**：由 moon.css 基底（Claude palette）+ SOP-C 拼接 `Claude/character.css`；**特色只改 character.css，基底排版改 moon.css 后重跑 SOP-C**。
- 代码高亮三份独立：`Moon/codeblock.xydark.css`（暗源）↔ `Sun/codeblock.xylight.css`（亮镜像）；`Claude/codeblock.xydark.css`（Claude 深卡 one-dark 语法，独立）。

### 文件地图

```
moon.css                     暗色主题（唯一手改源）
sun.css                      亮色主题·金（SOP-A 派生，勿手改排版）
claude.css                   亮色主题·Claude（SOP-C 重建 = moon 基底 + character 层）
Moon/codeblock.xydark.css    CodeMirror 暗色高亮（镜像源）
Sun/codeblock.xylight.css    CodeMirror 亮色高亮（xydark 的亮色镜像）
Claude/codeblock.xydark.css  Claude 深卡语法（one-dark 语义色 on #181715，独立）
Claude/character.css         Claude 特色层（唯一手改；改后跑 SOP-C 拼入 claude.css）
Moon/FiraMono-Regular.ttf    遗留未用（@font-face 已注释）
Moon/FiraMono-Bold.ttf       遗留未用
Readme.md
agent.md
```

## SOP-A：改 Moon/Sun 排版 → 只改 moon.css，重新派生 sun.css

moon.css 配色全走 `:root` CSS 变量，正文规则零硬编码色——这是可派生的前提（唯一硬编码色块是 mermaid Apple 卡，见设计基线）。改完 moon.css 后执行：

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

def derive(palette, import_line, header, out_file):
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
    body = ''.join(out).replace("@import './Moon/codeblock.xydark.css';", import_line)
    open(out_file, 'w', encoding='utf-8').write(header + body)

HEADER_SUN = ("/* ============================================================\n"
              "   Sun (Light) · Typora Theme\n"
              "   与 Moon 主题排版/字体/间距完全一致，仅配色为亮色。\n"
              "   派生自 Moon.css，两文件请同步维护。\n"
              "   ============================================================ */\n")
derive(SUN, "@import './Sun/codeblock.xylight.css';", HEADER_SUN, 'sun.css')
print('sun.css regenerated')
PYEOF
```

⚠️ `:root` 块扫描的 break 只能放在 `}` 分支内（`if d == 0: break` 不能写成无分支的紧凑循环，否则从 `:` 起第一轮就 break，palette 全部落空 → 亮色文件复制成暗色 :root）。此 bug 曾真实发生（Sun 一度渲染成暗色），靠下方 palette 断言兜底。

## SOP-C：改 Claude 基底/特色 → 重建 claude.css（基底 + character 层）

**只改特色**（标题、代码卡、mermaid、引用等）→ 编辑 `Claude/character.css`，然后重建：

```bash
cd "~/Library/Application Support/abnerworks.Typora/themes" && python3 << 'PYEOF'
import re
moon = open('moon.css', encoding='utf-8').read()

CLAUDE = {   # 基底亮色 = 奶油画布 + 珊瑚（awesome-design-md/claude DESIGN.md）
  '--primary-color': '#cc785c', '--primary-color-rgb': '204, 120, 92',
  '--bg-color': '#faf9f5', '--bg-color-dark': '#efe9de',
  '--dark-trait': '#e6dfd8', '--light-trait-100': '#f5f0e8',
  '--light-trait-200': '#ebe6df', '--light-trait-300': '#8e8b82',
  '--light-trait-400': '#6c6a64', '--text-color': '#141413',
  '--text-color-secondary': '#6c6a64', '--text-highlight-color': '#141413',
  '--select-text-bg-color': 'rgba(204, 120, 92, 0.22)', '--code-color': '#a9583e',
}
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
    for k, v in CLAUDE.items():
        block = re.sub(r'(?m)^(\s*' + re.escape(k) + r'\s*:\s*)[^;]+;',
                       lambda m, k=k, v=v: m.group(1) + v + ';', block)
    out += [moon[i:st], block]; i = j + 1
body = ''.join(out)
body = body.replace("@import './Moon/codeblock.xydark.css';",
                    "@import './Claude/codeblock.xydark.css';")
HEADER = ("/* ============================================================\n"
          "   Claude (Light) · Typora Theme — Claude 设计语言（awesome-design-md/claude）\n"
          "   基底派生自 Moon.css（排版与 Moon/Sun 同源，勿手改基底排版）；\n"
          "   尾部为 Claude/character.css 特色层（衬线标题/深色代码卡/奶油 mermaid）。\n"
          "   重建方式见 agent.md SOP-C；特色只改 Claude/character.css。\n"
          "   ============================================================ */\n")
character = open('Claude/character.css', encoding='utf-8').read().rstrip() + '\n'
open('claude.css', 'w', encoding='utf-8').write(HEADER + body + '\n' + character)
print('claude.css rebuilt (moon base + character layer)')
PYEOF
```

**改 moon 排版后**：先跑 SOP-A（sun），再跑 SOP-C（claude 基底同步 + 特色层自动保留）。
**改颜色**：sun/claude 基底色 → 改各自 palette dict 重跑；Claude 特色色 → 改 character.css。

### 基底色对照表（:root 叶子值；Claude 特色视觉另见 character.css）

| 变量 | Moon（暗） | Sun（亮 · 白底金） | Claude（基底 · 奶油珊瑚） | 语义 |
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

其余 :root 键全为 `var()` 派生，自动跟随，无需三写。基底正文区 Moon/Sun 逐字一致；Claude 正文基底同源，但**渲染外观可能被 character.css 覆盖**（标题/代码/引用/表格/图片/mermaid），属预期。

## SOP-B：改 Moon/Sun 代码高亮 → 同步镜像

结构改动（增删选择器/规则）需同时落到 `Moon/codeblock.xydark.css`（源）与 `Sun/codeblock.xylight.css`（镜像）。**Claude 代码高亮独立**（Claude/codeblock.xydark.css，深卡 one-dark 语法），一般不随 Moon/Sun 改动；若需同步结构，参照其语法映射手动同步。

亮色镜像参考映射（dark → light，GitHub 语义）：`#fff→#1f2328`、`#b8bfc6→#24292f`、`#6699CC/#EC5F67→#cf222e`、`#99C794→#1a7f37`、`#65737E→#6e7781`、`#C594C5→#8250df`、`#d8d5d5→#afb8c1`、`#d3d3ef→#0969da`、`#b7b3b3→#57606a`、`#f3b3f8→#8250df`、`#5FB3B3→#57606a`、`#84B6CB→#0550ae`、`#F99157→#0550ae`、`#9FBAD5→#0969da`、`#1cc685→#1a7f37`、`#4a89dc→#cfe3ff`。

## 校验（每次改完必跑）

```bash
# 1) sun 与 moon 结构一致（镜像契约；排除 header/@import/:root 值后 0 差异）
python3 -c "
import re
def norm_lines(t, skip=0):
    lines = t.splitlines()[skip:]
    k = re.compile(r'^\s*--(?:primary-color|primary-color-rgb|bg-color|bg-color-dark|dark-trait|light-trait-\d+|text-color|text-color-secondary|text-highlight-color|select-text-bg-color|code-color):')
    out = []
    for ln in lines:
        if ln.startswith('@import'): out.append('@IMPORT'); continue
        if k.match(ln): out.append(ln.split(':')[0].strip() + ': <VAL>'); continue
        out.append(ln.rstrip())
    return [l for l in out if l != '']
m = norm_lines(open('moon.css').read())
s = norm_lines(open('sun.css').read(), 5)
assert len(m) == len(s) and not [(i,x,y) for i,(x,y) in enumerate(zip(m,s)) if x!=y], 'sun 偏离镜像'
print('sun == moon 镜像 OK')
"
# 2) palette 生效断言（结构 diff 跳过 :root 值；改 palette 时同步更新锚点）
python3 -c "
def root_of(p):
    t = open(p).read(); i = t.find(':root {'); return t[i:t.find('}', i)+1]
sr, cr = root_of('sun.css'), root_of('claude.css')
assert '--bg-color: #ffffff' in sr and '--text-color: #1d1d1f' in sr and '--primary-color: #b58900' in sr, 'sun palette NOT applied'
assert '--bg-color: #faf9f5' in cr and '--text-color: #141413' in cr and '--primary-color: #cc785c' in cr, 'claude palette NOT applied'
print('palette OK (sun 白底金 / claude 奶油珊瑚)')
"
# 3) claude.css = moon 基底(仅 :root 值/@import 不同) + character 层（剥离后 0 差异）
python3 -c "
import re
def norm_lines(t, skip=0):
    lines = t.splitlines()[skip:]
    k = re.compile(r'^\s*--(?:primary-color|primary-color-rgb|bg-color|bg-color-dark|dark-trait|light-trait-\d+|text-color|text-color-secondary|text-highlight-color|select-text-bg-color|code-color):')
    out = []
    for ln in lines:
        if ln.startswith('@import'): out.append('@IMPORT'); continue
        if k.match(ln): out.append(ln.split(':')[0].strip() + ': <VAL>'); continue
        out.append(ln.rstrip())
    return [l for l in out if l != '']
m = norm_lines(open('moon.css').read())
cl = open('claude.css').read().splitlines()
ci = next(i for i,l in enumerate(cl) if 'Claude Character.css' in l)
start = ci
while start > 0 and not cl[start].startswith('/* ===='): start -= 1
b = norm_lines('\n'.join(cl[:start]), 6)   # claude header = 6 行
assert len(b) == len(m) and not [(i,x,y) for i,(x,y) in enumerate(zip(m,b)) if x!=y], 'claude 基底偏离 moon'
assert any('font-weight: 400 !important' in l for l in cl[start:]), 'character 特色层缺失'
print('claude base == moon (character layer present) OK')
"
# 4) codeblock 镜像结构 diff（dark 源 vs Sun 亮色，色值归一后应一致；Claude 深卡独立不参与）
diff <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Moon/codeblock.xydark.css) \
     <(sed 's/#[0-9A-Fa-f]\{3,8\}/#X/g' Sun/codeblock.xylight.css) && echo 'mirror OK'
# 5) 括号配平
python3 -c "for p in ['moon.css','sun.css','claude.css','Claude/character.css','Claude/codeblock.xydark.css']:
    t=open(p).read(); assert t.count('{')==t.count('}'), p
print('braces balanced')"
```

## Typora 平台约束（踩过的坑，勿再犯）

1. **主题 css 文件名必须小写开头**（`sun.css`/`moon.css`/`claude.css`）。大写开头（`Sun.css`）Typora 扫描器不识别 → 主题菜单消失/空。显示名 = 去扩展名首字母大写（sun→Sun、moon→Moon、claude→Claude）。
2. **改名/新增主题后如不可见，检查 plist**：`~/Library/Preferences/abnerworks.Typora.plist` 按显示名记忆当前主题（`theme`=亮色、`darkTheme`=暗色、`useDarkTheme`）。若当前选中主题指向已删除/改名文件必须**先完全退出 Typora** 再 `defaults write`（运行中写会被退出时内存旧值覆盖）。新增主题无需写 plist（菜单实时扫描）。
3. 主题仍不可见时清缓存：退出后删 `~/Library/WebKit/abnerworks.Typora` 与 `~/Library/Caches/abnerworks.Typora`。
4. APFS 大小写不敏感盘上改名用两步 `git mv A.css tmp.css && git mv tmp.css a.css`。
5. 文件末尾追加内容前先确认有换行，否则会拼行（曾把 `.DS_Store` 拼成 `.workbuddy.DS_Store`）。

## 设计基线（勿随意偏离）

- 正文字体：`"HONOR Sans Design Regular"` 本地字体栈（未装则回退系统栈），三主题基底一致。
- 等宽字体：`'JetBrains Mono', 'HONOR Sans Design Regular', 'Zconsola', monospace`。
- mermaid：**Moon/Sun** = Apple 亮色画布卡（`#f5f5f7` + Action Blue `#0066cc`，嵌在 moon.css 基底）；**Claude** = character.css 覆盖为 Claude 奶油卡（`#faf9f5` + 珊瑚 `#cc785c` + cream 节点）。
- **Claude 特色**（character.css，依 awesome-design-md/claude DESIGN.md）：标题衬线 400 不粗体；链接珊瑚 + hairline 下划线；引用珊瑚左线；代码块深卡 `#181715`（cream↔dark 节奏）；行内代码 surface-card 底 + 深珊瑚字；表格 cream-strong 表头；图片/面板圆角 12px（rounded.lg）。
- CSS 语法用 python 原子替换或整文件 Write，慎用多次 Edit（曾出现同文件连续 Edit 漏落盘）。

## Git 约定

- 每次改动：`git add -A && git commit -m "<英文简短描述>" && git push origin main`。
- `.gitignore` 已含 `.idea` / `.workbuddy` / `.DS_Store`；`.workbuddy/` 下是记忆日志，不入库。
- 改 moon.css 排版后提交需同时含重新生成的 sun.css 与 claude.css（一条提交，不留中间态）。
- 新增 Claude 特色文件 Claude/character.css 与重建产物 claude.css 一同提交。
