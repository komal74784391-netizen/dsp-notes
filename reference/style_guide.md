# Code Reference — formatting rules

These rules apply to `reference/code_reference.md`, the code companion to the
DSP course deck.

1. The file starts with the title line `# DSP Code Reference`.
2. Create exactly one section for every slide of the deck that embeds a code
   snippet; slides without code must not appear.
3. Sections are ordered by ascending slide number (1-based).
4. The heading of each section is written as `### Slide <number>` — three
   number signs, the word `Slide`, a space, then the slide number.
5. Immediately below the heading there is one fenced code block opened with
   three backticks followed by `python`.
6. The code block reproduces the snippet from the deck verbatim, character for
   character: every Greek letter, arrow, subscript, superscript, operator and
   space must match the deck exactly.
7. After the code block, write one explanation paragraph of at least 40
   characters describing what the snippet computes. It should read as a short
   note to a colleague, not as a restatement of the code.
8. Do not add commentary inside the code block and do not reformat indentation.
