# Transformer training and inference

本文保留为运行过程入口。翻译 Encoder-Decoder 与 decoder-only LLM 的训练、推理分别拆分，避免将不同架构的运行规则混在同一页。

| 主题 | 文档 | 关键问题 |
| --- | --- | --- |
| 原论文训练 | [Transformer 训练](transformer_training.md) | 右移目标序列、teacher forcing、cross-attention、loss 和反向传播如何工作？ |
| 原论文推理 | [Transformer 推理](transformer_inference.md) | Encoder memory、cross-attention、beam search 与自回归生成如何工作？ |
| LLM 训练 | [Decoder-only LLM 训练](decoder_only_llm_training.md) | 整段序列、标签遮罩、causal loss 与参数更新如何工作？ |
| LLM 推理 | [Decoder-only LLM 推理](decoder_only_llm_inference.md) | prefill、decode、KV cache 与采样如何工作？ |

若目标是理解现代 LLM，建议先阅读 [Decoder-only LLM 总览](decoder_only_llm.md)，再按“训练 -> 推理”的顺序继续。

[返回学习路线](transformer_prerequisites.md)
