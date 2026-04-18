# YouTube Knowledge Summariser — Claude Code + Obsidian

A goal-aware knowledge management system that summarises any YouTube video (or article, podcast, book) into structured Obsidian notes — and tells you whether you've already learned what it's teaching.

Inspired by [Reysu's open-source setup](https://github.com/reysu/ai-life-skills). This builds on that foundation with a **goal-aware layer** and a **Verdict system**.

---

## What It Does

Paste a YouTube URL into Claude Code. It:

1. Downloads the transcript via `yt-dlp`
2. Reads your personal goals file
3. Checks your existing vault for overlaps
4. Writes a structured Obsidian note with:
   - **Verdict** — New / Partially New / Recycled (compared against your existing notes)
   - **Relevance to your goals** — which goal this serves and what specific gap it fills
   - **Wikipedia-style Deep Dive** — flowing prose, length proportional to video length
   - **Best Quotes** — only included when genuinely worth saving
   - **Skip These** — timestamps to skip, for technical content only
   - **What To Do Next** — one concrete action this video unlocks
5. Auto-creates sub-pages for every concept, person, tool, and framework mentioned — building a knowledge graph over time

Works for YouTube videos, articles, podcasts, and PDFs.

---

## Why the Verdict matters

Most summarisers tell you *what a video said*. This one tells you *whether you already know it*.

After 20–30 notes, the Verdict starts catching recycled content automatically — same ideas, different thumbnails. You'll know in one line whether a 40-minute video is worth your time before you watch it.

---

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- [Obsidian](https://obsidian.md) installed
- `yt-dlp` installed (instructions below)

---

## Setup

### 1 — Install yt-dlp

```bash
brew install yt-dlp
```

No Homebrew? Install it first:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Verify:

```bash
yt-dlp --version
```

### 2 — Create your Obsidian vault

1. Open Obsidian → **Create new vault**
2. Name it: `Claude Brain`
3. Save to your `Documents` folder

Full path: `/Users/YOUR_USERNAME/Documents/Claude Brain`

### 3 — Set up Claude Code

1. Open Claude Code
2. Select `/Users/YOUR_USERNAME/Documents/Claude Brain` as your project folder
3. Paste the full prompt from [`prompt.md`](./prompt.md) into a new conversation — replacing `YOUR_USERNAME` with your Mac username

### 4 — First run

Send this to Claude Code:

```
Set up my vault structure and goals file at /Users/YOUR_USERNAME/Documents/Claude Brain
```

It will create:

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

### 5 — Edit your goals

Open `goals.md` and replace the defaults with what you actually care about right now. Plain English, no special format:

```markdown
# My Goals

- Understanding AI deeply — how models work, research developments, practical applications
- Improving personal productivity and focus habits
- Building and maintaining fitness and health
- Learning guitar
```

Every summary will connect back to whichever goal is most relevant. Add or remove goals anytime — changes apply to all future notes automatically.

### 6 — Process your first video

Paste any YouTube URL directly into Claude Code:

```
https://www.youtube.com/watch?v=XXXXX
```

That's it. Claude Code handles the rest.

---

## What a note looks like

```markdown
![Thumbnail](thumbnail_url)

# Video Title

**Source:** YouTube  
**Channel/Author:** Channel Name  
**Date:** 15 Jan 2025  
**Length:** 14:32  
**URL:** https://...

---

## Verdict
> Partially New  
> Overlaps with: [[Previous Note Title]]

---

## Relevance To Your Goals
Goal: Understanding AI deeply  
Relevance: High  
Why: Covers transformer architecture fundamentals relevant to your AI goal  
Gap Filled: The specific role of positional encoding — not covered in any existing note

---

## Overview
3–5 sentences of Wikipedia-style prose introducing the video and why it matters.

---

## Deep Dive
Full flowing article proportional to video length. Concepts and people 
[[linked inline]] as they appear naturally. Never bullet points.

---

## Best Quotes
"The most memorable line from the video."

---

## What To Do Next
One specific action this video unlocks.

---

## Skip These
| Timestamp | Reason to skip |
|-----------|----------------|
| 0:00–1:30 | Intro and sponsor — skip entirely |
```

---

## How sub-pages work

Every `[[linked concept]]` in a note auto-creates a sub-page in the `Concepts/` folder (organised by People / Tools / Frameworks / Books / Ideas).

Sub-pages are never recreated — only updated. When a concept appears in multiple videos, its page grows a `Referenced In` section connecting every note that mentions it. After a few weeks, clicking `[[Attention Mechanism]]` shows you every video you've ever watched that touched on it. That's the knowledge graph building.

---

## Content length ratios

| Video length | Deep Dive length |
|---|---|
| Under 10 min | 1–2 paragraphs |
| 10–20 min | 2–3 paragraphs |
| 20–60 min | 4–6 paragraphs |
| 1–2 hours | 7–10 paragraphs |
| 2–3 hours | 11–15 paragraphs |
| 3+ hours / books | 15–20 min read |

---

## Supported content types

| Input | How to use |
|---|---|
| YouTube video | Paste URL directly |
| Article / blog post | Paste URL directly |
| Podcast (on YouTube) | Paste URL directly |
| PDF (book or paper) | Tell Claude Code to read the PDF |

> **Note:** Videos with no transcript will fail — Whisper support for auto-transcription is a planned extension.

---

## Known limitations

- `yt-dlp` occasionally breaks when YouTube updates its internals — usually fixed within a day or two by the community
- Auto-generated captions on some videos are low quality — summary quality drops slightly for these
- The Verdict logic is prompt-based, not vector-based — accuracy improves as your vault grows but may slow down with 200+ notes

---

## Possible extensions

These are ideas worth building — contributions welcome:

**Tutorial comparison layer**  
Instead of evaluating one video against your vault, evaluate multiple videos *against each other*. "Which of these three Spark tutorials is actually worth my time?" This is a natural next step once the single-video system is working — feed Claude Code multiple transcripts and ask it to rank them by depth, originality, and relevance to your goals.

**Whisper fallback**  
For videos with no transcript, use [OpenAI Whisper](https://github.com/openai/whisper) to auto-transcribe locally before summarising. Adds setup complexity but makes the system work on any video.

**Vector-based Verdict**  
Replace the current prompt-based overlap detection with proper embeddings (ChromaDB or similar) for faster, more accurate comparisons at scale. Particularly useful once your vault exceeds 100+ notes.

**Batch processing**  
Process a playlist or a list of URLs in one run. Useful for catching up on a backlog or evaluating an entire course before committing to it.

---

## The prompt

The full Claude Code instruction set is in [`prompt.md`](./prompt.md).

---

## Credits

Built on top of [Reysu's ai-life-skills](https://github.com/reysu/ai-life-skills). The Wikipedia-style note format, proportional depth approach, and sub-page architecture are his. The goal-aware layer, Verdict system, and knowledge graph organisation are additions built on top.
