# LeaferJS Poster

**可独立运行的社交货币海报生成器**

> 一段作品描述，得到一张 **比例精确、可直接发布** 的平面海报。
> 自带任意大模型即可运行；产物是零依赖的单文件 HTML，画布由 LeaferJS 程序化绘制。

![BYOM](https://img.shields.io/badge/model-any%20LLM-6f5cff)
![LeaferJS](https://img.shields.io/badge/LeaferJS-2.3.4-6f5cff)
![Single File](https://img.shields.io/badge/output-single--file%20HTML-2ea44f)
![Ratio](https://img.shields.io/badge/ratio-3%3A4%20default-6f5cff)

---

## 简介

**LeaferJS Poster 是一个可独立运行的 AI 工具**，不是某个平台的插件，也不需要登录任何账号。

它的全部能力来自一份系统提示词 [`appPrompt.md`](./appPrompt.md)：把这份提示词交给任意支持长输出的 LLM，模型就会把你的作品描述变成一张社交货币海报，并以严格 JSON 交付。取 JSON 里的 `code` 字段保存为 `.html`，打开就是完整画面。

```text
作品描述 ──► 任意 LLM ──► JSON ──► poster.html ──► 浏览器截图
                ▲
          appPrompt.md
```

工具链上没有隐藏服务：没有后端、没有模板库、没有构建步骤。你需要的只是一个模型端点（云端 API 或本地模型）和一个浏览器。

## 为什么它可以独立运行

| 维度 | 说明 |
| --- | --- |
| **模型自由** | OpenAI、DeepSeek、Claude、Gemini、Ollama、vLLM 均可；只要支持 system prompt 与长输出 |
| **无平台锁定** | 除 `qr` 模式加载公开的 `qr-code-styling` 脚本外，不调用任何私有接口；该脚本可自托管替换，平台页面只是参考实现 |
| **本地执行** | 提示词、调用脚本、产物都在你的机器上；使用本地模型时可完全离线生成 |
| **产物可移植** | 输出是单个 `.html` 文件，可双击打开、静态托管或直接截图分发 |
| **比例可承诺** | 需求写 4:5 就是 4:5，画布比例误差 ≤ 1 设计单位，不偷偷回退默认值 |
| **过程可审计** | 提示词、版式规则和生成结果全部可见，可 diff、可二次修改 |

## 它生成什么

一张值得被收藏的平面设计作品，而不是「标题 + 背景图」：

- **比例自适应**：`3:4`、`4:5`、`1:1`、`2:3`、`9:16`、`4:3`、`16:9` 等；短边固定 900 设计单位
- **三种版式族**：竖版单栏纵向流式、方形单栏、横版左文右图两栏
- **七个版面块**：顶部元信息 / 主标题 / 主视觉 / 描述 / 链接展签 / 扫码提示 / 作者
- **六种构图**：偏心大标题、左文右图、中心仪式、底部信息带、全幅压字、网格编号
- **四种风格**：几何构成、编辑极简、瑞士国际主义、复古印刷；四选一，不混用
- **链接展签**：需求含链接时自动判定 `qr` / `hero` / `slot`，生成可扫描二维码或图片展签
- **文字清晰度**：不缩字、不缩放文字层、不用小画布放大；`pixelRatio: 3`
- **静态作品**：无动画、无时间轴、无页面级 UI，导出交给查看器截图
- **零外部素材**：只用基础图形与系统字体，质感程序化生成

## 快速开始（独立运行）

### 前置条件

- Node.js 18+（内置 `fetch`，无需 `npm install`）
- 一个 OpenAI 兼容的模型端点；建议单次输出上限 **≥ 8K tokens**

### 1. 获取工具

```bash
git clone https://github.com/liuwenjin/LeaferJS-poster.git
cd LeaferJS-poster
```

### 2. 创建本地运行器

把下面的代码保存为 `run.mjs`（放在仓库根目录）：

> 不想用 Node.js？任何 HTTP 客户端都可以：把 `appPrompt.md` 作为 system message、需求作为 user message 发送，再解析返回的 JSON。

```js
// run.mjs — Node.js 18+，零依赖
import fs from 'node:fs';

const system = fs.readFileSync('appPrompt.md', 'utf8');
const idea = process.argv.slice(2).join(' ') || '为「手写体生成器」做一张 4:5 的海报，链接 https://example.com/writing';
const base = (process.env.LLM_BASE_URL || 'https://api.deepseek.com').replace(/\/$/, '');

const res = await fetch(`${base}/chat/completions`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.LLM_API_KEY}`
  },
  body: JSON.stringify({
    model: process.env.LLM_MODEL || 'deepseek-chat',
    messages: [
      { role: 'system', content: system },
      { role: 'user', content: idea }
    ],
    max_tokens: Number(process.env.LLM_MAX_TOKENS || 8000),
    temperature: 0.7
  })
});

if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
const body = await res.json();
if (body.choices?.[0]?.finish_reason === 'length') {
  throw new Error('输出被截断：请提高 LLM_MAX_TOKENS，或换用输出上限更大的模型');
}

const raw = body.choices[0].message.content.trim()
  .replace(/^`{3}(?:json)?\s*/, '')
  .replace(/\s*`{3}$/, '');
const data = JSON.parse(raw);
fs.writeFileSync('poster.html', data.code, 'utf8');
console.log(`✓ ${data.title} → poster.html（${data.width}×${data.height}）`);
```

### 3. 生成并查看

```bash
LLM_API_KEY=sk-... node run.mjs "为「手写体生成器」做一张 4:5 的小红书海报，作品是把一句话变成书法，链接是 https://example.com/writing"
open poster.html     # macOS；其他系统直接双击
```

海报会按比例完整居中显示。用截图工具截取画布区域即可得到成品；`pixelRatio: 3` 已按短边 900 设计单位输出，足够社媒与常规印刷。

### 4. 换一个模型

| 服务 | `LLM_BASE_URL` | `LLM_MODEL` 示例 |
| --- | --- | --- |
| OpenAI | `https://api.openai.com/v1` | `gpt-4.1` |
| DeepSeek | `https://api.deepseek.com` | `deepseek-chat` |
| Ollama（本地） | `http://localhost:11434/v1` | `qwen2.5:14b` |
| vLLM（本地） | `http://localhost:8000/v1` | 你部署的模型名 |
| 其他网关 | 任意 OpenAI 兼容地址 | 以服务商文档为准 |

```bash
LLM_BASE_URL=https://api.openai.com/v1 LLM_MODEL=gpt-4.1 \
LLM_API_KEY=sk-... node run.mjs "为「城市声音地图」做一张 1:1 方形海报，链接 https://example.com/sound"
```

> Claude、Gemini 的原生 API 协议不同，可通过 OpenAI 兼容网关接入，或自行替换运行器中的请求部分。模型不是工具的一部分，可随时替换。

## 交付协议（硬约束）

模型必须**只输出一个合法 JSON 对象**，前后不允许任何解释文字、Markdown 代码围栏或注释。顶层字段固定：

| 字段 | 类型 | 约束 |
| --- | --- | --- |
| `type` | `string` | 固定为 `poster` |
| `height` | `number` | 画布高度（设计单位） |
| `width` | `number` | 画布宽度（设计单位） |
| `title` | `string` | 作品名，≤ 16 字；超长只缩写，不改写作品名 |
| `description` | `string` | 一句话说明这张海报画了什么，≤ 30 字，不换行 |
| `code` | `string` | 完整单文件 HTML，源码 180–380 行，以 `<!DOCTYPE html>` 开头、`</html>` 结尾 |
| `highlights` | `string[3]` | 恰好 3 条，每条 ≤ 20 字；写设计记忆点，不写功能点 |

结构示意（`code` 已省略）：

```json
{
  "type": "poster",
  "height": 1200,
  "width": 900,
  "title": "折叠的城市",
  "description": "一张被反复折叠又展开的城市地图。",
  "code": "<!DOCTYPE html> ... </html>",
  "highlights": [
    "标题压住折痕的偏心构图",
    "单色墨线 + 一处朱红强调",
    "右下角白色二维码展签"
  ]
}
```

### `code` 字段的转义铁律

`code` 的值本身是一段 JSON 字符串。为了不让裸双引号提前结束字符串，提示词要求：

1. `code` 源码内**零双引号**：HTML 属性、JS 字符串、CSS 字体名全部用单引号，插值用反引号模板字符串。
2. `code` 源码内**零反斜杠**：不写 `\n`、`\t`、`\u`、正则转义或 `<\/script>`；换行用 `String.fromCharCode(10)` 或拆成多个 `Text` 元素。
3. URL 中的单引号写成 `%27`、反斜杠写成 `%5C`，不要靠转义绕过规则。
4. JSON 序列化时真实换行写成 `\n`；禁止尾随逗号、注释与代码围栏。

## 链接与展签

需求中含 `http://` 或 `https://` 链接时，先判定展示方式，再谈设计（判定只做一次，写死在代码常量里）：

| 模式 | 适用 | 表现 |
| --- | --- | --- |
| `qr` | 页面链接，或用户明确要扫码 | 加载 `qr-code-styling`，绘制可扫描二维码 + 扫码提示 |
| `hero` | 链接是作品图 / 封面 / 主图 | 图片占据主视觉区，不加载二维码库 |
| `slot` | 链接是 logo / 图标 / 头像 / 二维码图 | 图片进入右下展签位，按类型决定是否保留扫码提示 |
| 无链接 | — | 不加载二维码库，不画二维码，不用装饰方块占位 |

链接图片必须走「位图 → blob → `L.Image` → 画布」，禁止外链图片、`<img>` 或 CSS 背景图，避免污染画布导致导出失败。

## 技术栈

| 层 | 选型 |
| --- | --- |
| 生成引擎 | `appPrompt.md`（系统提示词）+ 任意 LLM |
| 运行器 | 约 40 行 Node.js 脚本，仅用内置 `fetch` |
| 渲染引擎 | LeaferJS 2.3.4 |
| 可选依赖 | `qr` 模式加载 `https://transweb.cn/common/qr-code-styling.js` |
| 画布 | 短边 900 设计单位，`pixelRatio: 3`；`stage` 等比缩放居中 |
| 图形 | `Rect`、`Ellipse`、`Polygon`、`Star`、`Text`、`Group`、`Image`（仅链接位图） |
| 文字 | `fontSize` / `letterSpacing` / `lineHeight` 全部是像素（`lineHeight` 不是倍数） |
| 产物 | 单文件 HTML，无构建、无框架、静态渲染 |

LeaferJS 只允许引入一次：

```text
https://unpkg.com/leafer-ui@2.3.4/dist/web.min.js
```

## 目录结构

```text
LeaferJS-poster/
├── appPrompt.md     # 工具引擎：完整系统提示词（必需）
├── README.md        # 本文件
├── appCode.vue      # 可选参考实现：平台端 UI（独立运行不需要）
├── appPage.html     # 可选参考实现：iframe 嵌入页
└── manifest.json    # 元数据
```

`run.mjs` 不在仓库内，按「快速开始」创建即可；`appCode.vue` 与 `appPage.html` 只是同款提示词在 transweb.cn 上的 Web 外壳，**不参与**独立运行链路，删掉它们不影响工具使用。

## 设计标准摘要

### 社交货币十准则

1. **作品优先**：画作品本身，不画 AI 技术。
2. **非凡优先**：只放大最值得惊叹的一处，其余卖点删掉。
3. **身份表达**：版面克制、细节精确、作者清楚。
4. **差异化**：按作品气质选风格，不套模板。
5. **视觉证据**：画真实产物、交互帧、数字；界面作品做几何重构而非贴截图。
6. **情绪驱动**：主视觉只制造一种情绪。
7. **故事传播**：让人能用一句话说清「把什么变成什么」。
8. **荣誉可视化**：真实数据才转成编号、计数、日期、刻度。
9. **低成本分享**：标题、描述、作者、链接四件套完整。
10. **品牌一致**：同一作者共享边距、字体与展签基因。

### 版面纪律

- 标题下沿与主视觉上沿 ≥ 24；主视觉下沿与描述上沿 ≥ 36。
- 边距固定 72；竖版 6 栏、栏距 24；基线单位 12。
- 主视觉可破格、可出血，文字不可以。
- 没有链接时删掉展签与扫码提示，空出的右下保持安静。
- 一个主视觉、一个视觉入口、一个记忆点；同时存在的视觉重点不超过两个。

### 设计反模式（命中即改）

- 机器人、芯片、大脑、电路板、蓝紫霓虹、发光粒子、悬浮玻璃卡片、手机 mockup。
- 纯黑 `#000` / 纯白 `#fff`（链接展签卡除外）、彩虹渐变、五个以上色相。
- 贴界面截图冒充主视觉；编造数据；把标题当说明书。
- 每张海报都套同一种风格；用装饰填满留白。

## 交付验收清单

- [ ] `code` 源码内双引号数量为 0、反斜杠数量为 0
- [ ] 画布比例与需求一致，误差 ≤ 1 设计单位
- [ ] 短边 ≥ 900 设计单位，`pixelRatio: 3`
- [ ] 所有视觉元素都在 canvas 内
- [ ] 主视觉第一眼可辨，且只承担一个隐喻
- [ ] 标题逐字使用作品名，断行合理，无孤字
- [ ] 描述、作者、展签位置符合版面锚点
- [ ] `qr` 二维码可扫描且指向用户链接；`hero` / `slot` 图片未裁切变形
- [ ] 文字对比度达标，未缩字
- [ ] 无 `requestAnimationFrame`、无动画、无页面级 UI
- [ ] 无控制台异常、无 404 资源

## 常见问题

**怎么导出 PNG / JPG？**
本页面不提供下载按钮，导出由截图完成。用浏览器开发者工具的设备像素比或系统截图工具截取画布区域即可；画布本身已是 3 倍像素密度。

**没有链接也能用吗？**
可以。没有链接时不加载二维码库、不画二维码，右下区域保持留白。

**二维码为什么需要联网？**
`qr` 模式会加载一个公开的 `qr-code-styling` 脚本（默认地址在 `transweb.cn`，可自行镜像）。完全离线时把该脚本下载到本地静态目录并替换地址即可。

**可以商用吗？**
本仓库暂未声明许可证。提示词与生成代码的版权归各自作者所有，商用前请自行确认授权范围。

## 参考实现（可选）

- 在线效果参考：<https://transweb.cn/agent.html?id=c19fca2dd60e295a12948cd241fb5b23>
- `appPage.html` 通过 iframe 嵌入同一在线版本；它只用于预览，不是运行本工具的必要条件。

## 相关链接

- LeaferJS 官网：<https://www.leaferjs.com/>
- LeaferJS 文档：<https://www.leaferjs.com/ui/guide/>
- 本仓库：<https://github.com/liuwenjin/LeaferJS-poster>

## 许可

本仓库暂未指定开源许可证。提示词文本与生成的海报代码版权归各自作者所有；使用前请自行确认授权范围。
