# Academic Paper Workflow

A Claude Code skill for structured academic paper reading, discussion, note-taking, and knowledge retrieval — designed for humanities and social science researchers.

## What This Does

**Academic Paper Workflow** turns Claude into a disciplined research partner who follows your reading process. It enforces a "user-led, AI-assisted" approach: you read first, you set the discussion focus, and notes only get written when you confirm.

### Key Features

- **Structured Discussion Loop** — User pre-reading → custom prompt → targeted summary → discussion → confirmed writing. No unsolicited summaries.
- **Standardized Note Format** — Three-section structure (summary / arguments with citations / discussion log) with metadata and classification tags.
- **Local RAG Strategy** — Searches your personal paper library and notes before falling back to training data. Cites sources clearly.
- **Thesis Proposal Accumulation** — Gradually builds up thesis modules (significance, background, literature review, methods, etc.) through daily discussions, not marathon sessions.

## Installation

### Manual Installation

Copy the skill files to your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/academic-paper-workflow
cp SKILL.md ~/.claude/skills/academic-paper-workflow/
```

Or clone directly (after you publish to a git repo):

```bash
git clone https://github.com/AsahiE0221/academic-paper-workflow.git ~/.claude/skills/academic-paper-workflow
```

Then use it by typing `/academic-paper-workflow` in Claude Code.

## Usage

### Start a Paper Discussion

```
/academic-paper-workflow

> "我刚读完张三的《日本政教关系研究》，帮我总结一下他的方法论框架"
```

The skill will:
1. Read the paper PDF from your library
2. Provide a focused summary based on your prompt
3. Engage in discussion
4. Wait for your confirmation before writing notes

### Search Your Library

```
> "我之前读过的论文里，谁讨论过靖国神社问题？"
```

The skill will search your notes first (fast), then fall back to PDFs (for original text), and respond with cited sources.

### Accumulate Thesis Materials

After each note-writing session, the skill tags insights against thesis modules. Over time, your notes accumulate the content needed for your proposal and defense.

## Workflow Phases

| Phase | Name | Description |
|-------|------|-------------|
| 0 | Setup | Configure paper/notes directories on first run |
| 1 | Discussion | User pre-reads → prompt → summary → discuss → confirm |
| 2 | Note Writing | Write structured notes with 3-section format |
| 3 | Local RAG | Search notes → fall back to PDFs → cite sources |
| 4 | Accumulation | Tag insights against thesis proposal modules |

## Philosophy

1. **You are the scholar.** Claude assists but doesn't replace your reading and thinking.
2. **Discussion before documentation.** Notes capture what was discussed, not what Claude guessed.
3. **Your library, your knowledge.** Local RAG means answers draw from papers you've actually read.
4. **Gradual accumulation beats last-minute cramming.** Thesis materials build up naturally.

## Requirements

- [Claude Code](https://claude.ai/claude-code) CLI
- A local directory containing paper PDFs
- A local directory for reading notes (markdown)

## License

MIT — Use it, modify it, share it.
