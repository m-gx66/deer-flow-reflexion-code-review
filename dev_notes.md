deer-flow/
├── Makefile              # 根目录总调度文件：启动/停止整套服务、docker、初始化部署（dev/start/stop）
├── config.example.yaml   # 配置模板！复制改名成 config.yaml，并且这个config.yaml已经写进gitignore，不会上传GitHub
├── extensions_config.example.json # 扩展配置模板，复制成 extensions_config.json（gitignore），用来配置MCP服务器、技能skills
├── backend/              # Python后端，FastAPI网关，核心Agent框架在这里
│   ├── Makefile          # 后端模块单独的Makefile：后端开发、网关、测试、代码检查、数据库迁移
│   ├── extensions/sources/ # 本地Python扩展的可部署快照                                         +++可做reflexion中间件
│   ├── packages/extension-api/ # deerflow-extension-api 包，对外扩展接口规范（扩展契约）
│   ├── packages/harness/  # deerflow-harness 核心包，也就是 Agent 运行框架，import deerflow.*   +++执行循环就在这里！
│   └── app/              # FastAPI网关 + IM通讯通道，import app.*                              +++网关，IM渠道
├── frontend/             # Next.js 前端项目，包管理器是                                         +++
├── docker/               # docker-compose编排、nginx反向代理配置、初始化部署脚本
├── skills/               # Agent技能包                                                         +++
│   ├── public/           # 公开技能，会提交到git仓库
│   └── custom/           # 用户自定义技能，gitignore，不上传GitHub
│   # 全局集成技能包路径：.deer-flow/integrations/skills/{provider}/
│   # 账号密钥、启用状态是按用户单独保存
├── contracts/            # 跨组件JSON契约文件，子代理状态、技能审核这类跨模块通信协议               +++
├── examples/             # 扩展示例代码：deerflow-extension-example、bookmarks、agent-teams
├── scripts/              # 根目录脚本，被根Makefile调用：环境检测、初始化向导、部署、nginx、docker、排障脚本
├── tests/                # 根目录测试，目前主要是 skills 相关测试
└── docs/                 # 跨模块文档、方案设计、开发计划笔记
