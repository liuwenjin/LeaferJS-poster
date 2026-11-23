# LeaferJS Poster

**可独立运行的海报生成器**

> 纯前端 + AIGC：输入一段作品描述，生成一张比例精确、可直接发布的社交货币海报。
> 仓库包含 AIGC 结构化提示词、前端结果渲染组件与本地网页入口；AI 推理由 transweb.cn 的 serverless 服务提供。

![Pure Frontend](https://img.shields.io/badge/architecture-pure%20frontend-6f5cff)
![Vue SFC](https://img.shields.io/badge/component-Vue%20SFC-42b883)
![Serverless](https://img.shields.io/badge/AI-transweb.cn%20serverless-2ea44f)
![LeaferJS](https://img.shields.io/badge/LeaferJS-2.3.4-6f5cff)

---

## 项目简介

LeaferJS Poster 是一个**可独立运行的海报生成器**，也是一个纯前端项目。

仓库内没有自建后端、没有数据库、没有构建配置，也不需要在本地安装依赖。打开 [`appPage.html`](./appPage.html)，输入作品描述，前端会把用户输入与当前应用标识提交给 **transweb.cn 的 serverless 服务**；服务按 AIGC 结构化提示词调用大模型，流式返回 JSON，前端组件解析后把结果渲染成一张按比例排版的静态海报，并支持版本管理、编辑与导出。

一句话概括：

```text
本地网页（纯前端） ──► transweb.cn serverless ──► 大模型 ──► 结构化 JSON ──► 前端渲染
```

## 项目组成

| 组成 | 文件 | 职责 |
| --- | --- | --- |
| **AIGC 结构化提示词** | [`appPrompt.md`](./appPrompt.md) | 约束模型的角色、交付格式、版式系统、字体规范、链接判定与验收 |
| **前端 AI 结果渲染组件** | [`appCode.vue`](./appCode.vue) | Vue 单文件组件：输入向导、流式状态、海报渲染、版本历史、编辑与导出 |
| **本地浏览器网页** | [`appPage.html`](./appPage.html) | 本地入口页，打开即用；以 iframe 加载 transweb.cn 上的同款应用 |
| **元数据** | [`manifest.json`](./manifest.json) | 应用来源、创建时间与文件清单 |

## 架构与数据流

```text
┌────────────────────────── 浏览器（纯前端） ──────────────────────────┐
│  appPage.html（本地入口）                                            │
│        │ iframe                                                      │
│        ▼                                                             │
│  appCode.vue（Vue SFC）                                              │
│   ├─ 输入作品描述 / 比例 / 链接                                      │
│   ├─ 提交 context + documentId                                       │
│   ├─ 接收流式结果、解析 JSON                                         │
│   └─ 渲染海报 / 版本 / 编辑 / 导出                                   │
└───────────────┬──────────────────────────────────────────────────────┘
                │ HTTPS 流式请求
                ▼
   transweb.cn serverless（提示词编排 / 模型调用 / 结果存储 / 登录与额度）
                │
                ▼
             大模型 → 结构化 JSON
```

### 提示词与前端的关系

- `appPrompt.md` 是本项目的 **AIGC 结构化提示词**，也是模型输出必须遵守的契约。
- 在线应用按 `documentId` 绑定平台侧的提示词配置；前端提交的是用户输入（`context`）与应用标识，而不是每次都上传整份提示词。
- 修改仓库里的 `appPrompt.md` 不会自动改变线上应用；需要在平台更新提示词配置，或把提示词接入你自建的 serverless 服务。

## 为什么是「纯前端 + serverless」

| 维度 | 说明 |
| --- | --- |
| **无自建后端** | 仓库只有 HTML、Vue SFC、Markdown 与 JSON，没有服务端代码 |
| **无需构建** | 没有 `package.json`、打包器或编译步骤；打开网页即可使用 |
| **前端负责交互与渲染** | 输入、流式状态、JSON 解析、海报渲染、版本与导出都在浏览器完成 |
| **AI 在 serverless** | 模型调用、流式返回、结果存储由 transweb.cn 承担，前端不持有模型密钥 |
| **静态可托管** | 入口页可放在任意静态服务器、对象存储或本地目录 |

**独立运行的含义**：不依赖本地开发环境、不需要部署自己的后端、不需要安装 npm 包。它不是「离线运行」，生成能力依赖 transweb.cn 的 serverless 服务与网络。

## 运行方式

### 方式一：直接打开（最简单）

双击 [`appPage.html`](./appPage.html)，或在浏览器里打开它。页面会以 iframe 加载 transweb.cn 上的同款应用。

### 方式二：本地静态服务（推荐）

某些浏览器对 `file://` 下的 iframe、剪贴板或跨域行为有限制，建议起一个静态服务：

```bash
# 在仓库根目录
python3 -m http.server 8080
# 打开 http://localhost:8080/appPage.html
```

或者使用任意静态服务器（Nginx、Caddy、VS Code Live Server 等）。

### 运行前提

- 能访问 `transweb.cn`（AI 生成与结果存储依赖该服务）
- 现代浏览器（Chrome、Edge、Safari、Firefox）
- 无需 Node.js、npm、构建工具
- 登录状态与调用额度以 transweb.cn 当前策略为准

## serverless 依赖说明

| 能力 | 是否在仓库内 | 说明 |
| --- | --- | --- |
| 模型调用 | 否 | 由 transweb.cn serverless 调用大模型 |
| 提示词编排 | 否 | 平台按 `documentId` 绑定 AIGC 提示词 |
| 流式返回 | 否 | 前端通过 `aigcInterface` 接收流式结果 |
| 结果存储与版本 | 否 | 历史版本由 transweb.cn 保存 |
| 输入、解析、渲染、导出 | 是 | `appCode.vue` 在浏览器内完成 |

前端不保存模型 API Key，也不直接连接模型服务；所有生成请求都经过 transweb.cn。服务不可用、未登录或额度不足时，页面会给出对应提示。

## 目录结构

```text
LeaferJS-poster/
├── appPrompt.md     # AIGC 结构化提示词（模型契约）
├── appCode.vue      # 前端 AI 结果渲染组件（Vue SFC，基于 transweb 前端运行时）
├── appPage.html     # 本地浏览器入口（iframe 加载在线应用）
├── manifest.json    # 应用元数据与来源
└── README.md        # 本文件
```

## 它生成什么

一张值得被收藏的平面设计作品，而不是「标题 + 背景图」：

- **比例自适应**：`3:4`、`4:5`、`1:1`、`2:3`、`9:16`、`4:3`、`16:9` 等；短边固定 900 设计单位
- **三种版式族**：竖版单栏纵向流式、方形单栏、横版左文右图两栏
- **七个版面块**：顶部元信息 / 主标题 / 主视觉 / 描述 / 链接展签 / 扫码提示 / 作者
- **六种构图**：偏心大标题、左文右图、中心仪式、底部信息带、全幅压字、网格编号
- **四种风格**：几何构成、编辑极简、瑞士国际主义、复古印刷；四选一，不混用
- **链接展签**：需求含链接时自动判定 `qr` / `hero` / `slot`，生成可扫描二维码或图片展签
- **文字清晰度**：不缩字、不缩放文字层、不用小画布放大；`pixelRatio: 3`
- **静态作品**：无动画、无时间轴、无页面级 UI，导出交给截图完成
- **零外部素材**：只用基础图形与系统字体，质感程序化生成

## AIGC 结构化提示词

[`appPrompt.md`](./appPrompt.md) 把模型设定为「七人平面视觉设计团队」，并强制约束输出。核心协议如下。

### 交付格式（硬约束）

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

## 前端渲染组件

[`appCode.vue`](./appCode.vue) 是平台运行时下的 Vue 单文件组件，负责从输入到成品的完整前端链路：

- **输入**：作品描述、比例要求、链接、示例模板，`solo` 模式与嵌入模式自适应
- **生成**：调用 `aigcInterface.streamRequest` 发起流式请求，展示加载进度与阶段提示，支持中止
- **解析**：用 `WebTool.extractJSON` 提取 JSON，格式异常时提示并允许重试
- **渲染**：把 `code` 交给结果面板渲染为海报画布，支持画布预览与错误面板
- **版本**：历史版本列表、版本对比、切换与恢复
- **编辑**：基于当前内容继续修改，生成新版本
- **导出**：存档 HTML、下载 Markdown、导出图片、复制分享链接
- **反馈**：内置问题反馈入口

> 组件依赖 transweb 前端运行时与 serverless 接口，不是可以直接 `import` 到任意 Vue 工程的 npm 包；它的价值在于展示 AIGC 结果如何被解析、渲染和管理。

## 技术栈

| 层 | 选型 |
| --- | --- |
| 本地入口 | 原生 HTML + iframe |
| 前端组件 | Vue 单文件组件（Options API）、Element 组件库、Tailwind 原子类 |
| AIGC 提示词 | Markdown 结构化提示词（`appPrompt.md`） |
| AI 服务 | transweb.cn serverless，流式返回 |
| 结果渲染 | LeaferJS 2.3.4 |
| 可选依赖 | `qr` 模式加载公开的 `qr-code-styling` 脚本 |
| 画布 | 短边 900 设计单位，`pixelRatio: 3`；`stage` 等比缩放居中 |
| 结果存储 | transweb.cn 版本与历史记录 |

LeaferJS 只允许引入一次：

```text
https://unpkg.com/leafer-ui@2.3.4/dist/web.min.js
```

## 独立运行的边界

| 可以 | 不可以 |
| --- | --- |
| 本地双击或静态托管 `appPage.html` | 完全离线生成（需要 transweb.cn） |
| 不安装 Node.js、不构建、不部署后端 | 自带模型或本地模型直接推理 |
| 浏览器内完成输入、渲染、版本、图片导出 | 不依赖账号/额度无限调用 |
| 把生成的海报 HTML 单独保存、分享 | 直接修改 `appPrompt.md` 就让线上生效 |

如果要把 AI 能力迁到自己的基础设施，需要实现与 `aigcInterface` 兼容的流式接口，或把提示词接入自己的模型网关；这部分不在本仓库内。

## 交付验收清单

- [ ] 打开 `appPage.html` 能看到应用界面，而不是白屏
- [ ] 输入作品描述后能看到流式生成状态
- [ ] 画布比例与需求一致，误差 ≤ 1 设计单位
- [ ] 短边 ≥ 900 设计单位，`pixelRatio: 3`
- [ ] 主视觉第一眼可辨，且只承担一个隐喻
- [ ] 标题逐字使用作品名，断行合理，无孤字
- [ ] `qr` 二维码可扫描且指向用户链接；`hero` / `slot` 图片未裁切变形
- [ ] 历史版本可切换，编辑后可生成新版本
- [ ] 存档 HTML、下载 Markdown、导出图片可用
- [ ] 网络异常或服务不可用时有明确提示

## 常见问题

**打开 `appPage.html` 一直白屏或转圈？**
先确认能访问 `https://transweb.cn`，再打开浏览器控制台看 iframe 是否被拦截。必要时改用本地静态服务（见「运行方式」），或直接访问在线应用地址。

**需要安装 Node.js 或 npm 吗？**
不需要。项目没有构建步骤；本地静态服务只是可选的更稳妥的打开方式。

**怎么导出 PNG / JPG？**
使用结果面板的图片导出，或直接截取画布区域；画布本身已是 3 倍像素密度。导出由前端完成。

**没有链接也能用吗？**
可以。没有链接时不加载二维码库、不画二维码，右下区域保持留白。

**可以离线使用吗？**
不能离线生成。生成的海报 HTML 可以保存到本地查看，但 `qr` 模式会加载二维码脚本；需要完全离线时，可把脚本下载到本地并替换地址。

**能换成自己的模型吗？**
可以，但需要替换 `aigcInterface` 适配层或自建兼容的 serverless 服务，并自行实现流式协议。仓库不包含这部分实现。

**为什么改了 `appPrompt.md` 线上没变化？**
线上应用读取的是平台侧绑定的提示词配置；仓库里的文件是提示词契约与版本记录。要生效，需要在平台更新配置，或接入你自建的生成服务。

## 相关链接

- 在线应用：<https://transweb.cn/agent.html?id=c19fca2dd60e295a12948cd241fb5b23>
- 本地入口：[`appPage.html`](./appPage.html)
- LeaferJS 官网：<https://www.leaferjs.com/>
- LeaferJS 文档：<https://www.leaferjs.com/ui/guide/>
- 本仓库：<https://github.com/liuwenjin/LeaferJS-poster>

## 许可

本仓库暂未指定开源许可证。提示词文本、前端组件与生成代码版权归各自作者所有；使用前请自行确认授权范围。
