# Task 1 — Models, Tokens and Cost

Model specs change, and context sizes are model-specific. This comparison uses one current representative from each family (checked 5 Nov 2026). “Open weights” means weights can be obtained and run by users; it does not mean the license is unrestricted open source.

## Family comparison

| Family / representative | Owner | Weights | Context window | Strengths (provider-described) |
|---|---|---|---:|---|
| GPT — GPT-6 Luna | OpenAI | Closed; hosted API | 1.05M tokens | Efficient for focused, high-volume tasks; text and image input, multilingual support, and tool use. [Official model catalog](https://developers.openai.com/api/docs/models) |
| Claude — Claude Sonnet 5.5 | Anthropic | Closed; hosted API and selected cloud platforms | 1M tokens | Speed/intelligence balance; text and image input, multilingual capability, vision, and tool use. [Official models overview](https://platform.claude.com/docs/en/models/overview) |
| Llama — Llama 4 Scout | Meta | Open weights under Meta Llama 4 Community License (review terms for intended commercial use) | 10M tokens | Multimodal text/vision and long-context capability; 17B active parameters in a 109B-parameter mixture-of-experts model. [Official model page](https://ai.meta.com/resources/models-and-libraries/llama-downloads/) |
| Gemini — Gemini 3.8 Flash | Google | Closed; hosted API | 1,048,576 tokens | Long-horizon software engineering, autonomous agents, enterprise workflows, multimodal inputs, and structured outputs. [Official model page](https://ai.google.dev/gemini-api/docs/models/gemini-3.8-flash) |

These are the context limits published for the named models, not family-wide guarantees. Hosted service limits, output caps, and available features can differ by endpoint and change over time.

## Token counts

The executable source is `llm_lab/task1.py`. It uses `tiktoken` with `cl100k_base` for all samples, including Hindi. “Words” means whitespace-separated units, so the ratio is a rough descriptive measure rather than a linguistic word segmentation score. Counts can differ from provider-billed usage because request framing and modality-specific accounting are not included. The tokenizer choice follows the [tiktoken README](https://github.com/openai/tiktoken).

| Sample | Words | Tokens | Words/token | Fits 8k? | Fits 128k? |
|---|---:|---:|---:|:---:|:---:|
| Sentence | 11 | 12 | 0.917 | Yes | Yes |
| Paragraph | 99 | 115 | 0.861 | Yes | Yes |
| Page (671-word support operations page) | 671 | 780 | 0.860 | Yes | Yes |
| JSON ticket record | 13 | 74 | 0.176 | Yes | Yes |
| Hindi sentence | 16 | 86 | 0.186 | Yes | Yes |

All five samples individually fit within both limits. A context window is shared by the complete request (system instructions, conversation history, retrieved context, current input, and generated output), so these sample-only checks do not guarantee that a full conversation fits.

## Cost estimate

Function used: `estimate_cost(input_tokens, output_tokens, price_in, price_out) = (input_tokens × price_in + output_tokens × price_out) / 1,000,000`, where prices are USD per million tokens.

For a concrete rate, the calculation uses GPT-6 Luna standard API rates from [OpenAI’s official model catalog](https://developers.openai.com/api/docs/models): **$0.10/M input** and **$0.50/M output**. Assumptions: 1,000 chats/day, 1,500 input tokens/chat, 400 output tokens/chat, and 30 days/month; excludes tools, taxes, retries, cached-input discounts, and infrastructure.

- Per chat: `(1,500 × $0.10 + 400 × $0.50) / 1,000,000` = **$0.00035**.
- Per day: 1.5M input + 0.4M output tokens = **$0.35/day**.
- 30-day month: 45M input + 12M output tokens = **$10.50/month**.

## Recommendations (five lines)

1. **Public customer-support bot: GPT-6 Luna via the hosted API.**
2. It is positioned for efficient, high-volume tasks such as triage and routine responses, with tool support for approved knowledge lookup and actions.
3. Keep customer data handling, escalation, grounded answers, and abuse testing in the application; a model choice alone does not make the bot safe.
4. **Private on-premise tool: Llama 4 Scout**, because Meta makes its weights available for local deployment under its community license.
5. This gives the operator control over hosting and data flow, at the cost of GPU/infrastructure work and responsibility for license review, security, and evaluation.

## Reproduce

```sh
python -m llm_lab.task1
```
