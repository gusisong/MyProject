# Spec: Context Optimization & Memory Continuity Strategy

## 1. Context
The user prefers deep, continuous discussions within a single topic but faces high token usage per session (Avg 255K). To maintain memory without context overflow, a hybrid approach is adopted.

## 2. Optimization Strategy (A+B)

### Scheme A: MCP Memory Integration (Long-term)
- **Mechanism**: Use `memory` MCP server to store architectural decisions, user preferences, and project-specific conventions.
- **Action**: Any significant design choice must be mirrored to the memory graph.

### Scheme B: Document-Driven Development (Short-term)
- **Mechanism**: Every major feature or refactor MUST start with a design spec in `docs/superpowers/specs/`.
- **New Session Workflow**: When starting a new session, the agent should first read the latest relevant Spec/Plan to "rehydrate" context efficiently.

## 3. Current Implementation Status
- **Compression Model**: `github-copilot/gpt-5-mini` set as `small_model` for background tasks.
- **Memory Snapshot**: Initial entities (Preferences, Configs) created in MCP Memory.
- **Workflow Standard**: Adopted "Spec-First" principle.

## 4. Maintenance
- Monthly cleanup of stale memory entities.
- Archiving completed session specs to a `history/` sub-directory.
