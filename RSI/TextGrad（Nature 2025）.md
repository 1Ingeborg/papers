# **TextGrad**（Nature 2025）

![image-20261119194903654](./Image/TextGrad%EF%BC%88Nature%202025%EF%BC%89/image-20261119194903654.png)

## Abstract

**Automatic “Differentiation” via Text：通过文本实现自动“微分”**

开发具有理论依据且能够自动化优化的**复合人工智能系统（Compound AI Systems）** 的方法，已成为当前最重要的新挑战之一。早期神经网络也曾面临类似的挑战，直到反向传播（Backpropagation）和自动微分（Automatic Differentiation）技术的出现，才使优化过程变得简单易用且高度自动化。

受此启发，我们提出了 **TextGrad**，一个通过文本实现自动“微分”的强大框架。TextGrad 通过反向传播由大型语言模型提供的**文本反馈（Textual Feedback）**，来改进复合人工智能系统中的各个组件。TextGrad 遵循 PyTorch 的语法和抽象设计，具有灵活且易于使用的特点。该框架能够直接应用于多种任务，用户只需提供**目标函数（Objective Function）**，而无须调整框架内部的组件或提示词。

**自动微分（Automatic Differentiation, AD）：** 是一种基于计算图和链式法则，**自动计算损失函数对模型参数的梯度**。

## **Introduction**

人工智能领域的许多进展都依赖于人工神经网络和可微优化（Differentiable Optimization）。神经网络中的不同部分（例如两个神经元）通过矩阵乘法等可微函数进行信息交互。因此，利用数值梯度（Numerical Gradients）和反向传播（Backpropagation） 来确定各个参数的调整方向，从而改善模型性能，已成为训练 AI 模型的自然方式。实现反向传播的灵活自动微分框架，已经成为 AI 模型发展过程中不可或缺的工具。

在这里，我们将“微分”和“梯度”作为一种比喻，用于描述 LLM 生成的文本反馈。在这一框架中，把复合 AI 系统（多个 LLM 调用、工具、模拟器串起来的系统）看成一张**计算图（Computational Graph）**。

其中的 $\color{#228B22}{\text{变量}}$是复杂函数调用的**输入和输出**，但这些函数不一定是可微的。



## **TextGrad**

![image-20261124154708613](./Image/TextGrad%EF%BC%88Nature%202025%EF%BC%89/image-20261124154708613.png)

首先通过一个由**两次 LLM 调用**组成的系统，展示 TextGrad 的工作方式，然后给出适用于任意复杂系统的更一般形式。

### **计算图（computation graph）**

考虑以下系统：

$$
\text{Prediction}
    =
{\color{#623F99}\text{LLM}}
({\color{#228B22}\text{Prompt}}+\text{Question}),
\qquad (1)
$$

$$
\text{Evaluation}
    =
{\color{#623F99}\text{LLM}}
(\text{Evaluation Instruction}+\text{Prediction}),
\qquad (2)
$$

使用 $\color{#228B22}{\text{绿色}}$ 表示需要优化的自由参数，即提示词（Prompt）。

使用 ${\color{#623F99}\text{LLM}}(x)$ 表示将 $x$ 作为提示词输入语言模型，并获取其响应。

也可以使用链式表示法：

$$
\begin{aligned}
{\color{#228B22}\text{Prompt}}+\text{Question}
\xrightarrow{\color{#623F99}{\text{LLM}}}
\text{Evaluation Instruction}+\text{Prediction}
\xrightarrow{\color{#623F99}{\text{LLM}}}
\text{Evaluation},
\end{aligned}
\qquad (3)
$$

### **梯度计算（gradient computation）**

在这个示例系统中，为了根据评测结果改进 $\color{#228B22}{\text{Prompt}}$，我们通过以下方式构建了一种类似于**反向传播算法**机制。

针对：

$$
{\color{#228B22}\text{Prompt}}
\xrightarrow{\text{LLM}}
\text{Prediction}
\xrightarrow{\text{LLM}}
\text{Evaluation}
$$

可以进行类似的反向传播：

$$
\frac{\partial\text{Evaluation}}{\partial\text{Prediction}}={\color{#623F99}\nabla_{\text{LLM}}}(\text{Prediction},\text{Evaluation}),\qquad (4)
$$

$$
\begin{aligned}\frac{\partial\text{Evaluation}}{\partial{\color{#228B22}\text{Prompt}}}=\frac{\partial\text{Evaluation}}{\partial\text{Prediction}}\circ\frac{\partial\text{Prediction}}{\partial{\color{#228B22}\text{Prompt}}}={\color{#623F99}\nabla_{\text{LLM}}}\left({\color{#228B22}\text{Prompt}},\text{Prediction},\frac{\partial\text{Evaluation}}{\partial\text{Prediction}}\right)\end{aligned}\qquad (5)
$$

使用 $\color{#623F99}{\nabla_{\text{LLM}}}$ 表示其梯度算子（Gradient Operator）。具体而言，该算子函数返回自然语言形式的反馈，例如：

*“这一预测结果可以通过以下方式得到改进……”*。描述了应该如何修改变量，以改善下游目标，其作用类似于优化过程中的梯度。

> **梯度算子的示例实现（Example implementation for the gradient operator）**
>
> $$
> \begin{aligned}
> \frac{\partial \mathcal L}{\partial x}
> &=
> {\color{#623F99}\nabla_{\text{LLM}}}
> \left(x,y,\frac{\partial \mathcal L}{\partial y}\right)
> \\
> &\triangleq
> \text{“Here is a conversation with an LLM: }
> \lbrace x|y\rbrace\text{.”}
> \end{aligned}
> \qquad (6)
> $$
>
> $$
> +\ {\color{#623F99}\text{LLM}}
> \begin{pmatrix}
> \text{Here is a conversation with an LLM: }\lbrace x|y\rbrace.\\
> \text{Below are the criticisms on }\lbrace y\rbrace:\\
> \displaystyle
> \left\lbrace\frac{\partial \mathcal L}{\partial y}\right\rbrace\\
> \text{Explain how to improve }\lbrace x\rbrace.
> \end{pmatrix}
> $$
>
> 其中，LLM 接收的提示词含义为：
>
> - 以下是与 LLM 的一段对话： $\lbrace x|y\rbrace$。
> - 以下是针对 $\lbrace y\rbrace$ 的批评意见： $\displaystyle\frac{\partial\mathcal L}{\partial y}$。
> - 请解释应该如何改进 $\lbrace x\rbrace$。
>
> 梯度对象（Gradient Object）由对象出现时所处的上下文以及从 LLM 获取的反馈共同组成。
>
> （人话版：梯度对象为**两个部分**的组合：上下文（Context） $\lbrace x\mid y\rbrace$ 和  反馈（Feedback） $\color{#623F99}{\text{LLM}} \left(x,y,\frac{\partial L}{\partial y}, \text{Instruction}\right)$ ）
>
> 注意，该梯度算子并不依赖于具体的应用领域。一旦完成实现，用于 LLM 调用的梯度算子就可以在整个框架的所有应用中保持固定。
>
> ---
>
> 注 ：我们对梯度算子的符号进行了重载，使其既能够表示输出变量没有后继节点的情况，例如：
>
> $$
> {\color{#623F99}\nabla_{\text{LLM}}}(\text{Prediction},\text{Evaluation})
> $$
>
> 也能够表示输出变量具有后继节点、因而具有梯度的情况，例如：
>
> $$
> {\color{#623F99}\nabla_{\text{LLM}}}\left({\color{#228B22}\text{Prompt}},\text{Prediction},\frac{\partial\text{Loss}}{\partial\text{Prediction}}\right)
> $$



### **优化器（Optimizer）**

在标准梯度下降（Gradient Descent）中，当前参数值通过减去梯度进行更新，例如：

$$
\begin{aligned}
\theta_{\text{new}}
&={\color{#623F99}\mathrm{GradientDescent.step}}
\left(\theta,\frac{\partial\mathcal L}{\partial\theta}\right)
=\theta-\frac{\partial\mathcal L}{\partial\theta}.
\end{aligned}
\qquad (7)
$$

沿用基于梯度的优化方法这一类比，我们使用**文本梯度下降（Textual Gradient Descent，TGD）**：

$$
\begin{aligned}
&{\color{#228B22}\text{Prompt}_{\text{new}}}
={\color{#623F99}\mathrm{TGD.step}}
\left(
{\color{#228B22}\text{Prompt}},
\frac{\partial\text{Evaluation}}
{\partial{\color{#228B22}\text{Prompt}}}
\right).
\end{aligned}
\qquad (8)
$$

> **单次 TGD 迭代的示例实现（Example implementation of one TGD iteration）**
>
> $$
> \begin{aligned}
> x_{\text{new}}
> &=
> {\color{#623F99}\mathrm{TGD.step}}
> \left(x,\frac{\partial\mathcal L}{\partial x}\right)
> \\
> &\triangleq
> {\color{#623F99}\text{LLM}}
> \begin{pmatrix}
> \text{Below are the criticisms on }\lbrace x\rbrace:\\
> \displaystyle
> \left\lbrace\frac{\partial\mathcal L}{\partial x}\right\rbrace\\
> \text{Incorporate the criticisms,}\\
> \text{and produce a new variable.}
> \end{pmatrix}
> \end{aligned}
> \qquad (9)
> $$
>
> 其中，LLM 接收的提示词含义为：
>
> - 以下是针对 $\lbrace x\rbrace$ 的批评意见： $\displaystyle\frac{\partial\mathcal L}{\partial x}$。
> - 将这些批评意见融入修改过程，并生成一个新的变量。
>
> 其中， $x$ 是我们希望改进的变量，而 $\displaystyle\frac{\partial\mathcal L}{\partial x}$ 是我们在反向传播过程中针对该变量获得的反馈。
>
> 与梯度算子类似，该函数也不依赖于具体的应用领域。因此，在框架的所有应用中，TGD 的实现都是相同的。
>

### **一般情况**

上述两次LLM调用可以很容易地应用于任意复杂的系统。

![image-20261124231031351](./Image/TextGrad%EF%BC%88Nature%202025%EF%BC%89/image-20261124231031351.png)

定义一个通用的计算图：

$$
v={\color{#623F99}f_v}
(\mathrm{PredecessorsOf}(v)),
\quad \forall v\in\mathcal V
\qquad (10)
$$

其中， $v$ 是计算图中的一个变量， $\mathcal V$ 是计算图中所有变量的集合。在本文的大部分实验结果中， $v$ 都是自然语言文本。

- $\mathrm{SuccessorsOf}(v)$：返回变量 $v$ 的所有后继节点。
- $\mathrm{PredecessorsOf}(v)$：返回变量 $v$ 的所有前驱节点。

我们将 $\color{#623F99}{f_v}$ 定义为一个变换函数，它接收一组变量作为输入，并生成变量 $v$。例如，我们可以使用 $\color{#623F99}{\text{LLM}}$ 或 $\color{#623F99}{\text{Numerical Simulator}}$（数值模拟器）作为变换函数。

传统自动微分：

$$
\frac{\partial L}{\partial v}
    =
{\sum}_{w\in\mathrm{SuccessorsOf}(v)}
\frac{\partial L}{\partial w}
\frac{\partial w}{\partial v}
$$

TextGrad梯度通过以下方式计算：

$$
\frac{\partial\mathcal L}{\partial v}
    =
\bigcup_{w\in\mathrm{SuccessorsOf}(v)}
{\color{#623F99}\nabla_f}
\left(
v,w,\frac{\partial\mathcal L}{\partial w}
\right),
\qquad (11)
$$

函数 $\color{#623F99}{\nabla_f}$ 接收以下信息作为输入：

- 损失函数 $\mathcal L$ 相对于变量 $v$ 的后继节点的梯度；
- 变量 $v$ 自身的值；
- 后继节点自身的值。

需要注意的是，最终得到的 $\frac{\partial\mathcal L}{\partial v}$梯度变量由**一组上下文和批评意见**组成，这些信息来自该变量被使用的所有位置。

最后，为了更新计算图中任意一个所需的变量 $v$，我们可以使用优化器：

$$
v_{\text{new}}
    =
{\color{#623F99}\mathrm{TGD.step}}
\left(
v,\frac{\partial\mathcal L}{\partial v}
\right),
\qquad (12)
$$

### **目标函数（Objective functions）**

在TextGrad中，目标函数可以不可微：可以是一段 LLM 评估 prompt，也可以是单元测试结果，或者 Vina、matRad 这类模拟器的输出。例如，对于一个代码片段，可以定义如下简单的损失函数：

$$
\begin{aligned}
&\mathrm{Loss}({\color{#228B22}\text{code}},\text{target goal})\\
&={\color{#623F99}\text{LLM}}
\begin{pmatrix}
\text{以下是一段代码：}\lbrace\text{code}\rbrace。\\
\text{以下是该代码需要实现的目标：}\lbrace\text{target goal}\rbrace。\\
\text{请评价该代码的正确性和运行时间复杂度。}
\end{pmatrix}
\end{aligned}
\qquad (13)
$$

### **实例优化与提示词优化（Instance vs Prompt Optimization）**

我们研究了两类优化问题。

Instance optimization（实例优化）：优化的是这道题的答案本身

Prompt optimization（提示词优化）：优化的是所有题共用的那段 system prompt

![image-20261125201815669](./Image/TextGrad%EF%BC%88Nature%202025%EF%BC%89/image-20261125201815669.png)

## **Result**

### **代码优化（Code optimization）**

![image-20261125223522470](./Image/TextGrad%EF%BC%88Nature%202025%EF%BC%89/image-20261125223522470.png)

代码优化是**实例优化（Instance Optimization）** 的应用场景。

代码优化形式的计算图：

$$
\begin{aligned}
\text{Code-Refinement Objective}
={\color{#623F99}\text{LLM}}\big(
\text{Problem}+{\color{#228B22}\text{Code}}+\text{Test-time Instruction}+\text{Local Test Results}\big)
\end{aligned}
\qquad (14)
$$

- 被优化的变量： ${\color{#228B22}\text{Code}}$
- 优化时用的信号：题目自带的几个简单本地测试的运行结果，加上 LLM 的自我评估
- 最终打分：把代码提交到 LeetCode 平台，跑不公开的隐藏测试。隐藏测试的结果不会回传给优化过程，相当于一个留出的测试集，所以不存在泄题。

**任务（Task）：** 我们使用 LeetCode Hard 数据集 [26] 作为代码优化的benchmark。

**基线（Baseline）：** Reflexion [26] 是当时 LeetCode Hard 数据集上最先进的方法。

![image-20261125222956304](./Image/TextGrad%EF%BC%88Nature%202025%EF%BC%89/image-20261125222956304.png)



剩下的不写了，感觉这些benchmark不是很重要。