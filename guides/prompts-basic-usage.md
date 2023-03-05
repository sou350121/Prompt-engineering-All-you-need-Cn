# Basic Prompting

在上一篇指南中，我们介绍并给出了一个提示的基本例子。

在本指南中，我们将提供更多关于如何使用提示的例子，并介绍对更高级指南很重要的关键概念。

通常，学习概念的最好方法是通过实例来学习。下面我们介绍几个例子，说明精心设计的提示语如何用于执行各种类型的有趣和不同的任务。

Topics:
- [Text Summarization](#text-summarization)
- [Information Extraction](#information-extraction)
- [Question Answering](#question-answering)
- [Text Classification](#text-classification)
- [Conversation](#conversation)
- [Code Generation](#code-generation)
- [Reasoning](#reasoning)

---

## Text Summarization
自然语言生成中的一个标准任务是文本总结。文本总结可以包括许多不同的味道和领域。事实上，语言模型最有前途的应用之一是能够将文章和概念总结成快速和易于阅读的摘要。让我们尝试一下使用提示语的基本总结任务。

假设我对了解抗生素感兴趣，我可以尝试这样的提示。

*Prompt:*
```
Explain antibiotics

A:
```

*Output:*
```
Antibiotics are a type of medication used to treat bacterial infections. They work by either killing the bacteria or preventing them from reproducing, allowing the body’s immune system to fight off the infection. Antibiotics are usually taken orally in the form of pills, capsules, or liquid solutions, or sometimes administered intravenously. They are not effective against viral infections, and using them inappropriately can lead to antibiotic resistance.
```

A: "是一种明确的提示格式，在回答问题时使用。我在这里用它来告诉模型，有一个预期的进一步。在这个例子中，并不清楚这与不使用它有什么用，但我们将把它留在后面的例子中。让我们假设这信息太多，想进一步总结一下。事实上，我们可以指示模型将其总结为一句话，像这样。

*Prompt:*
```
Antibiotics are a type of medication used to treat bacterial infections. They work by either killing the bacteria or preventing them from reproducing, allowing the body’s immune system to fight off the infection. Antibiotics are usually taken orally in the form of pills, capsules, or liquid solutions, or sometimes administered intravenously. They are not effective against viral infections, and using them inappropriately can lead to antibiotic resistance.

Explain the above in one sentence:
```

*Output:*
```
Antibiotics are medications used to treat bacterial infections by either killing the bacteria or stopping them from reproducing, but they are not effective against viruses and overuse can lead to antibiotic resistance.
```

在不太注意上述输出的准确性的情况下，这是我们将在以后的指南中涉及的问题，该模型试图用一句话来概括这段话。你可以对指令进行巧妙的处理，但我们将把这个问题留给后面的章节。请随意在这里暂停，并进行实验，看看你是否能得到更好的结果。

---
## Information Extraction
虽然语言模型被训练来执行自然语言生成和相关任务，但它也非常能够执行分类和一系列其他自然语言处理（NLP）任务。. 

下面是一个从给定段落中提取信息的提示例子。

*Prompt:*
```
Author-contribution statements and acknowledgements in research papers should state clearly and specifically whether, and to what extent, the authors used AI technologies such as ChatGPT in the preparation of their manuscript and analysis. They should also indicate which LLMs were used. This will alert editors and reviewers to scrutinize manuscripts more carefully for potential biases, inaccuracies and improper source crediting. Likewise, scientific journals should be transparent about their use of LLMs, for example when selecting submitted manuscripts.

Mention the large language model based product mentioned in the paragraph above:
```

*Output:*
```
The large language model based product mentioned in the paragraph above is ChatGPT.
```

我们有很多方法可以改进上面的结果，但这已经非常有用了。

现在应该很明显，你可以通过简单地指示模型做什么来要求它执行不同的任务。这是一种强大的能力，人工智能产品建造者已经在使用这种能力来建造强大的产品和体验。


Paragraph source: [ChatGPT: five priorities for research](https://www.nature.com/articles/d41586-023-00288-7) 

---
## Question Answering

让模型回答具体答案的最好方法之一是改进提示的格式。如前所述，一个提示可以结合指示、背景、输入和输出指标来获得改进的结果。虽然这些部分不是必须的，但它成为一种良好的做法，因为你的指示越具体，你将得到更好的结果。下面是一个例子，说明在一个更有条理的提示之后，会出现什么样的情况。

*Prompt:*
```
Answer the question based on the context below. Keep the answer short and concise. Respond "Unsure about answer" if not sure about the answer.

Context: Teplizumab traces its roots to a New Jersey drug company called Ortho Pharmaceutical. There, scientists generated an early version of the antibody, dubbed OKT3. Originally sourced from mice, the molecule was able to bind to the surface of T cells and limit their cell-killing potential. In 1986, it was approved to help prevent organ rejection after kidney transplants, making it the first therapeutic antibody allowed for human use.

Question: What was OKT3 originally sourced from?

Answer:
```

*Output:*
```
Mice.
```

Context obtained from [Nature](https://www.nature.com/articles/d41586-023-00400-x).

---

## Text Classification
到目前为止，我们已经使用了简单的指令来执行任务。作为一名提示工程师，你需要更好地提供更好的指示。但这还不是全部! 你还会发现，对于较难的用例，仅仅提供指示是不够的。这时，你需要更多地考虑上下文和你可以在提示中使用的不同元素。你可以提供的其他元素是 "输入数据 "或 "示例"。

让我们试着通过提供一个文本分类的例子来证明这一点。

*Prompt:*
```
Classify the text into neutral, negative or positive. 

Text: I think the food was okay. 
Sentiment:
```

*Output:*
```
Neutral
```

我们给出了对文本进行分类的指令，模型回应说"'中性'"，这是正确的。这没有什么不对，但是我们说，我们真正需要的是让模型以我们想要的确切格式给出标签。因此，我们希望它返回 "中立"，而不是 "中性"。我们如何实现这一点。有不同的方法来做到这一点。我们在这里关心的是特异性，所以我们能提供的提示信息越多，结果就越好。我们可以尝试提供例子来指定正确的行为。让我们再试一次。

*Prompt:*
```
Classify the text into neutral, negative or positive. 

Text: I think the vacation is okay.
Sentiment: neutral 

Text: I think the food was okay. 
Sentiment:
```

*Output:*
```
neutral
```

完美！这一次，模型返回 "中性"，这是我正在寻找的具体标签。这一次，模型返回了 "中性"，这是我正在寻找的具体标签。看来，提示中提供的例子帮助模型在其输出中变得具体。为了强调为什么有时具体化是很重要的，看看这个例子并发现问题。

*Prompt:*
```
Classify the text into nutral, negative or positive. 

Text: I think the vacation is okay.
Sentiment:
```

*Output:*
```
Neutral
```

问题在哪?

---

## Conversation
也许你可以通过提示工程实现的更有趣的事情之一是告诉系统如何表现，其意图和身份。这在你构建对话系统时特别有用。

例如，让我们创建一个能够对问题做出更多技术和科学回答的对话系统。注意我们是如何通过指令明确地告诉它如何行为的。

*Prompt:*
```
The following is a conversation with an AI research assistant. The assistant tone is technical and scientific.

Human: Hello, who are you?
AI: Greeting! I am an AI research assistant. How can I help you today?
Human: Can you tell me about the creation of blackholes?
AI:
```

*Output:*
```
Sure! Black holes are regions of spacetime where the gravitational force is so strong that nothing, not even light, can escape from it. They are created when a very massive star dies and its core collapses in on itself, forming a singularity of infinite density. The intense gravity of the singularity pulls in all the matter and radiation around it, creating the black hole.
```

我们的人工智能研究助理听起来有点太技术化了，对吗？好吧，让我们改变这种行为，指示系统给出更容易理解的答案。

*Prompt:*
```
The following is a conversation with an AI research assistant. The assistant answers should be easy to understand even by primary school students.

Human: Hello, who are you?
AI: Greeting! I am an AI research assistant. How can I help you today?
Human: Can you tell me about the creation of black holes?
AI: 
```

*Output:*
```
Sure! Black holes are created when a very massive star runs out of fuel and collapses in on itself. The gravity of the star is so strong that nothing, not even light, can escape its pull. This creates a point in space-time with an intense gravitational force called a black hole.
```

你可以继续改进它。如果你增加更多的例子，可能会得到更好的结果。

---

## Code Generation

LLMs相当有效的一个应用是代码生成。Copilot是这方面的一个很好的例子。有大量的代码生成任务，你可以通过巧妙的提示来完成。让我们看一下下面的几个例子。

首先，让我们试试一个简单的程序，向用户打招呼。

*Prompt:*
```
/*
Ask the user for their name and say "Hello"
*/
```

*Output:*
```
let name = prompt("What is your name?");
console.log(`Hello, ${name}!`);
```

你可以看到，我们甚至不需要指定要使用的语言。

让我们转换一下层次。我想向你展示，只要在设计提示语上多下点功夫，LLM就能变得多么强大。

*Prompt:*
```
"""
Table departments, columns = [DepartmentId, DepartmentName]
Table students, columns = [DepartmentId, StudentId, StudentName]
Create a MySQL query for all students in the Computer Science Department
"""
```

*Output:*
```
SELECT StudentId, StudentName 
FROM students 
WHERE DepartmentId IN (SELECT DepartmentId FROM departments WHERE DepartmentName = 'Computer Science');
```

This is very impressive. In this case we provided data about the database schema and asked it to generate a valid MySQL query. 

---

## Reasoning

对于今天的LLM来说，最困难的任务之一也许是需要某种形式的推理。推理是我最感兴趣的领域之一，因为LLM可以产生复杂的应用类型。

在涉及数学能力的任务上已经有了一些改进。也就是说，必须注意的是，目前的LLM在执行推理任务时很困难，所以这需要更先进的提示工程技术。我们将在下一篇指南中介绍这些高级技术。现在，我们将介绍几个基本的例子来展示算术能力。

*Prompt:*
```
What is 9,000 * 9,000?
```

*Output:*
```
81,000,000
```

试试更难的。

*Prompt:*
```
The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 

A: 
```

*Output*
```
No, the odd numbers in this group add up to an odd number: 119.
```

That's incorrect! Let's try to improve this by improving the prompt.

*Prompt:*
```
The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 

Solve by breaking the problem into steps. First, identify the odd numbers, add them, and indicate whether the result is odd or even. 
```

*Output:*
```
Odd numbers: 15, 5, 13, 7, 1
Sum: 41 
41 is an odd number.
```

好多了。顺便说一下，我试了几次，系统有时会失败。如果你提供一个更好的指导，结合实例，可能有助于获得更准确的结果。

我们将继续在指南的这一部分包括更多的常见应用实例。

在即将到来的指南中，我们将涵盖更多的高级提示工程概念，以提高所有这些和更困难的任务的性能。


---

[Previous Section (Prompts Introduction)](./prompts-intro.md)

[Next Section (Advanced Prompting Techniques)](./prompts-advanced-usage.md)
