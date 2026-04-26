# 安装 PaddleOCR 技能

请在当前 OpenClaw、AutoClaw 或兼容的 Agent 宿主环境中安装 PaddleOCR 技能，并一次性完成下载、安装、配置、依赖处理和验证。

核心原则：

- 下载来源必须固定为 ClawHub API。
- 安装目录、配置文件、配置命令和 skill entry schema 必须按当前宿主环境发现和适配。
- 不要把 OpenClaw / AutoClaw 的某个默认路径当成所有宿主的强制路径。

## 输入信息

请把下面占位符替换为真实值后再执行：

- ClawHub Token: `<CLAWHUB_TOKEN>`，必须以 `clh_` 开头
- 文档解析 API: `<DOC_PARSING_API_URL>`，用于 PaddleOCR-VL-1.5 document parsing
- 文字识别 API: `<OCR_API_URL>`，用于 PP-OCRv5 text recognition
- PaddleOCR Access Token: `<ACCESS_TOKEN>`，PaddleOCR 官网访问令牌

## 硬性要求

1. 两个技能只能从 ClawHub API 下载。
2. 不要使用 `skills.sh`。
3. 不要使用 GitHub raw download。
4. 不要从 GitHub release、npm、pip 或其他第三方源替代下载技能。
5. 不要用宿主自带的 marketplace、installer 或 shell 脚本替代 ClawHub API 下载；宿主 CLI 只能用于注册本地技能或写入配置。
6. 如果 ClawHub 下载失败，必须修复 ClawHub Token、下载 API、网络或 HTTP 错误；不要绕道换源。
7. 不要把技能文件安装到当前项目仓库，也不要把任何 Token 或 API 凭据写入会同步到 GitHub 的文件。
8. 不要使用宿主自带的嵌入式 Python 来安装依赖；它可能没有 pip。优先使用 `uv run`。

## 安装来源

两个技能均从 ClawHub 安装，页面仅用于确认来源：

- `paddleocr-doc-parsing`: `https://clawhub.ai/bobholamovic/paddleocr-doc-parsing`
- `paddleocr-text-recognition`: `https://clawhub.ai/bobholamovic/paddleocr-text-recognition`

实际下载必须使用 ClawHub 下载 API：

```http
GET https://wry-manatee-359.convex.site/api/v1/download?slug=<skill-slug>
Authorization: Bearer <CLAWHUB_TOKEN>
```

其中 `<skill-slug>` 只能是：

- `paddleocr-doc-parsing`
- `paddleocr-text-recognition`

API 返回 ZIP 包，正常情况下应包含 `SKILL.md`、`scripts/`、`references/` 和 `_meta.json`。

## 宿主适配

安装前先发现当前宿主环境，并形成一个本次安装计划。不要跳过这一步。

需要确认并记录：

- `HOST_NAME`: 当前宿主，例如 OpenClaw、AutoClaw 或其他兼容 Agent。
- `HOST_HOME`: 当前宿主的本机配置根目录。
- `SKILLS_ROOT`: 当前宿主实际使用的 managed skills 目录。
- `CONFIG_WRITE_METHOD`: 当前宿主支持的配置写入方式。
- `CONFIG_FILE`: 如需编辑本机配置文件，记录其路径。
- `ENTRY_SCHEMA`: 当前宿主的 skill entry 字段形态。

发现顺序：

1. 优先读取当前宿主已有配置或 CLI 输出，找出已有 skill entry 使用的 `path`、`env`、`enabled` 等字段。
2. 如果存在宿主配置命令，例如 `gateway config.patch` 或等价的本机配置命令，先查看 `--help` 或本机文档，确认它写入的配置位置和 patch 格式。
3. 如果没有配置命令，但存在明确的本机配置文件，可以备份后按原 schema 修改配置文件。
4. 如果已有配置中能发现 managed skills 目录，必须沿用该目录。
5. 如果无法发现 managed skills 目录，按宿主本机配置根目录推导 `<HOST_HOME>/skills/`，并在执行前说明推导依据。
6. 如果无法确定宿主、本机配置根目录或配置 schema，停止并要求用户提供宿主的 skills 目录和配置方式；不要猜测，不要写入项目仓库。

兼容性提示：

- OpenClaw / AutoClaw 可能使用 `~/.openclaw-autoclaw/skills/`，但这只是候选目录，不是强制目录。
- 其他兼容 Agent 可能使用 `~/.config/<host>/skills/`、`~/.local/share/<host>/skills/` 或自己的 managed skills 目录。
- 配置文件不一定叫 `openclaw.json`，配置字段也不一定固定为 `skills.entries`。
- `path` 可以是绝对路径、`~` 路径或宿主专用 URI；必须沿用当前宿主已有配置风格。

## 执行步骤

1. 校验输入信息：
   - `<CLAWHUB_TOKEN>` 非空且以 `clh_` 开头。
   - `<DOC_PARSING_API_URL>`、`<OCR_API_URL>`、`<ACCESS_TOKEN>` 均非空。
   - 如果任何值缺失，先要求用户补齐，不要用占位符继续执行。
2. 确认工具：
   - 确认 `uv` 可用。
   - 如果 `uv` 不可用，优先安装或启用用户级 `uv`；不要把依赖安装进项目仓库。
   - 如果宿主提供配置 CLI，确认该 CLI 可用；如果不可用，转为本机配置文件 patch 流程。
3. 制定安装计划：
   - 输出将使用的 `HOST_NAME`、`SKILLS_ROOT`、配置写入方式和配置文件。
   - 如果计划会编辑本机配置文件，先创建同目录备份。
   - 如果发现多个候选 skills 目录，选择当前配置正在使用的目录；无法判断时询问用户。
4. 使用 ClawHub API 分别下载两个 ZIP：
   - 请求必须带 `Authorization: Bearer <CLAWHUB_TOKEN>`。
   - 保存到临时目录，不要保存到项目仓库。
   - 校验 HTTP status、文件大小和 ZIP 可解压性。
   - 如果返回 401 / 403，检查 ClawHub Token。
   - 如果返回 404，检查 slug 是否完全一致。
   - 如果网络或 5xx 失败，重试一次；仍失败则报告 ClawHub 下载失败，不要切换到其他来源。
5. 解压并标准化目录：
   - 解压到临时目录。
   - 无论 ZIP 是否多包了一层目录，最终待安装目录根部都必须直接包含 `SKILL.md`、`scripts/`、`_meta.json`。
   - 读取 `_meta.json`，记录版本号。
6. 安全审查：
   - 阅读两个技能的 `SKILL.md`。
   - 检查 `scripts/` 中会执行的脚本，确认没有删除用户文件、上传本机敏感文件、修改项目仓库或绕过配置的行为。
   - 如发现明显风险，停止安装并报告具体文件和原因。
7. 安装技能：
   - 将两个技能分别安装到 `<SKILLS_ROOT>/<skill-slug>/`。
   - 如果目标技能目录已存在，先备份为同级目录，例如 `<skill-slug>.bak-YYYYMMDD-HHMMSS`。
   - 将解压后的技能内容复制到目标路径。
   - 确认两个目标目录下都存在 `SKILL.md`、`scripts/smoke_test.py`、`_meta.json`。
8. 持久化宿主配置：
   - 按 `CONFIG_WRITE_METHOD` 写入当前宿主的本机配置。
   - 优先使用宿主官方配置命令；如果不存在，再备份并修改本机配置文件。
   - 不要写入项目仓库。
   - 不要强行套用 `openclaw.json` 或 `skills.entries`；必须沿用 `ENTRY_SCHEMA`。
   - 两个 entry 必须表达出：技能启用、技能路径、技能 slug 或 name，以及对应环境变量。

   `paddleocr-doc-parsing` 至少需要：

   ```json
   {
     "enabled": true,
     "path": "<DOC_SKILL_DIR>",
     "env": {
       "PADDLEOCR_DOC_PARSING_API_URL": "<DOC_PARSING_API_URL>",
       "PADDLEOCR_ACCESS_TOKEN": "<ACCESS_TOKEN>"
     }
   }
   ```

   `paddleocr-text-recognition` 至少需要：

   ```json
   {
     "enabled": true,
     "path": "<OCR_SKILL_DIR>",
     "env": {
       "PADDLEOCR_OCR_API_URL": "<OCR_API_URL>",
       "PADDLEOCR_ACCESS_TOKEN": "<ACCESS_TOKEN>"
     }
   }
   ```

   如果宿主 schema 使用 `environment`、`env_vars`、数组形式、全局 skill env 或其他字段名，请按宿主 schema 转换，不能因为字段名不同就放弃配置。
9. 依赖处理：
   - 这两个技能预期使用 PEP 723 内联依赖，主要依赖 `httpx`。
   - 优先使用 `uv run scripts/smoke_test.py` 自动解析依赖。
   - 如果技能目录意外包含 `requirements.txt`，先按 `SKILL.md` 说明处理；不要改用嵌入式 Python。
   - 不要为了 smoke test 手动把依赖安装进项目仓库。
10. 运行 smoke test：
    - 在实际安装目录中运行，不要使用硬编码路径。
    - 使用当前 shell 的安全方式传入临时环境变量，不要把 Token 写入命令输出或项目文件。

    文档解析技能：

    ```bash
    cd "<DOC_SKILL_DIR>"
    PADDLEOCR_DOC_PARSING_API_URL="<DOC_PARSING_API_URL>" PADDLEOCR_ACCESS_TOKEN="<ACCESS_TOKEN>" uv run scripts/smoke_test.py
    ```

    文字识别技能：

    ```bash
    cd "<OCR_SKILL_DIR>"
    PADDLEOCR_OCR_API_URL="<OCR_API_URL>" PADDLEOCR_ACCESS_TOKEN="<ACCESS_TOKEN>" uv run scripts/smoke_test.py
    ```

    如果当前是 PowerShell、Windows cmd 或其他 shell，请使用等价的临时环境变量写法，但语义必须相同。
11. smoke test 失败时继续修复：
    - `CONFIG_ERROR`：检查环境变量是否注入到当前进程和宿主配置。
    - 401 / 403：检查 PaddleOCR Access Token。
    - 429：检查额度、限流或调用频率，必要时稍后重试一次。
    - endpoint 错误：检查文档解析 API 与文字识别 API 是否填反。
    - `module not found`：检查是否使用 `uv run`，以及 PEP 723 依赖是否被 uv 正确解析。
    - ZIP 或路径错误：重新检查安装目录根部是否直接包含 `SKILL.md` 和 `scripts/`。
12. 验证配置回读：
    - 回读当前宿主配置，确认两个 skill entry 都存在。
    - 确认两个技能都已启用。
    - 确认路径指向实际安装目录。
    - 确认环境变量已经持久化，但不要完整打印 Token。

## 完成报告

最后输出安装报告，必须包含：

- 当前识别到的宿主环境。
- 使用的 `SKILLS_ROOT`。
- 配置写入方式和配置文件路径。
- 两个技能是否安装成功。
- 两个技能的安装路径。
- 两个 `_meta.json` 中的版本号。
- 使用的 `uv` 路径。
- 三个环境变量是否已经持久化：
  - `PADDLEOCR_DOC_PARSING_API_URL`
  - `PADDLEOCR_OCR_API_URL`
  - `PADDLEOCR_ACCESS_TOKEN`
- 两个 smoke test 的结果。
- 后续在 LLM-Wiki ingest 中如何调用：
  - PDF、扫描件、复杂表格、公式、图表和版面结构优先调用 `paddleocr-doc-parsing`。
  - 单张图片或简单文字识别优先调用 `paddleocr-text-recognition`。

## 安全要求

- 不要把 Token 写入项目仓库。
- 不要把 Token 写入会同步到 GitHub 的文件。
- 如果必须持久化配置，只能写入当前宿主的本机配置或用户级本机配置。
- 最终报告不要明文展示完整 Token，只显示前 8 位 + `****` + 后 4 位。
