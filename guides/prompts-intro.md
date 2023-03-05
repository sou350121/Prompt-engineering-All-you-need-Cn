# Prompting Introduction

提示工程是一种相对较新的学科，用于开发和优化提示，以高效地利用语言模型（LMs）来解决各种应用和研究主题。提示工程技能有助于更好地了解大型语言模型（LLMs）的能力和局限性。研究人员使用提示工程来提高LLMs在广泛的常见和复杂任务上的能力，例如问答和算术推理。开发人员使用提示工程来设计稳健而有效的提示技术，与LLMs和其他工具进行接口。

本指南涵盖了标准提示的基础知识，以提供如何使用提示与大型语言模型（LLMs）交互和指导的大致想法。

所有示例都在 text-davinci-003 上进行测试（使用OpenAI的playground），除非另有说明。它使用默认配置，例如 temperature=0.7 和 top-p=1。

Topic:
- [Basic Prompts](#basic-prompts)
- [A Word on LLM Settings](#a-word-on-llm-settings)
- [Standard Prompts](#standard-prompts)
- [Prompt Elements](#elements-of-a-prompt)
- [General Tips for Designing Prompts](#general-tips-for-designing-prompts)

---

## Basic Prompts

你可以利用提示语获得很多信息，但结果的质量取决于你提供的信息量。提示语可以包含像instruction或question这样的信息，同时还可以包含其他细节，例如inputs或examples。

这里是一个简单提示语的基本示例：

*Prompt*
```
The sky is
```

*Output:*
```
blue

The sky is blue on a clear day. On a cloudy day, the sky may be gray or white.
```

正如您所看到的，语言模型输出了一系列与上下文“The sky is”相关联的字符串。输出可能是意料之外的，或与我们想要完成的任务相去甚远。

这个基本示例还突出了提供更多上下文或指示的必要性，以便更明确地指定我们想要实现的任务。

让我们试着稍微改进一下：

*Prompt:*
```
Complete the sentence: 

The sky is
```

*Output:*

```
 so  beautiful today.
```

这样好一些了吗？我们告诉模型去完成句子，所以结果看起来好很多，因为它完全按照我们告诉它要做什么的方式执行（“完成句子”）。这种通过设计最佳提示来指示模型执行任务的方法称为**提示工程**。

上面的示例是当前使用LLMs可以实现的基本说明。现在的LLMs能够执行各种高级任务，从文本摘要到数学推理到代码生成。

---
## A Word on LLM Settings

当使用 prompts 时，您将通过 API 或直接与 LLM 进行交互。您可以配置一些参数，以获得不同的 prompts 结果。

**Temperature** - 简而言之，温度越低，结果越具有确定性，因为始终选择最有可能的下一个令牌。增加温度可能会导致更多的随机性，从而鼓励更多多样化或创意性的输出。我们本质上是增加了其他可能的令牌的权重。在应用方面，我们可能希望在基于事实的问答（fact-based QA）等任务中使用较低的温度，以鼓励更加简明扼要和基于事实的回答。对于诗歌生成或其他创意性任务，增加温度可能是有益的。

**Top_p** - 同样地，使用称为“核心采样”的带温度采样技术的 top_p 参数，可以控制模型生成响应的确定性程度。如果您正在寻找准确和事实性的答案，请保持此值较低。如果您正在寻找更多样化的响应，则将其增加到较高值。

一般的建议是只更改一个参数而不是同时更改两个参数。

在开始一些基本示例之前，请注意您的结果可能会因您使用的 LLM 版本而有所不同。

---
## Standard Prompts

我们上面尝试了一个非常简单的提示。一个标准提示的格式如下：

```
<Question>?
```
 
这可以格式化为问答格式，这是许多问答数据集中的标准格式，如下所示：

```
Q: <Question>?
A: 
```

给定上述标准格式，一种流行且有效的提示技术被称为 few-shot prompting，其中我们提供样例。Few-shot prompts 可以格式化如下：

```
<Question>?
<Answer>

<Question>?
<Answer>

<Question>?
<Answer>

<Question>?

```


您已经猜到，它的 QA 格式版本将如下所示：

```
Q: <Question>?
A: <Answer>

Q: <Question>?
A: <Answer>

Q: <Question>?
A: <Answer>

Q: <Question>?
A:
```

请记住，不一定需要使用问答格式。格式取决于手头的任务。例如，您可以执行一个简单的分类任务，并给出演示任务的示例，如下所示：

*Prompt:*
```
This is awesome! // Positive
This is bad! // Negative
Wow that movie was rad! // Positive
What a horrible show! //
```

*Output:*
```
Negative
```

Few-shot prompt可以实现上下文学习，即语言模型仅通过少量示例即可学习任务的能力。我们将在即将到来的指南中看到更多相关内容。

---
## Elements of a Prompt

随着我们探讨越来越多的例子和应用程序，您会注意到某些元素组成了一个提示。一个提示可能包含以下任何一个组件：


**Instruction** - 希望模型执行的具体任务或指令

**Context** - 可以涉及外部信息或额外的背景，可以引导模型做出更好的反应

**Input Data** - 有兴趣找到答案的输入或问题。

**Output Indicator** - 表示输出的类型或格式。

并非所有组件都是提示的必备部分，其格式取决于手头的任务。在接下来的指南中，我们将涉及更多具体的示例。

---
## General Tips for Designing Prompts

这里有一些提示，在你设计你的提示时要牢记。


### Start Simple
当你开始设计提示语时，你应该记住，这其实是一个反复的过程，需要大量的实验来获得最佳的结果。使用像OpenAI的或Cohere的这样一个简单的游乐场是一个好的起点。

你可以从简单的提示开始，然后不断添加更多的元素和背景，因为你的目标是获得更好的结果。为此，沿途对你的提示进行改版是至关重要的。当我们阅读指南时，你会看到许多例子，在这些例子中，具体性、简单性和简洁性往往会给你带来更好的结果。

当你的大任务涉及到许多不同的子任务时，你可以尝试将任务分解成更简单的子任务，并在你获得更好的结果时不断建立起来。这样可以避免在一开始就给提示设计过程增加太多复杂性。

### The Instruction
你可以为各种简单的任务设计有效的提示，通过使用命令来指示模型你想要达到的目的 "Write", "Classify", "Summarize", "Translate", "Order", etc.

请记住，你还需要进行大量的实验，以便看到什么是最有效的。用不同的关键词、背景和数据尝试不同的指令，看看什么对你的特定用例和任务最有效。通常情况下，上下文与你要执行的任务越具体、越相关，就越好。我们将在接下来的指南中触及采样和添加更多上下文的重要性。

还有人建议将指示放在提示的开头。还建议使用一些明确的分隔符，如 "##"，以分隔指令和上下文。

For instance:

*Prompt:*
```
### Instruction ###
Translate the text below to Spanish:

Text: "hello!"
```

*Output:*
```
¡Hola!
```

### Specificity
Be very specific about the instruction and task you want the model to perform. The more descriptive and detailed the prompt is, the better the results. This is particularly important when you have a desired outcome or style of generation you are seeking. There aren't specific tokens or keywords that lead to better results. It's more important to have a good format and descriptive prompt. In fact, providing examples in the prompt is very effective to get desired output in specific formats. 

When designing prompts you should also keep in mind the length of the prompt as there are limitations regarding how long this can be. Thinking about how specific and detailed you should be is something to consider. Too many unnecessary details in not necessarily a good approach. The details should be relevant and contribute to the task at hand. This is something you will need to experiment with a lot. We encourage a lot of experimentation and iteration to optimize prompts for your applications.

As an example, let's try a simple prompt to extract specific information from a piece of text.

*Prompt:*
```
Extract the name of places in the following text. 

Desired format:
Place: <comma_separated_list_of_company_names>

Input: "Although these developments are encouraging to researchers, much is still a mystery. “We often have a black box between the brain and the effect we see in the periphery,” says Henrique Veiga-Fernandes, a neuroimmunologist at the Champalimaud Centre for the Unknown in Lisbon. “If we want to use it in the therapeutic context, we actually need to understand the mechanism.""
```

*Output:*
```
Place: Champalimaud Centre for the Unknown, Lisbon
```

Input text is obtained from [this Nature article](https://www.nature.com/articles/d41586-023-00509-z).

### Avoid Impreciseness

Given the tips above about being detailed and improving format, it's easy to fall into the trap of wanting to be too clever about prompts and potentially creating imprecise descriptions. It's often better to be specific and direct. The analogy here is very similar to effective communication -- the more direct, the more effective the message gets across.

For example, you might be interested in learning the concept of prompt engineering. You might try something like:

```
Explain the concept prompt engineering. Keep the explanation short, only a few sentences, and don't be too descriptive.
```

It's not clear from the prompt above how many sentences to use and what style. You might still somewhat get good responses with the above prompts but the better prompt would be one that is very specific, concise, and to the point. Something like:

```
Use 2-3 sentences to explain the concept of prompt engineering to a high school student.
```

### To do or not to do?
Another common tip when designing prompts is to avoid saying what not to do but say what to do instead. This encourages more specificity and focus on the details that lead to good responses from the model.

Here is an example of movie recommendation chatbot failing at exactly what I don't want it to do because of how I wrote the instruction -- focusing on what not to do. 

*Prompt:*
```
The following is an agent that recommends movies to a customer. DO NOT ASK FOR INTERESTS. DO NOT ASK FOR PERSONAL INFORMATION.

Customer: Please recommend a movie based on my interests.
Agent: 
```

*Output:*
```
Sure, I can recommend a movie based on your interests. What kind of movie would you like to watch? Do you prefer action, comedy, romance, or something else?
```

Here is a better prompt:

*Prompt:*
```
The following is an agent that recommends movies to a customer. The agent is responsible to recommend a movie from the top global trending movies. It should refrain from asking users for their preferences and avoid asking for personal information. If the agent doesn't have a movie to recommend, it should respond "Sorry, couldn't find a movie to recommend today.".

Customer: Please recommend a movie based on my interests.
Agent:
```

*Output:*
```
Sorry, I don't have any information about your interests. However, here's a list of the top global trending movies right now: [list of movies]. I hope you find something you like!
```

Some of the examples above were adopted from the ["Best practices for prompt engineering with OpenAI API" article.](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)


---
[Next Section (Basic Prompting)](./prompts-basic-usage.md)
