# model-quirks

Quirks, edge cases, and interesting bits of various models.

Related projects:

- [llm-info](https://github.com/paradite/llm-info): Information on LLM models, context window token limit, output token limit, pricing and more.
- [send-prompt](https://github.com/paradite/send-prompt): A unified TypeScript library for AI model interactions with standardized interfaces and function calling.
- [ai-file-edit](https://github.com/paradite/ai-file-edit): A library for editing files using AI models.
- [16x Eval](https://eval.16x.engineer): Your personal workspace for prompt engineering and model evaluation.

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

- Google models accept `user` or `model` as roles. [source](https://ai.google.dev/gemini-api/docs/text-generation)
- OpenAI models accept `user`, `assistant`, `system` and `developer` roles. [source](https://platform.openai.com/docs/guides/text?api-mode=chat)
  - OpenAI recommends using `developer` role in favor of `system` role for o1 models and newer. [source](https://platform.openai.com/docs/api-reference/chat/create#chat-create-messages)
- Anthropic models accept `user` and `assistant` roles. [source](https://docs.anthropic.com/en/api/messages)

## Reasoning content and tags

DeepSeek-R1 and Qwen3 both use `<think>` and `</think>` tags to denote reasoning content. [DeepSeek-R1 source](https://huggingface.co/deepseek-ai/DeepSeek-R1) [Qwen3 source](https://qwenlm.github.io/blog/qwen3/)

Different providers have different ways of providing reasoning content.

- DeepSeek providers reasoning content is in the `reasoning_content` field. [source](https://api-docs.deepseek.com/guides/reasoning_model)
- OpenRouter providers reasoning content is in the `reasoning` field. [source](https://openrouter.ai/docs/use-cases/reasoning-tokens)
- Fireworks do not expose separate reasoning field, the reasoning content is within the message content. [source](https://docs.fireworks.ai/api-reference/post-chatcompletions)

## Reasoning token count from OpenRouter

To get reasoning token count from OpenRouter, you need to pass `usage: { include: true }` to the API request.

This works in OpenAI TypeScript SDK as long as you use `any` type to avoid type errors. [source](https://openrouter.ai/docs/use-cases/usage-accounting)

```ts
const openaiResponse = await openai.chat.completions.create({
  model,
  messages,
  usage: {
    include: true,
  },
} as any);
```

## Temperature and sampling parameters

Temperature range supported is different for different providers.

- OpenAI models support temperature range of `[0, 2]`. [source](https://platform.openai.com/docs/api-reference/responses/create#responses-create-temperature)
- Anthropic Claude models support temperature range of `[0, 1]`. [source](https://docs.anthropic.com/en/api/messages#body-temperature)
- Google Gemini models support temperature range of `[0, 2]`. [source](https://ai.google.dev/api/generate-content#generationconfig)

Temperature and sampling parameters are not supported by all models.

- Some OpenAI models (e.g. `o3-mini` and `codex-mini-latest`) do not support temperature (and other sampling parameters like `top_p`, `top_k`, etc.), sending a temperature value will result in an error. [source](https://github.com/openai/openai-python/issues/2072)
- Vercel AI SDK will always send a temperature value (default to `0` if you don't specify) to the models, even if the model does not support it. [source](https://ai-sdk.dev/docs/ai-sdk-core/settings#temperature) [issue](https://github.com/vercel/ai/issues/5609) [source code](https://github.com/vercel/ai/blob/05c2da505b4564074456ab9c544c9266cdd2244a/packages/ai/core/prompt/prepare-call-settings.ts#L101C35-L101C35)
