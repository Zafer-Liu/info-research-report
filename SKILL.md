---
name: info-research-report
description: 信息调研报告自动化工作流。一键完成：多源搜索 → 深度挖掘 → 政府风格 DOCX 报告生成。
metadata:
  openclaw:
    command-dispatch: tool
    command-tool: exec
  requirements:
    python_packages:
      - python-docx
      - requests
      - beautifulsoup4
    external_tools:
      - mcporter (可选/Optional)
      - browseros-mcp (可选/内置)
  environment_variables:
    - MINIMAX_API_KEY (可选/Optional)
    - OPENAI_API_KEY (可选/Optional)
  permissions:
    - read_files
    - execute_scripts
    - network_access
---

# 信息调研报告自动化（政府标准版）

本 skill 用于自动生成"政府 / 机关单位风格"的调研报告：

- 封面（研究报告 + 主题 + 编制单位 + 编制时间）
- 报告说明 / 元信息
- 一、研究背景和目的
- 二、研究方法
- 三、总体形势与关键结论
- 四、分专题分析（按来源类别）
- 五、详细来源分析
- 六、参考资料

---

## 工作流程

### 步骤1：搜索主题

使用 browseros 或其他工具搜索，提取结果保存为 `results.json`：

```bash
mcporter call browseros.new_page url="https://duckduckgo.com/html/?q=你的主题"
mcporter call browseros.get_page_content -- page=1
```

### 步骤2：准备 results.json

```json
[
  {
    "title": "结果标题",
    "url": "https://example.com/xxx",
    "content": "（可选）网页完整正文"
  }
]
```

### 步骤3：生成报告

```bash
python run.py "主题" [results.json] [--no-fetch]
```

- `--no-fetch`：跳过网页抓取，速度更快

---

## 参数

| 参数 | 说明 |
|------|------|
| 主题 | 研究报告的主题（必填） |
| results.json | 搜索结果文件（可选） |
| --no-fetch | 跳过网页抓取 |

---

## 输出

- DOCX 报告文件，保存在当前目录

---

## 依赖

- Python: python-docx, requests, beautifulsoup4
- MCP: browseros-mcp（内置，可选）
- 权限: read_files, execute_scripts, network_access

### 安装依赖

```bash
pip install -r requirements.txt
```

---

## 网页抓取优先级

1. **本地 browseros-mcp.sh**（内置）✅
2. **mcporter call browseros**
3. **requests + BeautifulSoup**（无 MCP 也能用）

如不启动 MCP 服务，程序会自动 fallback 到 requests 方式抓取网页。

---

## 环境变量（可选）

- `MINIMAX_API_KEY` - MiniMax API Key（用于生成摘要）
- `OPENAI_API_KEY` - OpenAI API Key（备用）

> ⚠️ 本 skill 目录已包含 `.env` 文件，内置 MiniMax API Key。

---

## 示例

```bash
# 生成报告
python run.py "人工智能发展趋势" "results.json"
```

运行后当前目录生成：`Report_人工智能发展趋势_202604091200.docx`