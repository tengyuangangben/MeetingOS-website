# MeetingOS 产品宣发站

> MeetingOS 企业级会议纪要智能管理平台的独立静态产品介绍页。

[在线访问](#部署到免费静态托管平台) · [页面结构](#页面内容) · [技术说明](#技术实现) · [维护指南](#维护指南)

***

## 一、项目定位

本项目是 MeetingOS 主系统之外单独拆分的**产品宣发 Web 站点**，用于：

- 展示 MeetingOS 的产品定位与核心价值；
- 对外介绍会议管理、LLM 编译、音频转写、权限隔离、备份恢复等能力；
- 展示系统架构、技术栈和权限模型；
- 展示从 MVP 到当前版本的产品迭代历程；
- 部署到 GitHub Pages、Netlify、Vercel、Cloudflare Pages 等免费静态网站平台。

该站点只负责产品展示，**不包含 MeetingOS 后端接口、数据库、登录系统或业务数据**。页面中的产品能力、架构和版本信息属于宣传说明，实际功能以主系统代码和正式文档为准。

## 二、项目特点

- 单文件静态站点，无前端构建流程；
- 不需要 Node.js、Python、数据库或服务器运行时；
- 响应式布局，适配桌面端、平板和移动端；
- 深色 Operator Console 风格视觉设计；
- 内置滚动渐入、背景光效、M 水印视差和减少动效适配；
- 使用 ECharts 绘制可交互系统架构树；
- 使用语义化锚点实现页面内导航；
- 页面数据、样式和交互逻辑集中在 `index.html`，方便直接部署和维护。

## 三、目录结构

```text
deploy/
├── index.html       # 宣发站点完整页面：HTML + CSS + JavaScript
├── README.md        # 独立宣发站项目文档
```

当前站点没有图片、字体文件或构建产物目录。外部资源通过 CDN 加载：

| 外部资源          | 用途                                        | 地址                     |
| ------------- | ----------------------------------------- | ---------------------- |
| Google Fonts  | Montserrat、Noto Sans SC、JetBrains Mono 字体 | `fonts.googleapis.com` |
| ECharts 5.5.0 | 架构图交互与渲染                                  | `cdn.jsdelivr.net`     |

如果部署环境无法访问 Google Fonts 或 jsDelivr，页面主体仍可显示，但字体会回退到系统字体，架构图可能无法渲染。

## 四、页面内容

### 4.1 首屏 Hero

首屏使用“让每一次会议都有回声”作为主宣传语，展示：

- 企业级产品定位；
- 统一存储、智能编译、跨公司隔离、时间轴追溯等核心价值；
- LLM 编译、权限隔离、时间轴、待办、供应商、Word 导出和音频转写能力索引；
- `ALL SYSTEMS · OPERATIONAL` 产品状态装饰信息。

> 注意：`ALL SYSTEMS · OPERATIONAL` 是静态宣传文案，不代表在线探活结果。

### 4.2 核心能力

页面目前展示以下产品能力：

1. **LLM 智能编译**：从会议原文提炼标题、摘要、决策、待办、参会人和标签；
2. **四级权限体系**：超级管理员、公司管理员、普通用户、只读用户；
3. **待办追溯**：原文上下文、位置索引、多负责人和状态机；
4. **时间轴浏览**：年/月/日视图、全文搜索、标签筛选和趋势图；
5. **安全分享**：阅后即焚、限时访问、密码保护和访问日志；
6. **可视化备份管理**：定时备份、多目标存储和恢复；
7. **Word 文档导出**：单会议、决策清单和待办清单导出；
8. **LLM 供应商管理**：预置供应商与 OpenAI 兼容自定义接口；
9. **音频转写**：ASR 网关、说话人分离、时间轴分段和 LLM 编译衔接。

### 4.3 系统架构

使用 ECharts Tree 图展示五层请求流向：

```text
浏览器 / Vue 3 SPA
        ↓
Nginx 接入层
        ↓
FastAPI + Gunicorn 后端服务
        ↓
PostgreSQL + 文件存储
        ↓
LLM 推理供应商
```

架构图在页面脚本中初始化，主要入口为 `initArchChart()`。点击节点可以展开或收起子节点，窗口尺寸变化时会自动执行 resize。

### 4.4 技术栈

页面展示的主系统技术栈包括：

- Vue 3、TypeScript、Vite、Pinia；
- FastAPI、Python 3.11、SQLAlchemy 2.0；
- PostgreSQL 15+；
- Nginx、Gunicorn；
- OpenAI SDK 兼容模式；
- ASR 网关和五路分片上传机制。

### 4.5 权限模型

页面使用静态权限矩阵展示以下角色：

| 能力      | 超级管理员 | 公司管理员 | 普通用户 | 只读用户 |
| ------- | ----: | ----: | ---: | ---: |
| 查看公开会议  |     ✓ |     ✓ |    ✓ |    ✓ |
| 查看机密会议  |     ✓ |     ✓ |    ✓ |    — |
| 查看绝密会议  |     ✓ |     ✓ |    — |    — |
| 上传 / 编译 |     ✓ |     ✓ |    ✓ |    — |
| 删除会议    |     ✓ |     ✓ |    — |    — |
| 管理用户    |     ✓ |   本公司 |    — |    — |
| 跨公司访问   |     ✓ |     — |    — |    — |

实际权限以 MeetingOS 后端 ABAC 策略和接口鉴权为准，不能仅依据此页面的表格判断用户权限。

### 4.6 版本历程

页面按时间线展示主要版本节点，包括：

- v1.13.0：MVP 发布；
- v1.16.x：时区治理、安全加固和组件治理；
- v1.17.0 / v1.17.1：Word 导出落地及导出方案精简；
- v1.18.3：备份恢复多租户隔离；
- v1.19.0：异步恢复和选择性恢复；
- v1.20.x：备份稳定性与上线前安全治理；
- v1.21.x：音频转写、任务队列、分页和状态体验优化。
- v1.22.0:   首次生产环境部署版本

版本历程是产品叙事内容。新增主系统版本时，应同步检查此处的版本号、功能描述和宣传口径。

## 五、技术实现

### 5.1 页面组成

`index.html` 内部由三部分组成：

```text
<head>
├── 页面元信息
├── Google Fonts CDN
└── ECharts CDN

<style>
├── 设计 Token
├── 导航与 Hero
├── 功能卡片
├── 架构图容器
├── 技术栈与权限表格
├── 时间线
├── 响应式布局
└── 动效与滚动显示

<body>
├── 导航栏
├── Hero 首屏
├── 数据统计
├── 功能特性
├── 系统架构
├── 技术栈
├── 权限模型
├── 企业级细节
├── 版本历程
├── 联系 CTA
└── Footer

<script>
├── Hero M 水印视差
├── ECharts 架构树
├── 窗口 resize
├── IntersectionObserver 滚动渐入
└── 导航平滑滚动
```

### 5.2 设计系统

页面使用 CSS 自定义属性集中管理视觉 Token：

- 深色背景：`--color-bg`、`--color-bg-card`、`--color-bg-elevated`；
- 主色：青绿色 `--color-primary`；
- 辅助色：琥珀色 `--color-accent`、紫色 `--color-violet`；
- 字体：Montserrat、Noto Sans SC、JetBrains Mono；
- 圆角：`--radius-sm` 到 `--radius-xl`；
- 动效曲线：`--ease-out`、`--ease-in-out`。

调整整体视觉时，优先修改 `:root` Token，不要在大量组件样式中逐处替换颜色。

### 5.3 动效策略

页面包含以下动效：

- Hero 内容淡入上移；
- M 水印浮动与鼠标视差；
- O 同心圆光晕呼吸和旋转；
- 功能条目依次出现；
- 卡片 hover 上浮；
- 滚动进入视口时渐入。

已经通过 `prefers-reduced-motion: reduce` 对部分动效进行降级。新增动效时应继续遵守该约定，避免影响对动效敏感的用户。

### 5.4 ECharts 架构图

架构图使用 ECharts 5.5.0 的 Tree 系列：

- `orient: 'LR'`：从左到右展示请求流；
- `expandAndCollapse: true`：支持节点展开和折叠；
- `roam: false`：禁用拖拽漫游，保持宣传页布局稳定；
- `window.resize`：响应窗口变化；
- 移动端将图表高度从 760px 调整为 640px。

如果架构图空白，优先检查：

1. 浏览器是否能访问 jsDelivr；
2. 页面中是否成功加载 `echarts`；
3. `#archChart` 是否有明确高度；
4. 浏览器 Console 是否出现脚本错误。

## 六、部署到免费静态托管平台

### 6.1 GitHub Pages

GitHub Pages 最适合当前项目，因为站点是纯静态文件，不需要构建和服务器运行时。

#### 方式 A：将 `index.html` 放在仓库根目录

如果独立仓库内容如下：

```text
meetingos-promo/
├── index.html
├── README.md
└── .gitignore
```

在 GitHub 仓库中执行：

1. 打开仓库 `Settings`；
2. 进入 `Pages`；
3. 在 `Build and deployment` 中选择 `Deploy from a branch`；
4. 选择 `main` 分支和 `/ (root)` 目录；
5. 点击保存；
6. 等待 GitHub Pages 发布。

默认访问地址类似：

```text
https://<github-用户名>.github.io/<仓库名>/
```

#### 方式 B：使用 `deploy` 作为发布目录

如果仓库仍保留主项目结构，推荐将 `deploy` 目录单独推送成独立仓库。GitHub Pages 发布时应选择独立仓库的根目录，而不是主项目的 `deploy/` 子目录。

GitHub Pages 不需要配置 API 地址，因为本页面没有调用 MeetingOS 后端接口。

### 6.2 Netlify

1. 登录 Netlify；
2. 选择 `Add new site` → `Import an existing project`；
3. 连接 GitHub 仓库；
4. 构建命令留空；
5. 发布目录填写 `/`；
6. 保存并部署。

如果平台强制要求构建命令，可以使用一个不会修改文件的命令，例如：

```text
构建命令：echo "static site"
发布目录：.
```

### 6.3 Vercel

1. 登录 Vercel；
2. 选择 `Add New Project`；
3. 导入独立宣发仓库；
4. Framework Preset 选择 `Other`；
5. Build Command 留空；
6. Output Directory 使用 `.`；
7. 点击 Deploy。

### 6.4 Cloudflare Pages

1. 登录 Cloudflare Dashboard；
2. 进入 `Workers & Pages`；
3. 创建 Pages 项目并连接 GitHub；
4. 构建命令留空或使用 `echo static`；
5. 构建输出目录填写 `/`；
6. 部署。

### 6.5 自定义域名

免费平台通常支持绑定自定义域名。配置时需要：

1. 在托管平台添加域名；
2. 按平台提示配置 DNS 的 CNAME 或 A 记录；
3. 等待 HTTPS 证书签发；
4. 用无痕窗口检查首页、字体、架构图和邮件链接。

## 七、本地预览

由于页面是纯静态文件，可以直接双击 `index.html` 预览，但推荐使用本地 HTTP 服务，以避免浏览器对本地文件和外部资源的限制。

### 使用 Python

在 `deploy/` 目录执行：

```powershell
python -m http.server 8080
```

然后访问：

```text
http://localhost:8080/
```

### 使用 Node.js

如果本机安装了 Node.js，可以使用：

```powershell
npx serve .
```

## 八、发布前检查

### 页面功能

- [ ] 首屏标题和产品定位无误；
- [ ] 导航栏的五个锚点可以正常滚动；
- [ ] ECharts 架构图正常显示；
- [ ] 架构图节点可以展开和收起；
- [ ] 鼠标移动时 M 水印视差正常；
- [ ] 滚动页面时卡片可以渐入；
- [ ] 联系团队按钮可以打开邮件客户端；
- [ ] GitHub 链接已替换为真实仓库地址；
- [ ] 页面移动端没有横向滚动条；
- [ ] 深色背景下所有文本具备足够对比度。

### 外部依赖

- [ ] Google Fonts CDN 可访问；
- [ ] jsDelivr ECharts CDN 可访问；
- [ ] 若需要国内网络兼容，已准备字体和 ECharts 的本地资源方案；
- [ ] CDN 资源版本已固定，避免无意升级导致页面变化。

### 宣传内容

- [ ] 页面版本号与当前宣发版本一致；
- [ ] 功能描述与主系统实际能力一致；
- [ ] “4GB 大文件”“4 workers”等量化描述经过确认；
- [ ] “ALL SYSTEMS · OPERATIONAL”未被误解为实时监控状态；
- [ ] 权限矩阵注明以实际后端策略为准；
- [ ] 未放入账号、密码、API Key、内部地址或真实业务数据。

## 九、维护指南

### 9.1 新增产品能力

建议同步修改四个区域：

1. Hero 右侧能力索引；
2. `#features` 核心能力卡片；
3. “细节面面俱到”能力细节；
4. 版本历程对应版本节点。

如果能力涉及新技术栈，还要同步更新技术栈卡片和系统架构图。

### 9.2 修改版本号

当前页面版本号位于导航栏：

```html
<span class="nav-version">v1.21.12</span>
```

新增版本发布时，应同时检查：

- 导航栏版本号；
- 版本历程标题和说明；
- Footer 年份；
- 独立仓库 README 的版本说明；
- 主系统 CHANGELOG 中对应记录。

### 9.3 修改品牌视觉

优先调整：

- `:root` 中的颜色 Token；
- 字体 CDN 配置；
- Hero 标题和副标题；
- Logo SVG；
- `hero-watermark` 与 `orbit-glow` 的尺寸、透明度和动画。

不要在没有移动端验证的情况下直接放大装饰元素，M 水印和 O 光晕很容易造成小屏溢出。

### 9.4 CDN 失败时的本地化方案

当前页面依赖两个外部 CDN。若目标用户网络环境不稳定，可以：

1. 下载 ECharts 固定版本到 `assets/vendor/echarts.min.js`；
2. 将 `<script src="...jsdelivr...">` 改为相对路径；
3. 下载字体文件到 `assets/fonts/`；
4. 在 CSS 中使用 `@font-face`；
5. 发布前使用浏览器离线模式验证页面。

本地化后需要同步修改本 README 的目录结构和依赖说明。

## 十、常见问题

### 页面打开但架构图不显示

通常是 ECharts CDN 加载失败，或者图表容器没有高度。检查浏览器 Console 和 Network，确认 `echarts.min.js` 返回 200。

### 页面字体与设计稿不一致

检查 Google Fonts 是否可访问。网络受限时会自动回退到系统字体，这是预期行为。

### GitHub Pages 页面 404

确认：

- 仓库是否已开启 Pages；
- 发布分支和目录是否正确；
- `index.html` 是否位于发布目录根部；
- 仓库是否为公开仓库，或账号是否具备对应 Pages 权限。

### 页面中的 GitHub 链接没有跳转到项目仓库

当前页面 Footer 使用的是通用地址：

```html
<a href="https://github.com" target="_blank" rel="noopener">GitHub</a>
```

正式发布前应替换为真实仓库地址。

## 十一、许可与内容说明

本目录用于 MeetingOS 产品展示。产品名称、品牌视觉、文案、架构说明和版本历程属于项目内容；第三方字体、ECharts 及其 CDN 服务遵循各自的许可和服务条款。
