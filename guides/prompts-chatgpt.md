# ChatGPT Prompt Engineering

在本节中，我们将介绍ChatGPT的最新提示工程技术，包括提示、应用、限制、论文和其他阅读材料。

**Note that this section is under heavy development.**

Topics:
- [ChatGPT Introduction](#chatgpt-introduction)
- [Reviewing The Conversation Task](#reviewing-the-conversation-task)
- [Converstions with ChatGPT](#conversations-with-chatgpt)

---
## ChatGPT Introduction

ChatGPT是一个新的模型[由OpenAI训练](https://openai.com/blog/chatgpt)，它有能力以对话的方式进行互动。这个模型被训练成能够遵循提示中的指令，在对话的背景下提供适当的回应。ChatGPT可以帮助回答问题，建议食谱，用某种风格写歌词，生成代码，等等。

ChatGPT是使用人类反馈强化学习（RLHF）来训练的。虽然这个模型比以前的GPT迭代能力强很多（而且还经过训练以减少有害和不真实的输出），但它仍然有局限性。让我们通过具体的例子来介绍一些能力和限制。

你可以使用ChatGPT的研究预览[这里](chat.openai.com)，但对于这些例子，我们将使用OpenAI Playground上的聊天模式。

---
## Reviewing The Conversation Task

在以前的一份指南中，我们介绍了一些关于对话能力和角色提示的内容。我们介绍了如何指示LLM以特定的风格进行对话，有特定的意图、行为和身份。

让我们回顾一下我们之前的基本例子，我们创建了一个对话系统，能够对问题产生更多的技术和科学回答。

*Prompt:*
```
The following is a conversation with an AI research assistant. The assistant tone is technical and scientific.

Human: Hello, who are you?
AI: Greeting! I am an AI research assistant. How can I help you today?
Human: Can you tell me about the creation of blackholes?
AI:
```

从上面的示例中，您可以看到两个重要的组件：
- 指示聊天机器人的**意图**或解释
- 指示聊天机器人将用于响应的样式或语气的**身份**

上面的简单例子在使用`text-davinci-003'的文本补全API中运行良好。最近，OpenAI[宣布了ChatGPT APIs](https://openai.com/blog/introducing-chatgpt-and-whisper-apis)，这是一个更强大和更便宜的模型，称为`gpt-3.5-turbo`，是专门为这种类型的功能（聊天完成）建立的。事实上，OpenAI推荐这是他们的最佳模型，即使是在非聊天用例中。 使用ChatGPT APIs的其他好处是大大降低了成本（90%）和效率。

像Snap Inc.和Instacart这样的大公司已经在其产品上整合了由ChatGPT提供的对话功能，范围包括个性化推荐和开放式购物目标。

---
## Conversations with ChatGPT
为了开始展示ChatGPT的能力，我们将使用上面的聊天机器人助手例子，并讨论结果。与`text-davinci-003`相比，支持ChatGPT的`gpt-3.5-turbo`模型使用聊天格式作为输入。该模型期望有一系列的信息作为输入，并使用这些信息来生成一个响应。

*Input:*
```
SYSTEM: You are an AI research assistant. You use a tone that is technical and scientific.
USER: Hello, who are you?
ASSISTANT: Greeting! I am an AI research assistant. How can I help you today?
USER: Can you tell me about the creation of black holes?
```

*Output:*
```
ASSISTANT: Of course! Black holes are created when a massive star runs out of fuel and collapses under its own gravity. This collapse causes the star to become incredibly dense, with a gravitational field so strong that not even light can escape it. This point of no return is known as the event horizon. Once a star crosses the event horizon, it is considered a black hole...
```

请注意，在上面的例子中，我已经简化了输入和输出，但ChatGPT聊天完成API要求消息必须是特定的格式。我在下面添加了一个快照，说明这个例子在OpenAI Playground中使用 "聊天模式 "会是什么样子。

![](../img/chatgpt-1.png)

对于我们的例子来说，更正式的API调用将看起来像下面的例子。

```python
import openai

openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are an AI research assistant. You use a tone that is technical and scientific."},
        {"role": "user", "content": "Hello, who are you?"},
        {"role": "assistant", "content": "Greeting! I am an AI research assistant. How can I help you today?"},
        {"role": "user", "content": "Can you tell me about the creation of black holes?"}
    ]
)
```
事实上，未来开发者与ChatGPT的互动方式有望通过[聊天标记语言](https://github.com/openai/openai-python/blob/main/chatml.md)（简称ChatML）来完成。

聊天格式可以实现多轮对话，但它也支持类似于我们在`text-davinci-003`中使用的单轮任务。这意味着我们可以使用ChatGPT来执行类似于我们为原始GPT模型所演示的任务。例如，让我们尝试用ChatGPT执行以下问题的回答任务。

*Input:*
```
USER: Answer the question based on the context below. Keep the answer short and concise. Respond "Unsure about answer" if not sure about the answer.

Context: Teplizumab traces its roots to a New Jersey drug company called Ortho Pharmaceutical. There, scientists generated an early version of the antibody, dubbed OKT3. Originally sourced from mice, the molecule was able to bind to the surface of T cells and limit their cell-killing potential. In 1986, it was approved to help prevent organ rejection after kidney transplants, making it the first therapeutic antibody allowed for human use.

Question: What was OKT3 originally sourced from?

Answer:
```

*Output:*
```
ASSISTANT: Mice.
```

请记住，我添加`USER'和`ASSISTANT'标签是为了更好地演示如何使用ChatGPT执行任务。下面是使用Playground的例子。

![](../img/chatgpt-classic.png)

更正式地说，这是API调用（我只包括请求的消息部分）。

```python
CONTENT = """Answer the question based on the context below. Keep the answer short and concise. Respond \"Unsure about answer\" if not sure about the answer.

Context: Teplizumab traces its roots to a New Jersey drug company called Ortho Pharmaceutical. There, scientists generated an early version of the antibody, dubbed OKT3. Originally sourced from mice, the molecule was able to bind to the surface of T cells and limit their cell-killing potential. In 1986, it was approved to help prevent organ rejection after kidney transplants, making it the first therapeutic antibody allowed for human use.

Question: What was OKT3 originally sourced from?

Answer:
"""

response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": CONTENT},
    ],
    temperature=0,
)
```

你可以尝试一下 [this notebook](../notebooks/pe-chatgpt-intro.ipynb) 以了解更多关于如何调用ChatGPT APIs的信息。

---

More coming soon!

---
## References

- [Will Affective Computing Emerge from Foundation Models and General AI? A First Evaluation on ChatGPT](https://arxiv.org/abs/2303.03186) (Mar 2023)
- [UZH_CLyp at SemEval-2023 Task 9: Head-First Fine-Tuning and ChatGPT Data Generation for Cross-Lingual Learning in Tweet Intimacy Prediction](https://arxiv.org/abs/2303.01194) (Mar 2023)
- [How to format inputs to ChatGPT models](https://github.com/openai/openai-cookbook/blob/main/examples/How_to_format_inputs_to_ChatGPT_models.ipynb) (Mar 2023)
- [Can ChatGPT Assess Human Personalities? A General Evaluation Framework](https://arxiv.org/abs/2303.01248) (Mar 2023)
- [Cross-Lingual Summarization via ChatGPT](https://arxiv.org/abs/2302.14229) (Feb 2023)
- [Dr ChatGPT, tell me what I want to hear: How prompt knowledge impacts health answer correctness](https://arxiv.org/abs/2302.13793) (Feb 2023)
- [An Independent Evaluation of ChatGPT on Mathematical Word Problems (MWP)](https://arxiv.org/abs/2302.13814) (Feb 2023)
- [ChatGPT: A Meta-Analysis after 2.5 Months](https://arxiv.org/abs/2302.13795) (Feb 2023)
- [Let's have a chat! A Conversation with ChatGPT: Technology, Applications, and Limitations](https://arxiv.org/abs/2302.13817) (Feb 2023)
- [Check Your Facts and Try Again: Improving Large Language Models with External Knowledge and Automated Feedback](https://arxiv.org/abs/2302.12813) (Feb 2023)
- [On the Robustness of ChatGPT: An Adversarial and Out-of-distribution Perspective](https://arxiv.org/abs/2302.12095) (Feb 2023)
- [How Generative AI models such as ChatGPT can be (Mis)Used in SPC Practice, Education, and Research? An Exploratory Study](https://arxiv.org/abs/2302.10916) (Feb 2023)
- [Can ChatGPT Understand Too? A Comparative Study on ChatGPT and Fine-tuned BERT](https://arxiv.org/abs/2302.10198) (Feb 2023)
- [A Prompt Pattern Catalog to Enhance Prompt Engineering with ChatGPT](https://arxiv.org/abs/2302.11382) (Feb 2023)
- [Zero-Shot Information Extraction via Chatting with ChatGPT](https://arxiv.org/abs/2302.10205) (Feb 2023)
- [ChatGPT: Jack of all trades, master of none](https://arxiv.org/abs/2302.10724) (Feb 2023)
- [A Pilot Evaluation of ChatGPT and DALL-E 2 on Decision Making and Spatial Reasoning](https://arxiv.org/abs/2302.09068) (Feb 2023)
- [Netizens, Academicians, and Information Professionals' Opinions About AI With Special Reference To ChatGPT](https://arxiv.org/abs/2302.07136) (Feb 2023)
- [Linguistic ambiguity analysis in ChatGPT](https://arxiv.org/abs/2302.06426) (Feb 2023)
- [ChatGPT versus Traditional Question Answering for Knowledge Graphs: Current Status and Future Directions Towards Knowledge Graph Chatbots](https://arxiv.org/abs/2302.06466) (Feb 2023)
- [What ChatGPT and generative AI mean for science](https://www.nature.com/articles/d41586-023-00340-6) (Feb 2023)
- [Applying BERT and ChatGPT for Sentiment Analysis of Lyme Disease in Scientific Literature](https://arxiv.org/abs/2302.06474) (Feb 2023)
- [ChatGPT for Good? On Opportunities and Challenges of Large Language Models for Education](https://www.edu.sot.tum.de/fileadmin/w00bed/hctl/_my_direct_uploads/ChatGPT_for_Good_.pdf) (Jan 2023)
- [Techniques to improve reliability - OpenAI Cookbook](https://github.com/openai/openai-cookbook/blob/main/techniques_to_improve_reliability.md)
- [Awesome ChatGPT Prompts](https://github.com/f/awesome-chatgpt-prompts)
- [Introducing ChatGPT](https://openai.com/blog/chatgpt) (Nov 2022)

---
[Previous Section (Applications)](./prompts-applications.md)

[Next Section (Adversarial Prompting)](./prompts-adversarial.md)
