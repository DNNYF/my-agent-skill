# Platform Guide

How to deliver documentation to specific platforms. Each platform has different capabilities, constraints, and formatting rules. Always confirm the destination with the user before starting.

**Capability check reminder**: Before using any platform, verify the required MCP tools or skills are available. If they're not, tell the user immediately with specific alternatives. See the Capability Check Protocol in `SKILL.md`.

---

## Local Files (Always Available)

Local file output requires no special tools and works in every environment.

### Markdown (.md)

The default output format. Always available, always works.

- **Where to save**: Ask the user. Suggest `docs/` in the project root. If a `docs/` folder exists, use it. If not, ask before creating.
- **File naming**: Use lowercase kebab-case. `project-overview.md`, `technical-docs.md`, `handoff.md`. Never `Documentation.MD` or `Project Documentation.md`.
- **Images and diagrams**: Save in `docs/assets/` or `docs/images/`. Reference with relative paths: `![Architecture](./assets/architecture.png)`.
- **Mermaid diagrams**: Embed inline in markdown. Most viewers (GitHub, VS Code, Notion import) render them natively. If the user needs rendered images, check if `doc-output` skill is available for conversion.

### PDF

Requires the `pdf` skill or `doc-output` skill (for Markdown-to-PDF with Mermaid rendering).

**If available:**
- Generate Markdown first, then convert to PDF
- `doc-output` handles Mermaid diagram rendering into the PDF automatically
- Ask the user about page size (A4 vs Letter) and orientation

**If NOT available:**
```
I can't generate PDFs directly — the `pdf` skill isn't available.

Options:
1. I'll create a polished Markdown file — you can convert it with:
   - VS Code: "Markdown PDF" extension
   - CLI: `pandoc docs/overview.md -o docs/overview.pdf`
   - Online: paste into a tool like Dillinger or HackMD and export
2. I can create a Word doc instead (if `docx` skill is available)

Which works?
```

### Word / DOCX

Requires the `docx` skill.

**If available:**
- Ask about formatting preferences: formal (cover page, TOC, headers/footers) vs simple
- Ask about branding: company logo, colors, fonts
- Include page numbers and a table of contents for documents over 5 pages

**If NOT available:**
```
The `docx` skill isn't available, so I can't create Word documents directly.

Options:
1. I'll create Markdown — you can convert with Pandoc:
   `pandoc docs/report.md -o docs/report.docx`
2. I'll create Markdown — you can paste into Google Docs or Word and format there
3. I can try a different format (PDF, Notion page, etc.)

Which do you prefer?
```

### PowerPoint / PPTX

Requires the `pptx` skill.

**If available:**
- Follow the presentation structure from `references/document-types.md`
- Ask about: color theme, slide count preference, whether to include speaker notes
- Use the `pptx` skill's design guidance — no boring white slides with bullets
- Run the `pptx` skill's QA process (visual inspection via subagent) before delivering

**If NOT available:**
```
The `pptx` skill isn't available, so I can't create PowerPoint files directly.

Options:
1. I can create a Canva presentation instead (if Canva MCP is connected)
2. I'll create a Markdown outline with speaker notes — you can build slides from it
3. I'll create slide content as structured Markdown with clear slide boundaries

Which works for you?
```

### Excel / XLSX

Requires the `xlsx` skill. Relevant when documentation includes data tables, comparison matrices, or tracking sheets.

**If NOT available:**
```
The `xlsx` skill isn't available. I can include the data as:
1. A Markdown table in the document
2. A CSV file (plain text, opens in any spreadsheet app)

Which do you prefer?
```

---

## Notion

Requires Notion MCP tools (`notion_notion-search`, `notion_notion-create-pages`, `notion_notion-fetch`, `notion_notion-update-page`, etc.).

### Before Starting

1. **Check tools are available** — Look for `notion_*` tools in the environment
2. **Ask the user**:
   - Which workspace?
   - Which page should this go under? (Get a page ID or URL, or search by name)
   - Create a new page or update an existing one?

### Creating Notion Pages

**Search first**: Before creating, search for existing pages with similar names to avoid duplicates.

```
Before I create the page, let me check if one already exists...
[search]
I found "[Page Name]" last updated [date]. Should I update this page or create a new one?
```

**Page structure considerations**:
- Notion has its own rich text format. Write content in Notion-compatible markdown.
- Use headings (`#`, `##`, `###`) — they create a nice table of contents in Notion.
- Use toggle blocks (`<details>`) for collapsible sections — useful for long technical details.
- Use callout blocks for warnings, tips, and important notes.
- Tables render well in Notion — use them for structured data.
- Code blocks with language tags render with syntax highlighting.

**What Notion handles well**:
- Project overviews, technical docs, handoff documents, API docs
- Living documents that need frequent updates
- Team-collaborative documents

**What Notion handles poorly**:
- Pixel-perfect layouts (use Canva or PDF for that)
- Documents that need to be printed (use PDF or Word)
- Complex diagrams (Mermaid support is limited — consider embedding images)

### Organizing in Notion

Ask the user about page hierarchy:
```
Where should this page live in your Notion workspace?
1. Under an existing page (give me the page URL or name)
2. In a specific teamspace
3. At the workspace root
4. In a database (e.g., a "Documentation" database with properties)
```

### Updating Existing Notion Pages

When updating:
- Fetch the current content first with `notion_notion-fetch`
- Show the user what will change: "I'll update the Tech Stack section and add a new Deployment section. The rest stays the same. OK?"
- Use `update_content` command with `old_str`/`new_str` for targeted edits, not full page replacement
- Never silently delete content. If sections need removal, confirm with the user.

---

## Jira

Requires Jira MCP tools (`jira_*`). Jira is typically used for status reports, project tracking documentation, and linking docs to issues.

### Before Starting

1. **Check tools are available** — Look for `jira_*` tools in the environment
2. **Ask the user**:
   - Which Jira site? (cloud ID or site URL)
   - Confluence or Jira issues? (Confluence for docs, Jira for issues/tracking)
   - Which project or space?

### Confluence Pages (via Jira MCP)

Confluence is Jira's documentation platform. Use it for:
- Technical documentation
- Project overviews
- Meeting notes and decision records
- Architecture documents

**Creating pages**:
- Get the space ID first (`jira_getConfluenceSpaces`)
- Ask where in the page hierarchy it should go (parent page)
- Use markdown format for content (`contentFormat: "markdown"`)

**Content format notes**:
- Confluence supports markdown but renders it in its own format
- Use standard markdown headings, lists, tables, and code blocks
- For diagrams, describe them or reference attached images
- Confluence handles tables well — use them for structured data

### Jira Issues

Less common for documentation, but useful for:
- Creating documentation tasks ("Write API docs for v2 endpoints")
- Attaching status reports to existing epics
- Creating sub-tasks for documentation work items

**When creating issues for documentation work**:
- Use clear, actionable summaries: "Document payment integration API" not "Documentation"
- Add acceptance criteria: "API doc covers all /api/payments/* endpoints with request/response examples"
- Link to related code issues if applicable

---

## Canva

Requires Canva MCP tools (`canva-design_*`). Best for visually polished presentations and designed documents.

### Before Starting

1. **Check tools are available** — Look for `canva-design_*` tools in the environment
2. **Ask the user**:
   - What type of design? (presentation, document, infographic, social post)
   - Any brand kit to use? (Check with `canva-design_list-brand-kits`)
   - Audience and tone? (this affects design choices)

### Presentations via Canva

**When to choose Canva over PPTX**:
- User wants a polished, designed look without manual design work
- User's team already uses Canva
- The presentation will be shared as a link, not a downloaded file

**Creating presentations**:
1. Use `canva-design_generate-design` with `design_type: "presentation"`
2. Provide a detailed query — Canva's AI generates from your description
3. Ask the user to pick from generated candidates
4. Create the design with `canva-design_create-design-from-candidate`
5. Edit content with the editing transaction tools if needed
6. Export to desired format (`canva-design_export-design`)

**Brand consistency**:
- Check for brand kits first: `canva-design_list-brand-kits`
- If available, ask the user which brand kit to use
- Pass `brand_kit_id` to the generate tool

### Documents via Canva

Use Canva Docs (`design_type: "doc"`) for:
- Proposals with visual polish
- Reports that need to look designed
- Documents that will be shared as Canva links

### Exporting from Canva

Always ask the user what format they need the final output in:
- PDF — for sharing, printing, archiving
- PPTX — for editing in PowerPoint later
- PNG/JPG — for embedding in other docs or sharing slides as images

Check available export formats first with `canva-design_get-export-formats`.

---

## Multi-Platform Output

Sometimes the user needs the same content in multiple places. Handle this efficiently:

### Primary + Derivatives Strategy

1. **Ask**: "Which format is the primary one? I'll create that first, then derive others."
2. **Create the primary** in the richest format (usually Markdown — it's the most convertible)
3. **Derive others** from the primary:
   - Markdown → Notion page (near 1:1 mapping)
   - Markdown → PDF (via `pdf` or `doc-output` skill)
   - Markdown → Word (via `docx` skill)
   - Content → PPT/Canva (restructure for slides)
   - Content → Jira/Confluence (restructure for wiki format)

### Content Adaptation Rules

The same content needs different treatment on different platforms:

| Aspect | Long-form (Markdown, PDF, Word) | Slides (PPT, Canva) | Wiki (Notion, Confluence) |
|--------|------|--------|------|
| Depth | Full detail | Key points only | Moderate detail |
| Code blocks | Full snippets | Minimal, only key lines | Full snippets with syntax highlight |
| Diagrams | Embedded or linked | One per slide, simplified | Embedded or linked |
| Tables | Detailed, multi-column | Simple, max 3-4 columns | Detailed, sortable |
| Tone | Formal/technical | Conversational, punchy | Semi-formal, scannable |
| Length | No strict limit | 30 words max per slide | Scannable sections |

### Confirm Each Destination

For multi-platform output, confirm each destination separately:
```
You want this in Markdown + Notion + PPT. Let me confirm each:

1. Markdown: I'll save to `docs/project-overview.md` — OK?
2. Notion: I'll create a page under "[Parent Page]" in your workspace — OK?
3. PPT: I'll create via [Canva/pptx skill] with [color theme] — OK?

All good?
```

---

## Platform Decision Helper

When the user isn't sure which platform to use, help them decide:

| Need | Best Platform | Why |
|------|--------------|-----|
| Living document updated frequently | Notion or Confluence | Version history, easy editing, collaborative |
| Formal deliverable for client/stakeholder | PDF or Word | Professional, printable, attachable |
| Internal presentation | PPT or Canva | Visual, presentable, speaker notes |
| Developer reference | Markdown in repo | Lives with the code, versioned with git |
| Task tracking / sprint documentation | Jira | Integrated with development workflow |
| Visually designed report/proposal | Canva | Professional design without design skills |
| Quick internal update | Notion or Markdown | Fast to create, easy to share link |

Ask:
```
A few questions to pick the right format:
1. Will this be updated regularly, or is it a one-time deliverable?
2. Does it need to look designed/polished, or is content more important?
3. Who will read it — developers, stakeholders, clients, or mixed?
4. Does it need to be printable or emailable as an attachment?
```
