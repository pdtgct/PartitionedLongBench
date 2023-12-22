# Partitioned LongBench

Most performance benchmarks for inference frameworks test using small context
sizes. For example [Llama 2 on Amazon SageMaker a Benchmark](https://www.philschmid.de/sagemaker-llama-benchmark),
has a mean of 233 and max of 1024 tokens (measured using the LLaMA 2 SentencePiece Tokenizer).

The performance characteristics of inference framework deployments using long
contexts of 2,000 - 4,--- or mroe tokens are not commonly measured.

The [LongBench](https://arxiv.org/abs/2308.14508) dataset contains some long 
examples with query use cases and is available on GitHub at 
[https://github.com/THUDM/LongBench](https://github.com/THUDM/LongBench).

This repository partitions LongBench samples by context length, which may be 
applied to prompt and request templates to form benchmark performance and load
test datasets. An example dataset for LLaMA 2 is provided.

## Reference LLaMA 2 Prompt Template

This is based on [LLaMA 2 generation.py](https://github.com/facebookresearch/llama/blob/ef351e9cd9496c579bf9f2bb036ef11bdc5ca3d2/llama/generation.py#L42-L48)
for a QASum task, with a well-known extension adding the instruction after 
the final `[/INST]`, see [LLaMA Recipes - Prompt Llama 2](https://github.com/facebookresearch/llama-recipes/blob/main/docs/inference.md#prompt-llama-2).

````text
<s>[INST] <<SYS>>
{{ system_prompt }}
<</SYS>>

```
{{ context }}
```

Question: {{ question }}

[/INST]
Answer:
````

where `system_prompt` is adapted from [LangChain's prompt hub](https://smith.langchain.com/hub/rlm/rag-prompt).

```text
You are an assistant for question-answering tasks. Use the following pieces of \
retrieved context in the section demarcated by "```" to answer the question. \
If you don't know the answer just say that you do not know. Use three sentences
maximum and keep the answer concise.
```

So the final prompt is:

````
<s>[INST] <<SYS>>
You are an assistant for question-answering tasks. Use the following pieces of \
retrieved context in the section demarcated by "```" to answer the question. \
If you don't know the answer just say that you don't know. Use three sentences
maximum and keep the answer concise.
<</SYS>>

```
{{ context }}
```

Question: {{ question }}

[/INST]
Answer:
````

Using the LLaMA 2 Tokenizer, the above prompt, when `context` and `question` 
are empty, is `98` tokens.


## Large Files

The dataset included here is over 100 MB so you will need to use git-lfs.
