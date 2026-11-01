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

yaml
# ========== 沙箱配置（代码执行核心，Docker模式） ==========
sandbox:
  use: deerflow.sandbox.docker:DockerSandboxProvider
  # 可选：关闭沙箱（不推荐，失去自动跑代码、验证能力）
  # use: deerflow.sandbox.null:NullSandboxProvider
  timeout: 300                       # 单段代码最大执行时长
  memory_limit_mb: 2048              # 沙箱内存上限
  cpu_limit: 1.0
# ========== 工具开关（联网搜索等） ==========
tools:
  - use: deerflow.community.ddg_search:DDGSearchTool
    enabled: true                    # true=允许联网搜索；false=断网模式
  # 后续可以在这里加 tavily 搜索等其他工具
# ========== Agent全局行为控制 ==========
agent:
  max_turns: 15                      # 一轮对话最多思考轮次，防止无限循环
  enable_skill_evolution: false      # 自动生成技能，新手建议先关
  max_subagents: 2                   # 子代理数量上限，别开太大吃资源
# ========== 前端、网关基础（一般不用改） ==========
gateway:
  host: 0.0.0.0
  port: 8001

frontend:
  port: 3000

.en 格式，禁止上交git 存放密钥
# 火山方舟 豆包配置
LLM_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxx
LLM_BASE_URL=https://ark.cn-beijing.volces.com/api/v1
LLM_MODEL=doubao-1-5-pro-32k

# 可选：搜索工具密钥（tavily，不用就留空或者注释掉）
# TAVILY_API_KEY=tvly-xxxxxx

# 可选：LangSmith调试追踪（不懂就注释掉）
# LANGSMITH_TRACING=true
# LANGSMITH_API_KEY=lsv2_pt_xxxx

常用修改速查表
目标	                修改位置	                              说明
换模型                	config.yaml model + .env LLM_MODEL	  复制方舟模型 ID
降低幻觉、适合写代码   	temperature: 0.1~0.3	                越高越发散
关闭联网搜索	          tools.enabled: false	                不访问外网
关闭代码沙箱	          sandbox use 切换为 NullSandboxProvider	无法自动执行验证代码
增加思考轮次	          max_turns	                            复杂任务调到 20
解决超时	              request_timeout / sandbox timeout	    拉长至 600/900