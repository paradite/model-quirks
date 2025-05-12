# model-quirks

Quirks and interesting bits of various models

## Claude 3.7 Sonnet

Tool use (function calling)

- Claude 3.7 Sonnet (and Claude 3.5 Sonnet) do not support calling the same tools multiple times in one response (for example, calling a file-edit tool to edit multiple times to edit multiple files). This is case even with `token-efficient-tools-2025-02-19` header.
- Anthropic has a [sample notebook implementation](https://github.com/anthropics/anthropic-cookbook/blob/main/tool_use/parallel_tools_claude_3_7_sonnet.ipynb) of "batch tool" that can act as a meta-tool to support parallel tool calls, but I have not verified if it works for calling the same tools multiple times.

## Gemini 2.5 Pro

Thinking budget

- While Gemini 2.5 Flash supports thinking budget, it is not currently available for Gemini 2.5 Pro as of May 12, 2025. [source](https://ai.google.dev/gemini-api/docs/thinking)

## Message Roles

- Google models accept "user" or "model" as roles. [source](https://ai.google.dev/gemini-api/docs/text-generation)
- OpenAI models accept "user", "assistant", and "developer" roles. [source](https://platform.openai.com/docs/guides/text?api-mode=chat)
- Anthropic models accept "user" and "assistant" roles. [source](https://docs.anthropic.com/en/api/messages)
