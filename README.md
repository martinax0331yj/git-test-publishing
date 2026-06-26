# 📚 Notion-Zotero Literature Workflow

一个面向论文写作的 Zotero → Notion → Excel / Markdown / Word → GitHub 文献自动化工作流。

这个仓库用于保存文献工作流的输出结果、配置规则、运行日志和写作材料包。实际运行脚本位于 Mac 本地的 Python 工具项目中，仓库则作为可追踪、可归档、可同步到 GitHub 的成果目录。

## 🧭 项目定位

这个工具面向论文写作、文献管理和材料整理，把 Zotero、Notion、Excel、Markdown、Word 和 GitHub 串联成一条本地自动化流程。

它主要用于：

- 从 Zotero 读取 PDF 识别后的文献元数据；
- 将文献信息同步到 Notion 文献数据库；
- 管理翻译、主题、用途、章节、优先级和导出状态；
- 自动翻译缺失的英文标题和摘要；
- 同步翻译状态和处理状态到 Notion；
- 导出 Excel / CSV 文献总览；
- 生成跨笔记 Markdown 材料包；
- 生成 Word 草稿素材包；
- 记录每次运行日志；
- 将 `logs / exports / materials / config` 归档到 GitHub。

## 🏗️ 系统架构

```text
PDF / DOI / Zotero 文献
  ↓
Zotero 解析元数据
  ↓
zotero_to_notion.py
  ↓
Notion Literature Database
  ↓
main.py
  ↓
Excel / CSV / Markdown / Word / Logs
  ↓
GitHub Repository
```

更完整的工作流：

```text
Zotero 文献条目
  ↓
按 to-notion 标签筛选
  ↓
去重后写入 Notion
  ↓
在 Notion 中维护主题、章节、用途和是否导出
  ↓
自动补充缺失翻译并回写状态
  ↓
导出 Excel / CSV
  ↓
生成 Markdown 和 Word 材料包
  ↓
写入本仓库并提交到 GitHub
```

## ✅ 当前功能清单

### 📥 Zotero 入库层

- 从 Zotero Local API 读取文献；
- 支持 PDF 元数据解析后的 Zotero 条目；
- 通过 Zotero 标签 `to-notion` 控制是否导入；
- 根据 `Zotero Key` 和 `DOI` 去重；
- 自动创建 Notion 文献记录。

### 🧠 Notion 管理层

- 维护文献标题、作者、年份、来源、DOI、摘要；
- 维护中文题名、中文摘要；
- 维护主题、用途、对应章节、优先级；
- 使用 `是否导出` 字段控制是否进入材料包；
- 自动同步翻译状态和处理状态。

### 🌐 自动翻译层

- 使用代理 API / Codex API Key；
- 支持 `codex_API_KEY` 和 `codex_BASE_URL`；
- 使用 `TRANSLATION_MODEL=gpt-5.5`；
- 默认使用 `chat.completions` 兼容代理 API；
- 只翻译缺失的中文题名和中文摘要；
- 已有中文内容不会重复翻译；
- 使用 `translation_cache.json` 缓存，避免重复消耗。

### 📊 导出层

- 生成 `exports/literature_notes.xlsx`；
- 生成 `exports/literature_notes.csv`；
- 只导出 Notion 中 `是否导出` 被勾选的文献。

### 📝 材料包层

- 生成全部文献材料包；
- 按主题生成材料包；
- 按章节生成材料包；
- 生成“第二章文献综述材料包”；
- 生成“理论基础材料包”；
- 按论文大纲和优先级排序。

### 📄 Word 草稿层

- 生成 `全部文献草稿素材包.docx`；
- 生成 `第二章文献综述材料包.docx`；
- 生成 `理论基础材料包.docx`；
- 用于后续论文写作素材整理。

### 🧾 日志与 GitHub 层

- 每次运行生成 Markdown 日志；
- 每次运行生成 JSONL 日志；
- 自动提交 `logs / exports / materials / config`；
- 自动尝试 `git push`；
- 如果 push 失败，保留本地文件，网络恢复后可以手动 push。

## 🗂️ 目录结构

GitHub 仓库结构：

```text
github仓库文件/
├── README.md
├── .gitignore
├── config/
│   ├── topics.yaml
│   └── outline_order.yaml
├── logs/
│   ├── sync_YYYY-MM-DD.md
│   └── sync_YYYY-MM-DD.jsonl
├── exports/
│   ├── literature_notes.xlsx
│   ├── literature_notes.csv
│   └── translation_cache.json
└── materials/
    ├── all_material_pack.md
    ├── by_topic/
    ├── by_chapter/
    ├── special/
    │   ├── 第二章文献综述材料包.md
    │   └── 理论基础材料包.md
    └── word/
        ├── 全部文献草稿素材包.docx
        ├── 第二章文献综述材料包.docx
        └── 理论基础材料包.docx
```

Python 工具项目结构：

```text
notion-literature-tool/
├── .env
├── run_sync.sh
├── src/
│   ├── main.py
│   ├── zotero_to_notion.py
│   ├── notion_client.py
│   ├── translate_text.py
│   ├── export_excel.py
│   ├── material_pack.py
│   ├── word_export.py
│   ├── github_sync.py
│   └── logger.py
```

## 🧱 Notion 数据库字段

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| 标题 / Name | Title | 文献英文标题或主标题 |
| Zotero Key | Text | Zotero 唯一识别码 |
| DOI | Text | DOI |
| 作者 | Text | 作者信息 |
| 年份 | Text / Number | 出版年份 |
| 来源 | Text | 期刊、会议或出版物 |
| Abstract | Text | 英文摘要 |
| 中文题名 | Text | 自动或手动翻译的中文标题 |
| 中文摘要 | Text | 自动或手动翻译的中文摘要 |
| 主题 | Multi-select / Text | 文献主题 |
| 用途 | Select / Multi-select / Text | 文献综述、理论基础、研究方法等 |
| 对应章节 | Select / Text | 如“第二章 文献综述” |
| 优先级 | Select | A / B / C |
| 是否导出 | Checkbox | 是否进入 Excel、材料包和 Word |
| 翻译状态 | Select / Status | 待翻译、部分已翻译、已翻译 |
| 处理状态 | Select / Status | 已同步、已导出、已生成材料包 |

## ⚙️ 环境变量

`.env` 文件位于本地 Python 工具项目中：

```bash
/Users/xueyeye/Documents/notion-literature-tool/.env
```

示例：

```env
NOTION_TOKEN=你的_Notion_访问令牌
NOTION_DATABASE_ID=你的_Notion_数据库_ID
GITHUB_REPO_PATH=/Users/xueyeye/Desktop/github仓库文件

ZOTERO_API_BASE=http://127.0.0.1:23119/api/users/0
ZOTERO_TAG_FILTER=to-notion

codex_API_KEY=你的代理APIKey
codex_BASE_URL=https://你的代理API域名/v1
TRANSLATION_MODEL=gpt-5.5
TRANSLATION_API_MODE=chat
```

⚠️ `.env` 文件包含密钥，不应提交到 GitHub。

## 🚀 安装与依赖

在 Mac 本地初始化 Python 环境：

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
python3 -m venv .venv
source .venv/bin/activate
pip install requests python-dotenv pandas openpyxl python-docx openai
```

## 🧪 使用步骤

### Step 1：Zotero 导入 PDF

```text
1. 打开 Zotero 桌面端
2. 拖入 PDF
3. 右键 PDF → Retrieve Metadata for PDF
4. 给需要进入 Notion 的文献添加标签：to-notion
```

### Step 2：同步 Zotero 到 Notion

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python src/zotero_to_notion.py
```

### Step 3：整理 Notion 字段

在 Notion 中补充或确认：

- 中文题名；
- 中文摘要；
- 主题；
- 用途；
- 对应章节；
- 优先级；
- 是否导出。

如果启用自动翻译，中文题名和中文摘要可以由脚本自动补充。

### Step 4：运行完整工作流

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python src/main.py
```

### Step 5：打开结果

```bash
open "/Users/xueyeye/Desktop/github仓库文件/exports/literature_notes.xlsx"
open "/Users/xueyeye/Desktop/github仓库文件/materials"
open "/Users/xueyeye/Desktop/github仓库文件/materials/word"
```

### Step 6：一键运行

也可以直接双击桌面的一键运行工具：

```text
Notion文献同步.command
```

## 📦 输出文件说明

| 输出 | 路径 | 作用 |
| --- | --- | --- |
| Excel 文献总览 | `exports/literature_notes.xlsx` | 用于查看和筛选文献 |
| CSV 文献数据 | `exports/literature_notes.csv` | 用于数据处理 |
| 全部材料包 | `materials/all_material_pack.md` | 全部勾选文献的 Markdown 材料 |
| 主题材料包 | `materials/by_topic/` | 按主题拆分 |
| 章节材料包 | `materials/by_chapter/` | 按章节拆分 |
| 第二章材料包 | `materials/special/第二章文献综述材料包.md` | 文献综述写作 |
| 理论基础材料包 | `materials/special/理论基础材料包.md` | 理论基础写作 |
| Word 草稿素材包 | `materials/word/*.docx` | 用于论文写作草稿 |
| 运行日志 | `logs/*.md / logs/*.jsonl` | 每次运行记录 |

## ❓ 常见问题

### Q1：GitHub push 失败怎么办？

如果出现：

```text
Could not resolve host: github.com
Empty reply from server
```

通常是网络或 DNS 问题，不影响本地生成文件。网络恢复后执行：

```bash
cd "/Users/xueyeye/Desktop/github仓库文件"
git push
```

### Q2：为什么导出数量是 0？

请检查 Notion 中是否勾选了 `是否导出`。只有被勾选的文献才会进入 Excel、Markdown 材料包和 Word 草稿素材包。

### Q3：为什么 Zotero 没有导入？

请检查：

- Zotero 桌面端是否已打开；
- PDF 是否已识别元数据；
- 文献是否已添加 `to-notion` 标签；
- Zotero Local API 是否可访问。

测试本地 API：

```bash
curl "http://127.0.0.1:23119/api/users/0/items?limit=3"
```

### Q4：为什么自动翻译不工作？

请检查 `.env` 中是否配置：

```env
codex_API_KEY=
codex_BASE_URL=
TRANSLATION_MODEL=gpt-5.5
TRANSLATION_API_MODE=chat
```

同时确认代理 API 支持当前模型名，并且中转域名以 `/v1` 结尾。

### Q5：为什么 Word 或材料包为空？

请检查：

- `是否导出` 是否勾选；
- `对应章节` 是否填写；
- `主题` 或 `用途` 是否填写；
- 第二章材料包依赖“第二章 / 文献综述”等关键词；
- 理论基础材料包依赖“理论基础 / 理论 / 模型 / 框架”等关键词。

## 🔐 安全说明

- 不要提交 `.env`；
- 不要把 Notion Token、API Key、代理 API Key 发到 GitHub；
- `.DS_Store` 不应提交；
- 如果 key 泄露，要立刻重置；
- README 中只保留示例变量，不写真实密钥或真实中转地址。

## 🛣️ 后续计划

- 自动按论文大纲生成章节草稿；
- 支持引用格式导出；
- 支持 GB/T 7714 引文辅助；
- 支持 Zotero PDF 批注同步；
- 支持 Notion 中“引用次数”统计；
- 支持按研究问题生成专题材料包。
