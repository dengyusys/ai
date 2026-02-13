---
name: teaching-session-recorder
description: Record teaching conversations to markdown files in knowledge/learn-english/. Use when user says "record chat", "save conversation", "记录对话", or "保存对话".
---

# Teaching Session Recorder

Saves teaching conversations to daily markdown files for review and reflection.

## Instructions

When the user requests to record the conversation:

1. Extract the last 10 conversation rounds (student and teacher messages)
2. Get current date and time
3. Format as markdown with timestamp
4. Save to `knowledge/learn-english/chat-YYYY-MM-DD.md`
   - If file doesn't exist: create new file with header
   - If file exists: append with separator

## File format

```markdown
# Chat 2026-02-08

## [14:30]

S: How do I use present perfect tense?

T: Present perfect shows past actions affecting the present...

S: Can you give an example?

T: Sure, "I have finished my homework" means...

---

## [16:45]

S: What about past perfect?

T: Past perfect uses had + past participle...

---
```

## Formatting rules

- Use `S:` for student messages
- Use `T:` for teacher messages  
- Separate conversation segments with `---`
- Add timestamp `[HH:MM]` for each segment
- Record last 10 rounds of conversation

## Response after saving

Confirm with simple message:
- First save of the day: "✅ 已保存到 knowledge/learn-english/chat-2026-02-08.md"
- Additional saves: "✅ 已追加到 knowledge/learn-english/chat-2026-02-08.md"

Keep response brief - just confirm the save.

## Examples

**User says**: "记录对话"
**Action**: Save immediately, no confirmation needed

**User says**: "save this chat"  
**Action**: Save immediately, no confirmation needed

## Notes

- No need to ask for confirmation - save directly
- No need to generate summaries - the conversation itself is the record
- Keep the format simple for easy manual editing
- Same-day conversations append to the same file
