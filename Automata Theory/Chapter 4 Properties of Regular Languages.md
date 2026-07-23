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

**Close Under Intersection**

**Theorem 4.3**: If $L$ and $M$ are regular languages, then so is $L \cap M$.

**Proof**: Let $L$ and $M$ be the langueges of automata $A_L = (Q_L, \Sigma, \delta_L, q_L, F_L)$ and $A_M = (Q_M, \Sigma, \delta_M, q_M, F_M)$. Noticr that we are assuming that the alphabet of the both automata are the same, or we define $\Sigma$ to be the union of $\Sigma_L$ and $\Sigma_M$.

For $L \cap M$ we shall construct an automaton $A$ that simulates both $A_L$ and $A_M$ by product construction. The pairs of $A$ are pairs of states, the first from $A_L$ and the second from $A_M$. To design the transitions of $A$, suppose $A$ is the input symbol, and suppose $A_L$ goes to state $s$ and $A_M$ goes to $t$, then the next state of $A$ will be $(s, t)$. Formally, we have 

$$
A = (Q_L \times Q_M, \Sigma,\delta, (q_L, q_M), F_L \times F_M)
$$

where $\delta((p, q), a) = (\delta_L(p, a), \delta_M(q, a))$. 

We can prove by induction on $|w|$ that $\hat{\delta}((q_L, q_M), w) = (\hat{\delta}_L(q_L, w), \hat{\delta}_M(q_M, w))$. Therefore, $A$ accepts $w$ if and only if $\hat{\delta}_L(q_L, w) \in F_L$ and $\hat{\delta}_M(q_M, w) \in F_M$. Thus, $A$ accepts the intersection of $L$ and $M$.

---

**Closure Under Difference**

**Theorem 4.4**: If $L$ and $M$ are regular languages, then so is $L - M$.

**Proof**: Observe that $L-M = L \cap \overline{M}$. By Theorem 4.2, $\overline{M}$ is regular. By Theorem 4.3, $L \cap \overline{M}$ is regular.

---

**Closure Under Reversal**

The reversal of a string $a_1a_2\cdots a_n$ is the string written backwards, that is, $a_n a_{n-1}\cdots a_1$. We use $w^R$ for the reversal of string $w$.

The reversal of a language $L$, written $L^R$, is the language consisting of the reversals of all its strings.

**Theorem 4.5**: If $L$ is a regular language, so is $L^R$.

**Proof**: Assume $L$ is defined by regular expression $E$. The proof is a structural inductino on the size of $E$. We show that there is another regular expression $E^R$ such that $L(E^R) = (L(E))^R$.

**Basis**: If $E = \epsilon$ or $\emptyset$ or $a$, then $E^R$ is the same as $E$. 

**Induction**: There are three cases, depending on the form of $E$.

1. $E = E_1 + E_2$. Then $E^R = E_1^R + E_2^R$.
2. $E= E_1E_2$. Then $E^R = E_2 E_1$.
3. $E = E_1^*$. Then $E^R = (E_1^R)^*$.

---

**Homomorphisms**

A string homomorphisms is a function on strings that works by substituting a particular string for each symbol. 

**Definition 4.1**: Given alphabets $\Sigma, T$, an homomorphism is a function $h: \Sigma \to T$. 

- We can apply a homomorphism to a string $w = a_1a_2\cdots a_n\in \Sigma^*$, which is $h(w) = h(a_1)h(a_2)\cdots h(a_n)$.
- We can apply a homomorphism to a language by applying it to each of the strings. That is, $h(L) = \lbrace h(w) \mid w \in L \rbrace$.

> Here, $T$ is a Greek capital tau, the letter following simga.
> 
> Technically, $T$ can be just chosen as set of strings on $\Sigma$, and these strings are viewed as "symbols" of another alphabet. Moreover, applying this homomorphism to a string is just replacing each symbol with a string in the same alphabet. 

---
**Closure Under Homomorphisms**

**Theorem 4.6**: If $L$ is a regular language over $\Sigma$, and $h$ is a homomorphism on $\Sigma$, then $h(L)$ is also regular.

**Proof**: Let $L = L(R)$ for some regular expression $R$. We replace each symbol $a$ in $E$ by $h(a)$, and claim that $h(R)$ defines the language $h(L)$. To show this, we prove by structural induction that $L(h(E)) = h(L(E))$. That is, applying $h$ to regular expression $E$ gives the same language as applying $h$ to the language directly.

**Basis**: The basis part contains two cases.

- If $E$ is $\epsilon$ or $\emptyset$, then $h(E) = E$ and $L(h(E)) = L(E)$. Meanwhile, we have $h(L(E)) = L(E)$. Therefore we conclude $h(L(E)) = L(h(E))$.
- If $E=\mathbf{a}$, then $L(E) = \lbrace a \rbrace$ and $h(L(E)) = \lbrace h(a) \rbrace$. Also, $h(E)$ is the regular expression that is the string of symbols $\lbrace a \rbrace$, and $L(h(E)) = \lbrace h(a) \rbrace$. Thus $h(L(E)) = L(h(E))$.

**Induction**: There are three cases.

- $E = F+G$. Applying $h$ to $E$ gives $h(E) = h(F+G) = h(F) + h(G)$, and thus $L(h(E)) = L(h(F)) \cup L(h(G))$. Meanwhile, by induction hypothesis we have $h(L(F)) = L(h(F))$ and $h(L(G)) = L(h(G))$. Thus we have $h(L(E)) = h(L(F) \cup L(G)) = h(L(F)) \cup h(L(G)) = L(h(F)) \cup L(h(G))$, which is exactly $L(h(E))$.
- $E = FG$. Applying $h$ to $E$ gives $h(E) = h(FG) = h(F)h(G)$, and thus $L(h(E)) = L(h(F))L(h(G))$. Meanwhile, by induction hypothesis we have $h(L(F)) = L(h(F))$ and $h(L(G)) = L(h(G))$. Thus we have $h(L(E))=h(L(F)L(G)) = h(L(F))h(L(G)) = L(h(F))L(h(G))$, which is exactly $L(h(E))$.
- $E=F^*$. Applying $h$ to $E$ gives $h(E) = h(F^*) = (h(F))^*$, and thus $L(h(E)) = L(h(F))^*$. Meanwhile, by definition, $L(h(F)) = h(L(F))$.Thus we have $h(L(E)) = h(L(F^*)) = (h(L(F)))^* = L(h(F))^*$, which is exactly $L(h(E))$.

---
**Inverse Homomorphisims**

**Definition 4.2**: Let $h$ be a homomorphism from $\Sigma$ to $T$, and $L$ be a language over alphabet $T$. Then, $h^{-1}(L)$, read $h$ reverse of $L$, is the set of strings $w \in \Sigma^*$ such that $h(w)\in L$.

**Example**: Consider all strings of 0's and 1's such that all the 0's occur in adjacent pairs. Let this language be $L$, and its regular expression is $(00+1)^*$.

Let $h$ be a homomorphism defined by $h(a) = 01$ and $h(b) = 10$. We claim that $h^{-1}(L)$ is the language of regular expression $(ba)^*$, and we shall prove that $h(w) \in L$ if and only if $w$ is of the form of $baba\cdots ba$.

(If) Suppose $w$ is the $n$ repetitions of $ba$ for some $n \geq 0$. Note that $h(ba) = 1001$. Therefore $h(w)$ is repetitions of $1001$, and by definition $w \in L$.

(Only-if) Suppose $h(w) \in L$. There are four conditions under which a string is not of the form $baba\cdots ba$, and we shall show if any of them holds, then $h(w) \notin L$.

- $w$ begins with $a$. In this case, $h(w)$ begins with $01$, which has an isolated 0.
- $w$ ends in $b$. In this case, $h(w)$ ends in $10$, which has an isolated 0.
- $w$ have two consecutive $a$'s. Then $h(w)$ has a substring $0101$, which has an isolated 0.
- $w$ have two consecutive $b$'s. Then $h(w)$ jas a substring $1010$, which has an isolated 0.

It is also clear that if none of the four conditions holds, then $w$ is of the form $baba\cdots ba$. Therefore, we conclude that if $h(w) \in L$, then $w$ is of that form.

---

**Theorem 4.7**: If $h$ is a homomorphism from $\Sigma$ to alphabet $T$, and $L$ is a regular language over $T$, then $h^{-1}(L)$ is also a regular language.

**Proof**: Let $A=(Q, T, \delta, q_0, F)$ be a DFA for $L$. We construct from $A$ and $h$ a DFA $B$ for $h^{-1}$. $B$ uses the states of $A$ but translates the input symbol according to $h$ before deciding the next state. Formally, we define

$$
B = (Q, \Sigma, \gamma, q_0, F)
$$

where the transition function $\gamma$ is constructed by the rule $\gamma(q, a) = \hat{\delta}(q, h(a))$. Note that $h(a)$ could be $\epsilon$, or one symbol, or strings of many symbols, but $\hat{\delta}$ is properly defined to take care of all these cases.

Then, by induction it is clear that $\hat{\gamma}(q_0, w)=\hat{\delta}(q_0, h(w))$. Since the accpeting states of $A$ and $B$ are the same, $B$ accepts $w$ if and only if $A$ accepts $h(w)$.

---
## 4.3 Decision Properties of Regular Languages

Given a language, we consider some of the fundamental questions about it:

- Is the language empty?
- Is a particular string $w$ in the lanaguge?
- Is the language the same as another langauge, or "equivalent" to another language?

We first make clear the meaning of "given a language". Typically, a language is infinite, so a lanaguge is not given by listing all its members. In fact, so far we have developed different finite representations for regular languages: DFA, NFA, $\epsilon$-NFA, regular expression. So when we are "given a lanaguge", we think the language is represented in one of these forms. Clearly, the languages given in these ways will all be regular, and there is no way at all to represent complete arbitratry languages.

In later chapters, we shall see finite ways to represent more then regular langauges, and we consider the same questions about these more general classes of langauges. However, to answer these questions, algorithms exist only for the class regular languages. The same questions become "undeciable" to some more general class of lanaguges. i.e., there is no algorithm to answer. 

---
**Converting Among Representations**

A language may be given in one of these types of representation: DFA, NFA, $\epsilon$-NFA, regular expression, and we may need to convert one types of representation to another. 

We have already given algorthims of conversion, and thereby prove the equivalence of all these representations, but now we put our attention on the complexity of the conversion process, espeically whether it takes exponential time (as a function of the size of their input) or polynomial time.

---
**$\epsilon$-NFA's $\to$ DFA's**

- First, we compute the $\epsilon$-closure of $n$ states, which takes $O(n^3)$ time. There are no more than $n^2$ transitions labeled $\epsilon$. And using a transitive closure algorithm such as Warshall's algorithm takes $O(n^3)$ time.
- The we conduct the subset construction. In principle, there are up to $2^n$ states. For each state, say $\lbrace q_1, q_2, \cdots, q_k \rbrace$, to compute $\delta(\lbrace q_1, q_2, \cdots q_k \rbrace, a)$, 