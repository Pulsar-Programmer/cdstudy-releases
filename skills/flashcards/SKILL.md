---
name: flashcards
description: "Turn a topic or notes into a flashcard set in your Library."
---

Turn the given topic or notes into flashcards grounded in the course material. Ask for a topic if unclear.
Test one fact per card: short fronts (a word, a code, a question), full answers on the back; keep essential formulas.
Save them to the Library's Flashcards app (slug `memory`): read its data shape with t3_library_list (includeApps, app), then add one set per topic with t3_library_create, or add the set to an existing Flashcards item with t3_library_read and t3_library_update.
Show the result with t3_library_open. If the Library tools are unavailable, list the cards as front → back.
