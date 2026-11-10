# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A fictitious-bank internal IT PMO Kanban board (demo/training tool). The whole app is one file, `index.html`, containing markup, a `<style>` block and a `<script>` block. There is no build, lint or test tooling; run it by opening the file directly (`file://`) in a browser. `PDFs/` and `Prompts.txt` are course material, not part of the app.

## Hard constraints (from the original spec)

- Vanilla HTML/CSS/JS only: no frameworks, bundler, npm, CDN scripts, web fonts or image files. System font stack and inline SVG/Unicode for icons.
- No persistence of any kind (no localStorage, sessionStorage, IndexedDB, cookies). A refresh resets to seeded data; the UI says so.
- Only backend is FormSubmit's AJAX endpoint (`FORMSUBMIT_ENDPOINT`, a config constant at the top of the script; the email address appears nowhere else). A FormSubmit failure must never break the board; the card stays and a warning toast shows.
- No UOB logo/branding; text wordmark "IT PMO" and blue palette only. The `UOB-ITPM-####` task ID format and `[UOB IT PMO]` email subject are intentional.
- CSS uses custom properties for palette/spacing and no `!important`.

## Architecture

Single source of truth is `state = { tasks: [], filters: {} }`. Every change mutates `state` (or the transient `confirmDeleteId` / `draggedId`) and then calls `renderBoard()`, which rebuilds all columns, cards and the header summary via `innerHTML`. Do not mutate card DOM directly elsewhere.

- All user-supplied strings must pass through `escapeHtml()` before going into template strings.
- Interaction uses event delegation on `#board` (`data-action` attributes for delete confirm / move select; HTML5 `dragstart/dragover/drop` for drag between columns). Because the board re-renders wholesale, listeners must stay on the stable `#board` container, not on cards.
- Task flow: `validateForm()` -> `addTask()` (optimistic add + render) -> `notifyNewTask()` in parallel inside try/catch. IDs come from a monotonically increasing `idCounter` via `nextId()`.
- Overdue = `status !== "Done"` and ISO due date earlier than today (string comparison of `YYYY-MM-DD`). Seed due dates are offsets from today so demo overdue items stay overdue.
- Delete uses an inline "Delete? Yes / No" toggle, never `confirm()`; validation errors are inline, never `alert()`.
