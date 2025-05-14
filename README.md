# model-quirks

Quirks, edge cases, and interesting bits of various models.

Related projects:

- [llm-info](https://github.com/paradite/llm-info): Information on LLM models, context window token limit, output token limit, pricing and more.
- [send-prompt](https://github.com/paradite/send-prompt): A unified TypeScript library for AI model interactions with standardized interfaces and function calling.
- [ai-file-edit](https://github.com/paradite/ai-file-edit): A library for editing files using AI models such as GPT, Claude, and Gemini.

## Claude 3.7 Sonnet

Max tokens for output

- Claude 3.7 Sonnet requires the `max_tokens` parameter to be set in the API request. [source](https://docs.anthropic.com/en/api/messages#body-max-tokens)
- Models may have different max output token limits, and an `invalid_request_error` error will be returned if the `max_tokens` parameter is higher than the model's max output token limit. So `max_tokens` needs to be set dynamically for each model to get the maximum output token limit for the model.

Tool use (function calling)

- Claude 3.7 Sonnet (and Claude 3.5 Sonnet) do not support calling the same tools multiple times in one response (for example, calling a file-edit tool to edit multiple times to edit multiple files). This is case even with `token-efficient-tools-2025-02-19` header.
- Anthropic has a [sample notebook implementation](https://github.com/anthropics/anthropic-cookbook/blob/main/tool_use/parallel_tools_claude_3_7_sonnet.ipynb) of "batch tool" that can act as a meta-tool to support parallel tool calls, but I have not verified if it works for calling the same tools multiple times.

## Gemini 2.5 Pro

Thinking budget

- While Gemini 2.5 Flash supports thinking budget, it is not currently available for Gemini 2.5 Pro as of May 12, 2025. [source](https://ai.google.dev/gemini-api/docs/thinking)

Multi-round tool use

- For multi-round tool use, Gemini 2.5 Pro requires the response from the previous tool call to be included in the messages. [source](https://ai.google.dev/gemini-api/docs/function-calling?example=meeting#step_4_create_user_friendly_response_with_function_result_and_call_the_model_again)
- Empirically, if the response from the previous tool call is not included, it will be stuck at the first tool call.

## Message Roles

- Google models accept "user" or "model" as roles. [source](https://ai.google.dev/gemini-api/docs/text-generation)
- OpenAI models accept "user", "assistant", and "developer" roles. [source](https://platform.openai.com/docs/guides/text?api-mode=chat)
- Anthropic models accept "user" and "assistant" roles. [source](https://docs.anthropic.com/en/api/messages)
