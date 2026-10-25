# Key Building Blocks of an LLM (Large Language Model)

A Large Language Model (LLM) is an AI model trained on large amounts of text and other data to understand and generate human-like language. Examples include GPT, Claude, and Llama.

To understand how an LLM works, break it down into its major building blocks, from text processing to model training and response generation.

## 1. LLM architecture at a glance

1\. Input Text

User prompt or question

2\. Tokenization & Embeddings

Convert text into numeric representations

3\. Transformer Architecture

Attention, neural network layers and learned parameters

4\. Output Probabilities

Calculate likely next tokens

5\. Text Generation

Generate the response token by token

## 2. The 8 key building blocks

[LLMs from the Inside 1: Tokenization | by John the Quant | Medium](https://images.openai.com/static-rsc-4/O2VX0OSGRHaCglf3DCuAwYBsM0GzKSQbR1GU2v4uKmXt_lvgCFVxLw6poB92N44yUrPwReXiIMxUlAYANysvHTKZoMEJ2FPRvAKKhdLJXQEZIx5UkUW5eHny0wwn5JbDZQXJMQklciETi6FTzG_NCyDnlU45DBzoafZUd5CIUiw?purpose=inline)

1\. Tokenizer

Converts text into smaller units called tokens. A token can be a word, part of a word, punctuation, or another text unit.

Example: `DevOps is amazing` → token IDs representing those text pieces.

[Tokens And Embeddings: The Foundation of Language Models. | by Lokesh Gaur | Jan, 2026 | Medium](https://images.openai.com/static-rsc-4/lZSn2CBAn86GyHK_pyeNNlUNlJGSfZFkh0f_AgzHZ8CBPNsPK5hiPUuWIV8C7t668uiygpwGxiDarfxH3AQfJabpT8_bkjv0QIwdmZc5pnqyeECaYXFBP39PLn4DJCopHDOA6yDZiY18MU13mqsu5AjyVVvvuPva5hatLlKpZ8o?purpose=inline)

2\. Embeddings

Convert token IDs into numerical vectors that the model can process. These vectors help represent learned patterns and relationships between tokens.

Example: Related concepts such as Kubernetes and containers may have related representations.

[What is Transformers in Deep Learning: A Guide 2026](https://images.openai.com/static-rsc-4/xO4fPox9ELTrYHdqurOpfbPtoHytHgEgmh5g8rkWfhUFfpkH9hgzv4zmeWEnGHh6LGEIkw3D6VcTWPZ3RiFzyq_UH16e76U4NB1nyfR4-DpZQ3PFTFCPpFlP8Y0-0F4Qn-xpySScL0lnZIl-65d5pOF-xekJxjxZSWjZYNm4X1o?purpose=inline)

3\. Attention mechanism

Helps the model determine which other tokens are relevant when processing a token. Self-attention is a central component of the Transformer architecture.

Example: In “The server failed because it ran out of memory,” attention helps relate “it” to “the server.”

[SpanAttNet: A hybrid SpanConv SPDConv architecture with residual self attention for viral protein subcellular localization - Computational Biomedicine | Science Exploration Press](https://images.openai.com/static-rsc-4/EJ507_OVp5m_Cn4mjDMtta5MKXYQQR1yp7CA8HrwO9vnQDZWbR-pPaZ-OskyRYVchk13r90J9cDA_RzdCLX1JFWoems1mQusPHROFal8h9VXuOxbk7CZqTD_REqlOVGkCdOhzMRWbDXRib0ZIB4BJUCXa_nqVylWqZgxIpVI9FU?purpose=inline)

4\. Transformer layers

Combine attention with feed-forward neural networks, residual connections and normalization. Stacking these layers enables the model to learn increasingly complex patterns.

[A Nitty-Gritty Explanation of How Neural Networks Really Work](https://images.openai.com/static-rsc-4/XBxZ6AFgku9WMKuXjJyMWCzC00bW93Ln4hkL0uwsLK-t21QrcvAwiAZmzQbnzJRVROwgCfOZLM3UrM93c6DxuG_KHcLnZvKJER6Hy2gSSw-iKK3X85vVsBbEx4Q6sna697AQb4_tNRFT1NjMstSmxjldvdSSs-jghgp92sIuU0g?purpose=inline)

5\. Model parameters (weights)

Learned numerical values inside the neural network. Training adjusts these weights to improve the model's predictions.

Example: A model with billions of parameters stores learned statistical patterns, not a simple database of answers.

[PyTorch 학습 프로세스 완벽 가이드: Forward, Backward, Step 핵심 정리](https://images.openai.com/static-rsc-4/bR6kH7GaUasvp078N8M2BOXgnoCpjVp0Y4hDBYQItUyyBCr0PIDTenO_d1cCKBxp7_f39avhBNFSdqQlB8aw-mcTEuu4V7iClP-GBqWwn9pnL5Pm65Gt4SLJbWlbtK0N64gbZYiBmKjeayzKTj34EQBLevkk4O8FNkhGulv0ZfM?purpose=inline)

6\. Training and optimization

The model predicts tokens, calculates the prediction error using a loss function, and updates its weights through backpropagation and an optimizer such as AdamW.

[GitHub - FareedKhan-dev/Understanding-Transformers-Step-by-Step-math-example: Understanding Large Language Transformer Architecture like a child](https://images.openai.com/static-rsc-4/GjoBBiXppkRxxRwl5UUnI9nUY1xG3dG_u01HxSEKLsC1thPPsOmi-P81mrvupmAd2zjQjHCsnH6vWngnmOwwCUCWK9CzbKCKYbhbGdej4N_f_FQSyviQbCTz4AB_qI4YiP6AkXkFDHKSiQT3Id1_aUNWjCmgLZipcALBy0R_mlQ?purpose=inline)

7\. Output layer and token prediction

Produces scores called logits, which are converted into probabilities for possible next tokens. A decoding strategy selects the next token.

[What is an LLM? | Sathyamoorthi v posted on the topic | LinkedIn](https://images.openai.com/static-rsc-4/lwNxvGVX6t3Y6fmI0nd7DRyfLZokUan7J6ioJgh8fdUyI4TE4lyKyub4pTMz_bbOFJ42Ncb6OuS9L_pGRY9OVcX3RncscRtIiTyReVjiKFtNQbXMffYWuiFvSj2zwvSY4wI1XxMEHY9YU0rJ5xyuMc7DUupuWfDD-3ucCa8XWhA?purpose=inline)

8\. Inference and decoding

Runs the trained model to generate a response. Methods such as greedy decoding, temperature and top-p sampling influence how tokens are selected.

## 3. What happens during LLM training?

There are three important stages in many modern LLM development pipelines.

| Stage                                 | Purpose                                                                                                                  |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Pre-training                          | Learn language patterns and general capabilities from large datasets, commonly by predicting the next token.             |
| Supervised fine-tuning (SFT)          | Train the model using examples of desired instructions and responses.                                                    |
| Alignment and preference optimization | Improve response quality and behavior using human or AI preferences, such as with DPO or reinforcement learning methods. |

Not every model uses exactly the same pipeline.

## 4. Important concepts surrounding an LLM

These are often discussed alongside the core building blocks, but they are not all components of the neural network itself.

- Context window: The amount of input and generated content the model can process in a single context.
- Positional information: Helps the model represent token order and position.
- Inference engine: Runs the model efficiently on CPUs or GPUs.
- Quantization: Reduces numerical precision to lower memory usage and often improve inference speed.
- RAG (Retrieval-Augmented Generation): Supplies relevant external documents to the model at inference time.
- Guardrails: Apply policies and validation to reduce unsafe or unwanted outputs.


<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/9b5a6b7a-8720-459a-b789-6b062760b658" />

