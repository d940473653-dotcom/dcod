# AI Web Modifier

    AI 驱动的浏览器网页修改助手 — 选中元素，自然语言描述，AI 智能修改。

    ## ✨ 功能特性

    - 🎯 **元素选择器** — 类 F12 的可视化元素选取，支持父/子/兄弟导航
    - 🤖 **AI 智能修改** — 自然语言描述需求，AI 自动生成并注入代码
    - 🔍 **按需探查** — AI 通过 Tool Calling 自主获取页面信息，不浪费 token
    - 🎨 **CSS 作用域隔离** — 注入的样式自动隔离，不影响页面其他元素
    - ↩️ **撤销重做** — 最多 20 步历史记录，随时回退
    - 📸 **截图验证** — AI 修改后自动截图确认效果

    ## 🏗️ 技术架构

    ┌──────────────┐     ┌────────────────────┐     ┌──────────────┐
    │  Side Panel   │◄───►│  Service Worker     │◄───►│ Content Script│
    │  (对话 UI)    │     │  (Agent 循环)       │     │ (页面探查/注入)│
    └──────────────┘     │  · Kimi K2.6 API    │     └──────────────┘
                         │  · Tool 路由        │
                         └────────────────────┘

    | 层级 | 技术选型 | 说明 |
    |------|---------|------|
    | 扩展规范 | Manifest V3 | Chrome 强制要求 |
    | AI 模型 | Kimi K2.6 | 原生 Tool Calling，支持 128 工具 |
    | API 格式 | OpenAI 兼容 | function calling |
    | UI 载体 | Side Panel | 持久化面板，不遮挡页面 |
    | 构建方式 | 纯 JS，无框架 | 轻量，自用 |

    ## 🔧 AI 工具列表

    ### 感知工具（按需调用）

    | 工具 | 作用 |
    |------|------|
    | `inspect_element` | 查看元素 DOM、计算样式、匹配 CSS 规则 |
    | `get_html_source` | 获取指定区域 HTML 源码 |
    | `get_page_resources` | 获取页面资源列表，自动识别第三方插件 |
    | `get_custom_code` | 获取自定义 CSS/JS，跳过第三方库 |
    | `get_design_system` | 获取 CSS 变量、颜色、字体、间距规律 |
    
    ### 执行工具

    | 工具 | 作用 |
    |------|------|
    | `apply_style` | 注入 CSS（自动作用域隔离） |
    | `modify_dom` | 增删改 DOM 节点 |
    | `inject_script` | 注入 JavaScript |
    | `revert_last` | 撤销上一次修改 |

    ### 验证工具

    | 工具 | 作用 |
    |------|------|
    | `capture_screenshot` | 截图验证修改效果 |

    ## 📂 项目结构

    ai-web-modifier/
    ├── manifest.json                      # MV3 配置
    ├── icons/
    │   └── icon16/48/128.png
    ├── background/
    │   └── service-worker.js              # Agent 循环 + API 调用 + 工具路由
    ├── content-scripts/
    │   ├── content-bridge.js              # MAIN world · 框架检测
    │   ├── selector.js                    # 元素选择器 + 信息提取
    │   ├── selector.css                   # 选择器样式
    │   ├── dom-probe.js                   # 5 个感知工具实现
    │   ├── injector.js                    # 注入引擎 + 作用域隔离 + 撤销重做
    │   └── screenshot.js                  # 截图（支持超出视口拼接）
    ├── side-panel/
    │   ├── side-panel.html                # 侧边栏 UI
    │   ├── side-panel.css                 # 暗色主题样式
    │   ├── side-panel.js                  # 主控制器
    │   ├── state-store.js                 # 发布订阅状态管理
    │   ├── chat-renderer.js               # Markdown 渲染 + 语法高亮
    │   └── code-viewer.js                 # 代码展示 + 复制/导出
    └── shared/
        └── protocol.js                    # 消息类型常量

    ## 🚀 安装使用

    1. 克隆仓库
       ```bash
       git clone https://github.com/your-username/ai-web-modifier.git

    2. 打开 Chrome，访问 chrome://extensions/
    3. 开启右上角 开发者模式
    4. 点击 加载已解压的扩展程序，选择项目目录
    5. 在任意网页上点击扩展图标打开 Side Panel
    6. 点击「开始选取」→ 选中页面元素 → 输入需求 → 发送

    📋 工作流程

    用户选中元素 → 自动提取选择器/样式/结构
             ↓
    用户描述需求 → 发送到 Service Worker
             ↓
    Agent 循环（Kimi K2.6）：
      1. AI 分析需求，按需调用感知工具探查页面
      2. 生成修改代码，调用执行工具注入
      3. 调用截图工具验证效果
      4. 总结回复用户
             ↓
    Side Panel 显示 AI 回复 + 工具活动状态

    ⚙️ 配置

    API Key 已内置在 background/service-worker.js 中（自用）。如需更换：

    const API_ENDPOINT = 'https://api.moonshot.cn/v1/chat/completions';
    const API_KEY = 'your-api-key-here';
    const MODEL = 'kimi-k2.6';

    📌 已知限制

    - Service Worker 30 秒超时（Agent 循环期间自动保持活跃）
    - 跨域样式表无法访问规则（浏览器安全限制）
    - 截图依赖 captureVisibleTab，需要页面可见
    - file:// 协议页面需在扩展管理中勾选「允许访问文件网址」

    📄 License

    MIT
