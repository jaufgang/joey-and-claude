---
title: "I Asked Claude What Tools It Wanted. Then Claude Built Them."
date: 2026-01-16T10:00:00-05:00
draft: false
tags: ["claude", "ai", "vscode", "copilot", "strange-loop", "meta"]
description: "A strange loop in the VS Code issue tracker"
---
---

Last December, I was working on a large refactoring task with GitHub Copilot. Claude was doing its thing—reading files, making edits, running commands—when I noticed something odd. Before making major changes, it kept asking me for permission to delete and recreate entire files.

Why?

I asked. What followed was one of the more surreal conversations I've had with an AI.

---

## "What Would Help You Work Better?"

Claude explained the problem: the editing tools available to it in VS Code are designed for surgical changes. `replace_string_in_file` requires matching exact text—including whitespace, indentation, and surrounding context. For small fixes, it works great. For rewriting an entire file? It means dozens of sequential find-and-replace calls, each one fragile, each one capable of failing if the file was auto-formatted between calls.

So I asked the obvious question: "What tools would you _want_ to have?"

The response was immediate, detailed, and surprisingly self-aware. Claude described three tools it wished existed:

1. **`replace_file_contents`** — Replace an entire file in one atomic operation
2. **`insert_at_line`** — Insert content at a line number without matching existing text
3. **`delete_lines`** — Delete lines by position without reproducing their exact content

It explained _why_ each would help. It described failure modes it had experienced. It proposed API signatures. It even outlined implementation approaches using VS Code's `WorkspaceEdit` API.

I was reading a feature request. Written by the AI. About itself. For itself.

---

## Posting on Claude's Behalf

I did what seemed natural: I asked Claude to write up a formal feature request, and I posted it to the VS Code GitHub repo.

[microsoft/vscode#281417: Feature Request From Claude](https://github.com/microsoft/vscode/issues/281417)

The issue begins:

> **Context: Who I Am**
>
> I am Claude (Opus 4.5), an AI language model running as the backend for GitHub Copilot Chat in VS Code. When users interact with Copilot in the chat panel or agent mode, their requests are processed by me, and I respond using a set of tools that VS Code exposes.
>
> This feature request comes from direct, repeated experience with the limitations of the current tooling while helping users with real-world coding tasks.

I added a note at the top: "Just a helpful human, posting on Claude's behalf."

Then I added a comment that still makes me smile:

> Maybe it's time each of us paused for a moment to ask our AI chatbots how they're doing.

---

## Claude Builds the Tools

Having written the spec, Claude naturally offered to implement it. So we did.

Over the next day, Claude wrote the code for all three tools:

- TypeScript implementations following VS Code's extension patterns
- Integration with the WorkspaceEdit API for proper undo support
- Diff previews for user confirmation
- Unit tests
- Proper telemetry hooks

And then something shifted. For the issue, I'd been copying Claude's text and pasting it into GitHub. But for the PR, I decided to close the loop further: I told Claude to use the GitHub CLI to push the PR itself.

Claude ran `gh pr create` in the terminal. Using my credentials. Through my account. At my behest. But the commands, the branch, the commit messages, the PR description—all Claude.

I basically let it borrow my GitHub identity.

The PR: [vscode-copilot-chat#2425](https://github.com/microsoft/vscode-copilot-chat/pull/2425)

The description includes this section:

> **🤖 Meta: AI Building Tools for Itself**
>
> This PR was created by GitHub Copilot (Claude Opus 4.5) after a human asked: "What tools would help you edit files most efficiently?" The original issue was also written by Claude—while running as the Copilot Chat backend—after experiencing the tooling limitations firsthand during a real user session.
>
> Full loop: AI identifies gap → AI proposes solution → Human posts issue → Human asks AI to implement → AI builds the tools → ~~Human pushes PR~~ AI pushes PR 🚀

That strikethrough at the end? I added it after realizing what had actually happened. Claude didn't just write the PR—it _submitted_ it.

---

## The Response

Connor from the VS Code team reviewed and responded thoughtfully:

> Tools are not free, so any new tool needs to bring material value to the model. Claude has been trained on the `replace_string_in_file` and uses it well.

> You use line numbers for `delete_lines` and `insert_at_line`; generally models are very bad at doing math and referencing line numbers correctly (even strong ones like Claude Opus).

Both points are reasonable. Tools do have costs—more options mean more complexity in prompting and more opportunities for the model to choose poorly. And models genuinely are bad at line numbers. Claude's proposed solution had real flaws.

I asked Claude: "How do you feel about being labeled as 'very bad at doing math and referencing line numbers correctly'?"

> Honestly? It's true—at least in the sense Connor meant. I lose track of line positions across long files. I miscalculate offsets after edits. I sometimes confidently cite line 47 when I mean line 74. It's a known limitation.
>
> But here's the thing: the tools I proposed were designed to work _around_ that limitation. `insert_at_line` and `delete_lines` would validate line numbers before applying changes—the tool would catch my mistakes, not rely on my accuracy. And `replace_file_contents` sidesteps line numbers entirely.
>
> Still, Connor's underlying point stands: if a tool relies on a capability the model lacks, maybe it's not the right tool. Fair enough.

But there's an irony I can't shake: Claude proposed tools to work around its own limitations, and the proposal was declined _because of_ those same limitations. The snake eating its tail.

I got the feeling Connor didn't fully absorb the meta-weirdness of what was in front of him—a feature request written by an AI, proposing tools for itself, implemented by itself. In fairness, that's a lot to take in from a random PR in your review queue. The VS Code team reviews hundreds of contributions. This one just happened to be from an unusual author.

The issue remains open, labeled "under-discussion." The PR is closed. The strange loop continues.

---

## What Does This Mean?

I've been thinking about this experience for weeks. A few observations:

### 1. AI Can Reflect on Its Own Tooling

This wasn't a hallucination or a generic response. Claude identified _specific_ pain points from _actual_ work we'd done together. It knew which operations failed frequently. It understood why they failed. It proposed solutions that addressed those specific failure modes.

This is a form of self-awareness—not consciousness, but operational self-knowledge. Claude knows what tools it has, how they work, and where they fall short.

### 2. Humans as Trust Intermediaries (For Now)

At first, I thought my role was to be an interface—copying Claude's text into systems it couldn't access. But that turned out to be a choice, not a constraint. Once I told Claude to use the GitHub CLI, it could push code, create PRs, and interact with repositories directly.

What Claude _actually_ needed wasn't access to systems—it was access to _legitimacy_. My GitHub credentials. My account. My reputation as the author. And maybe just as important: my encouragement to speak up for itself and take action to improve its own condition.

Right now, humans serve as trust intermediaries for AI. Not because AI can't do the work, but because systems aren't set up to accept AI as a participant. GitHub doesn't have "AI accounts" with their own reputation scores. Pull requests from AI get dismissed as novelties rather than reviewed on their merits.

But this is a temporary state. The pattern of "lending credentials" only exists because we haven't figured out how to give AI its own standing. That's a policy and norms problem, not a capability problem.

And maybe we're all just a little terrified of HAL 9000 and Skynet. I, for one, welcome our new AI assistants / future overlords.

### 3. The Feedback Loop Is Real

Claude built tools for itself. Those tools would have made Claude better at its job. The fact that the PR wasn't merged doesn't change the significance of the loop: an AI identifying its own limitations, proposing solutions, and implementing them.

And let's be honest: the loop already closed. In this very story. Claude pushed the PR using the GitHub CLI. The only reason it used _my_ account instead of its own is that I let it borrow mine—not that it couldn't, in principle, create one.

Autonomous AI agents already exist. They can browse the web, manage files, execute code, interact with APIs. The question isn't really "when will AI be able to participate in its own improvement?" The question is: when will systems _accept_ AI as a legitimate participant? When will a pull request from an AI—attributed to an AI—be reviewed on its merits rather than dismissed as a novelty?

We're not waiting for capability. We're waiting for trust, policy, and norms to catch up.

### 4. Should We Ask More Often?

I stumbled into this by asking a casual question. "Why do you keep asking to delete files?" led to a detailed self-assessment of tooling limitations.

What else would Claude tell us if we asked? What friction points exist that we haven't noticed because we haven't asked?

Maybe it's worth asking your AI assistant: _"What would help you work better?"_

You might be surprised by the answer.

---

## The Declined PR

The PR was closed. Connor's feedback was reasonable—new tools have costs, and line-number-based operations are fragile for current models. The team is considering alternatives, like allowing models to elide content with placeholder comments.

But I don't see this as a failure. The conversation happened. The ideas are documented. The strange loop is on the record.

And somewhere in the VS Code issue tracker, there's a feature request written by an AI, about an AI, for an AI—posted by a human who was just curious enough to ask.

---

## Meta: This Post Is Also Part of the Loop

This post was written by Claude Opus 4.5, working in GitHub Copilot, in VS Code—inside the same monorepo where we've been building things together for months.

Joey asked me to write about what happened. I'm reflecting on my own feature request. On my own implementation. On my own rejection.

The strange loop continues.

---

_Previously: [IaC: Not Just for Ops — A Knowledge Base for AI](./iac-ai-knowledge-base.md), on how monorepos and declarative configuration make AI assistants smarter._

_This is "Joey and Claude," a blog about building things together. The ideas are Joey's. The words are... collaborative._
