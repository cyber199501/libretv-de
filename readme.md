# LibreTV - 免费在线视频搜索与观看平台

## 📺 项目简介

LibreTV 是一个轻量级、免费的在线视频搜索与观看平台，提供来自多个视频源的内容搜索与播放服务。无需注册，即开即用，支持多种设备访问。项目采用前端技术构建，并**利用 Serverless Functions 实现内部代理**，以解决跨域请求问题并处理 M3U8 播放列表，可轻松部署在 Cloudflare Pages、Vercel、Netlify 等现代托管服务上。

本项目基于 [bestZwei/LibreTV](https://github.com/LibreSpark/LibreTV)，主要增加内部代理功能

演示站：(请自行部署，不再提供演示站)

<img src="https://testingcf.jsdelivr.net/gh/bestZwei/imgs@master/picgo/image-20250406231222216.png" alt="LibreTV 界面截图" style="zoom:67%;" />

## ✨ 主要特性

-   🔍 **多源搜索**: 同时聚合多个视频源的搜索结果。
-   📱 **响应式设计**: 完美适配电脑、平板和手机访问。
-   🔗 **内部代理**: 通过 Serverless Function 解决 API 跨域问题，处理 M3U8 播放列表。
-   🔄 **自定义 API**: 支持添加符合标准的苹果CMS V10 API 接口。
-   💾 **搜索历史**: 使用 localStorage 记录最近搜索，方便快速访问。
-   ⚡️ **现代部署**: 轻松部署于 Cloudflare Pages、Vercel、Netlify 等平台。
-   🛡️ **广告过滤**: 播放器内置基础的 M3U8 分片广告过滤（可开关）。
-   🎬 **定制播放器**: 基于 DPlayer 和 HLS.js，提供流畅的 HLS 播放体验。
-   ⌨️ **快捷键支持**: 播放器支持常用快捷键操作。

## ⌨️ 键盘快捷键

LibreTV 播放器支持以下键盘快捷键：

-   **Alt + 左箭头**: 播放上一集
-   **Alt + 右箭头**: 播放下一集
-   **空格键**: 暂停/播放
-   **左/右箭头**: 快退/快进 5 秒
-   **上/下箭头**: 调高/调低音量
-   **F**: 进入/退出全屏

## 📹 视频源与代理说明

-   **代理作用**: 由于浏览器同源策略限制，前端无法直接请求第三方 API。本项目使用 Serverless Function（部署在 Cloudflare Pages / Vercel / Netlify）作为内部代理，代为请求目标 API 和 M3U8 文件，解决跨域问题，并统一处理 M3U8 文件中的 URL，确保播放流畅。
-   **CMS 兼容性**: 支持标准的**苹果CMS V10 API**格式。
    -   搜索接口格式: `/api.php/provide/vod/?ac=videolist&wd=关键词`
    *   详情接口格式: `/api.php/provide/vod/?ac=videolist&ids=视频ID` (注意：v10详情接口通常也是`ac=videolist`)
-   **自定义接口添加**:
    1.  在设置面板选择"自定义接口"。
    2.  接口地址**只需填写到域名部分**，例如：`https://jszyapi.com` 或 `http://ffzy5.tv` (注意 `http` 或 `https`)。
    3.  项目代码会自动在后端函数（或代理）中补全 `/api.php/provide/vod/...` 等路径。
-   **非标准接口**: 如果 CMS 的 API 路径不是标准的 `/api.php/provide/vod/`，你可能需要修改对应平台函数文件 (`/api/proxy/[...path].js` 或 `/netlify/functions/proxy.js`) 中的 `API_CONFIG` 部分。

## 🛠️ 技术栈

-   HTML5 + CSS3 + JavaScript (ES6+)
-   Tailwind CSS (通过 CDN 引入)
-   Serverless Functions: (Cloudflare Pages Functions / Vercel Serverless Functions / Netlify Functions) 用于实现内部代理。
-   HLS.js: 用于 HLS 流处理和广告过滤。
-   DPlayer: 视频播放器核心。
-   localStorage: 用于本地存储设置和历史记录。

## 🚀 一键部署

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2FCTZZG%2FLibreTV)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/CTZZG/LibreTV)
[![Deploy to Cloudflare Pages](https://img.shields.io/badge/Deploy%20to-Cloudflare%20Pages-F38020?logo=cloudflare&logoColor=F38020)](https://dash.cloudflare.com/?to=/:account/pages/new/deploy-with-git)

**重要**: 使用上方按钮部署后，仍需根据下文**手动配置环境变量**才能使代理功能正常工作！

## 🚀 部署指南

本项目包含前端静态文件和一个 Serverless Function 代理。部署到不同平台需要注意函数路径和环境变量配置。

### A. Cloudflare Pages 部署

1.  **Fork/克隆仓库**: 将本仓库 Fork 或克隆到你的 GitHub/GitLab 账户。
2.  **连接仓库**: 登录 Cloudflare Dashboard -> Pages -> 创建项目 -> 连接你的仓库。
3.  **构建设置**:
    *   构建命令：留空 (无需构建)
    *   输出目录：留空 (或填 `/`)
4.  **环境变量**: **【关键步骤】** 进入项目设置 -> 函数 -> 环境变量绑定 -> **添加生产和预览环境**的变量：
    *   `CACHE_TTL`: `86400` (代理缓存时间，秒)
    *   `MAX_RECURSION`: `5` (M3U8 最大递归层数)
    *   `USER_AGENTS_JSON`: `["Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.0 Safari/605.1.15"]` (JSON 字符串数组格式，至少包含一个 User-Agent)
    *   `DEBUG`: `false` (设为 `true` 可在函数日志中看到更多信息)
5.  **部署**: 保存并部署。Cloudflare 会自动识别 `/functions` 目录下的函数。

### B. Vercel 部署

1.  **Fork/克隆仓库**: 同上。
2.  **导入项目**: 登录 Vercel -> Add New -> Project -> Import Git Repository -> 选择你的仓库。
3.  **构建设置**: Vercel 通常会自动检测到这是一个静态项目（无框架）。
    *   Framework Preset: Other
    *   Build Command: 留空
    *   Output Directory: 留空 (或 `.`)
    *   Install Command: 留空
4.  **环境变量**: **【关键步骤】** 进入项目设置 -> Environment Variables -> 添加以下变量 (确保同时添加到 Production, Preview, Development):
    *   `CACHE_TTL`: `86400`
    *   `MAX_RECURSION`: `5`
    *   `USER_AGENTS_JSON`: `["...", "..."]` (同上)
    *   `DEBUG`: `false`
5.  **部署**: 点击 Deploy。Vercel 会自动识别 `/api` 目录下的函数。

### C. Netlify 部署

1.  **Fork/克隆仓库**: 同上。
2.  **连接仓库**: 登录 Netlify -> Add new site -> Import an existing project -> 选择你的 Git 提供商 -> 选择你的仓库。
3.  **构建设置**:
    *   Build command: 留空
    *   Publish directory: 留空 (或 `.`)
    *   Functions directory: `netlify/functions` (确保 Netlify 识别函数目录)
4.  **环境变量**: **【关键步骤】** 进入 Site settings -> Build & deploy -> Environment -> Environment variables -> Add environment variables:
    *   `CACHE_TTL`: `86400`
    *   `MAX_RECURSION`: `5`
    *   `USER_AGENTS_JSON`: `["...", "..."]` (同上)
    *   `DEBUG`: `false`
5.  **部署**: 点击 Deploy site。Netlify 会部署静态文件并识别 `/netlify/functions` 目录下的函数。

## 🔧 自定义配置
前端配置 (js/config.js):
- `PROXY_URL`: 修改为你自己的代理服务地址 (CF Pages、Vercel、Netlify无需调整，保持默认即可)。
- `API_SITES`: 添加或修改视频源API接口。
- `SITE_CONFIG`: 更改站点名称、描述等基本信息。
- `PLAYER_CONFIG`: 调整播放器参数，如自动播放、广告过滤等。
- `HIDE_BUILTIN_ADULT_APIS`: 用于控制是否隐藏内置的黄色采集站API，默认值为true。设置为true时，内置的某些敏感API将不会在设置面板中显示，可根据实际需要修改配置。
- `后端代理配置 (环境变量)`: 在部署平台的设置界面修改 CACHE_TTL, DEBUG, MAX_RECURSION, USER_AGENTS_JSON 等环境变量。

## 🌟 项目结构 (适配多平台)

Cloudflare Pages 结构:

```
LibreTV/
├── css/
│   └── styles.css       // 自定义样式
├── functions/
│   └── proxy/
│       └── [[path]].js  // CF Pages Function
├── js/
│   ├── app.js           // 主应用逻辑
│   ├── api.js           // API请求处理
│   ├── config.js        // 全局配置
│   └── ui.js            // UI交互处理
├── player.html          // 自定义视频播放器
├── index.html           // 主页面
├── robots.txt           // 搜索引擎爬虫配置
└── sitemap.xml          // 站点地图
```

Vercel 结构:

```
LibreTV/
├── api/
│   └── proxy/
│       └── [...path].mjs // Vercel Function
├── css/
│   └── styles.css        // 自定义样式
├── js/
│   ├── app.js            // 主应用逻辑
│   ├── api.js            // API请求处理
│   ├── config.js         // 全局配置(config.js 修改 PROXY_URL='/api/proxy/')
│   └── ui.js             // UI交互处理
├── player.html           // 自定义视频播放器
├── index.html            // 主页面
├── robots.txt            // 搜索引擎爬虫配置
├── package.json          // package配置
├── package-lock.json     // package版本锁定
└── vercel.json           // vercel配置
└── sitemap.xml           // 站点地图
```

Netlify 结构:

```
LibreTV/
├── css/
│   └── styles.css       // 自定义样式
├── js/
│   ├── app.js           // 主应用逻辑
│   ├── api.js           // API请求处理
│   ├── config.js        // 全局配置(config.js 修改 PROXY_URL='/api/proxy/')
│   └── ui.js            // UI交互处理
├── netlify/
│   └── functions/
│       └── proxy.mjs    // Netlify Function
├── player.html          // 自定义视频播放器
├── index.html           // 主页面
├── netlify.toml         // netlify配置
├── robots.txt           // 搜索引擎爬虫配置
├── package.json         // package配置
├── package-lock.json    // package版本锁定
└── sitemap.xml          // 站点地图
```

(注意：实际项目中你只需保留与你目标平台对应的函数文件和配置)

## ⚠️ 免责声明

LibreTV 仅作为视频搜索工具，不存储、上传或分发任何视频内容。所有视频均来自第三方 API 接口提供的公开搜索结果。内部代理仅用于解决浏览器跨域限制和处理 M3U8 格式，不修改视频内容本身。如有侵权内容，请联系相应的内容提供方处理。使用本工具产生的任何法律后果由使用者自行承担。

## 🔄 更新日志
同步原项目更新，添加内部代理
