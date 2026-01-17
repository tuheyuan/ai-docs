# LangExtract：用大模型提取结构化信息，只需要三个示例

这两年我试过各种用大模型提取结构化信息的方法，最终发现 LangExtract 是最直接有效的。我有三个必须推荐它的理由：

1. 不需要写复杂的 Prompt
2. 长文档也能准确提取
3. 每个结果都能追溯到原文

## 为什么是 LangExtract

传统的信息提取方案，要么需要写几百行的 Prompt，要么动不动就幻觉乱编。LangExtract 的思路完全不一样——你只需要给几个示例，它就能理解你想要什么。

最关键的是，它把每个提取结果都映射到了原文的具体位置。这意味着什么？意味着你可以一键生成一个交互式的 HTML 文件，里面所有提取出来的实体都能高亮显示在原文上下文里。这对于需要验证准确性的场景，比如医疗文档分析，简直就是刚需。

而且它处理长文档的能力特别强。之前我试过直接把几十页的临床笔记扔给 GPT，结果基本就是漏提取。LangExtract 的做法是先把文档切成小块，并行处理，然后再多轮提取。这样就能保证那些藏在几百页里的关键信息，也不会被漏掉。

## 怎么用

用法简单到不可思议。只需要定义你的提取规则，然后给几个示例就行了。

```python
import langextract as lx

# 定义你想提取什么
prompt = "提取人物、情感和关系，按出现顺序排列"

# 提供示例引导模型
examples = [
    lx.data.ExampleData(
        text="ROMEO. But soft! What light through yonder window breaks?",
        extractions=[
            lx.data.Extraction(
                extraction_class="character",
                extraction_text="ROMEO",
                attributes={"emotional_state": "wonder"}
            ),
            lx.data.Extraction(
                extraction_class="emotion",
                extraction_text="But soft!",
                attributes={"feeling": "gentle awe"}
            ),
        ]
    )
]

# 一行代码完成提取
result = lx.extract(
    text_or_documents="你的文本或URL",
    prompt_description=prompt,
    examples=examples,
    model_id="gemini-2.5-flash"
)
```

就这么简单。不需要写几百行的 Prompt，也不需要微调模型。几个高质量的示例，就够了。

## 支持的模型

LangExtract 对模型的选择很灵活。云端的话，首推 Gemini 2.5 Flash，速度快、成本低，质量还够用。如果你有更复杂的推理需求，可以上 Gemini 2.5 Pro。

不想用云服务的，本地模型也完全没问题。它内置了 Ollama 的支持，你可以直接用 Gemma、Llama 这些开源模型在本地跑。OpenAI 的 GPT-4o 也支持，安装个依赖包就行了。

最实用的一点是，它支持直接给 URL。比如你想分析整本《罗密欧与朱丽叶》，直接把 Project Gutenberg 的链接扔给它就行。它会自动下载、分块、并行处理，最后把几百个提取结果整理好，还能生成可视化界面。

## 安装和使用

安装就一行命令：

```bash
pip install langextract
```

如果用 Gemini 模型，需要去 Google AI Studio 申请个 API Key。设置成环境变量 `LANGEXTRACT_API_KEY` 就行了。

GitHub 仓库在 google/langextract，文档写得很详细，各种场景的示例都有。医疗信息提取、文学作品分析、临床笔记处理，代码直接复制就能用。

LangExtract 是 Google 开源的，用的是 Apache 2.0 协议。如果你要在生产环境或者论文里用，记得引用一下。

这个工具最大的价值，就是让结构化信息提取这件事，变得真正可用、可靠、可验证。
