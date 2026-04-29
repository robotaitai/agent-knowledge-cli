Save project memory and reset the context window.

Steps:

1. Run in terminal: `bedrock sync --project .`

2. Review this session's work and identify what stable project knowledge changed.
   For each changed area, update the relevant `./bedrock/Memory/<branch>.md`:
   - Edit the Current State section with confirmed facts
   - Add a dated entry to Recent Changes: `YYYY-MM-DD -- what changed`

3. If branch summaries changed, update `./bedrock/Memory/MEMORY.md`.

4. Summarize in one sentence what was saved and what was skipped.

5. Start a new chat to continue working with a clean context window.
