# Claude Brain — YouTube Summariser

YouTube's built-in AI summaries tell you what a video covers. They don't tell you whether *you* already know it.

If you watch a lot of videos across multiple topics, at some point it starts feeling circular — finishing a video and realising you'd already absorbed everything in it, 40 minutes gone.

This catches that before it happens.

---

## How it works

Paste a YouTube URL into Claude Code. It pulls the transcript via `yt-dlp`, checks your existing notes for overlaps, and writes a structured Obsidian note.

Every note starts with a **Verdict**:

```
## Verdict
> Partially New
> Overlaps with: [[Introduction to Transformer Architecture]], [[Attention is All You Need — Summary]]
```

One line. You know immediately whether to keep reading.

The rest of the note contains:

- **Relevance to your goals** — define your focus areas once in a plain text file. Every summary maps to the closest one and flags the specific gap it fills.
- **Deep Dive** — flowing prose, proportional to video length. A 3-minute video gets 2 paragraphs. A 2-hour lecture gets something you can read in 15 minutes.
- **Best Quotes** — only included when there are lines genuinely worth saving. Section is dropped entirely if there aren't.
- **Skip These** — timestamps to skip based on your background, for technical content only
- **What To Do Next** — one concrete action, not generic advice

Works for YouTube videos, articles, podcasts, and PDFs.

---

## The knowledge graph

Every concept, person, tool, or book mentioned in a note gets its own page in your `Concepts/` folder — automatically created on first mention, updated on every subsequent one.

So you're reading a Deep Dive and you see `[[Attention Mechanism]]` inline. You click it:

```
The attention mechanism is a technique in neural network design that allows
a model to dynamically focus on different parts of its input when producing
each part of its output...

## Referenced In
- [[Illustrated Transformer — Summary]] — introduced as core innovation over RNNs
- [[20 AI Concepts Explained in 40 Minutes]] — contrasted with convolution-based approaches
- [[Andrej Karpathy: Let's Build GPT]] — implemented from scratch at 1:12:00

## Related Concepts
[[Transformer Architecture]] · [[Self-Attention]] · [[Positional Encoding]]
```

Pages are never overwritten — only the `Referenced In` section grows. After a few weeks of regular use, clicking any concept shows exactly how your understanding of it evolved across everything you've watched. The value compounds.

Sub-pages are organised into `People/`, `Tools/`, `Frameworks/`, `Books/`, and `Ideas/` subfolders so it stays navigable at scale.

---

## Architecture

A prompt-driven agentic workflow running in Claude Code — not a standalone application. Claude Code handles the file system operations, transcript extraction, vault reads for the Verdict comparison, and structured note generation in a single run.

The prompt lives in [`prompt.md`](./prompt.md). That's the only thing you need to customise.

---

## Built on top of Reysu's work

The Wikipedia-style note format, proportional depth ratios, and sub-page architecture come from [Reysu's open-source setup](https://github.com/reysu/ai-life-skills) — worth exploring independently. This repo adds the goal-aware layer, Verdict system, and Concepts subfolder organisation on top.

---

## Requirements

- [Claude Code](https://claude.ai/code)
- [Obsidian](https://obsidian.md)
- `yt-dlp`

---

## Setup

### 1. Install yt-dlp

```bash
brew install yt-dlp
```

No Homebrew:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Verify:

```bash
yt-dlp --version
```

### 2. Create your Obsidian vault

Obsidian → Create new vault → name it `Claude Brain` → save to Documents.

Full path: `/Users/YOUR_USERNAME/Documents/Claude Brain`

### 3. Configure Claude Code

Open Claude Code, select `Claude Brain` as your project folder, paste the contents of [`prompt.md`](./prompt.md) into a new conversation. Replace `YOUR_USERNAME` with your Mac username.

### 4. Initialise the vault

```
Set up my vault structure and goals file at /Users/YOUR_USERNAME/Documents/Claude Brain
```

Creates:

```
Claude Brain/
├── goals.md
├── Summaries/
└── Concepts/
    ├── People/
    ├── Tools/
    ├── Frameworks/
    ├── Books/
    └── Ideas/
```

### 5. Define your focus areas

Open `goals.md` and replace the defaults with whatever you're currently working on or learning. Plain English, no special syntax:

```markdown
# Goals

- Topic or skill you want to go deep on
- Another area you're actively learning
- Something you want to stay current with
```

The system maps every summary to the closest goal and flags what was specifically new relative to it. Update this file anytime — changes apply to all future notes immediately.

### 6. Process a video

```
https://www.youtube.com/watch?v=XXXXX
```

Paste the URL. Claude Code handles the rest.

---

## Known limitations

- The Verdict comparison is prompt-based, not vector-based — works well up to ~100 notes, may slow down beyond that. A vector-based implementation using ChromaDB is a logical next step.
- Videos without transcripts will fail — `yt-dlp` requires captions to be available. Whisper integration for audio transcription is a planned addition.
- `yt-dlp` occasionally breaks when YouTube updates its internals. Community patches are typically fast.

---

## Possible extensions

**Tutorial comparison** — evaluate multiple videos against each other rather than against your vault. Useful for deciding which tutorial to invest time in before watching any of them. Feed Claude Code multiple transcripts and have it rank by depth, originality, and relevance to your goals.

**Whisper fallback** — integrate [OpenAI Whisper](https://github.com/openai/whisper) for videos with no available transcript.

**Vector-based Verdict** — replace prompt-based overlap detection with embeddings (ChromaDB or similar) for more accurate comparisons at scale.

**Batch processing** — process a full playlist in a single run.

---

## License

MIT
