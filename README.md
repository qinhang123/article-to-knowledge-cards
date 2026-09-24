# Make Knowledge Cards

> 将任意文档转化为 5-10 张精炼知识卡片的开源 Skill

## 1. 项目解决了什么问题？

在学习和工作中，我们经常面临一个困境：读完一篇长文档、一份技术 PPT 或一篇 PDF 论文后，感觉"看过了"但"没记住"。长文档信息密度高、知识点分散，**人脑很难在一次阅读中有效吸收和组织所有关键信息**。

现有的文档摘要工具大多只做"压缩"，产出一大段缩写文本——它解决了"太长"的问题，却没有解决"如何记住"的问题。摘要缺乏结构化框架，读者无法逐条自测、无法快速定位单个知识点、无法判断自己是否真正理解。

**Make Knowledge Cards 解决了这个问题。** 它的核心思路是：

- **不是压缩，而是拆解**：将一篇文档拆成 5-10 个独立的知识点，每张卡片只讲一个
- **不是被动阅读，而是主动学习**：每张卡片配有自测问题，引导读者思考而非被动接收
- **不是泛泛而谈，而是精准提取**：只提取真正重要的知识，删除重复，不编造原文没有的内容
- **信息不足时不要强行凑数量**：3 个知识点就出 3 张卡片，宁可少而精，不要多而泛

典型应用场景：

| 场景 | 痛点 | 知识卡片的价值 |
|------|------|----------------|
| 读技术文档/PPT | 50+ 页看完就忘 | 提炼 8-10 个核心卡片，随时复习 |
| 学习新领域 | 不知道哪些是重点 | 自动识别核心知识点，聚焦学习 |
| 考前复习 | 翻全书效率低 | 卡片化知识点 + 自测问题，高效复习 |
| 团队知识分享 | 文档太长没人看 | 用卡片快速传递核心知识 |
| 论文/报告阅读 | 信息密集难以吸收 | 结构化拆解，逐点理解 |

## 2. 主要功能

### 2.1 多格式文档内容提取

支持 5 种常见文档格式的自动内容提取：

| 格式 | 扩展名 | 提取方式 |
|------|--------|----------|
| 纯文本 | `.txt` | 直接读取 UTF-8 内容 |
| Markdown | `.md` `.markdown` | 保留结构直接读取 |
| PDF | `.pdf` | 使用 pypdf 逐页提取文本 |
| PowerPoint | `.pptx` | 使用 python-pptx 提取幻灯片标题、正文和备注 |
| Word | `.docx` | 使用 python-docx 提取段落和表格 |

### 2.2 结构化知识卡片

每张卡片包含 6 个字段，覆盖**理解 → 记忆 → 应用**全链路：

```
┌─────────────────────────────────────────────┐
│  Card N                                      │
│  标题（5-15词，命名知识点）                    │
├─────────────────────────────────────────────┤
│  Core Knowledge    核心知识：这个知识点是什么   │
│  Explanation       简明解释：为什么/怎么运作    │
│  Example           例子：原文中的具体例子       │
│  Self-Test         自测问题：检验是否理解       │
│  Summary           总结/深层思考：归纳或延伸     │
└─────────────────────────────────────────────┘
```

### 2.3 智能质量控制

- **一卡一知识点**：绝不把多个概念塞进同一张卡片
- **删除重复**：相同概念在文档多处出现时，合并为一张卡片
- **不编造内容**：所有信息均来自原文，无例子时如实标注"原文未提供示例"
- **质量优于数量**：信息不足时自动降级，3 个知识点就出 3 张卡片
- **覆盖完整**：旨在覆盖文档全部核心要点，而非只取前几节

### 2.4 美观的 HTML 输出

生成的 HTML 文件特点：
- **自包含**：单个 HTML 文件，无需外部依赖，任何浏览器直接打开
- **可打印**：支持 `@media print` 样式，打印效果良好
- **视觉分区**：六个字段用不同颜色边框区分，阅读体验清晰
- **响应式**：适配桌面和移动端屏幕

### 2.5 边缘情况处理

- 不支持的文件格式 → 报错并列出支持的格式
- 文件不存在 → 报错提示
- 提取内容为空（如图片型 PDF）→ 警告并提示需要 OCR
- 文档过短（<200 词）→ 按实际内容产出卡片并说明限制

## 3. 安装及使用方法

### 3.1 环境要求

- Python 3.8+
- 依赖库：`pypdf`、`python-pptx`、`python-docx`

### 3.2 安装

```bash
# 克隆项目
git clone https://github.com/yourname/make-knowledge-cards.git
cd make-knowledge-cards

# 安装依赖
pip install pypdf python-pptx python-docx
```

如果还需要自行生成测试用的 PDF/PPTX/DOCX 文件，额外安装：

```bash
pip install reportlab
```

### 3.3 目录结构

```
make_knowledge_cards/
├── SKILL.md                      # Skill 主指令文件
├── LICENSE                       # MIT 开源协议
├── scripts/
│   ├── extract_content.py        # 内容提取脚本（txt/md/pdf/pptx/docx）
│   └── generate_cards.py         # 知识卡片 HTML 生成脚本
├── references/
│   └── card_schema.md            # 卡片 JSON 数据结构定义
├── assets/                       # 静态资源目录
└── test_files/                   # 测试文件与示例输出
```

### 3.4 使用方法

#### 方式一：作为 Skill 使用（推荐）

在支持 Skill 的 AI 助手中，提供文档并请求生成知识卡片。AI 助手会自动完成全部流程：

1. 运行 `extract_content.py` 提取文档全文
2. 分析内容，识别 5-10 个核心知识点
3. 为每个知识点构建结构化卡片数据（JSON）
4. 运行 `generate_cards.py` 生成美观的 HTML 知识卡片

#### 方式二：手动分步运行

**Step 1：提取文档内容**

```bash
python scripts/extract_content.py <input_file> [--output <output_path>]
```

参数说明：
- `input_file`：输入文件路径（必填）
- `--output` / `-o`：输出文件路径（可选，不指定则输出到终端）

示例：

```bash
# 提取 PDF，保存到文件
python scripts/extract_content.py report.pdf --output extracted.txt

# 提取 PPTX，直接输出到终端
python scripts/extract_content.py slides.pptx
```

**Step 2：构建卡片 JSON**

阅读提取的内容，为每个知识点创建一张卡片，组装为如下 JSON 格式：

```json
{
  "source_file": "report.pdf",
  "source_title": "文档标题",
  "card_count": 6,
  "cards": [
    {
      "title": "卡片标题（5-15词）",
      "core_knowledge": "核心知识点，2-4句话陈述关键事实。",
      "explanation": "简明解释，2-4句话说明为什么或怎么运作。",
      "example": "来自原文的具体例子。若原文无例子则填：原文未提供示例",
      "self_test": "自测问题？",
      "summary": "总结归纳或更深层次的思考，1-2句。"
    }
  ]
}
```

JSON 字段说明：

| 字段 | 必填 | 内容要求 |
|------|------|----------|
| `source_file` | 是 | 源文件名 |
| `source_title` | 是 | 文档标题（从内容或文件名提取） |
| `cards` | 是 | 卡片数组（5-10张，信息不足时可更少） |
| `title` | 是 | 5-15 词的知识点名称 |
| `core_knowledge` | 是 | 2-4 句核心知识陈述 |
| `explanation` | 是 | 2-4 句简明解释 |
| `example` | 是 | 原文例子，或"原文未提供示例" |
| `self_test` | 是 | 可从卡片内容推导答案的自测问题 |
| `summary` | 是 | 1-2 句总结或深层思考 |

**Step 3：生成 HTML 卡片**

```bash
python scripts/generate_cards.py <cards_json> [--output <output_html>]
```

示例：

```bash
python scripts/generate_cards.py cards.json --output knowledge_cards.html
```

### 3.5 验证 Skill 结构

```bash
python quick_validate.py make_knowledge_cards
# 输出：Skill is valid!
```

## 4. 输入输出示例

### 示例一：Markdown 文档 → 知识卡片

**输入**：`docker_fundamentals.md`（Docker 容器基础，约 1800 词）

```markdown
## What is a Container

A container is a lightweight, standalone executable package that includes
everything needed to run a piece of software, including the code, runtime,
system tools, system libraries, and settings...

## Layered Filesystem

Docker images use a layered filesystem based on Union File System. Each
instruction in a Dockerfile creates a new layer. When a container starts,
a thin writable layer is added on top...

## Container Networking

Docker provides several network drivers: bridge (default), host, none,
and overlay. Port mapping allows exposing container ports to the host...
```

**中间输出**：`docker_cards.json`

```json
{
  "source_file": "docker_fundamentals.md",
  "source_title": "Docker and Container Fundamentals",
  "card_count": 8,
  "cards": [
    {
      "title": "Container vs Virtual Machine",
      "core_knowledge": "容器是轻量级独立可执行包，共享宿主OS内核而非运行完整客户OS，体积几十MB而非几GB，秒级启动而非分钟级。",
      "explanation": "容器将应用与底层基础设施隔离，确保跨环境一致性。与VM的关键区别是不捆绑完整操作系统，从而大幅减小体积和启动时间。",
      "example": "文档指出容器通常几十MB而虚拟机几GB，秒级启动而虚拟机分钟级。",
      "self_test": "容器和虚拟机在操作系统层面的主要区别是什么？",
      "summary": "容器无需完整客户OS即可实现隔离，以更小的隔离性换取更小的体积和更快的启动。"
    },
    {
      "title": "Layered Filesystem and Copy-on-Write",
      "core_knowledge": "Docker镜像使用基于Union FS的分层文件系统。每条Dockerfile指令创建一个新层，层可跨镜像共享。容器启动时在只读层上方添加可写层。写时复制机制将修改的文件复制到可写层后再修改。",
      "explanation": "分层结构使共享基础层在磁盘上只存一份，节省空间。写时复制保护只读层不被修改，使基础镜像可被多个容器安全共享。",
      "example": "文档说明如果两个镜像共享相同基础层，它们在磁盘上共享相同文件，节省存储空间并减少构建时间。",
      "self_test": "Docker 的写时复制机制如何保护原始镜像？",
      "summary": "分层文件系统+写时复制实现了高效存储共享和基础镜像不可变性。"
    }
  ]
}
```

**最终输出**：`docker_knowledge_cards.html`

在浏览器中打开后，每张卡片渲染为：

```
┌──────────────────────────────────────────────────────┐
│ Card 1                                               │
│ Container vs Virtual Machine                         │
│                                                      │
│ ▌Core Knowledge                                      │
│  容器是轻量级独立可执行包，共享宿主OS内核...           │
│                                                      │
│ ▌Explanation                                          │
│  容器将应用与底层基础设施隔离，确保跨环境一致性...     │
│                                                      │
│ ▌Example                                              │
│  ┌──────────────────────────────────────────┐         │
│  │ 文档指出容器通常几十MB而虚拟机几GB...      │         │
│  └──────────────────────────────────────────┘         │
│                                                      │
│ ▌Self-Test                                            │
│  ┌──────────────────────────────────────────┐         │
│  │ 容器和虚拟机在操作系统层面的主要区别是什么？ │         │
│  └──────────────────────────────────────────┘         │
│                                                      │
│ ▌Summary                                              │
│  容器无需完整客户OS即可实现隔离...                    │
└──────────────────────────────────────────────────────┘
```

### 示例二：PPTX 演示文稿 → 知识卡片

**输入**：`database_indexing.pptx`（数据库索引与查询优化，9 张幻灯片）

提取命令：

```bash
python scripts/extract_content.py database_indexing.pptx --output extracted.txt
# 输出：Extracted content saved to: extracted.txt
#       Character count: 4635
```

提取内容示例（第 2 张幻灯片）：

```
--- Slide 2 ---
What is a Database Index
A database index is a data structure that improves the speed of data
retrieval operations on a database table. An index is created on one or
more columns of a table. Without an index, the database must scan every
row in the table to find matching records (a full table scan)...
```

**输出**：8 张知识卡片，涵盖数据库索引基础、B-Tree 结构、复合索引、索引选择性、覆盖索引、执行计划、索引维护、全文搜索等核心知识点。

| 卡片 | 标题 | 核心知识点 |
|------|------|------------|
| 1 | Database Index Fundamentals | 索引加速检索，避免全表扫描 |
| 2 | B-Tree Index Structure | 自平衡树，节点适配页大小 |
| 3 | Composite Indexes | 列顺序与最左前缀规则 |
| 4 | Index Selectivity | 选择性决定优化器是否用索引 |
| 5 | Covering Indexes | 索引覆盖查询，避免回表 |
| 6 | Query Execution Plans | EXPLAIN 揭示执行策略 |
| 7 | Index Maintenance | 碎片化与写性能权衡 |
| 8 | Full-Text Search Indexes | 倒排索引与文本预处理 |

### 示例三：PDF 文档 → 知识卡片

**输入**：`database_indexing.pdf`（数据库索引与查询优化，2 页）

```bash
# 提取
python scripts/extract_content.py database_indexing.pdf --output extracted.txt
# 输出：Character count: 4491

# 生成 HTML
python scripts/generate_cards.py database_indexing_cards.json --output database_indexing_cards.html
# 输出：Cards generated: 8
```

**输出**：8 张知识卡片，涵盖 B-Tree 结构、复合索引、索引选择性、覆盖索引、执行计划、索引维护、全文搜索等核心知识点。

### 支持的输入格式汇总

| 输入格式 | 提取库 | 提取效果 | 测试字符数 |
|----------|--------|----------|------------|
| `.txt` | 内置 | 完整保留 | 按原文 |
| `.md` | 内置 | 完整保留 | 按原文 |
| `.pdf` | pypdf | 逐页提取文本 | 4,491 |
| `.pptx` | python-pptx | 标题+正文+备注 | 4,635 |
| `.docx` | python-docx | 段落+表格 | 4,458 |

## 许可证

MIT License — 详见 [LICENSE](make_knowledge_cards/LICENSE)
