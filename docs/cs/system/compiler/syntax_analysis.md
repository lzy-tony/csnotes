# 语法分析

## 自顶向下语法分析

### 带回溯的自顶向下分析

自顶向下分析过程的两类非确定性：

- 选择哪一个非终结符展开；
- 对于选定的非终结符选择哪一个产生式。

最朴素的想法是采用试探和回溯。

### 自顶向下预测分析

通过向前扫描若干个字符进行确定性推导（并非所有文法都可以做到）。

### First 和 Follow 集合

#### First 集合

定义：设上下文无关文法 $G = (V_N, V_T, P, S)$，对于 $\alpha \in (V_N \cup V_T)^*$ 定义：

$$
\text{First}(\alpha) = \{a | \alpha \Rightarrow ^* a \beta, a \in V_T, \beta \in (V_N \cup V_T)^* \lor a = \varepsilon, \alpha \Rightarrow ^* \varepsilon \}
$$

直观理解：$\text{First}$ 集合是一个对于一个 **句型** 而言的，代表着该句型能够推导出的 **开头字符** 集合。

对于语法分析，我们关心所有可能句型的 $\text{First}$ 集合，即

$$
X_G = V_N \cup V_T \cup \{\varepsilon \} \cup \{v | A \rightarrow u \in P, v \text{ is suffix of } u\}
$$

可以迭代计算所有 $x \in X_G$ 的 $\text{First}$ 集合：

- 初始化：所有非终结符和 $\varepsilon$ 集合为自身（$\text{First}(x) = \{x\}$），否则为 $\Phi$。
- 重复直到所有的集合都不再变化：
    - 对于 $y_1y_2 \dots y_k \in \{v | A \rightarrow u \in P, v \text{ is suffix of } u\}, k \ge 1$。
        如果存在 $i$，$\varepsilon$ 在 $\forall 1 \le j \le i - 1, y_j$ 的 $\text{First}$ 集合中但不在 $y_i$ 的集合中，则
$$
\text{First}(y_1 \dots y_k) = \text{First}(y_1) \cup \dots \cup \text{First}(y_i) - \varepsilon
$$
        否则不需要 $-\varepsilon$，即
$$
\text{First}(y_1 \dots y_k) = \text{First}(y_1) \cup \dots \cup \text{First}(y_k)
$$
    - 若有 $A \rightarrow y_1 y_2 \dots y_k$，则置
$$
\text{First}(A) = \text{First}(A) \cup \text{First}(y_1y_2 \dots y_l)
$$

#### Follow 集合

定义：设上下文无关文法 $G = (V_N, V_T, P, S)$，对于 $A \in V_N$ 定义：

$$
\text{Follow}(A) = \{a | S$ \Rightarrow ^* \alpha A \beta $ \land a \in \text{First}(\beta $), \alpha, \beta \in (V_N \cup V_T)^* \}
$$

其中 $ 为符号序列的结束符。

直观理解：$\text{Follow}$ 集合是一个对于一个 **句型** 而言的，代表着该句型能够推导出的 **紧邻字符** 集合。

可以迭代计算所有 $A \in V_N$ 的 $\text{Follow}$ 集合：

- 初始时，置 $\text{Follow}(A) = \{\$\}$；对其它 $A \in V_N$，置 $\text{Follow}(A) = \Phi$。
- 重复直到所有的集合都不再变化：若有 $A \rightarrow \alpha B \beta \in P$，则
  - 令 $\text{Follow}(B) = \text{Follow}(B) \cup (\text{First} - \{\varepsilon \})$
  - 若 $\varepsilon \in \text{First}(\beta)$，则令 $\text{Follow}(B) = \text{Follow}(B) \cup \text{Follow}(A)$

### LL(1) 文法

定义：设上下文无关文法 $G = (V_N, V_T, P, S)$，对于任何产生式 $A \rightarrow \alpha \in P$，其预测集合 $\text{PS}(A \rightarrow \alpha)$ 定义为

- 如果 $\varepsilon \notin \text{First}(\alpha)$，那么 $\text{PS}(A \rightarrow \alpha) = \text{First}(\alpha)$；
- 如果 $\varepsilon \in \text{First}(\alpha)$，那么 $\text{PS}(A \rightarrow \alpha) = (\text{First}(\alpha) - \{\varepsilon\}) \cup \text{Follow}(A)$。

文法 $G$ 是 LL(1) 的，当且仅当对于 $G$ 中任何两个有相同左部的不同产生式 $A \rightarrow \alpha$ 和 $A \rightarrow \beta$，都满足：

$$
\text{PS}(A \rightarrow \alpha) \cap \text{PS}(A \rightarrow \beta) = \Phi
$$

直观理解：YouOnlyLookOnce，只需向前扫描一个字符就可以做出确定性推断。

### 递归下降分析

- 每遇到一个终结符，则判断当前读入的单词符号是否与该终结符相匹配，若匹配，则继续读取下一个单词；若不匹配，则进行错误处理。
- 每遇到一个非终结符，则调用相应的分析子程序。

直观理解：利用 LL(1) 的性质扫描进行 switch case 并递归下降。