# 信息调研报告自动化 (Automated Information Research Report)

一个用于自动生成政府风格调研报告的 OpenClaw Skill。

## 项目架构

```
info-research-report/
├── run.py                    # 主入口脚本
├── requirements.txt          # Python 依赖
├── .env                      # 环境变量配置
├── browseros-mcp/           # 内置 MCP 服务（可选）
│   ├── package.json
│   ├── server.js
│   └── ...
├── src/
│   ├── __init__.py
│   ├── fetcher.py           # 网页内容获取（支持 MCP fallback）
│   ├── parser.py            # 搜索结果解析
│   ├── report_generator.py  # DOCX 报告生成
│   ├── llm_client.py        # LLM API 调用封装
│   └── templates/           # 报告模板
│       └── default.docx
├── results/                  # 搜索结果目录
│   └── results.json
└── output/                  # 生成的报告输出目录
```

### 核心模块说明

| 模块 | 功能 |
|------|------|
| run.py | 命令行入口，参数解析和流程控制 |
| fetcher.py | 网页抓取，优先使用 MCP，失败时 fallback 到 requests |
| parser.py | 解析搜索结果为结构化 JSON |
| report_generator.py | 生成政府风格 DOCX 报告 |
| llm_client.py | 调用 MiniMax/OpenAI 生成摘要 |

## 功能

- 一键生成政府/机关风格的 DOCX 调研报告
- 自动调用 LLM 生成摘要（背景/趋势/风险三部分）
- 支持网页内容抓取和分析
- 自动 fallback：无 MCP 也能用

## 安装

### 依赖安装

```bash
pip install -r requirements.txt
```

### 环境变量（可选）

- `MINIMAX_API_KEY` - MiniMax API Key（用于生成摘要）
- `OPENAI_API_KEY` - OpenAI API Key（备用）

### 使用方式

1. **搜索主题**（或自行准备 results.json）
```bash
mcporter call browseros.new_page url="https://duckduckgo.com/html/?q=你的主题"
mcporter call browseros.get_page_content -- page=1
```

2. **提取结果保存为 results.json**
```json
[
  {
    "title": "结果标题",
    "url": "https://example.com",
    "content": "（可选）网页正文"
  }
]
```

3. **生成报告**
```bash
python run.py "主题" [results.json] [--no-fetch]
```

## 参数

| 参数 | 说明 |
|------|------|
| 主题 | 研究报告的主题（必填） |
| results.json | 搜索结果文件（可选） |
| --no-fetch | 跳过网页抓取，速度更快 |

## 输出

- DOCX 报告文件，保存在当前目录

## 报告结构

1. 封面
2. 报告说明
3. 研究背景和目的
4. 研究方法
5. 总体形势与关键结论
6. 分专题分析（按来源类别）
7. 详细来源分析
8. 参考资料

## 依赖

- Python: python-docx, requests, beautifulsoup4
- 本地 MCP: browseros-mcp/（已内置，可选）
- 外部工具: mcporter（可选）
- 权限: read_files, execute_scripts, network_access

## 启动 MCP 服务（可选）

内置了 browseros-mcp，如需使用网页抓取功能：

```bash
cd browseros-mcp
npm install
node server.js
```

如不启动 MCP，程序会自动使用 requests + BeautifulSoup fallback。

## License

MIT-0