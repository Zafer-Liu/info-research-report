# 信息调研报告自动化（Automated Information Research Report）

> 一个用于自动生成“政府/机关风格”调研报告的 OpenClaw Skill。  
> 输入研究主题与检索结果，自动输出结构化 DOCX 报告。

---

## ✨ 功能特性

- 一键生成规范化调研报告（DOCX）
- 自动调用 LLM 生成摘要（背景 / 趋势 / 风险）
- 支持网页内容抓取与解析
- 支持 MCP 抓取 + 本地 fallback（requests + BeautifulSoup）
- 可使用已有 `results.json` 跳过在线抓取

---

## 📁 项目结构

```text
info-research-report/
├── run.py                     # 主入口脚本
├── requirements.txt           # Python 依赖
├── .env                       # 环境变量配置（本地）
├── browseros-mcp/             # 内置 MCP 服务（可选）
│   ├── package.json
│   ├── server.js
│   └── ...
├── src/
│   ├── __init__.py
│   ├── fetcher.py             # 网页内容抓取（支持 MCP fallback）
│   ├── parser.py              # 搜索结果解析
│   ├── report_generator.py    # DOCX 报告生成
│   ├── llm_client.py          # LLM API 调用封装
│   └── templates/
│       └── default.docx       # 报告模板
├── results/
│   └── results.json           # 示例/缓存检索结果
└── output/                    # 报告输出目录
```

---

## 🚀 快速开始

### 1) 安装依赖

```bash
pip install -r requirements.txt
```

### 2) 配置环境变量（可选但推荐）

创建 `.env`（或导出系统环境变量）：

```env
MINIMAX_API_KEY=your_minimax_key
OPENAI_API_KEY=your_openai_key
```

> 至少配置一个可用的 LLM Key，用于生成摘要内容。

### 3) 准备检索结果（两种方式）

#### 方式 A：使用 MCP 抓取（可选）
```bash
mcporter call browseros.new_page url="https://duckduckgo.com/html/?q=你的主题"
mcporter call browseros.get_page_content -- page=1
```

#### 方式 B：手动准备 `results.json`（推荐先跑通）
```json
[
  {
    "title": "结果标题",
    "url": "https://example.com",
    "content": "（可选）网页正文"
  }
]
```

### 4) 生成报告

```bash
python run.py "主题" [results.json] [--no-fetch]
```

示例：
```bash
python run.py "低空经济发展现状与趋势" results/results.json --no-fetch
```

---

## 🧾 命令参数

| 参数 | 必填 | 说明 |
|------|------|------|
| 主题 | 是 | 报告研究主题 |
| results.json | 否 | 检索结果文件路径 |
| --no-fetch | 否 | 跳过网页抓取（更快，适合已有 content） |

---

## 📤 输出结果

- 生成的 `.docx` 报告默认输出到 `output/`（或当前目录，视你的实现而定）
- 报告建议包含以下结构：

1. 封面  
2. 报告说明  
3. 研究背景与目的  
4. 研究方法  
5. 总体形势与关键结论  
6. 分专题分析（按来源类别）  
7. 详细来源分析  
8. 参考资料

---

## 🧠 工作流程（简述）

1. 读取主题与检索结果  
2. 按需抓取网页正文（MCP 或 fallback）  
3. 解析并结构化信息  
4. 调用 LLM 生成核心摘要  
5. 套用模板输出 DOCX

---

## 🔌 MCP 使用说明（可选）

如需启用内置 `browseros-mcp`：

```bash
cd browseros-mcp
npm install
node server.js
```

若 MCP 未启动，程序会自动使用 `requests + BeautifulSoup` 进行 fallback 抓取。

---

## 🛠 依赖与环境

- Python 3.9+（建议）
- Python packages: `python-docx`, `requests`, `beautifulsoup4` 等
- 可选组件：
  - `browseros-mcp/`（本地 MCP 服务）
  - `mcporter`（MCP 调用工具）
- 运行权限：`read_files`, `execute_scripts`, `network_access`

---

## ✅ 常见问题（FAQ）

### 1. 不配置 API Key 能运行吗？
可以运行基础流程，但摘要生成质量/可用性会受限（取决于你的降级逻辑）。

### 2. 抓取失败怎么办？
- 先使用 `--no-fetch` + 完整 `content` 的 `results.json`
- 检查网络与目标站点反爬策略
- 启用 MCP 方式抓取

### 3. 输出目录是哪里？
默认建议为 `output/`。如与你代码实际不一致，请以 `run.py` 实现为准。

---

## 🔒 安全与合规建议

- 不要提交 `.env`、API Key、临时抓取数据到仓库
- 建议在 `.gitignore` 中忽略：
  - `.env`
  - `__pycache__/`
  - `output/`（若为运行产物）
  - `results/`（若包含临时数据）

---

## 📄 License

CPL
```
