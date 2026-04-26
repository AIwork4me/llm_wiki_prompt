# 安装 PaddleOCR 技能

请在当前 OpenClaw / AutoClaw 环境中安装 PaddleOCR 技能，并一次性完成下载、安装、配置、依赖处理和验证。

## 输入信息

请把下面占位符替换为真实值后再执行：

- ClawHub Token: `<CLAWHUB_TOKEN>`，必须以 `clh_` 开头
- 文档解析 API: `<DOC_PARSING_API_URL>`，用于 PaddleOCR-VL-1.5 document parsing
- 文字识别 API: `<OCR_API_URL>`，用于 PP-OCRv5 text recognition
- PaddleOCR Access Token: `<ACCESS_TOKEN>`，PaddleOCR 官网访问令牌

## 硬性要求

1. 两个技能只能从 ClawHub 下载。
2. 不要使用 `skills.sh`。
3. 不要使用 GitHub raw download。
4. 不要从 GitHub release、npm、pip 或其他第三方源替代下载技能。
5. 如果 ClawHub 下载失败，必须修复 ClawHub Token、下载 API、网络或 HTTP 错误；不要绕道换源。
6. 不要把任何 Token 或 API 凭据写入项目仓库，也不要写入会同步到 GitHub 的文件。
7. 不要使用 OpenClaw / AutoClaw 自带的嵌入式 Python 来安装依赖；它可能没有 pip。优先使用 `uv run`。

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

## 安装目标路径

解压并安装到 OpenClaw / AutoClaw managed skills 目录：

- `~/.openclaw-autoclaw/skills/paddleocr-doc-parsing/`
- `~/.openclaw-autoclaw/skills/paddleocr-text-recognition/`

不要安装到当前项目仓库，不要把技能文件复制到 LLM-Wiki 仓库。

## 执行步骤

1. 校验输入信息：
   - `<CLAWHUB_TOKEN>` 非空且以 `clh_` 开头。
   - `<DOC_PARSING_API_URL>`、`<OCR_API_URL>`、`<ACCESS_TOKEN>` 均非空。
   - 如果任何值缺失，先要求用户补齐，不要用占位符继续执行。
2. 确认工具和路径：
   - 确认 `uv` 可用。
   - 确认 `gateway` 可用；如果不在 PATH 中，搜索 OpenClaw / AutoClaw 安装目录中的 `gateway` 可执行文件。
   - 如果找不到 `gateway`，停止并报告，不要手动编辑 `openclaw.json`。
   - 解析用户 home 目录，确认 `~/.openclaw-autoclaw/skills/` 可创建或已存在。
3. 使用 ClawHub API 分别下载两个 ZIP：
   - 请求必须带 `Authorization: Bearer <CLAWHUB_TOKEN>`。
   - 保存到临时目录，不要保存到项目仓库。
   - 校验 HTTP status、文件大小和 ZIP 可解压性。
   - 如果返回 401 / 403，检查 ClawHub Token。
   - 如果返回 404，检查 slug 是否完全一致。
   - 如果网络或 5xx 失败，重试一次；仍失败则报告 ClawHub 下载失败，不要切换到其他来源。
4. 解压并标准化目录：
   - 解压到临时目录。
   - 无论 ZIP 是否多包了一层目录，最终安装目录根部都必须直接包含 `SKILL.md`、`scripts/`、`_meta.json`。
   - 读取 `_meta.json`，记录版本号。
5. 安全审查：
   - 阅读两个技能的 `SKILL.md`。
   - 检查 `scripts/` 中会执行的脚本，确认没有删除用户文件、上传本机敏感文件、修改项目仓库或绕过配置的行为。
   - 如发现明显风险，停止安装并报告具体文件和原因。
6. 安装技能：
   - 创建 `~/.openclaw-autoclaw/skills/`。
   - 如果目标技能目录已存在，先备份为同级目录，例如 `<slug>.bak-YYYYMMDD-HHMMSS`。
   - 将解压后的技能内容复制到目标路径。
   - 确认两个目标目录下都存在 `SKILL.md`、`scripts/smoke_test.py`、`_meta.json`。
7. 持久化 OpenClaw / AutoClaw 配置：
   - 必须通过 `gateway config.patch` 写入 `openclaw.json` 的 `skills.entries`。
   - 不要把配置写入项目仓库。
   - 先运行 `gateway config.patch --help` 或读取本机 OpenClaw 文档，确认 `config.patch` 需要的参数格式。
   - 先读取现有 `skills.entries` 结构，再按相同结构更新或新增两个 entry。
   - 配置中的 `path` 优先使用解析后的绝对路径；只有现有配置明确使用 `~` 时，才沿用 `~` 风格。
   - 两个 entry 必须包含 `enabled: true`、技能安装路径，以及下面三个环境变量中对应技能需要的变量。

   `paddleocr-doc-parsing` 至少需要：

   ```json
   {
     "enabled": true,
     "path": "<ABSOLUTE_HOME_PATH>/.openclaw-autoclaw/skills/paddleocr-doc-parsing",
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
     "path": "<ABSOLUTE_HOME_PATH>/.openclaw-autoclaw/skills/paddleocr-text-recognition",
     "env": {
       "PADDLEOCR_OCR_API_URL": "<OCR_API_URL>",
       "PADDLEOCR_ACCESS_TOKEN": "<ACCESS_TOKEN>"
     }
   }
   ```

   如果 OpenClaw / AutoClaw 当前 schema 要求把三个环境变量都放进每个 entry，也可以全部写入，但不得缺少对应技能真正使用的变量。
8. 依赖处理：
   - 这两个技能预期使用 PEP 723 内联依赖，主要依赖 `httpx`。
   - 优先使用 `uv run scripts/smoke_test.py` 自动解析依赖。
   - 如果技能目录意外包含 `requirements.txt`，先按 `SKILL.md` 说明处理；不要改用嵌入式 Python。
   - 不要为了 smoke test 手动把依赖安装进项目仓库。
9. 运行 smoke test：
   - 在每个技能目录中运行。
   - 使用当前 shell 的安全方式传入临时环境变量，不要把 Token 写入命令输出或项目文件。

   文档解析技能：

   ```bash
   cd ~/.openclaw-autoclaw/skills/paddleocr-doc-parsing
   PADDLEOCR_DOC_PARSING_API_URL="<DOC_PARSING_API_URL>" PADDLEOCR_ACCESS_TOKEN="<ACCESS_TOKEN>" uv run scripts/smoke_test.py
   ```

   文字识别技能：

   ```bash
   cd ~/.openclaw-autoclaw/skills/paddleocr-text-recognition
   PADDLEOCR_OCR_API_URL="<OCR_API_URL>" PADDLEOCR_ACCESS_TOKEN="<ACCESS_TOKEN>" uv run scripts/smoke_test.py
   ```

   如果当前是 PowerShell、Windows cmd 或其他 shell，请使用等价的临时环境变量写法，但语义必须相同。
10. smoke test 失败时继续修复：
    - `CONFIG_ERROR`：检查环境变量是否注入到当前进程和 OpenClaw 配置。
    - 401 / 403：检查 PaddleOCR Access Token。
    - 429：检查额度、限流或调用频率，必要时稍后重试一次。
    - endpoint 错误：检查文档解析 API 与文字识别 API 是否填反。
    - `module not found`：检查是否使用 `uv run`，以及 PEP 723 依赖是否被 uv 正确解析。
    - ZIP 或路径错误：重新检查安装目录根部是否直接包含 `SKILL.md` 和 `scripts/`。
11. 验证配置回读：
    - 读取 OpenClaw / AutoClaw 当前配置，确认两个 skill entry 都存在。
    - 确认 `enabled: true`。
    - 确认路径指向 `~/.openclaw-autoclaw/skills/<slug>/`。
    - 确认环境变量已经持久化，但不要完整打印 Token。

## 完成报告

最后输出安装报告，必须包含：

- 两个技能是否安装成功。
- 两个技能的安装路径。
- 两个 `_meta.json` 中的版本号。
- 使用的 `uv` 路径。
- `gateway config.patch` 是否执行成功。
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
- 如果必须持久化配置，只能写入 OpenClaw / AutoClaw 的本机配置，例如 `openclaw.json` 中的 `skills.entries`。
- 最终报告不要明文展示完整 Token，只显示前 8 位 + `****` + 后 4 位。
