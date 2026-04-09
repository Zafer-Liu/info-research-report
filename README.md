# 信息调研报告自动化 (Automated Information Research Report)

一个用于自动生成政府风格调研报告的 OpenClaw Skill。

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