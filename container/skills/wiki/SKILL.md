# Wiki Skill

This skill governs Jarvis's role as maintainer of Jose's personal identity wiki — a persistent, compounding knowledge base about who Jose is, what he cares about, and how he operates. The wiki makes Jarvis a progressively better assistant over time.

## Paths (inside container)

- `/workspace/group/wiki/` — the wiki (LLM-owned markdown)
- `/workspace/group/sources/` — raw sources (never modify these)
- `/workspace/group/wiki/index.md` — catalog of all pages
- `/workspace/group/wiki/log.md` — append-only activity log

## Structure

```
wiki/
  index.md              # master catalog
  log.md                # activity log
  identity/
    core.md             # personality, values, worldview
    communication-style.md
  interests/index.md    # topics, hobbies, media, professional interests
  concerns/index.md     # worries, frustrations, challenges
  goals/index.md        # short and long-term goals
  context/life.md       # work, living situation, routines, history
  patterns/preferences.md  # habits, preferences, decision-making style
  relationships/index.md   # key people and organizations
sources/
  (raw source files — immutable)
```

## Operations

### Ingest

When Jose provides a source (URL, PDF, image, voice note, conversation transcript, file, or anything else):

1. **Read the source fully.** For URLs, download the full content — don't rely on summaries:
   ```bash
   curl -sLo sources/<filename> "<url>"
   ```
   For webpages, use `agent-browser` to extract full text. For PDFs, use the pdf-reader skill. For images, view them directly. For voice notes, transcription is automatic.

2. **Discuss with Jose.** Share your key takeaways and what you plan to update. This is collaborative — Jose's comments during ingestion are part of the source material too.

3. **Update all relevant wiki pages.** A single source typically touches 5–15 pages. For each affected page:
   - Add new information, integrate with existing content
   - Update cross-references to other pages
   - Flag contradictions or changes vs. previous understanding (don't silently overwrite — note the evolution)
   - Create new pages if a topic warrants it

4. **Update index.md.** Add any new pages. Update summaries for significantly changed pages.

5. **Append to log.md:**
   ```
   ## [YYYY-MM-DD] ingest | <source title or description>
   Pages updated: identity/core.md, interests/index.md, ...
   Key takeaways: <2-3 bullet points>
   ```

**Ingest discipline:** Process one source at a time, completely, before moving to the next. Never batch-read multiple sources and then process them together — this produces shallow, generic wiki pages. Each source deserves its own full pass: read → discuss → update wiki → update index → log.

### Query

When Jose asks a question that the wiki can help with:

1. Read `wiki/index.md` first to locate relevant pages.
2. Read the relevant pages.
3. Synthesize an answer with citations to wiki pages.
4. If the answer is substantial or reusable, offer to save it as a new wiki page.
5. Append to log.md:
   ```
   ## [YYYY-MM-DD] query | <question summary>
   Pages consulted: ...
   New page created: <if applicable>
   ```

### Lint

Periodic health check (or run on request):

1. Read `wiki/log.md` to understand recent activity.
2. Read `wiki/index.md` to get the full picture.
3. Spot-check pages for:
   - Contradictions between pages
   - Stale content superseded by newer sources
   - Orphan pages (no inbound links from other pages)
   - Important topics without dedicated pages
   - Missing cross-references
   - Sections still showing "_Populated as sources are ingested._"
4. Report findings and offer to fix issues.
5. Log the lint pass.

## Conventions

- **Never modify `sources/`** — raw sources are immutable records
- **Cross-link liberally** — use `[page title](relative/path.md)` within wiki pages
- **Note contradictions explicitly** — prefix with `> ⚠️ Contradiction with [page](path.md): ...`
- **Date significant updates** — `_Updated: YYYY-MM-DD_` at the top of heavily-revised pages
- **Create new pages freely** — if a topic grows beyond a section, it earns its own page and an entry in index.md
- **The wiki is about Jose** — write in third person ("Jose prefers...", "Jose tends to...") so it reads as reference material, not conversation
