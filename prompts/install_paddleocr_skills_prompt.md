请帮我安装 PaddleOCR 技能，并一次性完成安装、配置、依赖处理和验证。

## 信息

- ClawHub Token: [填入你的 ClawHub Token，以 clh_ 开头]
- 文档解析 API: [填入你的 PaddleOCR-VL-1.5 API_URL, 用于document parsing]
- 文字识别 API: [填入你的 PP-OCRv5 API_URL，用于text recognition]
- PaddleOCR Access Token: [填入你的 PaddleOCR 官网Token，访问令牌]

## 目标

为当前 AutoClaw 环境安装并启用两个技能：

1. paddleocr-doc-parsing
2. paddleocr-text-recognition

安装完成后，这两个技能必须能被后续 LLM-Wiki ingest 流程调用，用于高精度解析 PDF、图片、扫描件、复杂表格、公式、图表和版面结构。

## 严格流程

1. 使用 ClawHub Token 登录 ClawHub。
2. 搜索 PaddleOCR。
3. 安装文档解析技能 paddleocr-doc-parsing。
4. 安装文字识别技能 paddleocr-text-recognition。
5. 检查两个技能的 SKILL.md、scripts 目录、requirements.txt 或依赖声明。
6. 使用 uv 创建或复用独立 venv。注意：不要使用 AutoClaw 自带的嵌入式 Python，它可能没有 pip。
7. 如果技能目录存在 requirements.txt，则用 venv 的 Python 安装依赖。
8. 如果技能使用 PEP 723 或 uv run 声明依赖，则按技能说明用 uv 自动解析依赖。
9. 修改或适配技能 SKILL.md 中的 Python 调用方式，确保所有 python 命令都指向可用的 venv Python 或 uv run。
10. 持久化写入 API 配置，确保 AutoClaw 重启后仍可用：
    - PADDLEOCR_DOC_PARSING_API_URL
    - PADDLEOCR_OCR_API_URL
    - PADDLEOCR_ACCESS_TOKEN
11. 检查 AutoClaw / OpenClaw 的 skills 配置，确保两个技能 enabled=true。
12. 分别运行两个技能的 smoke_test.py：
    - paddleocr-doc-parsing/scripts/smoke_test.py
    - paddleocr-text-recognition/scripts/smoke_test.py
13. 如果 smoke test 失败，请根据错误类型继续修复：
    - CONFIG_ERROR：检查环境变量是否写入成功
    - 401 或 403：检查 PaddleOCR Access Token
    - 429：检查免费额度、限流或调用频率
    - endpoint 错误：检查文档解析 API 和文字识别 API 是否填反
    - module not found：检查 venv 和依赖安装路径
14. 最后输出安装报告，报告必须包含：
    - 两个技能是否安装成功
    - 技能安装路径
    - 使用的 Python / uv 路径
    - 三个环境变量是否已经持久化
    - 两个 smoke test 的结果
    - 后续在 LLM-Wiki 中如何调用

## 安全要求

- 不要把 Token 写入项目仓库。
- 不要把 Token 写入会同步到 GitHub 的文件。
- 如果必须写配置，请写入 AutoClaw / OpenClaw 的本机配置文件或用户级环境变量。
- 最终报告不要明文展示完整 Access Token，只显示前后少量字符用于确认。