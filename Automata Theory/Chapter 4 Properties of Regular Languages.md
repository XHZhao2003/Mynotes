---
zhihu-title: Chapter 4 Properties of Regular Languages
zhihu-topics:
  - 自动机
  - 理论计算机
  - 形式语言
---
- This chapter explores the properties of regular languages.
- We will first introduce the "pumping lemma", which is a useful tool to prove that certain languages are not regular.
- We will discuss the closure properties of regular languages. These properties let us build complex regular languages (or in form of automata) that are constructed from other languages by certain operations.
- We will discuss the decision properties. The question we aim to answer is, whether two automata define the same language.

---
## 4.1 Proving Languages not to be Regular

**Theorem 4.1**: (Pumping lemma for regular languages) Let $L$ be a regular language. Then there exists a constant $n$ such that if $w\in L$ and $|w|\geq n$, then we can break $w$ into three strings $w=xyz$, such that:
1. $y \neq \epsilon$.
2. $|xy|\leq n$.
3. For all $k\geq 0$, $xy^kz \in L$.

That is, we can always find a nonempty string $y$ in the first $n$ symbols of $w$ that can be "pumped", i.e., repeating $y$ any number of times or just deleting it keeps the resulting string in the language $L$.

**Proof**: Since $L$ is regular, then there exists a DFA $A$ such that $L(A)=L$. Let $n$ be the number of states of $A$. Now consider a string $w=a_1a_2\cdots a_m$ where $m\geq n$. For $0\leq i \leq n$, define $p_i = \hat{\delta}(q_0, a_1a_2, \cdots a_i)$, i.e., $p_i$ is the state $A$ is in after reading the first $i$ symbols of $w$. By the pigeonhole principle, we can find two different integers $i$ and $j$ with $0 \leq i < j \leq n$ and $p_i = p_j$. Then we can break $w=xyz$ as follows:

1. $x = a_1a_2\cdots a_i$.
2. $y = a_{i+1}a_{i+2}\cdots a_j$.
3. $z = a_{j+1}a_{j+2}\cdots a_m$.

Then, consider what $A$ will do on the input $xy^kz$. First, by $\hat{\delta}(q_0, x) = p_i$, after reading $x$, $A$ will be in state $p_i$. Then, no matter how many times is $y$ repeated, we have $\hat{\delta}(p_i, y^k) = p_i$, i.e., after reading $y^k$, $A$ will still be in $p_i$. Finally, by $\hat{\delta}(p_i, z)=p_n$, after reading $z$, $A$ will be in $p_n$ which is the same as the result $A$ has on input $w=xyz$. Since $w\in L$, then $xy^kz \in L$.

---
**Use Pumping Lemma to Tell Languages that are not Regular**

**Example 1**: Consider showing that $L_{eq} = \lbrace w \mid w \text{ has an equal number of 0's and 1's.} \rbrace$ is not a regular language. 

Using the pumping lemma, we always prove by contradiction and assume the language is a regular language. Then there exists some $n$ satisfying the pumping lemma. Consider the string $w=0^n1^n$. To pump this string, we find that $y$ can only have 0's and no 1's, because $|xy|\leq n$. Then $xy^kz$ will has more 1's than 0's if $k > 1$, and $xy^kz \notin L_{eq}$.

---

**Example 2**: Consider showing that $L_{pr} = \lbrace w \mid w \text{ consists of 1's and its length is a prime number} \rbrace$ is not a regular language.

Suppose $L_{pr}$ is regular, then there exists some $n$ satisfying the pumping lemma. Consider some prime $p \geq n+2$ and $w = 1^p \in L_{pr}$. 

Now we are to construct a string whose length is not a prime number, by pumping a part of $w$. Let the length of pumped part given by the pumping lemma be $|y|=m$, then $|xz|=p-m$. The string we construct is $xy^{p-m}z$, whose length is 

$$
|xz|+(p-m)|y| = p-m +(p-m)m = (1+m)(p-m)
$$
which is not a prime number.

---
## 4.2 Closure Properties of Regular Languages

We first summary the principle closure properties for regular languages.

1. The union of two regular languages is regular.
2. The intersection of two regular languages is regular.
3. The complement of two regular languages is regular.
4. The difference of two regular languages is regular.
5. The reversal of a regular language is regular.
6. The closure of a regular language is regular.
7. The concatenation of two regular languages is regular.
8. A homomorphism of a regular language is regular.
9. The inverse homomorphism of a regular language is regular.

---
**Operations in Regular Expressions**

First, we note that there are three operations used in the definition of regular expressions: union, closure and concatenation.

Since these operations when performed on regular expressions give regular expression, and we have shown the equivalence between regular expressions and finite automata, then we conclude the regular languages is close under these three operations.

---

**Closure Under Complementation**

**Theorem 4.2**: If $L$ is a regular language over $\Sigma$, then $\overline{L} = \Sigma^* - L$ is regular.

**Proof**: Let $L = L(A)$ for some DAF $A = (Q, \Sigma, \delta, q_0, F)$. We construct a DFA $B$ such that $L(B) = \overline{L}$. In fact, we only need to define $B = (Q, \Sigma, \delta, q_0, Q-F)$. That is, $B$ accepts if and only if $A$ does not accept. Thus we conclude $L(B) = \overline{L(A)}$.

Notice it is important that $\hat{\delta}(q_0, w)$ always exists. If there were miss transitions in $A$, there would be strings that lead neither to an accepting nor non-accepting state of $A$. Fortunately, the strict definition of DFA guarantees this.

---




