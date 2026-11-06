---
name: expand
description: "Open a separate study thread for each curriculum item you choose."
---

Create one study thread for each specified curriculum item.
If no items are given, find the curriculum in this chat or the course project, then ask the student to select items using a selection tool, or a numbered list if unavailable. Wait for their choice.
Use create_threads / t3_thread_launch or the available equivalent; title each thread extremely concisely as course, topic number, and a short concept name, e.g. "Chem 4.1 Concept A".
Give each thread a starting prompt to teach that item, with its scope and relevant course file paths so it can stand alone.
Return links to the created threads. If thread creation is unavailable, provide ready-to-copy starting prompts instead.
