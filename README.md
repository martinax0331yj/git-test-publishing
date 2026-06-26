# 📚 Notion-Zotero Literature Workflow

一个面向论文写作的 Zotero → Notion → Excel / Markdown / Word → GitHub 文献自动化工作流。

这个仓库保存文献工作流的输出结果、运行日志、材料包和配置文件。实际 Python 工具项目运行在 Mac 本地，输出内容会写入本 GitHub 仓库，用于归档、版本追踪和后续论文写作。

## 🧭 项目简介

这个工具用于把论文写作中的文献管理、笔记整理、翻译、材料包生成和 GitHub 归档串成一条稳定流程：

- 从 Zotero 读取打了 `to-notion` 标签的文献；
- 同步文献元数据到 Notion 数据库；
- 在 Notion 中维护章节、主题、用途、优先级、正文笔记和评论；
- 翻译中文题名、中文摘要，并写回 Notion 字段；
- 读取 Notion 数据库属性、页面正文 blocks、页面评论 comments；
- 统一生成 Excel / CSV / Markdown / Word 材料包；
- 生成 APA / GB 引用和参考文献包；
- 写入本仓库并尝试提交、push 到 GitHub。

## 🔁 工作流总览

```text
PDF / DOI / Zotero 文献
  ↓
Zotero 解析元数据
  ↓
Zotero 标签 to-notion 筛选
  ↓
Python 同步到 Notion 文献数据库
  ↓
Notion 数据库字段 + 页面正文 blocks + 页面评论 comments
  ↓
完整文献材料对象
  ↓
Excel / CSV / Markdown / Word / 引用包 / 日志
  ↓
GitHub 输出仓库归档
```

新版核心逻辑：

```text
Notion 数据库字段
+
Notion 页面正文 blocks
+
Notion 页面评论 comments
↓
完整文献材料对象
↓
Markdown / Word / Excel
```

## 🏗️ 系统架构

```text
notion-literature-tool/
  ├── zotero_to_notion.py
  ├── diagnose_notion_structure.py
  ├── backfill_translation_fields.py
  ├── main.py
  ├── citation_export.py
  └── 输出到 GitHub 仓库

github仓库文件/
  ├── exports/
  ├── materials/
  ├── logs/
  └── config/
```

## ✅ 当前功能清单

| 模块 | 功能 |
| --- | --- |
| 📥 Zotero 入库 | 从 Zotero Local API 读取文献，按 `to-notion` 标签筛选，按 Zotero Key / DOI / 标题去重 |
| 🧠 Notion 管理 | 维护标题、作者、年份、来源、摘要、章节、主题、用途、优先级、阅读状态 |
| 🌐 字段翻译 | 翻译 `中文题名`、`中文摘要`，并写回 Notion 字段 |
| 📝 页面正文读取 | 读取 Notion 页面正文 blocks，递归读取子 blocks |
| 💬 评论读取 | 尝试读取 Notion 页面评论，权限不足时不中断 |
| 📊 Excel / CSV | 导出文献字段、正文读取状态、评论数量、翻译状态 |
| 📦 Markdown 材料包 | 生成全部、文献综述、理论基础、主题、章节材料包 |
| 📄 Word 材料包 | 生成全部文献、文献综述、理论基础三类核心 Word 包 |
| 📖 引用包 | 生成 APA / GB 引用字段和参考文献 Word 包 |
| 🧾 日志与 GitHub | 生成 Markdown / JSONL 日志，自动尝试 commit / push |

## 🧱 Notion 数据库字段说明

### 1. 基础元数据字段

| 字段名 | 类型建议 | 来源 | 用途 |
| --- | --- | --- | --- |
| `Name` | Title | Zotero | 文献主标题 |
| `中文题名` | Text / Rich text | 自动翻译 / 手动修改 | 中文标题 |
| `作者` | Text / Rich text | Zotero | 作者 |
| `年份` | Number / Text | Zotero | 出版年份 |
| `来源` | Text / Rich text | Zotero | 期刊、会议、出版社 |
| `DOI` | Text / Rich text | Zotero | 去重与引用 |
| `Zotero Key` | Text / Rich text | Zotero | 无 DOI 文献去重 |
| `Abstract` | Text / Rich text | Zotero | 原文摘要 |
| `中文摘要` | Text / Rich text | 自动翻译 / 手动修改 | 中文摘要 |
| `URL` | URL | Zotero | 原文链接 |

### 2. 写作管理字段

| 字段名 | 类型建议 | 推荐值 | 用途 |
| --- | --- | --- | --- |
| `是否导出` | Checkbox | 勾选 / 不勾选 | 控制是否进入材料包 |
| `对应章节` | Select / Multi-select | 文献综述、理论基础、研究方法、研究结果、讨论、结论 | 控制章节材料包 |
| `用途` | Multi-select | 文献综述、理论基础、概念定义、变量测量、机制解释、研究方法、案例支撑、反驳观点 | 控制文献用途 |
| `主题` | Multi-select | 品牌资产、国际传播、编辑团队、期刊影响力、AI审稿等 | 控制主题材料包 |
| `优先级` | Select | A、B、C | 控制排序 |
| `阅读状态` | Select | 待读、粗读、精读、已整理 | 管理阅读进度 |
| `翻译状态` | Select / Status | 待翻译、部分已翻译、已翻译 | 管理翻译进度 |
| `处理状态` | Select / Status | 已从 Zotero 导入、已同步、已生成材料包 | 管理自动化状态 |

### 3. 写作内容字段

| 字段名 | 类型建议 | 用途 |
| --- | --- | --- |
| `核心观点` | Text / Rich text | 记录文献核心观点 |
| `可支持论点` | Text / Rich text | 记录可用于论文中的哪个论点 |
| `可引用句` | Text / Rich text | 存放可转述或引用的关键句 |
| `我的评价` | Text / Rich text | 记录个人判断、批判、启发 |
| `适用段落` | Text / Multi-select | 标记可用于论文哪个段落 |
| `引用备注` | Text / Rich text | 记录引用页码、引用注意事项 |

### 4. 引用字段

| 字段名 | 类型建议 | 用途 |
| --- | --- | --- |
| `APA引用` | Text / Rich text | 自动生成 APA 引用 |
| `GB引用` | Text / Rich text | 自动生成 GB/T 7714 引用 |

## 📝 Notion 页面正文模板

建议每篇文献页面正文使用下面结构：

```markdown
## 一、文献核心内容

## 二、可用于我的论文的位置

## 三、理论与概念

## 四、方法与测量

## 五、我的评价

## 六、可引用材料

## 七、临时笔记
```

说明：

- 这些内容会被脚本读取为页面正文；
- 页面正文会进入 Markdown / Word 材料包；
- 评论如果有权限，也会进入材料包；
- 字段负责分类，正文负责内容。

## 🧩 页面正文与评论导出

### 页面正文 blocks

工具会读取 Notion 页面正文，并转换为 Markdown 和纯文本。支持：

- `heading_1` / `heading_2` / `heading_3`
- `paragraph`
- `bulleted_list_item`
- `numbered_list_item`
- `to_do`
- `quote`
- `callout`
- `toggle`
- 子 blocks 递归读取

如果某篇文献页面正文为空，脚本不会中断，只会在日志或 Excel 检查列中体现。

### 页面评论 comments

工具会尝试读取 Notion 页面评论。

⚠️ 如需导出评论，需要在 Notion Integration 中开启 `Read comments` 权限。  
如果权限不足，评论读取会失败，但主流程不会中断。

## 🌐 自动翻译说明

### 字段翻译回写

中文题名和中文摘要翻译是一个独立步骤：

```bash
python src/backfill_translation_fields.py
```

功能：

- 批量检查 `中文题名` 是否为空；
- 批量检查 `中文摘要` 是否为空；
- 如果为空，则调用代理 API / Codex API 翻译；
- 翻译后写回 Notion 界面；
- 更新 `翻译状态`；
- 使用 `translation_cache.json` 缓存，避免重复消耗；
- 不覆盖已有中文题名和中文摘要。

### Notion 正文翻译

数据库字段翻译和页面正文翻译是两件事：

- `中文题名`、`中文摘要` 会回写 Notion 字段；
- 页面正文翻译建议不覆盖 Notion 原文，而是进入导出的 Markdown / Word；
- 如果正文翻译功能已接入，材料包会出现“Notion 正文笔记（原文）”和“Notion 正文笔记（中文翻译）”；
- 如果正文翻译未接入，材料包只显示原文正文。

## 📦 Markdown / Word 统一材料包

核心 Markdown 与核心 Word 使用同一批完整文献材料对象。

| 材料包类型 | Markdown 输出 | Word 输出 |
| --- | --- | --- |
| 全部文献材料包 | `materials/all_material_pack.md` | `materials/word/全部文献材料包.docx` |
| 文献综述材料包 | `materials/special/文献综述材料包.md` | `materials/word/文献综述材料包.docx` |
| 理论基础材料包 | `materials/special/理论基础材料包.md` | `materials/word/理论基础材料包.docx` |
| 按主题材料包 | `materials/by_topic/主题名.md` | 后续可扩展 |
| 按章节材料包 | `materials/by_chapter/章节名.md` | 后续可扩展 |

每篇文献在材料包中尽量保持同一结构：

```text
文献标题
1. 基本信息
2. 分类信息
3. 摘要
4. Notion 正文笔记（原文）
5. Notion 正文笔记（中文翻译，如已启用）
6. Notion 评论
7. 可支持论点
8. 我的评价
9. Notion 链接
```

| 模块 | 内容来源 |
| --- | --- |
| 文献标题 | `Name` + `中文题名` |
| 基本信息 | 作者、年份、来源、DOI、URL |
| 分类信息 | 主题、用途、对应章节、优先级 |
| 摘要 | Abstract + 中文摘要 |
| Notion 正文笔记 | 页面正文 blocks |
| Notion 正文中文翻译 | 页面正文翻译结果，如果已启用 |
| Notion 评论 | 页面评论 / 块级评论 |
| 可支持论点 | `可支持论点` 字段 |
| 我的评价 | `我的评价` 字段 |
| Notion 链接 | Notion page url |

## 🎯 材料包筛选规则

| 材料包 | 进入条件 |
| --- | --- |
| 全部文献材料包 | `是否导出 = 是` |
| 文献综述材料包 | `是否导出 = 是` 且 `对应章节` 包含 `文献综述`，或 `用途` 包含 `文献综述` |
| 理论基础材料包 | `是否导出 = 是` 且 `对应章节` 包含 `理论基础`，或 `用途` 包含 `理论基础 / 理论 / 模型 / 框架 / 机制` |
| 主题材料包 | `是否导出 = 是` 且 `主题` 包含对应主题 |
| 章节材料包 | `是否导出 = 是` 且 `对应章节` 包含对应章节 |

⚠️ 页面正文里写“这篇文献可用于文献综述”不一定会进入文献综述材料包。  
必须在数据库字段中填写 `对应章节=文献综述` 或 `用途=文献综述`。

## 📖 自动生成 APA / GB 引用与参考文献包

新增或建议新增字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| `APA引用` | Text / Rich text | 自动生成 APA 格式参考文献 |
| `GB引用` | Text / Rich text | 自动生成 GB/T 7714 风格参考文献 |

涉及脚本：

```bash
python src/citation_export.py
```

功能：

- 根据 Notion 中的作者、年份、标题、来源、DOI、URL 生成 APA 引用；
- 生成 GB/T 7714 风格引用；
- 回写到 Notion 的 `APA引用` 和 `GB引用` 字段；
- 生成 Word 文件：`materials/word/参考文献包.docx`。

限制：

- 当前是基础引用格式；
- 如果要投稿级准确，需要补充卷、期、页码、文献类型、出版地、出版社等字段；
- 当前默认期刊论文可按 `[J]` 处理，后续可扩展 `文献类型` 字段。

## 📊 Excel 新增检查列

Excel / CSV 中包含或建议包含这些检查列：

| 字段 | 作用 |
| --- | --- |
| `block_count` | 页面正文块数量 |
| `comment_count` | 评论数量 |
| `page_body_preview` | 正文前 300 字预览 |
| `page_body_zh_preview` | 正文中文翻译预览 |
| `has_page_body` | 是否读取到页面正文 |
| `has_comments` | 是否读取到评论 |
| `has_page_body_translation` | 是否生成正文翻译 |

这些字段用于排查为什么某篇文献没有进入材料包、没有正文内容，或者评论没有被导出。

## 🗂️ 目录结构

GitHub 输出仓库：

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
    │   ├── 文献综述材料包.md
    │   └── 理论基础材料包.md
    └── word/
        ├── 全部文献材料包.docx
        ├── 文献综述材料包.docx
        ├── 理论基础材料包.docx
        └── 参考文献包.docx
```

Python 工具项目：

```text
notion-literature-tool/
├── .env
├── run_sync.sh
├── src/
│   ├── zotero_to_notion.py
│   ├── diagnose_notion_structure.py
│   ├── backfill_translation_fields.py
│   ├── translate_text.py
│   ├── main.py
│   ├── citation_export.py
│   ├── material_pack.py
│   ├── word_export.py
│   ├── export_excel.py
│   ├── github_sync.py
│   └── logger.py
```

## 🖱️ 桌面一键运行：不用每次敲终端

打 `to-notion` 标签不会自动导入。标签只是筛选条件，真正触发导入的是运行脚本或双击 `.command` 文件。

### 当前建议保留的桌面按钮

| 桌面按钮 | 对应脚本 | 功能 |
| --- | --- | --- |
| `01-Zotero导入Notion.command` | `run_zotero_import.sh` | 只导入 Zotero 中打了 `to-notion` 标签的文献 |
| `02-Notion结构诊断.command` | `run_diagnose_notion.sh` | 检查 Notion 字段、页面正文 blocks、评论权限 |
| `03-翻译功能测试.command` | `run_translation_test.sh` | 测试代理 API / Codex API 是否能正常翻译 |
| `04-Notion文献完整同步.command` | `run_full_sync.sh` | 导入、翻译、生成 Excel / MD / Word / 引用包、GitHub 归档 |
| `05-打开输出结果.command` | `run_open_outputs.sh` | 一键打开 Excel、Markdown、Word 输出目录 |

### 每个按钮的使用场景

| 场景 | 用户操作 | 双击哪个按钮 |
| --- | --- | --- |
| 新增 Zotero 文献 | 给文献主条目打 `to-notion` 标签 | `01-Zotero导入Notion.command` |
| 检查 Notion 配置 | 改了字段、评论权限或页面正文结构 | `02-Notion结构诊断.command` |
| 检查翻译 API | 换了代理 API、模型或 Key | `03-翻译功能测试.command` |
| 生成完整材料包 | Notion 已经整理好字段和正文 | `04-Notion文献完整同步.command` |
| 查看结果 | 想打开 Excel / MD / Word 输出目录 | `05-打开输出结果.command` |

### 一次性创建所有桌面按钮

```bash
cd /Users/xueyeye/Documents/notion-literature-tool

cat > run_zotero_import.sh <<'EOF'
#!/bin/bash
cd "/Users/xueyeye/Documents/notion-literature-tool" || exit 1
source ".venv/bin/activate"

echo "========== 01 Zotero → Notion =========="
echo "请确认 Zotero 已打开，文献主条目已打 to-notion 标签。"
echo ""

python src/zotero_to_notion.py

echo ""
echo "运行结束。按回车关闭。"
read
EOF

cat > run_diagnose_notion.sh <<'EOF'
#!/bin/bash
cd "/Users/xueyeye/Documents/notion-literature-tool" || exit 1
source ".venv/bin/activate"

echo "========== 02 Notion 结构诊断 =========="
echo ""

python src/diagnose_notion_structure.py

echo ""
echo "诊断结束。按回车关闭。"
read
EOF

cat > run_translation_test.sh <<'EOF'
#!/bin/bash
cd "/Users/xueyeye/Documents/notion-literature-tool" || exit 1
source ".venv/bin/activate"

echo "========== 03 翻译功能测试 =========="
echo ""

python - <<'PY'
from src.translate_text import translate_to_chinese

text = "This is a translation test paragraph about brand equity and academic publishing."

print("原文：")
print(text)
print("")
print("译文：")
print(translate_to_chinese(text))
PY

echo ""
echo "翻译测试结束。按回车关闭。"
read
EOF

cat > run_full_sync.sh <<'EOF'
#!/bin/bash
cd "/Users/xueyeye/Documents/notion-literature-tool" || exit 1
source ".venv/bin/activate"

echo "========== 04 Notion 文献完整同步 =========="
echo ""

echo "第一步：Zotero → Notion"
python src/zotero_to_notion.py

echo ""
echo "第二步：翻译中文题名 / 中文摘要并回写 Notion"
python src/backfill_translation_fields.py

echo ""
echo "第三步：Notion → Excel / Markdown / Word / GitHub"
python src/main.py

echo ""
echo "第四步：生成 APA / GB 引用并输出参考文献包"
python src/citation_export.py

echo ""
echo "完整同步结束。按回车关闭。"
read
EOF

cat > run_open_outputs.sh <<'EOF'
#!/bin/bash

echo "========== 05 打开输出结果 =========="
echo ""

open "/Users/xueyeye/Desktop/github仓库文件/exports/literature_notes.xlsx"
open "/Users/xueyeye/Desktop/github仓库文件/materials"
open "/Users/xueyeye/Desktop/github仓库文件/materials/word"

echo "已打开 Excel、Markdown、Word 输出目录。"
echo "按回车关闭。"
read
EOF

chmod +x run_zotero_import.sh
chmod +x run_diagnose_notion.sh
chmod +x run_translation_test.sh
chmod +x run_full_sync.sh
chmod +x run_open_outputs.sh

cat > "/Users/xueyeye/Desktop/01-Zotero导入Notion.command" <<'EOF'
#!/bin/bash
/Users/xueyeye/Documents/notion-literature-tool/run_zotero_import.sh
EOF

cat > "/Users/xueyeye/Desktop/02-Notion结构诊断.command" <<'EOF'
#!/bin/bash
/Users/xueyeye/Documents/notion-literature-tool/run_diagnose_notion.sh
EOF

cat > "/Users/xueyeye/Desktop/03-翻译功能测试.command" <<'EOF'
#!/bin/bash
/Users/xueyeye/Documents/notion-literature-tool/run_translation_test.sh
EOF

cat > "/Users/xueyeye/Desktop/04-Notion文献完整同步.command" <<'EOF'
#!/bin/bash
/Users/xueyeye/Documents/notion-literature-tool/run_full_sync.sh
EOF

cat > "/Users/xueyeye/Desktop/05-打开输出结果.command" <<'EOF'
#!/bin/bash
/Users/xueyeye/Documents/notion-literature-tool/run_open_outputs.sh
EOF

chmod +x "/Users/xueyeye/Desktop/01-Zotero导入Notion.command"
chmod +x "/Users/xueyeye/Desktop/02-Notion结构诊断.command"
chmod +x "/Users/xueyeye/Desktop/03-翻译功能测试.command"
chmod +x "/Users/xueyeye/Desktop/04-Notion文献完整同步.command"
chmod +x "/Users/xueyeye/Desktop/05-打开输出结果.command"
```

## 🚀 新版日常使用流程

### Step 1：Zotero 入库

懒人版：

```text
Zotero 中给文献主条目打 to-notion 标签
↓
双击 01-Zotero导入Notion.command
```

终端版：

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python src/zotero_to_notion.py
```

### Step 2：Notion 整理

在 Notion 中补充：

- 是否导出；
- 对应章节；
- 用途；
- 主题；
- 优先级；
- 页面正文笔记；
- 页面评论；
- 可支持论点；
- 我的评价。

### Step 3：结构诊断

懒人版：

```text
双击 02-Notion结构诊断.command
```

终端版：

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python src/diagnose_notion_structure.py
```

### Step 4：翻译测试

懒人版：

```text
双击 03-翻译功能测试.command
```

终端版：

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python - <<'PY'
from src.translate_text import translate_to_chinese
print(translate_to_chinese("This is a translation test."))
PY
```

### Step 5：中文题名 / 中文摘要翻译回写

懒人版：

```text
双击 04-Notion文献完整同步.command
```

终端版：

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python src/backfill_translation_fields.py
```

### Step 6：完整同步

懒人版：

```text
双击 04-Notion文献完整同步.command
```

终端版：

```bash
cd /Users/xueyeye/Documents/notion-literature-tool
source .venv/bin/activate
python src/zotero_to_notion.py
python src/backfill_translation_fields.py
python src/main.py
python src/citation_export.py
```

### Step 7：打开输出结果

懒人版：

```text
双击 05-打开输出结果.command
```

终端版：

```bash
open "/Users/xueyeye/Desktop/github仓库文件/exports/literature_notes.xlsx"
open "/Users/xueyeye/Desktop/github仓库文件/materials"
open "/Users/xueyeye/Desktop/github仓库文件/materials/word"
```

### Step 8：GitHub 手动 push

如果自动 push 失败，多数是网络问题，不影响本地文件。

终端版：

```bash
cd "/Users/xueyeye/Desktop/github仓库文件"
git status
git push
```

如果有未提交内容：

```bash
git add README.md logs exports materials config .gitignore
git commit -m "update literature workflow outputs"
git push
```

## 📄 输出文件说明

| 输出 | 路径 | 作用 |
| --- | --- | --- |
| Excel 文献总览 | `exports/literature_notes.xlsx` | 查看文献字段、正文读取状态、评论数量、翻译状态 |
| CSV 文献数据 | `exports/literature_notes.csv` | 数据处理 |
| 全部文献 Markdown | `materials/all_material_pack.md` | 全部勾选文献材料 |
| 文献综述 Markdown | `materials/special/文献综述材料包.md` | 文献综述写作 |
| 理论基础 Markdown | `materials/special/理论基础材料包.md` | 理论基础写作 |
| 主题 Markdown | `materials/by_topic/` | 按主题整理 |
| 章节 Markdown | `materials/by_chapter/` | 按章节整理 |
| 全部文献 Word | `materials/word/全部文献材料包.docx` | 全部文献写作素材 |
| 文献综述 Word | `materials/word/文献综述材料包.docx` | 文献综述写作素材 |
| 理论基础 Word | `materials/word/理论基础材料包.docx` | 理论基础写作素材 |
| 参考文献 Word | `materials/word/参考文献包.docx` | APA / GB 引用合集 |
| 日志 | `logs/*.md`、`logs/*.jsonl` | 同步记录 |

## 🧰 脚本模块说明

| 脚本 | 功能 |
| --- | --- |
| `src/zotero_to_notion.py` | Zotero → Notion 导入 |
| `src/diagnose_notion_structure.py` | 诊断 Notion 字段、正文 blocks、评论权限 |
| `src/backfill_translation_fields.py` | 翻译中文题名、中文摘要并回写 Notion |
| `src/translate_text.py` | 调用代理 API / Codex API 完成翻译 |
| `src/main.py` | 主流程：读取 Notion、导出 Excel / Markdown / Word、日志、GitHub |
| `src/citation_export.py` | 生成 APA / GB 引用并输出参考文献包 |
| `src/material_pack.py` | 生成 Markdown 材料包 |
| `src/word_export.py` | 生成 Word 材料包 |
| `src/export_excel.py` | 生成 Excel / CSV |
| `src/github_sync.py` | GitHub commit / push |
| `src/logger.py` | 生成日志 |

## ⚙️ 环境变量

`.env` 文件位于：

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

## ❓ FAQ

### Q1：为什么打了 `to-notion` 标签但没有导入？

- 标签必须打在 Zotero 文献主条目，不是 PDF 附件；
- Zotero 必须打开；
- 标签必须完全是 `to-notion`；
- 文献如果已存在，会按 Zotero Key、DOI、标题去重。

### Q2：为什么文献综述材料包为空？

- 只写在页面正文里不一定会命中；
- 必须在 Notion 数据库字段中填写 `对应章节=文献综述` 或 `用途=文献综述`；
- 同时必须勾选 `是否导出`。

### Q3：为什么理论基础材料包为空？

- 必须填写 `对应章节=理论基础`；
- 或填写 `用途=理论基础`；
- 或让 `用途/主题` 包含理论、模型、框架、机制等关键词；
- 同时必须勾选 `是否导出`。

### Q4：为什么中文题名和中文摘要没有出现在 Notion？

- 需要运行 `backfill_translation_fields.py`；
- 懒人版是双击 `04-Notion文献完整同步.command`；
- 如果字段名不是 `中文题名`、`中文摘要`，脚本可能无法识别；
- 如果 `翻译状态=已翻译` 但字段为空，需要重新运行翻译回写脚本或检查代码判断逻辑。

### Q5：为什么 Notion 正文没有进入 Word？

- 页面正文必须写在文献页面内部，不是只写在数据库字段里；
- 运行 `02-Notion结构诊断.command` 检查 `block_count`；
- 如果 `block_count` 为 0，说明页面正文为空或没有被读取。

### Q6：为什么评论没有导出？

- 需要 Notion Integration 开启 `Read comments` 权限；
- 如果权限不足，脚本会跳过评论，不影响其他导出。

### Q7：为什么 GitHub push 失败？

- 多数是网络问题；
- 本地文件通常已经生成；
- 网络恢复后手动执行：

```bash
cd "/Users/xueyeye/Desktop/github仓库文件"
git push
```

### Q8：APA / GB 引用是否完全准确？

- 当前是基础格式；
- 如果需要投稿级准确，建议补充卷、期、页码、文献类型、出版社、出版地等字段后进一步优化。

## 🔐 安全说明

- 不要上传 `.env`；
- 不要把 Notion Token、API Key、代理 API Key 写进 README；
- 不要提交 `.DS_Store`；
- 如果密钥泄露，立刻重置；
- README 中只保留示例变量，不写真实密钥或真实中转地址。

`.gitignore` 至少应包含：

```gitignore
.env
.DS_Store
**/.DS_Store
__pycache__/
*.pyc
.venv/
```

## 🧾 GitHub 同步说明

主流程会尝试提交并 push `logs / exports / materials / config / README.md`。如果 push 因网络失败，不需要重复运行脚本；本地文件已经生成时，网络恢复后手动执行：

```bash
cd "/Users/xueyeye/Desktop/github仓库文件"
git push
```

## 🛣️ 后续计划

- 自动按论文大纲生成章节草稿；
- 支持更精确的 GB/T 7714 引文生成；
- 支持 Zotero PDF 批注同步；
- 支持 Notion 中“引用次数”统计；
- 支持按研究问题生成专题材料包；
- 支持页面正文中文翻译与原文并列导出。
