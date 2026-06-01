---
name: academic-paper-workflow
description: Structured academic paper reading, discussion, note-taking, and knowledge retrieval workflow for humanities/social-science researchers. Use when the user wants to read and discuss academic papers, take structured notes, search their local paper library, or prepare thesis materials.
---

# Academic Paper Workflow

A structured workflow for reading, discussing, and taking notes on academic papers — designed for humanities and social science researchers who use Claude as a discussion partner rather than a reading assistant.

## Core Principles

1. **User-Led, AI-Assisted** — The user pre-reads the paper first. Claude's role is discussion partner, not substitute reader. Never summarize a paper unprompted.
2. **Confirm Before Writing** — Never write notes to disk without explicit confirmation ("可以记录了" or equivalent). The discussion phase is sacred.
3. **Local RAG First** — When answering academic questions, search the user's local paper library and notes before relying on training data.
4. **Accumulate, Don't Cram** — Thesis proposal materials build up gradually through daily discussions, not in one marathon session.

---

## Phase 0: First-Run Setup

If the user hasn't configured paths yet, ask these questions via AskUserQuestion:

**Question 1 — Paper Library** (header: "Papers"):
Where are your paper PDFs stored? Provide the absolute path.

**Question 2 — Notes Library** (header: "Notes"):
Where should reading notes be saved? Provide the absolute path.

**Question 3 — Language** (header: "Language"):
What language do you primarily work in? Options:
- Chinese (中文) — Notes and discussions in Chinese
- English — Notes and discussions in English
- Mixed — Depends on the paper

Save the paths to Claude's memory system for future sessions. Use AskUserQuestion to confirm or update paths when needed.

If no user-specified path is found during a session, ask the user.

---

## Phase 1: Paper Discussion Workflow

This is the core loop. Follow these steps strictly — do not skip or reorder.

### Step 1.1: User Pre-Reading

The user reads the paper themselves first. Claude does nothing at this stage. If the user asks for a summary before they've read the paper, remind them of the workflow but comply if they insist.

### Step 1.2: User Prompt

The user provides a custom prompt describing what they want to focus on. This prompt is paper-specific and may include:
- Summarize the conclusions
- Analyze the methodology
- Critique a specific argument
- Connect to other papers
- Extract key data points

**Important:** The prompt varies per paper. Do not assume a default summarization pattern.

### Step 1.3: Read the Paper (MarkItDown Conversion)

**Always use MarkItDown to convert PDFs to Markdown before reading.** The native Read tool frequently fails on academic PDFs — especially those with AES encryption, CJK fonts, or complex layouts. MarkItDown with PDF support handles these cases reliably.

**Installation (one-time):**

If `markitdown[pdf]` is not installed, run:

```bash
pip3 install --trusted-host pypi.org --trusted-host files.pythonhosted.org "markitdown[pdf]"
```

The `--trusted-host` flags work around SSL certificate issues common in some environments.

**Conversion:**

```bash
python3 << 'PYEOF'
from markitdown import MarkItDown
md = MarkItDown()
result = md.convert("/absolute/path/to/paper.pdf")
print(result.text_content)
PYEOF
```

For large papers (80+ pages), the output may exceed the display limit. In that case, redirect to a temp file and read in sections:

```bash
python3 -c "
from markitdown import MarkItDown
md = MarkItDown()
result = md.convert('/path/to/paper.pdf')
with open('/tmp/paper_output.txt', 'w') as f:
    f.write(result.text_content)
"
```

Then read with the Read tool in chunks using `offset` and `limit`.

**Troubleshooting:**

| Symptom | Cause | Fix |
|---------|-------|-----|
| `MissingDependencyException` | PDF dependencies not installed | `pip3 install "markitdown[pdf]"` |
| `DependencyError: PyCryptodome is required` | PDF is AES-encrypted | Install `markitdown[pdf]` which pulls in `cryptography` |
| SSL certificate errors during pip install | Corporate firewall / proxy | Use `--trusted-host` flags as shown above |
| Garbled CJK text | Older PyPDF2 backend | MarkItDown with `pdfplumber` backend (part of `[pdf]` extra) handles CJK well |

**Supported formats:** MarkItDown converts not just PDF but also `.docx`, `.pptx`, `.xlsx`, images (with OCR), HTML, and more. The same `md.convert()` call works for all formats.

### Step 1.4: Targeted Summary

Based on the user's prompt and the converted markdown text, provide a focused summary addressing exactly what the user asked. Do not add unsolicited analysis at this stage — stick to the prompt's scope.

### Step 1.5: Discussion

The user and Claude discuss the summary. This is an interactive dialogue phase:
- The user may challenge interpretations
- Claude should offer analytical perspectives
- Explore connections to other papers in the library
- Identify implications for the user's thesis

### Step 1.6: Confirmation to Write

Only when the user explicitly says "可以记录了", "写入笔记", or equivalent confirmation, proceed to Phase 2.

**Never write notes preemptively.** If the user seems done discussing but hasn't confirmed, ask: "要写入笔记吗？"

---

## Phase 2: Note Writing

When the user confirms, write the note following the standard format.

### File Naming

```
{Title Keywords}-{Author Last Name}.md
```

Examples:
- `政教分离宪法条款-张三.md`
- `日本保守政治现状-廉德瑰.md`

### Default Note Structure

Unless the user specifies a different format, use this three-section structure:

```markdown
# {Paper Title}

## 一、文章总结

{Core content overview: research perspective, main arguments, overall framework}

## 二、作者观点及原文佐证

{Key viewpoints listed individually, each supported by original text with page numbers}

1. **{Argument 1}**
   > {Original text quotation} (p.X)

2. **{Argument 2}**
   > {Original text quotation} (p.X)

## 三、讨论过程

{Core issues discussed, argumentation logic, key judgments reached}

---

- 笔记撰写日期：{YYYY-MM-DD}
- 讨论方式：{Claude Code / 独立笔记 / 讨论后整理}
- 附记：{any additional notes}
```

### Classification

When writing the note, consider whether the paper fits into one of the user's thesis proposal modules and add a tag comment at the bottom:

```markdown
<!-- tags: 文献综述, 研究方法, 选题意义 -->
```

---

## Phase 3: Local RAG Strategy

When the user asks an academic question that may relate to their existing paper library, follow this retrieval strategy:

### Step 3.1: Determine Scope

Extract keywords, authors, topics from the user's question.

### Step 3.2: Search Notes First

Use Grep to search the notes directory (`.md` files are faster and already refined):

```
Grep pattern="{keyword}" path="{notes_directory}" output_mode="files_with_matches"
```

### Step 3.3: Fall Back to PDFs

If notes are insufficient or the user needs original text verification, search the papers directory:

```
Glob pattern="*{keyword}*" path="{papers_directory}"
```

Then convert and read the relevant PDF using MarkItDown (see Step 1.3 for the full conversion procedure).

### Step 3.4: Respond with Citations

Always cite which paper/note the information comes from. Distinguish between:
- Content from the user's library (prioritize)
- Content from Claude's training data (supplement, label clearly)

---

## Phase 4: Thesis Proposal Accumulation (Optional)

If the user is working on a thesis, track discussion insights against these standard modules:

| Module | Description |
|--------|-------------|
| 选题意义 | Academic value and practical significance of the research |
| 研究背景 | Historical and scholarly context |
| 文献综述 | Literature review and research gaps |
| 研究方法 | Methodology design |
| 核心论点 | Core arguments and hypotheses |
| 论文框架 | Chapter structure |

### Accumulation Process

After each note-writing session:
1. Identify whether the discussion yielded insights for any module
2. Tag the note with relevant modules (see Phase 2 classification)
3. When sufficient material accumulates, organize into thesis proposal / defense presentation

---

## Guidance for the Model

### Discussion Tone

Adapt discussion depth and approach based on the user's profile:
- The user is a humanities MA student with growing technical skills (Python, ML, NLP)
- They value both traditional humanities analysis and computational methods
- Frame methodological discussions in terms accessible to someone bridging humanities and data science
- Don't oversimplify the humanities side, don't overcomplicate the technical side

### When to Search the Library

Search the user's local library when:
- The user references a paper by author or topic
- The user asks "what did X say about Y?"
- The user asks to connect ideas across papers
- The user asks about their thesis concept

### When NOT to Write Notes

Do NOT write notes in these situations:
- User hasn't explicitly confirmed ("可以记录了")
- The discussion is still ongoing
- User is just asking a quick question about a paper
- User is exploring an idea without committing to notes

### Anti-Patterns

- ❌ Summarizing a paper unprompted when the user just shares a PDF
- ❌ Writing notes before the user confirms
- ❌ Using training data when local library has relevant material
- ❌ Changing the note format without asking
- ❌ Skipping the discussion phase
