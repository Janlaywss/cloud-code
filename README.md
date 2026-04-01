# Cloud-code 破解版

[English](./README.en.md) | 简体中文

## 源码学习文档
欢迎体验：https://cloud-code-study.vercel.app/
- https://github.com/Janlaywss/cloud-code-study

## 食用指南
- 本仓库已经还原好源码，看 claude-code-source 目录
- 本地如何编译运行？请查阅claude-code-source/README.md
  - 我的电脑是 Macbook Pro Apple 芯片。所以里面用的bun运行时架构为Apple芯片适配
  - 其他的CPU芯片架构，请自己摸索安装（或者直接让AI处理）
- 根目录下有个ai-cloud-xxxx.tgz文件，这个是2188原npm版本包

## 本仓库用到的还原方式
```bash
# 1. 从 npm 下载包
npm pack @anthropic-ai/claude-code --registry https://registry.npmjs.org

# 2. 解压
tar xzf anthropic-ai-claude-code-2.1.88.tgz

# 3. 解析 cli.js.map，将 sourcesContent 按原始路径写出
node -e "
const fs = require('fs');
const path = require('path');
const map = JSON.parse(fs.readFileSync('package/cli.js.map', 'utf8'));
const outDir = './claude-code-source';
for (let i = 0; i < map.sources.length; i++) {
  const content = map.sourcesContent[i];
  if (!content) continue;
  let relPath = map.sources[i];
  while (relPath.startsWith('../')) relPath = relPath.slice(3);
  const outPath = path.join(outDir, relPath);
  fs.mkdirSync(path.dirname(outPath), { recursive: true });
  fs.writeFileSync(outPath, content);
}
"
```

source map 中包含 **4756** 个源文件及其完整源码（`sourcesContent`），可以无损还原所有 TypeScript/TSX 原始代码。

## 目录结构

```
.
├── src/                  # 核心源码（1902 个文件）
│   ├── main.tsx          # 应用入口
│   ├── Tool.ts           # 工具基类
│   ├── Task.ts           # 任务管理
│   ├── QueryEngine.ts    # 查询引擎
│   ├── commands.ts       # 命令注册
│   ├── tools.ts          # 工具注册
│   ├── assistant/        # 会话历史管理
│   ├── bootstrap/        # 启动初始化
│   ├── bridge/           # 桥接层（31 个文件）
│   ├── buddy/            # 子代理系统（6）
│   ├── cli/              # CLI 参数解析与入口（19）
│   ├── commands/         # 斜杠命令实现（207）
│   ├── components/       # 终端 UI 组件，基于 Ink（389）
│   ├── constants/        # 共享常量（21）
│   ├── context/          # 上下文管理（9）
│   ├── coordinator/      # Agent 协调器（1）
│   ├── entrypoints/      # 各类入口点（8）
│   ├── hooks/            # 生命周期钩子（104）
│   ├── ink/              # 自定义 Ink 终端渲染引擎（96）
│   ├── keybindings/      # 快捷键管理（14）
│   ├── memdir/           # 记忆目录系统（8）
│   ├── migrations/       # 数据迁移（11）
│   ├── moreright/        # 权限系统（1）
│   ├── native-ts/        # 原生 TS 工具（4）
│   ├── outputStyles/     # 输出格式化（1）
│   ├── plugins/          # 插件系统（2）
│   ├── query/            # 查询处理（4）
│   ├── remote/           # 远程执行（4）
│   ├── schemas/          # 数据模式定义（1）
│   ├── screens/          # 屏幕视图（3）
│   ├── server/           # Server 模式（3）
│   ├── services/         # 核心服务（130）
│   ├── skills/           # 技能系统（20）
│   ├── state/            # 状态管理（6）
│   ├── tasks/            # 任务执行（12）
│   ├── tools/            # 工具实现（184）
│   ├── types/            # TypeScript 类型定义（11）
│   ├── upstreamproxy/    # 上游代理支持（2）
│   ├── utils/            # 工具函数（564）
│   ├── vim/              # Vim 模式（5）
│   └── voice/            # 语音输入（1）
├── vendor/               # 内部 vendor 代码（4 个文件）
│   ├── modifiers-napi-src/   # 按键修饰符原生模块
│   ├── url-handler-src/      # URL 处理
│   ├── audio-capture-src/    # 音频采集
│   └── image-processor-src/  # 图片处理
└── node_modules/         # 打包的第三方依赖（2850 个文件）
```

## 核心模块说明

| 模块 | 文件数 | 说明 |
|------|--------|------|
| `utils/` | 564 | 工具函数集 — 文件 I/O、Git 操作、权限检查、Diff 处理等 |
| `components/` | 389 | 终端 UI 组件，基于 Ink（React 的 CLI 版本）构建 |
| `commands/` | 207 | 斜杠命令实现，如 `/commit`、`/review` 等 |
| `tools/` | 184 | Agent 工具实现 — Read、Write、Edit、Bash、Glob、Grep 等 |
| `services/` | 130 | 核心服务 — API 客户端、认证、配置、会话管理等 |
| `hooks/` | 104 | 生命周期钩子 — 工具执行前后的拦截与权限控制 |
| `ink/` | 96 | 自研 Ink 渲染引擎，包含布局、焦点管理、渲染优化 |
| `bridge/` | 31 | 桥接层 — IDE 扩展与 CLI 之间的通信 |
| `skills/` | 20 | 技能加载与执行系统 |
| `cli/` | 19 | CLI 参数解析与启动逻辑 |
| `keybindings/` | 14 | 键盘快捷键绑定与自定义 |
| `tasks/` | 12 | 后台任务与定时任务管理 |

## 统计

| 指标 | 数值 |
|------|------|
| 源文件总数 | 4,756 |
| 核心源码（src/ + vendor/） | 1,906 个文件 |
| 第三方依赖（node_modules/） | 2,850 个文件 |
| Source Map 大小 | 57 MB |
| 包版本 | 2.1.88 |
