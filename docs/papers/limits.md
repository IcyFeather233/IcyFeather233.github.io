Testing the limits: Unusual text inputs generation for mobile app crash detection with large language model

[Arxiv Link](https://arxiv.org/pdf/2310.15657.pdf)

**Accepted by ICSE24**

摘要：

移动应用已经成为我们日常生活中无处不在的一部分，为用户提供各种服务和实用工具的访问。文本输入作为用户和应用之间的重要交互通道，在搜索查询、身份验证、消息传递等核心功能中起着重要的作用。然而，某些特殊的文本（例如，字体大小为-18）可能会导致应用崩溃，因此，生成多样化的异常输入以全面测试应用是非常需要的。然而，由于爆炸性组合困境、高度的上下文敏感性和复杂的约束关系，这也是一项挑战。本文提出了一种名为InputBlaster的方法，利用LLM自动生成异常文本输入以检测移动应用的崩溃。它将异常输入生成问题视为一个任务，该任务的目标是产生一组测试生成器，每个生成器可以在相同的变异规则下产生一批异常文本输入。具体来说，InputBlaster利用LLM产生测试生成器，同时将变异规则作为推理链，并利用上下文学习模式向LLM展示例子以提高性能。InputBlaster在涉及31个流行Android应用的36个文本输入控件上进行了评估，结果显示，它实现了78%的错误检测率，比最好的基线高出136%。此外，我们将其与自动GUI测试工具集成，并在Google Play的真实世界应用中检测到了37个未见过的崩溃。

下面是Android App Bugs的例子：

<div style="text-align: center;">
    <img src="../../../assets/InputBlaster/bugs.png" width="90%">
</div>

这些Bug其实和复杂的约束关系相关，我们总结了各种约束关系的类别，包括组件内部、组件之间的多种约束：

<div style="text-align: center;">
    <img src="../../../assets/InputBlaster/constrains.png" width="50%">
</div>

下面是InputBlaster的架构图：

<div style="text-align: center;">
    <img src="../../../assets/InputBlaster/overview.png" width="90%">
</div>

下面演示了InputBlaster的工作示例：

<div style="text-align: center;">
    <img src="../../../assets/InputBlaster/how2work.png" width="50%">
</div>

下面是InputBlaster的一个输出例子：

<div style="text-align: center;">
    <img src="../../../assets/InputBlaster/output.png" width="50%">
</div>