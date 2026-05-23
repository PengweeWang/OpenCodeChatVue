# OpenCodeChat

基于 Vue 3 + Vite 构建的 [OpenCode AI](https://www.npmjs.com/package/opencode-ai) 聊天面板前端，通过 SSE 实时流式接收 AI 响应，支持多 Agent、多模型切换、思维链展示、工具调用可视化、权限审批等完整交互功能。

## 功能特性

- **实时流式消息** — 基于 SSE（Server-Sent Events），实时接收 AI 响应的增量文本
- **多 Agent 支持** — 运行时动态切换不同的 AI Agent
- **多模型支持** — 按提供商分组展示可用模型，支持切换 LLM
- **推理深度控制** — 支持设置 Thinking Effort（低/中/高/最高）
- **Markdown 渲染** — 代码块（语法高亮）、表格、标题、列表、引用、链接
- **思维链展示** — 可折叠的思考过程区块
- **工具调用可视化** — 状态颜色标识（运行中/已完成/失败），支持嵌套子 Agent 详情
- **工具链进度** — 逐步展示工具调用链，带勾选状态
- **交互式问答** — Agent 提问时弹出交互面板进行回答
- **权限审批** — 文件读取/写入/执行命令等操作需用户授权
- **会话管理** — 新建、终止、重置会话
- **可调整面板** — 拖动调整侧边栏宽度（350px ~ 800px）

## 技术栈

| 技术 | 用途 |
|------|------|
| Vue 3.4+ | 前端框架（Composition API + `<script setup>`） |
| Vite 5 | 构建工具与开发服务器 |
| `@vitejs/plugin-vue` | Vue SFC 编译 |
| OpenCode AI CLI | 后端 AI Agent 服务 |
| SSE（EventSource） | 实时事件流 |
| Fetch API | REST 接口调用 |
| CSS Custom Properties | 主题样式 |

## 快速开始

### 环境要求

- Node.js >= 18
- 全局安装 [opencode-ai](https://www.npmjs.com/package/opencode-ai)：`npm install -g opencode-ai`

### 安装依赖

```bash
npm install
```

### 启动开发

```bash
npm run dev
```

该命令会同时启动以下服务：

- **OpenCode 后端服务** — `http://127.0.0.1:4096`
- **Vite 前端开发服务器** — `http://localhost:5173`

访问 `http://localhost:5173` 即可使用。

### 构建生产版本

```bash
npm run build
```

构建产物输出至 `dist/` 目录。

```bash
npm run preview
```

预览生产构建。

## DevContainer（可选）

项目提供 VS Code DevContainer 配置，开箱即用：

1. 安装 VS Code 并启用 **Dev Containers** 扩展
2. 打开项目，选择「在容器中重新打开」
3. 容器基于 Node 24，自动安装 `opencode-ai` 并挂载主机 OpenCode 配置

## 项目结构

```
OpenCodeChat/
├── .devcontainer/
│   └── devcontainer.json          # DevContainer 配置
├── index.html                     # 入口 HTML
├── package.json                   # 项目元数据与脚本
├── vite.config.js                 # Vite 配置（包含路径别名）
└── src/
    ├── main.js                    # Vue 应用入口
    ├── App.vue                    # 根组件：侧边栏面板 + 内容占位区
    └── components/
        ├── OpenCodeChatPanel.vue  # ★ 主组件：可调整大小的聊天面板
        ├── useOpenCodeChat.js     # ★ 核心组合式函数：状态管理、API 调用、SSE 处理
        └── opencode/
            ├── ChatMessage.vue    # 消息气泡渲染（用户/助手/系统）
            ├── ChatInput.vue      # 输入编辑器 + Agent/Model/Thinking 选择器
            ├── ThinkingBlock.vue  # 可折叠思考区块
            ├── ToolCallCard.vue   # 工具调用卡片 + 嵌套子 Agent 详情
            ├── ToolChain.vue      # 工具链进度可视化
            ├── QuestionDock.vue   # 交互式问答面板
            ├── PermissionDock.vue # 权限审批面板
            └── utils.js           # 工具函数：Markdown 渲染、标签助手
```

## API 端点

面板连接至 OpenCode 后端（默认 `http://127.0.0.1:4096`），使用以下接口：

| 方法 | 端点 | 说明 |
|------|------|------|
| GET | `/global/health` | 健康检查 + 版本信息 |
| GET | `/global/event` | SSE 事件流 |
| GET | `/agent` | 获取可用 Agent 列表 |
| GET | `/config/providers` | 获取模型提供商与模型列表 |
| GET | `/command` | 获取可用工具命令 |
| POST | `/session` | 创建新会话 |
| POST | `/session/{id}/message` | 发送消息 |
| POST | `/session/{id}/abort` | 终止当前生成 |
| POST | `/question/{id}/reply` | 回答 Agent 提问 |
| POST | `/question/{id}/reject` | 拒绝/取消 Agent 提问 |
| POST | `/permission/{id}/reply` | 响应权限请求 |

SSE 事件类型：`session.status`、`message.updated`、`message.part.delta`、`message.part.updated`、`question.asked`、`question.replied`、`question.rejected`、`permission.asked`、`permission.replied`。

## License

[MIT](./LICENSE)
