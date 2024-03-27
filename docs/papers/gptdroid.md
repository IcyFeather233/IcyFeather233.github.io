Make LLM a Testing Expert: Bringing Human-like Interaction to Mobile GUI Testing via Functionality-aware Decisions

[Arxiv Link](https://arxiv.org/pdf/2310.15780.pdf)

**Accepted by ICSE24**

摘要：

自动化图形用户界面（GUI）测试在确保应用质量方面发挥着至关重要的作用，尤其是在移动应用已经成为我们日常生活中不可或缺的部分的今天。尽管学习型技术在自动化GUI测试中的使用日益普及，因其能够生成类似人的交互，但它们仍然存在几个限制，例如测试覆盖率低，泛化能力不足，以及过度依赖训练数据。受到像ChatGPT这样的大型语言模型（LLM）在自然语言理解和问题回答方面的成功启发，我们将移动GUI测试问题形式化为一个Q&A任务。我们提出了GPTDroid，让LLM通过传递GUI页面信息与移动应用进行对话，以引发测试脚本，并执行它们以继续向LLM传递应用反馈，迭代整个过程。在这个框架内，我们还引入了一个功能感知的记忆提示机制，使LLM具有保留整个过程的测试知识，并进行长期的，基于功能的推理以指导探索的能力。我们在来自Google Play的93个应用上对其进行了评估，并证明其在活动覆盖率方面比最好的基线高出32%，并以更快的速度发现了31%更多的错误。此外，GPTDroid在Google Play上发现了53个新的错误，其中35个已经被确认并修复。

下面是GPTDroid的架构图：

<div style="text-align: center;">
    <img src="../../../assets/GPTDroid/overview.png" width="50%">
</div>

下图给出了一个GPTDroid的工作示例：

<div style="text-align: center;">
    <img src="../../../assets/GPTDroid/demonstration.png" width="50%">
</div>

图中可以看到一个示例聊天记录。LLM可以理解应用的GUI，并提供详细的操作来导航应用（例如，图中的A1-A5）。为了弥补其错误的预测（图中的A2），GPTDroid通过实时反馈引导它重新生成输入，直到触发有效的页面转换。即使在长时间的测试追踪之后，它仍然保持清晰的测试逻辑，以进行复杂的操作推理（图中的A3，A4），并且它可以优先测试重要的功能（例如，图中的A5）。