

- 本节首先介绍自上而下解析的一般方法
- 然后，我们定义适用于自上而下解析的 LL(1) 文法
- 最后讨论自上而下的错误恢复

### 自上而下解析的一般方法

自上而下解析的一般过程是：

- 将文法的开始符号作为解析树的根结点，从此出发；
- 对每个结点尝试所有可能的产生式，并依据产生式构建子节点；
- 从上到下，从左到右为输入串建立解析树，即寻找最左推导。

这种方式是一种试探过程，因此在过程中所做的尝试有可能是错误的；在遇到解析失败时，这种方法需要回溯，确保尝试了所有可能的推导方案。

### 左递归

上述解析方法面临的根本性困难是左递归：

- 如果存在非终结符 $A$ 使得存在 $A \Rightarrow^+ A\alpha$，那么就称该文法存在左递归。
  - 如果 $A \to A\alpha$ ，则称由此产生的左递归为直接左递归。
- 如果文法中存在左递归，在为 $A$ 构建解析树时，我们有可能按照 $A \Rightarrow^+ A\alpha$ 的推导过程进行尝试，导致递归永远无法退出，陷入死循环。

---

我们可以通过如下的方式消除直接左递归：

- 考虑 $A$ 的所有产生式

  $$
  A\to A\alpha_1\, |\, A\alpha_2 \, | \, \cdots\, | \, A\alpha_m \,  | \,  \beta_1 \, | \, \beta_2 \, | \, \cdots \,  | \,  \beta_n
  $$

  其中 $\beta_i$ 都不以 $A$ 开头。

- 将其改写成如下形式：
  
  $$
  A \to \beta_1A' \mid\beta_2A'\mid\cdots\mid\beta_nA' \\
  A' \to \alpha_1A' \mid\alpha_2A'\,| \,\cdots\,| \,\alpha_mA'\mid\epsilon
  $$

- 本质上，$A$ 产生的任一符号串都形如 $\beta_i \alpha_{i_1}\alpha_{i_2}\cdots\alpha_{i_k}$

---

利用消除左递归的方法，我们可以使用如下算法系统地消除文法中的所有左递归。

- 算法假设文法中不存在 $A \Rightarrow^* A$ 以及 $A\Rightarrow\epsilon$，这两者都可以通过预处理消除。

算法的流程如下：

- 将所有非终结符排列成 $A_1, A_2, \cdots, A_n$；
- `for each i from i to n: `
  - `for each j from 1 to i - 1:`
    - 如果存在 $A_i \to A_j \gamma$，进行如下替换；
    - 假设 $A_j$ 的所有产生式为 $A_j \to \delta_1\mid \delta_2 \mid \cdots\mid \delta_k$ ；
    - 将 $A_i \to A_j\gamma$ 替换为 $A_j \to \delta_1\gamma\mid\delta_2\gamma\mid\cdots\mid\delta_k\gamma$；
    - 消除所有 $A_i$ 产生的直接左递归。


为了说明其正确性，我们指出在第 $i$ 轮外层循环结束后，我们都成立：

- 如果有 $A_k \to A_l \alpha$ 且 $k < i$，则一定有 $k < l$ 。

### 提左因子

我们的目标是，将文法改造成，只需要根据当前的输入以及当前处理的非终结符，就可以精确地选择产生式。所谓精确是指，如果选择的产生式匹配成功，那么它确实有效；如果选择的产生式最终匹配失败，则其他的任何选择也都无法匹配。

一般的文法是不满足这样的性质的。例如，条件语句有两个产生式：

$$
stmt \to \textbf{if } expr  \textbf{ then } stmt \textbf{ else } stmt \\
\mid \textbf{ if } expr \textbf{ then } stmt
$$

当看见输入记号 if 时，我们并不确定要用哪个产生式来扩展 $stmt$ 。为此，我们可以将文法改写成：

$$
stmt \to \textbf{ if } expr \textbf{ then } stmt \ optional\_else\_part \\
\mid \textbf{ other } \\
optional\_else\_part \to \textbf{ else } stmt \mid \epsilon
$$

这样，处理 $stmt$  时如果输入是 if 符号，我们就可以唯一确定应该尝试的产生式。

---

我们形式化地说明想要达成的条件：

- 定义 

  $$
  FIRST(\alpha) = \{a \mid \alpha \Rightarrow^* a\gamma,  a\in V_T \} \, \cup\, \{\epsilon\mid\alpha \Rightarrow ^* \epsilon\}
  $$

  - 符号串 $\alpha$ 的开始符号集合，是由 $\alpha$ 推出的所有非空串的首终结符组成的集合；
  - 特别地，如果 $\alpha$ 可以推出空串，规定 $\epsilon$ 也在开始符号集合中。

- 如果对于 $A$ 的任意两个不同的产生式选择 $\alpha_i$ 和 $\alpha_j$ ，有

  $$
  FIRST(\alpha_i) \ \cap \ FIRST(\alpha_j) = \emptyset
  $$

  则 $A$ 就可以根据当前面临的下一个符号 $a$ ，精准地选择某一个进行尝试，也就是满足 $a\in FIRST(\alpha_i)$ 的那一个。

---

- 然而，如果 $\epsilon \in FIRST(A)$，我们实际并不能确定应该选择哪一个产生式

  - 一种情况是，按照上面叙述的逻辑进行选择；
  - 另一种情况是，$A$ 会应用最终仅产生 $\epsilon$ 的推导规则，当前的字符对应到文法串中的下一个符号。

- 定义
  
  $$
  FOLLOW(A) = \{a \mid S \Rightarrow^* \alpha Aa\beta  \} \, \cup\, \{\$ \mid S\Rightarrow^* \alpha A\}
  $$

  - 非终结符 $A$ 的后继符号集合，是所有在句型中可以直接出现在 $A$ 后面的终结符的集合；
  - 特别地，如果 $A$ 是某个句型的最右符号，规定标识符 $\$$ 也属于该集合。这个标识符本身不出现在文法中。

- 

### LL(1) 文法

###  递归下降的解析预测

### 非递归的解析预测

### 预测解析的错误恢复

