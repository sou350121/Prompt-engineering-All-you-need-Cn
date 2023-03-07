# Advanced Prompting
很明显，P有助于改进提示以在不同任务上获得更好的结果。 这就是提示工程背后的全部理念。

虽然这些示例很有趣，但在我们进入更高级的概念之前，让我们更正式地介绍一些概念。

Topics:

- [Zero-shot Prompting](#zero-shot-prompting)
- [Few-shot Prompting](#few-shot-prompting)
- [Chain-of-Thought Prompting](#chain-of-thought-prompting)
- [Zero-shot CoT](#zero-shot-cot)
- [Self-Consistency](#self-consistency)
- [Generate Knowledge Prompting](#generated-knowledge-prompting)
- [Automatic Prompt Engineer](#automatic-prompt-engineer-ape)

---
## Zero-Shot Prompting 零样本零样本

今天的 LLMs 接受了大量数据的训练并调整以遵循指令，能够 Zero-Shot Prompting 零样本地执行任务。 我们实际上在上一节中尝试了一些零样本示例。 这是我们使用的示例之一：

*Prompt:*
```
Classify the text into neutral, negative or positive. 

Text: I think the vacation is okay.
Sentiment:
```

*Output:*
```
Neutral
```

请注意，在上面的提示中，我们没有为模型提供任何示例——这是工作中的零样本功能。 当 zero-shot 不起作用时，建议在提示中提供演示或示例。 下面我们将讨论称为小样本金的方法。

---
## Few-Shot Prompting 小样本提示

虽然大型语言模型已经展示了卓越的零样本能力，但在使用零样本设置时它们仍然无法完成更复杂的任务。 为了改进这一点，使用 Few-Shot Prompting 少量提示作为一种技术来启用上下文学习，我们在提示中提供演示以引导模型获得更好的性能。 这些演示用作后续示例的条件，我们希望模型在这些示例中生成响应。

Let's demonstrate few-shot prompting via an example that was presented in [Brown et al. 2020](https://arxiv.org/abs/2005.14165). 在示例中，任务是在句子中正确使用新词。

*Prompt:*
```
A "whatpu" is a small, furry animal native to Tanzania. An example of a sentence that uses
the word whatpu is:
We were traveling in Africa and we saw these very cute whatpus.
To do a "farduddle" means to jump up and down really fast. An example of a sentence that uses
the word farduddle is:
```

*Output:*
```
When we won the game, we all started to farduddle in celebration.
```

我们可以清楚地观察到，该模型仅通过提供一个示例（即 1-shot）就以某种方式学会了如何执行任务。 对于更困难的任务，我们可以尝试增加演示（例如，3-shot、5-shot、10-shot 等）。

Following the findings from [Min et al. (2022)](https://arxiv.org/abs/2202.12837), here a few more tips about demonstrations/exemplars when doing few-shot:

- “演示指定的**标签空间**和输入文本的分布都是关键（无论标签是否对单个输入正确）”
- 你使用的**格式**对性能也起着关键作用，即使你只是使用随机标签，这也比根本没有标签要好得多。
- 额外的结果表明，从标签的真实分布（而不是均匀分布）中选择随机标签也有帮助。

让我们尝试几个例子。 让我们首先尝试一个带有随机标签的示例（意味着标签 Negative 和 Positive 随机分配给输入）
*Prompt:*
```
This is awesome! // Negative
This is bad! // Positive
Wow that movie was rad! // Positive
What a horrible show! //
```

*Output:*
```
Negative
```

我们仍然得到正确的答案，即使标签是随机的。 请注意，我们还保留了格式，这也有帮助。 事实上，随着进一步的实验，我们正在实验的较新的 GPT 模型似乎对随机格式也变得更加稳健。 例子：

*Prompt:*
```
Positive This is awesome! 
This is bad! Negative
Wow that movie was rad!
Positive
What a horrible show! --
```

*Output:*
```
Negative
```

上面的格式不一致，但模型仍然预测了正确的标签。 我们必须进行更彻底的分析，以确认这是否适用于不同的和更复杂的任务，包括不同的提示变体。

### Limitations of Few-shot Prompting

标准的小样本提示适用于许多任务，但仍然不是一种完美的技术，尤其是在处理更复杂的推理任务时。 让我们来说明为什么会这样。 还记得前面的例子吗，我们提供了以下任务：

```
The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 

A: 
```

If we try this again, the model outputs the following:

```
Yes, the odd numbers in this group add up to 107, which is an even number.
```

这不是正确的回应，它不仅突出了这些系统的局限性，而且需要更高级的提示工程。

让我们尝试添加一些示例以查看小样本提示是否可以改善结果。

*Prompt:*
```
The odd numbers in this group add up to an even number: 4, 8, 9, 15, 12, 2, 1.
A: The answer is False.

The odd numbers in this group add up to an even number: 17,  10, 19, 4, 8, 12, 24.
A: The answer is True.

The odd numbers in this group add up to an even number: 16,  11, 14, 4, 8, 13, 24.
A: The answer is True.

The odd numbers in this group add up to an even number: 17,  9, 10, 12, 13, 4, 2.
A: The answer is False.

The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 
A: 
```

*Output:*
```
The answer is True.
```

那没有用。 对于此类推理问题，似乎少样本提示不足以获得可靠的响应。 上面的示例提供了有关任务的基本信息。 如果您仔细观察，我们介绍的任务类型涉及更多的推理步骤。 换句话说，如果我们将问题分解为多个步骤并向模型演示，可能会有所帮助。 最近，[思维链 (CoT) 提示](https://arxiv.org/abs/2201.11903) 得到普及，用于解决更复杂的算术、常识和符号推理任务。

总的来说，提供示例似乎对解决某些任务很有用。 当零样本提示和少样本提示不够时，这可能意味着模型学到的任何东西都不足以完成任务。 建议从这里开始考虑微调您自己的模型或尝试更高级的提示技术。 接下来我们将讨论一种**流行的提示技术**，称为 Chain-of-Thought Prompting 思维链提示，它已经很受欢迎。

---

## Chain-of-Thought Prompting

介绍于 [Wei et al. (2022)](https://arxiv.org/abs/2201.11903), 思维链 (CoT) 提示通过中间推理步骤启用复杂的推理能力。 您可以将它与少量提示结合使用，以便在响应前需要推理的更复杂任务中获得更好的结果。

*Prompt:*
```
The odd numbers in this group add up to an even number: 4, 8, 9, 15, 12, 2, 1.
A: Adding all the odd numbers (9, 15, 1) gives 25. The answer is False.

The odd numbers in this group add up to an even number: 17,  10, 19, 4, 8, 12, 24.
A: Adding all the odd numbers (17, 19) gives 36. The answer is True.

The odd numbers in this group add up to an even number: 16,  11, 14, 4, 8, 13, 24.
A: Adding all the odd numbers (11, 13) gives 24. The answer is True.

The odd numbers in this group add up to an even number: 17,  9, 10, 12, 13, 4, 2.
A: Adding all the odd numbers (17, 9, 13) gives 39. The answer is False.

The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 
A:
```

*Output:*
```
Adding all the odd numbers (15, 5, 13, 7, 1) gives 41. The answer is False.
```

当我们提供推理步骤时，我们可以看到一个完美的结果。 事实上，我们可以通过提供更少的例子来解决这个任务，即一个例子似乎就足够了：

*Prompt:*
```
The odd numbers in this group add up to an even number: 4, 8, 9, 15, 12, 2, 1.
A: Adding all the odd numbers (9, 15, 1) gives 25. The answer is False.

The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 
A:
```

*Output:*
```
Adding all the odd numbers (15, 5, 13, 7, 1) gives 41. The answer is False.
```

请记住，作者声称这是一种随着足够大的语言模型而出现的新兴能力。

---

## Zero-Shot CoT

最近出来的 [zero-shot CoT](https://arxiv.org/abs/2205.11916) (Kojima et al. 2022) 基本上是在原来的提示中加入 "让我们一步一步地思考"。让我们尝试一个简单的问题，看看模型的表现如何。

*Prompt:*
```
I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?
```

*Output:*
```
11 apples
```

答案是不正确的! 现在让我们用特别提示来试试。

*Prompt:*
```
I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?

Let's think step by step.
```

*Output:*
```
First, you started with 10 apples.
You gave away 2 apples to the neighbor and 2 to the repairman, so you had 6 apples left.
Then you bought 5 more apples, so now you had 11 apples.
Finally, you ate 1 apple, so you would remain with 10 apples.
```

实际上令人印象深刻的是，这个简单的提示能有效地完成这项任务。在你没有太多的例子可以在提示中使用时，这一点尤其有用。

---

## Self-Consistency

也许有一种更先进的提示工程技术是 self-consistency. [Wang et al. (2022)](https://arxiv.org/pdf/2203.11171.pdf)提出， Self-Consistency旨在 "取代思维链提示中使用的天真贪婪的解码"。它的想法是通过少量的CoT对多个不同的推理路径进行抽样，并使用世代来选择最一致的答案。这有助于提高CoT提示在涉及算术和常识性推理的任务上的表现。

让我们试试下面这个算术推理的例子。

*Prompt:*
```
When I was 6 my sister was half my age. Now
I’m 70 how old is my sister?
```

*Output:*
```
35
```

输出结果显然是错误的。 我们可能如何用Self-Consistency 来改善这个问题呢？ 让我们来试试:

*Prompt:*
```
Q: There are 15 trees in the grove. Grove workers will plant trees in the grove today. After they are done,
there will be 21 trees. How many trees did the grove workers plant today?
A: We start with 15 trees. Later we have 21 trees. The difference must be the number of trees they planted.
So, they must have planted 21 - 15 = 6 trees. The answer is 6.

Q: If there are 3 cars in the parking lot and 2 more cars arrive, how many cars are in the parking lot?
A: There are 3 cars in the parking lot already. 2 more arrive. Now there are 3 + 2 = 5 cars. The answer is 5.

Q: Leah had 32 chocolates and her sister had 42. If they ate 35, how many pieces do they have left in total?
A: Leah had 32 chocolates and Leah’s sister had 42. That means there were originally 32 + 42 = 74
chocolates. 35 have been eaten. So in total they still have 74 - 35 = 39 chocolates. The answer is 39.

Q: Jason had 20 lollipops. He gave Denny some lollipops. Now Jason has 12 lollipops. How many lollipops
did Jason give to Denny?
A: Jason had 20 lollipops. Since he only has 12 now, he must have given the rest to Denny. The number of
lollipops he has given to Denny must have been 20 - 12 = 8 lollipops. The answer is 8.

Q: Shawn has five toys. For Christmas, he got two toys each from his mom and dad. How many toys does
he have now?
A: He has 5 toys. He got 2 from mom, so after that he has 5 + 2 = 7 toys. Then he got 2 more from dad, so
in total he has 7 + 2 = 9 toys. The answer is 9.

Q: There were nine computers in the server room. Five more computers were installed each day, from
monday to thursday. How many computers are now in the server room?
A: There are 4 days from monday to thursday. 5 computers were added each day. That means in total 4 * 5 =
20 computers were added. There were 9 computers in the beginning, so now there are 9 + 20 = 29 computers.
The answer is 29.

Q: Michael had 58 golf balls. On tuesday, he lost 23 golf balls. On wednesday, he lost 2 more. How many
golf balls did he have at the end of wednesday?
A: Michael initially had 58 balls. He lost 23 on Tuesday, so after that he has 58 - 23 = 35 balls. On
Wednesday he lost 2 more so now he has 35 - 2 = 33 balls. The answer is 33.

Q: Olivia has $23. She bought five bagels for $3 each. How much money does she have left?
A: She bought 5 bagels for $3 each. This means she spent 5

Q: When I was 6 my sister was half my age. Now I’m 70 how old is my sister?
A:
```

*Output 1:*
```
When I was 6 my sister was half my age, so she was 3. Now I am 70, so she is 70 - 3 = 67. The answer is 67.
```

*Output 2:*
```
When the narrator was 6, his sister was half his age, which is 3. Now that the narrator is 70, his sister would be 70 - 3 = 67 years old. The answer is 67.
```

*Output 3:*
```
When I was 6 my sister was half my age, so she was 3. Now I am 70, so she is 70/2 = 35. The answer is 35.
```

计算最终答案涉及几个步骤（详情请查阅论文），但为了简单起见，我们可以清楚地看到，已经有一个多数人的答案出现，所以这基本上会成为最终答案。

---
## Generated Knowledge Prompting

利用类似的想法，模型是否也可以在做出预测之前生成知识？

这就是[Liu et al. 2022](https://arxiv.org/pdf/2110.08387.pdf)的论文中所尝试的 -- 生成知识，作为提示的一部分。特别是，这对常识性推理等任务有多大帮助？

让我们试试一个简单的提示。

*Prompt:*
```
Part of golf is trying to get a higher point total than others. Yes or No?
```

*Output:*
```
Yes.
```

这类错误揭示了LLM在执行需要更多世界知识的任务时的局限性。我们如何用知识生成来改善这一点呢？

首先，我们生成一些 "知识"。

*Prompt:*
```
Input: Greece is larger than mexico.
Knowledge: Greece is approximately 131,957 sq km, while Mexico is approximately 1,964,375 sq km, making Mexico 1,389% larger than Greece.

Input: Glasses always fog up.
Knowledge: Condensation occurs on eyeglass lenses when water vapor from your sweat, breath, and ambient humidity lands on a cold surface, cools, and then changes into tiny drops of liquid, forming a film that you see as fog. Your lenses will be relatively cool compared to your breath, especially when the outside air is cold.

Input: A fish is capable of thinking.
Knowledge: Fish are more intelligent than they appear. In many areas, such as memory, their cognitive powers match or exceed those of ’higher’ vertebrates including non-human primates. Fish’s long-term memories help them keep track of complex social relationships.

Input: A common effect of smoking lots of cigarettes in one’s lifetime is a higher than normal chance of getting lung cancer.
Knowledge: Those who consistently averaged less than one cigarette per day over their lifetime had nine times the risk of dying from lung cancer than never smokers. Among people who smoked between one and 10 cigarettes per day, the risk of dying from lung cancer was nearly 12 times higher than that of never smokers.

Input: A rock is the same size as a pebble.
Knowledge: A pebble is a clast of rock with a particle size of 4 to 64 millimetres based on the Udden-Wentworth scale of sedimentology. Pebbles are generally considered larger than granules (2 to 4 millimetres diameter) and smaller than cobbles (64 to 256 millimetres diameter).

Input: Part of golf is trying to get a higher point total than others.
Knowledge:
```

*Knowledge 1:*
```
The objective of golf is to play a set of holes in the least number of strokes. A round of golf typically consists of 18 holes. Each hole is played once in the round on a standard golf course. Each stroke is counted as one point, and the total number of strokes is used to determine the winner of the game.
```

*Knowledge 2:*
```
Golf is a precision club-and-ball sport in which competing players (or golfers) use many types of clubs to hit balls into a series of holes on a course using the fewest number of strokes. The goal is to complete the course with the lowest score, which is calculated by adding up the total number of strokes taken on each hole. The player with the lowest score wins the game.
```

我们使用的是[Liu et al. 2022](https://arxiv.org/pdf/2110.08387.pdf)论文中提供的提示。

下一步是整合知识，得到预测。我把问题重新格式化为QA格式，以指导答案的格式。

*Prompt:*
```
Question: Part of golf is trying to get a higher point total than others. Yes or No?

Knowledge: The objective of golf is to play a set of holes in the least number of strokes. A round of golf typically consists of 18 holes. Each hole is played once in the round on a standard golf course. Each stroke is counted as one point, and the total number of strokes is used to determine the winner of the game.

Explain and Answer: 
```

*Answer 1 (confidence very high):*
```
No, the objective of golf is not to get a higher point total than others. Rather, the objective is to play a set of holes in the least number of strokes. The total number of strokes is used to determine the winner of the game, not the total number of points.
```


```
Question: Part of golf is trying to get a higher point total than others. Yes or No?

Knowledge: Golf is a precision club-and-ball sport in which competing players (or golfers) use many types of clubs to hit balls into a series of holes on a course using the fewest number of strokes. The goal is to complete the course with the lowest score, which is calculated by adding up the total number of strokes taken on each hole. The player with the lowest score wins the game.

Explain and Answer:
```

*Answer 2 (confidence is a lot lower):*
```
Yes, part of golf is trying to get a higher point total than others. Each player tries to complete the course with the lowest score, which is calculated by adding up the total number of strokes taken on each hole. The player with the lowest score wins the game.
```

这个例子中发生了一些非常有趣的事情。在第一个答案中，该模型非常自信，但在第二个答案中却不那么自信了。为了演示，我简化了这个过程，但是在得出最终答案的时候，还有一些细节需要考虑。请看论文中的内容。

---

## Automatic Prompt Engineer (APE)

![](../img/APE.png)

[Zhou et al., (2022)](https://arxiv.org/abs/2211.01910)提出自动提示工程师(APE)是一个自动指令生成和选择的框架。指令生成问题被看作是自然语言合成问题，是一个使用LLMs生成和搜索候选解决方案的黑箱优化问题。

第一步涉及到一个大型的语言模型（作为推理模型），它被赋予输出示范，为一个任务生成指令候选方案。这些候选方案将指导搜索程序。使用目标模型执行指令，然后根据计算的评价分数选择最合适的指令。

APE发现了一个比人类设计的 "让我们一步步思考 "的提示更好的零拍CoT提示（Kojima等人，2022）。

提示 "让我们一步一步地解决这个问题，以确保我们有正确的答案。"引起了连环推理，提高了MultiArith和GSM8K基准的性能。

![](../img/ape-zero-shot-cot.png)

本文涉及到与提示工程相关的一个重要话题，即自动优化提示的想法。虽然我们在本指南中没有深入探讨这个话题，但如果你对这个话题感兴趣，这里有几篇关键的论文。

- [AutoPrompt](https://arxiv.org/abs/2010.15980) - proposes an approach to automatically create prompts for a diverse set of tasks based on gradient-guided search.
- [Prefix Tuning](https://arxiv.org/abs/2101.00190) - a lightweight alternative to fine-tuning that prepends a trainable continuous prefix for NLG tasks. 
- [Prompt Tuning](https://arxiv.org/abs/2104.08691) - proposes a mechanism for learning soft prompts through back propagation.

---
[Previous Section (Basic Prompting)](./prompts-basic-usage.md)

[Next Section (Applications)](./prompts-applications.md)
