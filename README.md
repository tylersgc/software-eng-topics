# Software Engineering Topics

Concise, quiz-friendly technical interview flashcards. **Source of truth: this GitHub repository.**

## Active subjects
- [System Design](system-design.md) — CAP, consistency and distributed trade-offs
- [Databases](databases.md) — ACID, isolation levels and serializability
- [AI](ai.md) — AI-assisted development and LLM inference

## Card format (v1)
Each subject file contains cards only. A card starts with a `Topic.Subtopic` path, a blank line, and a `## Title` heading. Cards are separated by a standalone `---` line.

Fields use `**field:**` labels. Required fields: `id`, `question`, `answer`. Optional fields: `mechanism`, `tradeoffs`, `example`, `caveat`, `related`, `source`. Multiline Markdown values (including lists) are allowed. A field ends at the next bold field label or card separator.

```md
Databases.Isolation

## Serializable Isolation

**id:** db-serializable

**question:** What is serializability?

**answer:** Concurrent committed transactions behave like some one-at-a-time order.

**related:** db-snapshot-isolation

---
```

IDs remain stable even if a card's title or topic moves, allowing quiz progress to persist. Quiz mode shows `path`, `title`, and `question` and hides the answer fields. Sources are for further reading, not a claim that a video transcript was verified.

The three subjects above are intentionally active at the user's request; future subjects should normally wait until an existing one is satisfactory.
