# 机器学习基础：从文本到概率

[返回学习路线](transformer_prerequisites.md) | [下一篇：Transformer 架构](transformer_architecture.md)

本文只介绍理解 Transformer 所需的通用输入、表示和预测概念，不讨论具体 Encoder 或 Decoder 层。

## 文本、token 与 token id

模型不能直接处理字符串。文本先由 tokenizer 切分为 token，再由词表映射为 token id。

| 阶段 | 示例 | 含义 |
| --- | --- | --- |
| 文本 | `I love cats` | 人类可读字符串。 |
| token | `["I", "love", "cat", "s"]` | 词、子词或特殊符号。 |
| token id | `[31, 842, 5179, 98]` | token 在词表中的整数索引。 |

token id 不包含语义；它只是“在词表和向量表中查找第几行”的地址。

### 特殊 token

| token | 全称 | 作用 |
| --- | --- | --- |
| `<bos>` | Beginning Of Sequence | 表示生成序列开始。 |
| `<eos>` | End Of Sequence | 表示生成序列结束。 |
| `<pad>` | Padding | 将同一 batch 中不同长度的序列补齐。 |

`<pad>` 通常由 padding mask 忽略；`<bos>` 和 `<eos>` 定义生成的起点和终点。

## Embedding：将离散 id 转为连续向量

Embedding 是将离散对象表示为连续向量的方法。输入层通常维护可训练矩阵：

```text
E: [vocab_size, d_model]
```

token id 为 `31` 时，embedding lookup 取出 `E[31]`：

```text
31 -> E[31] -> [0.12, -0.08, 0.44, 0.30]
```

它可等价写为 `one_hot(31) @ E = E[31]`，但实际实现直接索引，不构造稀疏 one-hot 向量。

| 概念 | 是否可学习 | 说明 |
| --- | --- | --- |
| token | 否 | 离散符号。 |
| token id | 否 | 词表索引。 |
| token embedding | 是 | token 对应的一行向量参数。 |
| contextual embedding | 是 | 经过模型上下文计算后的向量。 |

embedding 表随机初始化，随后由损失函数的梯度更新：

```mermaid
flowchart LR
    A["随机初始化"] --> B["前向预测"]
    B --> C["计算损失"]
    C --> D["反向传播"]
    D --> E["更新 embedding 表"]
    E --> B
```

## 位置编码：表示顺序

token embedding 表示“是什么”，但不表示“在哪里”。位置编码提供位置信息：

```text
input[pos] = token_embedding[token_id] + positional_encoding[pos]
```

原始 Transformer 采用固定正弦/余弦位置编码：

```text
PE(pos, 2i)     = sin(pos / 10000^(2i / d_model))
PE(pos, 2i + 1) = cos(pos / 10000^(2i / d_model))
```

后续模型也常使用可训练位置 embedding；两种方案都要生成与 token embedding 同维的位置向量。许多 decoder-only LLM 则采用 RoPE：它不在输入处相加位置向量，而是在 Q/K 投影后通过旋转写入位置信息。详见 [RoPE：旋转位置编码](rotary_position_embedding.md)。

## Softmax 与交叉熵

logits 是任意实数；softmax 将它们转换为非负且总和为 1 的概率：

```text
softmax(z_i) = exp(z_i) / sum_j(exp(z_j))
```

训练时，交叉熵衡量预测分布与真实 token 的差异：

```text
loss = -log p(真实 token)
```

预测越接近真实 token，loss 越小；反向传播据此更新 embedding 和其他参数。

## 下一步

[返回学习路线](transformer_prerequisites.md) | [下一篇：Transformer 架构](transformer_architecture.md)
