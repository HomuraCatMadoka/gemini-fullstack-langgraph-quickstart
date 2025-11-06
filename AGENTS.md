# Repository Guidelines

## 项目结构与模块组织
- 根目录区分 `backend/` 与 `frontend/`，所有 Python 代理逻辑集中在 `backend/src/agent/`，核心文件如 `graph.py` 定义 LangGraph 流程。
- LangGraph 配置示例位于 `backend/langgraph.json`，命令行脚本存放在 `backend/examples/`，图像资源保存在根目录。
- 共享配置、提示词和工具定义集中在 `configuration.py`、`prompts.py` 与 `tools_and_schemas.py`，修改时注意保持单一职责。
- 前端代码位于 `frontend/src/`，组件拆分在 `components/`，共享方法放在 `lib/`，静态资源置于 `public/`。

## 构建、测试与开发命令
- `make dev` 同时启动前后端热重载，适用于整体联调。
- `make dev-frontend`、`npm run dev` 分别用于前端，本地访问 `http://localhost:5173`。
- `make dev-backend` 或 `langgraph dev` 启动 FastAPI 服务，默认端口 `2024`，需先写 `.env`。
- `npm run build` 生成生产包；`npm run preview` 验证打包结果；`pip install .[dev]` 安装后端依赖。
- 生产部署可使用 `docker build -t gemini-fullstack-langgraph -f Dockerfile .` 并结合 `docker-compose up` 拉起全栈栈。

## 代码风格与命名约定
- 后端采用 Ruff 规则集，遵循 Google 风格 docstring；提交前执行 `ruff check backend/src` 与 `mypy backend/src`。
- Python 模块命名使用下划线，类用帕斯卡命名，LangGraph 节点函数以动词短语开头。
- 前端 TypeScript 保持 Vite 默认两空格缩进，React 组件命名使用帕斯卡，hooks 文件以 `use-*` 命名，Tailwind 类集中在 JSX 中。
- ESLint 规则见 `eslint.config.js`，如需新增规则请优先通过共享配置扩展，保持团队一致性。

## 测试指引
- Python 侧预期使用 `pytest`，测试文件放在 `backend/tests/`（若缺失可新建），命名模式 `test_*.py`。
- 建议为关键图谱节点编写快照或回归测试，并使用 `pytest -m "not slow"` 控制执行范围。
- 前端暂未集成单测，可使用 Vitest/React Testing Library；如新增测试请在 `frontend/src/__tests__/` 维护。
- 目标是保持核心路径（查询生成、消息流渲染）至少 80% 语句覆盖率，可通过 `pytest --cov` 与 Vitest `--coverage` 监控。

## 提交与 Pull Request 规范
- Git 历史以 `type/scope` 或 `type: summary` 形式为主，如 `fix/ime-input-form-submission`；请保持动词祈使语气。
- 每个 PR 需说明动机、主要改动、验证方式，涉及 UI 变更附带截图，关联 Issue 时使用 `Closes #id`。
- 推送前运行 lint 与测试，避免将 `.env`、密钥或生成文件提交。
- 若 PR 涉及 API 交互或协议调整，请附上前后端接口契约示例，便于评审同步。

## 安全与配置提示
- 后端依赖 `GEMINI_API_KEY`（可选 `LANGSMITH_API_KEY`），复制 `backend/.env.example` 后填入有效值，避免提交密钥文件。
- Docker 部署需确保 Redis 与 Postgres 可用，若只做本地演示可使用 `docker-compose up` 并配置端口转发。
- 若扩展外部工具或搜索 API，请在 `backend/tools_and_schemas.py` 中集中登记，确保输入校验和速率限制。
- 上线前请启用 HTTPS 与请求限流，必要时在反向代理层设置 IP 白名单与慢查询日志。
