# Repository Guidelines

## Project Structure & Module Organization
- `main.py` 是应用入口，只负责装配 `FastAPI`、生命周期和路由；本地启动请走 Granian，而不是 `python main.py`。
- `app/api/v1` 放 OpenAI 兼容接口与管理接口，`app/api/pages` 提供后台和功能页路由。
- `app/core` 集中配置、鉴权、存储、日志和通用中间件；优先复用这里的能力。
- `app/services` 按领域拆分为 `grok`、`reverse`、`token`、`cf_refresh` 等服务层。
- `_public/static` 存放后台与功能页的 HTML/CSS/JS；`tests/` 存放 Python 测试。
- 依赖清单位于 `requirements.txt`；运行时数据默认写入 `data/`（可由 `DATA_DIR` 覆盖）。
- 默认配置基线位于 `config.defaults.toml`。

## Build, Test, and Development Commands
- `python -m pip install -r requirements.txt`：安装运行与开发依赖。
- `granian --interface asgi --host 0.0.0.0 --port 8000 --workers 1 main:app`：本地启动服务。
- `docker compose up -d`：使用容器启动完整环境。
- `pytest`：运行测试。
- `ruff check .`：执行静态检查；提交前先跑一遍。

## Coding Style & Naming Conventions
- Python 使用 4 空格缩进，保持现有类型标注和异步优先风格。
- 模块、函数、变量使用 `snake_case`，类使用 `PascalCase`，常量使用全大写。
- 路由层只做参数处理与响应封装，业务逻辑放到 `app/services/*`，通用逻辑优先复用 `app/core` 和 `utils`。
- 前端静态资源命名保持页面对应关系，如 `chat.html`、`chat.js`、`chat.css`。

## Testing Guidelines
- 测试框架为 `pytest`，文件命名遵循 `tests/test_*.py`。
- 新增或修复流式、usage、token/存储逻辑时，补充最小可复现的回归测试。
- 涉及 SSE 或多阶段响应时，优先断言事件结构、结束标记和 usage 字段，参考 `tests/test_openai_usage.py`。

## Commit & Pull Request Guidelines
- Git 历史以简短 Conventional Commits 为主：`fix: ...`、`ci: ...`、`docs: ...`。
- PR 请按 `.github/pull_request_template.md` 填写 `Summary`、`Changes`、`Related Issues`、`Verification` 和 `Breaking Changes`。
- 影响接口时附上调用示例；影响 `_public/static` 页面时附截图或录屏。

## Security & Configuration Tips
- 不要提交真实 token、cookie、`.env` 或 `data/` 下的运行数据。
- 修改配置相关逻辑时，同时检查 `config.defaults.toml`、`readme.md` 和部署文件是否需要同步更新。
- 仓库已启用 `gitleaks`、`pip-audit` 和 `CodeQL`，新增依赖或密钥处理代码时请特别谨慎.
