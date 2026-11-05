# Task 3 — Transformer Concepts in Code

Implementation: [llm_lab/task3.py](llm_lab/task3.py), using NumPy for attention and positional encodings. Run `python -m llm_lab task3`. The positional plot is saved to [artifacts/positional_encoding.png](artifacts/positional_encoding.png). Use `python -m llm_lab.task3 --skip-hf` to run the numerical demos without model downloads.

## Single-head self-attention

The example has four token vectors (`X` is 4×4), and fixed projection matrices produce `Q`, `K`, and `V`. It computes:

`Scores = Q @ K.T / sqrt(d_k)`  
`Weights = softmax(Scores, axis=-1)`  
`Output = Weights @ V`

Printed attention weights:

```text
[[0.1571 0.2589 0.1571 0.4269]
 [0.0009 0.9457 0.0471 0.0064]
 [0.0333 0.6684 0.1491 0.1491]
 [0.0333 0.6684 0.1491 0.1491]]
Row sums: [1. 1. 1. 1.]
All rows sum to 1: True
```

## Two-head attention

Two separate 2D query/key/value projections each produce a 4×2 output. Concatenating the heads gives a 4×4 matrix, then `W_o` (4×4) projects it back to width four. The script prints the head row sums, concatenation and projection shapes, and the projected output. Both heads’ four rows each sum to one.

## Sinusoidal positional encoding

The function implements `PE(pos, 2i) = sin(pos / 10000^(2i/d))` and `PE(pos, 2i+1) = cos(pos / 10000^(2i/d))` for 10 positions and 8 dimensions. The generated matrix is 10×8; lower dimensions vary faster, while higher dimensions vary more slowly.

![Sinusoidal positional encoding heatmap](/Users/vicky/Desktop/Work/YOHO/04/artifacts/positional_encoding.png)

## Hugging Face examples

The code uses the Hugging Face `pipeline` interface and these public pretrained models:

- Fill mask: `google-bert/bert-base-uncased`, prompt `The capital of France is [MASK].`, top 3 predictions. [Model card](https://huggingface.co/google-bert/bert-base-uncased)
- Text generation: `openai-community/gpt2`, prompt `Once upon a time`, greedy decoding for 24 new tokens. [Model card](https://huggingface.co/openai-community/gpt2)

I attempted both demonstrations. The environment could not resolve/retrieve model files from Hugging Face, and the weights were not cached, so **pretrained inference outputs could not be recorded here**. The runnable calls are implemented and report the failure separately per model; run `python -m llm_lab task3` on a network-enabled machine to complete this part. The NumPy and plot portions ran successfully in this workspace.

## Encoder-only vs decoder-only (six lines)

1. Encoder-only models (such as BERT) use bidirectional attention, so each token can use context on both its left and right.
2. They are strong for understanding tasks such as classification, retrieval, and masked-token prediction.
3. Decoder-only models (such as GPT-2) use causal masks, so each token can attend only to earlier tokens.
4. This makes them naturally suited to next-token prediction and autoregressive text generation.
5. Transformers replaced many RNN workloads because attention processes sequence positions in parallel during training instead of stepping through tokens one at a time.
6. Attention also connects distant tokens directly, improving long-range dependency handling; its memory and compute can grow quadratically with sequence length.
