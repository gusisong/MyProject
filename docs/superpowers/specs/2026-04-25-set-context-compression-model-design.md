# Spec: Set Global Context Compression Model

## 1. Goal
Set `github-copilot/gpt-5-mini` as the global default model for context compression and other background tasks (summarization, title generation) in OpenCode.

## 2. Changes
- Modify `~/.config/opencode/opencode.json` to include the `small_model` field.

## 3. Impact
- OpenCode will use GPT-5-mini for lightweight, non-primary generation tasks.
- Improved efficiency and reduced token usage for main models by offloading metadata tasks.

## 4. Verification
- Verify the existence of the key in `opencode.json`.
