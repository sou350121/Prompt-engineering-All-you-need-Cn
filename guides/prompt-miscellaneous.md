# Miscellaneous Topics

在本节中，我们讨论了提示工程中的其他杂项和未分类的主题。它包括相对较新的想法和方法，当它们被更广泛地采用时，最终会被移到主要指南中。指南的这一部分对于了解提示工程的最新研究论文也很有用。

**Note that this section is under heavy development.**

Topic:
- [Active Prompt](#active-prompt)
- [Directional Stimulus Prompting](#directional-stimulus-prompting)
- [Program-Aided Language Models](#program-aided-language-models)
- [ReAct](#react)
- [Multimodal CoT Prompting](#multimodal-prompting)
- [GraphPrompts](#graphprompts)
- ...

---

## Active-Prompt

思维链（CoT）方法依赖于一组固定的人类注释的范例。这样做的问题是，这些典范可能不是对不同任务最有效的例子。为了解决这个问题，[Diao et al，(2023)](https://arxiv.org/pdf/2302.12246.pdf)最近提出了一种新的提示方法，称为Active-Prompt，以使LLM适应不同任务的特定例子提示（用人类设计的CoT推理来注释）。

下面是该方法的一个说明。第一步是用或不用一些CoT例子来查询LLM。为一组训练问题生成*k*可能的答案。根据*k*的答案计算出不确定性指标（disagreement used）。最不确定的问题被挑选出来由人类进行注释。然后，新的注释示例被用来推断每个问题。

![](../img/active-prompt.png)

---
## Directional Stimulus Prompting
[Li et al., (2023)](https://arxiv.org/abs/2302.11520) 提出了一种新的提示技术，以更好地指导LLM生成所需的摘要。

一个可调控的策略LM被训练来产生刺激/提示。看到了更多使用RL来优化LLM的情况。

下图显示了定向刺激提示与标准提示的比较。策略LM可以很小，而且可以优化，以产生指导黑盒冻结LLM的提示。

![](../img/dsp.jpeg)

Full example coming soon!

---
## ReAct

[Yao et al., 2022](https://arxiv.org/abs/2210.03629) 介绍了一个框架，其中LLM被用来以交错的方式生成推理痕迹和特定任务的行动。生成推理痕迹允许模型诱导、跟踪和更新行动计划，甚至处理异常情况。行动步骤允许与外部资源（如知识库或环境）对接并收集信息。

ReAct框架可以让LLM与外部工具互动，以检索更多的信息，从而获得更可靠的、符合事实的反应。

![](../img/react.png)

Full example coming soon!

---
## Multimodal CoT Prompting

[Zhang et al. (2023)](https://arxiv.org/abs/2302.00923) 最近提出了一种多模态的思维链提示方法。传统的CoT着重于语言模式。相比之下，多模态CoT将文本和视觉纳入了一个两阶段的框架。第一步涉及基于多模态信息的理由生成。接下来是第二阶段，答案推理，利用信息生成的理由。

多模态CoT模型（1B）在ScienceQA基准上的表现超过了GPT-3.5。

![](../img/multimodal-cot.png)

Further reading:
- [Language Is Not All You Need: Aligning Perception with Language Models](https://arxiv.org/abs/2302.14045) (Feb 2023)

---
## GraphPrompts

[Liu et al., 2023](https://arxiv.org/abs/2302.08043) 介绍了GraphPrompt，一个新的图形提示框架，以提高下游任务的性能。

More coming soon!

---
[Previous Section (Adversarial Prompting)](./prompt-adversarial.md)
