# Product Architect & UI/UX Agent Skills

A collection of portable, highly-specialized AI agent skills designed for end-to-end product development. From product architecture and frontend design to project documentation, these skills enforce **Zero AI Slop**, **Research-First thinking**, and **Confirm-Before-Acting protocols**.

Designed for developers who "vibecode" and need an AI that works like a senior engineer, not a generic template filler.

## About The Skills

### 1. Product Architect
Turns raw ideas into buildable product blueprints using a rigorous 5-phase thinking framework.
- **Triggers**: "plan a product", "create a PRD", "architect a system", "plan MVP"
- **Output**: Product Overview, User Personas, User Flows, PRD, Technical Architecture, Data Models, API Specs, Implementation Roadmaps.

### 2. Frontend Design
Builds distinctive, production-grade frontends with a strict "design system first" approach. Actively avoids the "default AI SaaS aesthetic" (e.g., generic indigo/purple interfaces).
- **Triggers**: "build a frontend", "create a UI", "design a page", "style this component"
- **Output**: Design tokens, component patterns, responsive asymmetric layouts, custom animation tokens.

### 3. Project Documenter
Reads a codebase to understand what was actually built, then produces documentation that reads like it was written by the lead engineer.
- **Triggers**: "document this project", "create project docs", "write a POC report", "write a handoff document"
- **Output**: 9 different document types including Markdown docs, PDFs, Word, PPT presentations, and Changelogs. Includes a strict multi-step confirmation protocol before writing.

---

## Installation

These skills use progressive disclosure (a lean `SKILL.md` + detailed `references/` folder) to optimize context windows while providing deep knowledge when needed.

### For OhMyOpenCode (Recommended)
Copy the skill folders to your OpenCode skills directory:
```bash
# User-level (available across all projects)
cp -r product-architect/ ~/.config/opencode/skills/product-architect/
cp -r frontend-design/skill/ ~/.config/opencode/skills/frontend-design/
cp -r project-documenter/ ~/.config/opencode/skills/project-documenter/
```

### For Claude Code
Copy the skill folders into your local Claude plugin or global skills directory:
```bash
cp -r product-architect/ ~/.claude/skills/product-architect/
cp -r frontend-design/skill/ ~/.claude/skills/frontend-design/
cp -r project-documenter/ ~/.claude/skills/project-documenter/
```

### For Cline / Cursor / Continue
These editors do not yet have a native multi-file skill system. You can load these skills by copying the contents of the `SKILL.md` file into your system prompt, custom rules file (e.g., `.cursorrules`), or directly into the chat context. 

If the agent needs more context for a specific task, manually paste the contents from the relevant files inside the `references/` directory.

### For Codex CLI
Append the core skills to your `AGENTS.md` knowledge base:
```bash
cat product-architect/SKILL.md >> AGENTS.md
cat frontend-design/skill/SKILL.md >> AGENTS.md
cat project-documenter/SKILL.md >> AGENTS.md
```
