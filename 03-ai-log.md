# 03 - AI Log (Personal Reflection)

Name: [Your Name]

## AI helped with
- Brainstorming Quick Cards and structure for deep-dive
- Drafting the SYSTEM_PROMPT and test harness for prompt boundary checks

## AI mistakes (hallucinations / unsafe suggestions)
- Example: LLM might suggest routing to a distant station even if battery critical — unsafe

## How I adjusted prompts
- Added strict directive to always include [DRAFT_ONLY]
- Added explicit rule: if battery < 5% -> dispatch mobile charger and include JSON action

## Short reflection
Using a mixture of rules and LLM text-generation allows useful flexibility while keeping safety-critical behavior deterministic.
