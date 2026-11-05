# LLM evaluation day

A small, runnable lab for deciding what to prototype before shipping an LLM feature. The project deliberately separates **measurement** (token counts, cost arithmetic, exact-match scores) from **claims about a particular model**. The included benchmark is deterministic and local; the optional API runner can measure a configured model when credentials are available.

## Run

Requires Python 3.10+. The attention example and local benchmark use only the standard library.

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m llm_lab all
```

Individual tasks:

```sh
python -m llm_lab task1
python -m llm_lab task2
python -m llm_lab task3
python -m llm_lab cost
python -m llm_lab limits
python -m llm_lab attention
python -m llm_lab prompts
```

Task 1's source-backed comparison and submission tables are in [TASK1.md](TASK1.md).
Task 2's qualitative results and safeguards are in [TASK2.md](TASK2.md).
Task 3's implementation and notes are in [TASK3.md](TASK3.md); Hugging Face inference needs model weights cached or downloadable.

`tiktoken` is used when installed to count GPT-family tokens. Without it, the cost demo reports a clearly labeled word-based estimate. Cost rates live in `config/pricing.json` and are examples only; check the provider pricing page and update them before budgeting. Tokenization of a string is not the same as full API usage: message framing, tools, cached tokens, and reasoning/output accounting can affect billed usage. API usage returned by the provider is the authoritative count.

## Tasks and decisions

1. **Token cost:** compare a short support prompt and a verbose version, calculate input/output cost for a sample workload, and show the prompt expansion tradeoff. Rates are configuration, not embedded facts.
2. **Limitations:** run local probes for underspecified input, arithmetic, untrusted instructions, and missing evidence. These are behavioral examples, not a universal model benchmark. Model behavior depends on prompt, model, and decoding settings.
3. **Transformer:** compute scaled dot-product self-attention with a tiny hand-readable input and show each intermediate matrix. This demonstrates one attention head; it is not a full transformer (no learned projections, residuals, normalization, or feed-forward layers).
4. **Prompt toolkit:** provide support-ticket triage and structured extraction prompts, plus fixed labeled examples and transparent scoring. The local baseline demonstrates schema/decision logic, not LLM quality. Replace it with the optional model adapter to evaluate a real system.

## Optional live evaluation

Set `OPENAI_API_KEY` in your shell and install dependencies. Then run `python -m llm_lab live`. This makes API calls and prints aggregate scores only; it does not print the key. The runner uses the Responses API and the model name in `config/pricing.json`. Live results incur usage charges and should be treated as a small smoke test, not a statistically meaningful evaluation.

## References

- [OpenAI API pricing](https://developers.openai.com/api/docs/pricing)
- [OpenAI tiktoken README](https://github.com/openai/tiktoken/blob/main/README.md)
- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)
