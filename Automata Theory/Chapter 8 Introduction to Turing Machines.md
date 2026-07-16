---
zhihu-title: Chapter 8 Introduction to Turing Machines
zhihu-topics:
  - 自动机
  - 图灵机
  - 理论计算机
zhihu-link: https://zhuanlan.zhihu.com/p/2060860263881086316
zhihu-created-at: 2026-07-15 23:07
zhihu-updated-at: 2026-07-15 23:09
---
- In this Chapter, we shall look at the question of what languages can be defined by any computational device whatsoever.
- This question is tantamount to the question of what computers can do, since recognizing the strings in a language is a formal way of expressing any problem.

---

- We will begin to show there are specific problems we cannot solve using a computer. These problems are called "undecidable".
- We then introduce a venerable formalism for computers, called Turing Machine.
- Turing Machine has been recognized as an accurate model for what any physical computing device is capable of doing.

## 8.1 Problems that Computers Cannot Solve

- In this section, we provide an informal proof that one specific problem cannot be solved by computers.
- This problem is to determine, whether the first thing a C program prints is `hello world`.
- The difficulty of this problem is that, the program may take an unimaginably long time before making any output.
- Not knowing when, if ever, something will occur, is the ultimate cause of our inability to tell what a program does.

---

### An Example

- We all know this program exactly prints `hello world`.

  ```c
  main()
  {
    printf("hello, world\n");
  }
  ```

- However, there are other programs that also print `hello world`.
- A program may first try to solve the integer equation below where $n>2$, and if it finds a solution, then it prints `hello world` and exits.
	$$
	  x^n + y^n=z^n
	$$
- The program will try to solve this equation just by simple searching.
- It takes human more than 300 years to prove that the equation has no integer solution, i.e., the simple program will not print `hello world`.
- We may let the program solve any problem that mathematicians have not yet been able to resolve, and print `hello world` if it succeeds.

---

### A Hypothetical Tester

- The proof of impossibility of making the "hello-world tester" is by contradiction.
- We assume there is a program $H$ to be the tester.
  - $H$ takes as input a program $P$ and input $I$;
  - and tells whether $P$ with input $I$ prints `hello world`.
  - That is, $H$ prints exactly `yes` or `no` for each circumstances.
- If $H$ really exists, then the problem of "hello-world testing" is said to be **decidable**. 
- Otherwise, the probelm is said to be **undecidable**.
- The formal definition of decidability will be introduced in Chapter 9.

---

### Proof by Contradiction

- We construct another program $H_1$ based on $H$.
  - $H_1$ also takes as input $P$ and $I$. 
  - $H_1$ first simulates $H$, and print its own results according to the result of $H$.
  - If $H$ prints `no`, then $H_1$ prints `hello world`.
  - If $H$ prints `yes`, then $H_1$ prints `yes`.

- Then we construct program $H_2$ based on $H_1$.
  - $H_2$ only takes as input a program $P$.
  - $H_2$ just simulates $H_1$ by using $P$ and $P$ as input for $H_1$, and prints whatever $H_1$ prints.
  - That is to ask, what $H_1$ will do on its own code.
- The contradiction will arise when we consider what $H_2$ will do on input $H_2$.
- On the one hand, $H_2$ cannot print `yes`.
  - If so, it means that $H_1$ says `yes` on $(H_2, H_2)$.
  - It further means $H$ will first print `hello world` on input $H_2$.
  - However, we assume it print `yes`.
- On the other hand, $H_2$ cannot print `hello world`.
  - If so, it means that $H_1$ says `hello world` on $(H_2, H_2)$.
  - It further means $H$ will not first print `hello world` on input $H_2$.
  - However, we assume it prints `hello world`.
- Therefore, we conclude that $H, H_1, H_2$ cannot exist.

> Note: The transformation above is essentially the insight that allowed Alan Turing to prove his undecidability result about Turing Machines.

### Why Undecidable Problems Must Exist

- Although it is tricky to prove the problem mentioned above is undecidable, it is quite easy to show that why almost all problems must be undecidable.
- Recall that  a "problem" is really membership of a string in a language.
- Let our alphabet be the ASCII alphabet.
- Then there will be only countable number of strings.
  - We can order them by length. For the same length, we order lexicographically.
  - That is, we can assign an integer for each string.
  - Any program can be considered as a string.
- However, there will be uncountable number of languages.
  - The set of languages is the power set of strings.
- As a result, we know there are infinitely fewer programs than there are problems.
- If we pick a language randomly, almost certainly is would be undecidable.
- However, we are only interested in those fairly simple, well-structured ones, which are often decidable.

---

### Reducing One Problem to Another

- If we want to know the decidability of another problem, we may also try to prove by contradiction that it is undecidable using similar techniques.
- However, once we have one problem that we know is undeciable, we can use this fact to show undecidability of other problems.
- Suppose $P_1$ is know to be undecidable, and $P_2$ is a new problem that we would like to prove is undecidable as well.
- We may invent a construction that converts instances of $P_1$ to instances of $P_2$ that have the same answer.
  - That is, any string in language $P_1$ is converted to some string in language $P_2$.
  - And ant string not in $P_1$ is converted to some string not in $P_2$.
- Once we have this construction, we can solve $P_1$ as long as we can solve $P_2$.
  - Given any instance of $P_1$, first convert it to an instance of $P_2$.
  - Test the instance of $P_2$ , and give the same answer for instance of $P_1$.
- We call this techique the **reduction** of $P_1$ to $P_2$.

---

### Example for Reduction

- Consider this problem. Does program $Q$ , given input $y$, ever call function `foo`?
- We construct a reduction of the hello-world problem $P_1$ to this problem $P_2$.
  -  If $Q$ has a function called `foo`, rename it and all its calls. Essentially the new program $Q_1$ does exactly what $Q$ does.
  - Add to $Q_1$ a function `foo` to get program $Q_2$. This function does nothing and is not called.
  - Modify $Q_2$ to remember the first 12 characters printed. And whenever it executes any output statement, it checks the characters already printed. If they are exactly `hello world`, call function `foo`.
- The resulting program $Q_3$ calls `foo` if and only if $Q$ first print `hello world`.

## 8.2 The Turing Machine

- The purpose of decidability theory is to provide a guidance to pragrammers about what they might or might not be able to accomplish through programming.
- Therefore, we need tools that allow us to prove everyday quesions decidable or not.
- However, the technology introduced in Section 8.1 does not translate easily to programs in unrelated domains.
  - For example, to determine whether a grammar is ambiguous.

- We will rebuild our theory of undecidability based on a very simple model of a computer, called Turing Machine.
- Using Turing Machine, we shall show in Section 9.4 that "Post's Correspondence Problem" is undecidable.
  - This problem makes it easy to show questions about grammars, such as ambiguity, to be undecidable.


---

### The Quest to Decide All Mathematical Questions

- The mathematician D. Hilbert asked at the turn of the 20th century, whether it was possible to find an algorithm for determining the truth of falsehood of any mathematical proposition.
- In particular, he asked if there was a way to determine any formula in the first-order predicate calculus, applied to intergers, was true.
  - The first-order predicate calculus of integers is sufficiently powerful to express statements like "this grammar is ambiguous" or "this program prints `hello world`".
  - The predicate calculus was not the only notion for "any possible computation".
  - Others include partial-recursive functions, and Turing Machines.
- All the serious proposals for a model of computation have the same power, i.e., they recognize the same languages.
- The **Church-Turing thesis** proposes the unprovable assumption that any general way to compute will allow us to compute only what what Turing machines or modern-day comptuers can compute.

---

### Notations for the Turing Machine

The Turing machine consists of

- A **finite control**, which can be in any of a finite set of states.
- A **tape**, divided into cells. Each cell can hold any one of a finite number of symbols.

The working procedure of a Turing machine is as follows.

- Initially, the **input**, which is a finite string of symbols from **input alphabet**, is placed on the tape. 

- All other tape cells, extending inifinitely to the left and right, hold a special symbol called **blank**. The blank is a **tape symbol** but not an input symbol.

- There is a **tape head** always positioned at one of the tape cells. The Turing machine is sad to be **scanning** that cell. Initially, the tape head is at the leftmost cell that holds the input.

- A move of the Turing machine is a funtion of the state and the tape symbol scanned. In one move, the machine will:

  - Change State. 

    > The next state optionally may be the same as the current state.

  - Write a tape symbol in the cell scanned. 

    > The written symbol replaces the existing symbol, and optionally may be the same.

  - Move the tape head left or right. 

    > We do not allow the head to remain stationary, and this does not constrain the ability of Turing Machine, because any sequence of moves with a stationary head could be condensed, along with the next tape-head move, into a single state change, a tape symbol, and a move left or right.

  ---


### Formal Definition of Turing Machines

  We describe a **Turing Machine** (TM) by the 7-tuple
$$
  M = (Q, \Sigma, \Gamma, \delta, q_0, B, F)
$$
  whose components have the following meanings:

  -  $Q$: The finite set of **states** of the finite control.
  - $\Sigma$: The finite set of **input symbols**.
  - $\Gamma$: The complete set of **tape symbols**; $\Sigma \subseteq \Gamma$.
  - $\delta: Q\times \Gamma \to Q \times \Gamma \times D$: The transition function, where $D$ is a direciton, either $L$ for left or $R$ for right.
  - $q_0$: The **start state**, a member of $Q$.
  - $B$: The **blank symbol**, $B \in \Gamma$ and $B \notin \Sigma$.
  - $F$: The set of **final** or **accepting states**, $F \subseteq Q$.

---

### Instantaneous Descriptions (IDs) for Turing Machines

- In order to describe formally what a TM does, we need notations for its configurations or **instantaneous descriptions**(IDs).
- Problem 1: A TM has an infinitely long tape, which is impossible to describe in principle.
  - After any finite number of moves, only a finite number of cells is visited.
  - There are infinite prefix and infinite suffix of cells that all hold blanks.
  - We only show the cells between the leftmost and rightmost nonblanks in an ID.
  - Special case: when the head is scanning a leading or trailing blank, some blanks should be included in the ID.
- Problem 2: To represent the finite control and tape-head position.
  - Embed the state in the tape, and place it immediately to the left of cell scanned.
  - States and tape symbols must have nothing in common, which can be ensured by simply renaming.

---

- We shall use the string $X_1X_2\cdots X_{i-1}qX_iX_{i+1}\cdots X_n$ to represent an ID in which

  - $q$ is the current state of TM.

  - The tape head os scanning the cell with $X_i$.

  - $X_1X_2\cdots X_n$ is the portion of tape between the leftmost and rightmost blank. For exception,  some prefix or suffix can be blank if $X_1$ or $X_n$ is being scanned. 

- We describe moves of a TM $M$ by the notation $\underset{M}{\vdash}$ , or just $\vdash$ when $M$ is understood.

- We use $\overset{\ast}{\vdash}$ to indicate zero, one or more moves of the TM $M$.

- Suppose $\delta(q, X_i)=(p, Y,L)$, then

  $$
  X_1X_2 \cdots X_{i-1}qX_i X_{i+1}\cdots X_n \vdash X_1X_2\cdots X_{i-2}pX_{i-1}YX_{i+1}\cdots X_n
  $$

  - If $i=1$, we have

    $$
    qX_1X_2\cdot X_n \vdash pBYX_2\cdots X_n
    $$

  - If $i = n$ and $Y = B$, we have

    $$
    X_1X_2\cdots X_{n-1}qX_n \vdash X_1X_2 \cdots X_{n-2}pX_{n-1}
    $$

- Suppose $\delta(q, X_i) = (p, Y, R)$, then

  $$
  X_1X_2\cdots X_{i-1}qX_iX_{i+1}\cdots X_n\vdash X_1X_2\cdots X_{i-1}YpX_{i+1}\cdots X_n
  $$

  - If $i = 1$ and $Y = B$, we have
    
    $$
    qX_1X_2\cdots X_N \vdash pX_2\cdots X_n
    $$

  - If $i = n$, we have
    
    $$
    X_1X_2\cdots X_{n-1}qX_n \vdash X_1X_2 \cdots X_{n-1}YpB
    $$
    

  ---

### Example 1 for Turing Machine

- Let us design a TM that accepts the language $\lbrace 0^n1^n \,\vert\,  n\geq 1 \rbrace$.
- We informally describe the behavior of the TM instead of giving every definition of states and transition function.
- Given a finite sequence of 0's and 1's on the tape, the TM will alternatively change a 0 to an $X$ and a 1 to a $Y$, until all 0's and 1's are matched.
  - It repeatedly changes a 0 to an $X$.
  - Then it moves to the right over whatever 0's and $Y$'s it sees, until it comes to a 1.
  - It changes the 1 to a $Y$.
  - Then it moves left over $Y$'s and 0's, until it finds an $X$.
  - Then it looks for a 0 immediately to the right, and if it finds one, repeats the process above.
- If the input is not  $0^\ast1^\ast$, then the TM will fail to have a next move and die.
- Only when the number of 0's and 1's are equal will the TM accept. 

---

### Example 2 for Turing Machine

- Turing's original view of his machine was a computer of integer-valued functions.
- Integers were represented by unary, and the machine computed by changing the length of the blocks of characters or constructing new characters.
- We show how a TM might compute **proper substraction** or **monus**
  - It is defined as $m\overset{\cdot}{-}n = \max(m-n, 0)$.
- This TM $M$ omits the accepting states, because it is not used to accept inputs.
- $M$ starts with a tape consisting of $0^m10^n$ surrounded by blanks.
- $M$ Halts with $0^{m\overset{\cdot}{-}n}$ on its tape, surrounded by blanks.

---

- $M$ repeatedly finds its leftmost remaining 0 and replaces it by a blank.
- It then searches right, looking for a 1.
- After finding a 1, it continues right, until it comes to a 0, and replace it by a 1.
- Then it returns left, looking for the leftmost 0, and repeat the process.

This repetition ends if either:

- All the $n$ 0's in $0^m10^n$ have been changed to 1's.
- The first m 0's have been changed to blanks.

---

### Definition of the Language of a Turing Machine

- Let $M = (Q, \Sigma, \Gamma, \delta, q_0, B, F)$ be a Turing Machine.

- $L(M)$ is the set of strings $w\in \Sigma^\ast$ such that $q_0w\overset{\ast}{\vdash}\alpha p \beta$ for some state $p\in F$ and any tape strings $\alpha, \beta$.

- The set of languages we can accept using a Turing machine is called the **recursively enumerable languages** or RE languages.

  > The term "recursive" as a synonym for "decidable" goes back to Mathematics as it existed prior to computers. Then, formalism based on recursion were commonly used as a notion of computation. In that sense, to say a problem was "recursive" had the positive sense of "it is sufficiently simple that it can be solved by a recursive function, and the function always finishes".
  >
  > The term "recursive enumerable" means that, there exists a function that could list all the members of a language in some order; that is, to enumerate them.  The languages that can have their members listed in some order are the same as the languages that are accepted by some TM, although that TM might run forever on inputs that it does not accept.

---

### Halting of Turing Machines

- A TM is said to **halt** if it enters a state $q$, scanning a tape symbol $X$, and there is no move available, i.e., $\delta(q, X)$ is undefined.

- If a TM accepts an input, we can always assume it halts.

  - We can make $\delta(q, X)$ undefined for all $q \in F$.
  - This will not change the language accepted.

- However, if a TM does not accept an input, it does not always halt.

  - Those languages with TMs that do halt, regardless of whether or not they accept, are called **recursive languages**.
  - Recursive languages are good models of "algorithms", because an algorithm should always ends or halts on any input.

## 8.3 Programming Techniques for Turing Machines

- Our goal in subsequent sections is to convince you that a TM is exactly as powerful as a convenrional computer.
- We present several techniques that allow TMs to have additional features, but do not extend the power of the TM model.
- We shall also learn the "introspective" ability of TM machines, i.e., performing calculations that simulate another TM machine.

---

### Storage in the State

- We can use the finite control not only to represent a position in the "program" of the TM, but to hold a finite amount of data.
- The technique requires no extention to the TM model.
- We think each state as a tuple that have two components.
  - A control portion. This portion remembers the current state, i.e., what the original TM is doing.
  - A data portion. This portion remembers all data we want to store in the TM. It can have multiple elements of data.
- Therefore, the set of states $Q$ will become $Q \times \mathcal{D}_1 \times \mathcal{D}_2 \times \cdots \times \mathcal{D_n}$, where $\mathcal{D}_i$ is the set of all possible values of the $i$-th data elements we want to store.  
- To "read" the data, the transition function may behave differently on tuples that have the same control portion but different data portion.
- To "write" the data, the transition function can choose tuples with any possible value of the data portion.

---

### Multiple Stacks

- The tape of a TM can be thought as composed of serverl tracks.
- We think the tape symbol to be tuples.
- The tape alphabet becomes $\Gamma^n$ for an $n$-track TM.
  - Each track holds one tape symbol
- In each move, $n$ symbols are read and written simultaneously.
- Note: There is only one tape head that moves either left or right in a single move.

---

### Subroutines

- We can build a TM from a collection of interacting subroutines (other TMs).
- Say TM $A$ wants to call TM $B$ as a subroutine.
- We add directly the states and transition functions of $B$ to $A$.
- For the state where $A$ wants to call $B$, we let this state move to the initial state of $B$.
  - The "call" of subroutine $B$ occurs whenever there is a transition to its initial state.
- To allow $B$ to return control to $A$, we add another "return" state.
  - $B$ always moves to the return state when it halts.
  - The TM will move from the return state to another state that is under the caller's control.
- When $B$ is called multiple times,  the design above cannot remember the "return address".
- Therefore, we make copies of the subroutine for each call.

---

### Example for Subroutines

- We shall design a TM to implement the function of multiplication.
- The TM starts with $0^m10^n1$ on its tape, and ends with $0^{mn}$ on its tape.

The outline of the strategy is as follows.

- In general, the tape will have one nonblank string of the form $0^i10^n10^{kn}$ for some $k$.
- In one basic step, we change a 0 in the first group to $B$ and add $n$ 0's to the last group, giving a string of the form $0^{i-1}10^n10^{(k+1)n}$.
- When the all 0's in the first group are changed to blanks, we will then have copied the group of $n$ 0's to then end $m$ times.
- The final step is to change the 1's in $10^n1$ to blanks.

The heart of this algorithm is a subroutine which we shall call `Copy`.

- `Copy` converts an ID $0^{m-k}1q_10^n10^{(k-1)n}$ to ID $0^{m-k}1q_20^n10^{kn}$.
  - $q_1$ is initial state of the TM of `Copy`.
  - $q_2$ is the state where the TM eventually halts.
- We will only need single copy of TM of the subroutine `Copy` to implement multiplication.
  - Because every time `Copy` returns, the caller's control is the same, or the return address is the same.

## 8.4 Extentions to the Basic Turing Machine

- We shall introduce serveral extensions to the basic Turing Machine model.
- However, they add no language-defining power to the basic model.

---

### Multitape Turing Machines

- A multitape TM has a finite number of tapes, as well as tape heads.

- Initially, the input is placed on the first tape. All other tapes hold blanks.

- The tape head of the first tape is at the left end of the input. It does not matter where other heads are placed initially.

- In a move:

  - The control enters a new state according to the current state and all symbols scanned by each of the tape heads.

  - On each tape, write a new tape symbol.

  - Each of the tape heads makes a move independently.

    > The difference between a Multitape TM and a Multistack TM lies here. A multitape TM extends the TM model but a multistack does not.

- We allow stationary move in multitape TM. The equivalence has been discussed.

---

### Equivalence of TM and Multitape TM

- Surely, multitape TM's accept all the recursively enumerable languages.
  - An one-tape TM is a multitape TM by definition.
- The theorem we will prove shows that multitape TM cannot accept languages other than RE languages.

**Theorem 8.9**: Every language accepted by a multitape TM is recursively enumerable.

**Proof**:

- Suppose $M$ is a $k$-tape TM with $L=L(M)$.
- We simulate $M$ with a multistack and one-tape TM $N$ whose tape has $2k$ tracks.
  - Half the tracks holds the tapes of $M$.
  - The other half of the tracks each hold only a single marker to indicate where the head of the corresponding tape is currently located.
- The problem is that, while $k$ heads in $M$ moves independently, there is only one head in $N$. Although we "mark" the positions of heads using $k$ extra tracks, we always need to start from the single head and find all these marks.
  - $N$ must remember how many markers are to the left or right of its own head.
  - $N$ can store this count in finite control, as discussed in Section 8.3.
  - With this information, in each move, $N$ can visit all markers on its tape, simulate all tapes of $M$, and update the stored count.
  - Finally, $N$ should move on the tape so as to satisfy its own tape head move. 
- The accepting states of $N$ are those that record $M$'s state as one of the accepting states of $M$.

---

### A Reminder About Finiteness

- In constructing a multistack TM to simulate a multitape TM, one may propose the following.
  - Store all $k$ positions of markers as integers in the finite control.
  - Carelessly, he may argue that after any $n$ moves, the head only has to store finite possibilities of integer values.
- The problem is that, while the positions are finite at any time, the complete set of positions at any time is infinite.
- A value that is finite at any time is different from a value that is finite.

---

### Running Time and Time complexity

- We not introduce two important concepts, the **running time** and the "**time complexity**" of a Turing Machine.
- The **running time** of TM $M$ on input $w$ is the number of steps that $M$ makes before halting.
- The **time complexity** of TM $M$ is the function $T(n)$ that is the maximum, over all inputs $w$ of length $n$, of the running time of $M$ on $w$.
  - For TMs that do not halt on all inputs, $T(n)$ may be infinite.

---

### Time Complexity of the Many-Tapes-to-One Construction

- The construction above seems clumsy, as the one-tape TM takes much more running time than the multitape TM.

- However, we will show the amounts of times taken by two TMs are commensurate, as it only increases polynomially.

  >We shall see in Chapter 10 that the difference between polynomial time and higher growth rates in running time is really the divide between what we can solve by computer and what is in practice not solvable.

**Theorem 8.10**: The time take by the one-tape TM $N$ of Theorem 8.9 to simulate $n$ Moves of the $k$-tape TM is $O(n^2)$.

**Proof**:

- After $n$ moves of $M$, the tape head markers can have separated by only no more than $2n$ cells.
- Assume $N$ first locates the leftmost marker. 
- It has to move no more than $2n$ cells right, to find all the markers.
- For $k$ markers, it needs no more than $2k$ steps to wipe out old markers and write new markers.
- $N$ may make excursions leftward in this process, but no more than $2n$ steps.
- Therefore, it takes no more than $4n+2k$ steps to simulate a single step of $M$.
- It takes $O(n^2)$ steps to simulate $n$ steps of $M$.

---

### Nondeterministic Turing Machines

- A **nondeterministic Turing Machine** (NTM) differs from the TM only at the transition function.
- Similar to nondeterministic versions of other automata, the transition function becomes $\delta: Q\times \Gamma \to 2^{Q\times \Gamma \times D}$.
- The NTM can choose at each step any subset of triples to be the next move.
- The strings accepted by an NTM is those that there exists some sequence of choices of moves that eventually accepts the string.

---

**Equivalence of NTM and DTM**

- The NTM's accept no languages not accepted by a TM (or DTM if we need to emphasize it is deterministic).

**Theorem 8.11**: If $M_N$ is a NTM, then there is a DTM $M_D$ such that $L(M_N)=L(M_D)$.

**Proof**:

- The overall strategy is to let $M_D$ to explores all IDs that $M_N$ can reach.
- $M_D$ will have two tapes.
  - One tape holds a queue of ID's of $M_N$, among which there is an ID marked as the "current" ID pointed by the tape head. The successors of current ID are to be discovered.
  - Another tape will be used as a buffer.
- To process the current ID:
  - $M_D$ examines state and tape symbol. The knowledge of what moves $M_N$ will make is built into $M_D$. If there is any accepting state, $M_D$ discovers and accepts.
  - If there $M_D$ discovers no accepting state, assuming there are $k$ possible moves, $M_D$ will copy the current ID on the second tape; make $k$ copies of the that ID at end of the first tape; and modify each of the $k$ copies according different one of $k$ choices of move.
  - $M_D$ will mark the next ID as current ID and repeat this process.
- The time complexity of $M_D$ will grow exponentially.
  - Suppose $m$ is the maximum number of choices of $M_N$.
  - After $n$ moves of $M_N$, it can reach up to $1+m+m^2+\cdots+m^n \leq nm^n$ IDs.
  - All IDs that can reached by $M_N$ will be discovered by $M_D$, so $L(M_N)=L(M_D)$.


## 8.5 Restricted Turing Machines

- In this section, we shall consider some examples of restrictions on the TM that also have the same language-recognizing power.

---

### Turing Machines With Semi-infinite Tapes

**Theorem 8.12**: Every language accepted by a TM $M_2$ is also accepted by a TM $M_1$ with the following restrictions.

- $M_1$'s head never moves left of its initial position.

  > The tape of $M_1$ is thus called semi-infinite, as it has a left end.

- $M_1$ never writes blanks.

**Proof**:

- For the second restriction, we just create a new tape symbol $B'$ that functions as a blank, and replace all $B$ by $B'$.
- For the first restriction, we will create two tracks on $M_1$'s semi-infinite tape.
  - The first track represents the cells of the original TM that are at or to the right of the initial head position, i.e., $X_0, X_1, \cdots$.
  - The second track represents the positions left of the initial position, but in reverse order, i.e., $X_{-1}, X_{-2}, \cdots$.
  - The first symbol in the second track is a placeholder $\ast$.

- Design the state set as $Q_1 = \lbrace q_0, q_1 \rbrace\, \cup\,\big(Q_2\times \lbrace U, L \rbrace\big)$.
  - $q_0$ is the initial state of $M_1$. $q_1$ is just another state. $Q_2$ is the state set of $M_2$.
  - $U$ means the head of $M_2$ is at or to the right of its initial position. 
  - $L$ means the head of $M_2$ is to the left of its initial position. 
- Design the tape alphabet as $\Gamma_1 = \Gamma_2 \times \Gamma_2$.
  - It's natural since we have a 2-track tape.
  - Additionally, for all $X \in \Gamma_2$, there is a pair $(X, \ast)$ in $\Gamma_1$ where $\ast$ is a new tape symbol. This is for the case where the tape write the initial position.
- The input symbols in $\Sigma_1$ are those pairs with an input symbol of $M_2$ in the first component and a blank in the second component.
- The blank symbol in $B_2 = (B_1, B_1)$.
- The transition function is as follows.
  - The first move is always $\delta_1(q_0, (a, B)) = (q_1, (a, \ast), R)$ for any $a\in \Sigma$.
    - It puts a $\ast$ in the second track's leftmost cells and moves right.
  - The second move is always $\delta_1(q_1, (X, B)) = ((q_2, U), (X, B), L)$ for any $X \in \Gamma_2$.
    - $q_2$ is the initial state of $M_2$
    - And it moves back to the initial position.
  - Then, for each move of $M_2$, $M_1$ have enough information to simulate it.
- The accepting states is $F_1 = F_2 \times \lbrace U, L \rbrace$.

---

### Multistack Machines

- We now consider models that are generalizations of PDAs.
- First, we consider what happens when a PDA has multiple stacks.
- A deterministic $k$-stack PDA is most the same as a DPDA. But a move of a $k$-stack PDA is based on
  - The state of the finite control.
  - The input symbol read, which cannot be $\epsilon$.
  - The top stack symbol on each of its stacks.
- In a move, the multistack machine can
  - Change to a new state.
  - Replace the top symbol of each stack with a string of zero of more stack symbols. Replacement for each stack can be different.
- We add one assumption for simplicity: there is a special endmarker $\$$ that appears only at the end of the input and is not part of input.

---

**Theorem 8.13**: If a language $L$ is accepted by a TM $M$, then $L$ is accepted by a two stack machine $S$.

**Proof**:

- The essential idea is that two stacks can simulate one TM tape, using the same strategy in proof of Theorem 8.12.
- $S$ begins with the endmarker on each stack.
- The input $w$ is first put in the first stack, i.e. $w \unicode{x24}$. $S$ pops each symbol and pushes into the second stack to get $\unicode{x24}w^R$, with the left end of $w$ at the top.
  - The first stack is for the positions left to the tape head.
  - The second stack is for the positions at or right to the tape head.
- Then for any move of $M$, $S$ has enough information to simulate.
- $S$ accepts if the new state of $M$ is accepting.

---

### Counter Machines

A counter machine may be thought of in two ways:

- The counter machine has the same structure as the multistack machine, but in place of each stack is a counter. 
- Counters hold nonnegative integers, but the finite control can only distinguish between zero and nonzero values. 
- That is, the move of the counter machine depends on its state, input symbol, and whether the counters are zero. 
- In one move, the counter machine changes state, and adds or subtracts 1 from any its counters independently. But the counters are not allowed to be negative.

The other way is to regard it as a restricted multistack machine. The restrictions are as follows:

- There are only two stack symbols $Z_0$ (the endmarker) and $X$.
- $Z_0$ is initially on each stack.
- We may replace $Z_0$ only by strings of the form $X_iZ_0$ for some $i \geq 0$.
- We may replace $X$ only by $X_i$ for some $i \geq 0$.

---

### The Power of Counter Machines

- Every language accepted by a counter machine is a RE language.
  - Counter machines are multistack machines, and multistack machines accept RE languages according to Theorem 8.9.
- Every language accepted  by a one-counter machine is a CFL.
  - One-counter machine is a special case of a PDA.
- We will show that two counters are enough to simulate a Turing machine and accept RE all languages.

---

**Theorem 8.14**: Every RE language is accepted by a three-counter machine.

**Proof**:

- We use a three-counter machine to simulate a two-stack machine, which can then simulate a TM according to Theorem 8.13.

Suppose there are $r-1$ tape symbols used by the multistack machine.

- We identify the symbols with digits 1 through $r-1$.

- The string $X_1X_2\cdots X_n$ can be represented by an integer in base $r$, i.e.

  $$X_nr^{n-1} + X_{n-1}r^{n-2} + \cdots + X_2r+X_1$$

- Two counters are used to hold integers that represent two stacks.

- The third counter is used to adjust the other two counters.

---

The operations on any stack consists of three kinds:

- Pop $X_1$ out of the stack. 
  - To do so, we replace the integer $i$ by $i/r$, throwing away any reminder, which is $X_1$.
- Push $X$ onto a stack that initially holds $i$. 
  - To do so, we replace $i$ by $ir+X$.
- Replace $X$ on the top of a stack by $Y$.
  - That is just first pop $Y$ and then push $X$.

---

The third stack is used to perform the operations of multiplication and division.

- Multiplication $i \to i\times r$
  - Repeatedly decrement the counter $i$ by 1 and increment the third counter by $r$.
  - Then copy the third counter into the original counter by incrementing and decrementing of step 1.
- Division $i \to i /r$
  - Repeatedly reduce the counter $i$ by $r$ and increase the third counter by $1$.
  - When the counter reaches 0, stop.
  - Then, copy the result into the original counter.

---

We also have to simulate the stacks in their initial condition.

- The two stacks can hold only the start symbol initially.
- Just increment the two counters to the corresponding integer.

---

**Theorem 8.15**: Every RE language is accepted by a two-counter machine.

**Proof**:

- With Theorem 8.14, we only need to simulate a 3-counter machine with a 2-counter machine.

- The idea is to represent three counters by a single integer.

- For example, $i, j, k$ can be represented as $m=2^i3^j5^k$.

  > Any choice of three distinct prime numbers is valid to map three integers to one integer.

- The first counter will hold this number and the second counter will perform multiplication and division.

The operations on any counter consists of three kinds:

- Increment by 1.
  - Multiply the first counter by either 2, 3 or 5, using the second counter.
- Tell which, if any, of $i, j, k$ are 0.
  - That is equivalent to telling if the first counter is divisible by 2, 3 or 5.
  - Perform the division process respectively and check if the reminder is 0.
- Decrement by 1.
  - Divide the first counter by either 2, 3, or 5, using the second counter.
  - If some counter of the 3-counter machine decrements below 0, it is an error, and the 2-counter machine should halts without accepting.


## 8.6 Turing Machines and Computers

- We will show that Turing machines and the common sort of computer that we use daily, accepts the same languages -- the RE languages.
- The notion of "a common computer" is not well defined mathematically, and the arguments in the section are necessarily informal.

---

### Simulating a Turing Machine by Computer

- Given a Turing machine $M$, we can write a program that acts like $M$.
  - States will be encoded as strings.
  - The program uses a transition table to simulate transitions.
  - Tape symbols are also encoded as strings.
- The problem is, the length of the tape is infinite, but the memory of computers is finite.
  - If storage devices are finite, then computers will only be a finite automaton.
  - However, common computers have swappable storage devices.
  - For example, hard disks can be removed and replaced by empty but otherwise identical disks.
  - We can have a human operator to swap disks whenever the TM machine wants to move to positions on tapes that are mapped to other disks.
  - All involved disks should be arranged in order, to simulate the linear tape.
  - We may assume there are infinite disks that the human operator can buy.

---

### Simulating a Computer by a Turing Machine

- We will answer two questions:
  - Are there any things a common computer can do while a Turing machine cannot?
  - Are there any things a common computer can do much faster than a Turing machine.
- The answer will be: a TM can simulate a TM sufficiently fast that "only" a polynomial separates the running time of the computer and TM on given problems.

---

We first give a realistic but informal model of how a typical computer operates.

- The storage of a computer consists of indefinitely long sequence of **words**, each with an **address**.
  - Address will be consecutive numbers 0, 1, 2 and so on.
  - Words in real computers will be of 32 or 64 bits long, but we do not limit the length of a word.
- The program is stored in some of the words of memory.
  - Words each represent a simple instruction.
  - Indirect addressing is permitted, so one instruction could use the content of another word as the the address of the word to which the operation is applied.
- Each instruction involves a limited number of words, and changes the value of at most one word.
- The operations that instruction may perform are not restricted.
  - The relative speed of different operations on different words will not be taken into account.
  - It does not matter if we are only interested the language-recognizing abilities.
  - Even if we are interested in the running time, the relative speeds differences are "only" a constant factor and not important.

---

The TM simulating a computer will have several tapes.

- The first tape represents the entire memory of the computer.
  - The words are encoded with their addresses in the following way
    $$\$ 0 \ast w_0 \#1\ast w_1 \#10\ast w_2 \# 11 \ast w_3 \cdots$$
  - The $\ast$'s are end-markers of words.
  - The \$'s are end-marker of the memory.
  - The \#'s are used to separate the words and their addresses.
  - Addresses are encoded in binary.
  - Registers, caches, main memory and disks of a real computer can be mapped properly onto this tape.

- The second tape is the instruction counter.

  - It holds one integer in binary, which represents one of memory locations on tape 1.
  - The value store in this location will be interpreted as the next instruction.

- The third tape holds a memory address or the contents of that address after the address has been located on tape 1.

  - This is for the addressing process.
  - The desired address is copied onto tape 3.
  - Then, the TM compares it with addresses on tape 1 until a match is found.
  - The contents of this address is then copied onto the third tape.

- The TM may use other tapes for computer's input file or for scratch.

---

The TM will simulate the instruction cycle of the computer as follows:

- Search the first tape for the instruction that matches the instruction number on tape 2.
- Examine the found instruction value. 
  - We assume the first few bits represent the action to be taken (e.g. copy, add, branch) and the remaining bits represents addresses that are involved in the action.
- Perform addressing. 
  - Copy addresses onto tape 3, search tape 1, and copy values onto tape 3.
- Execute the instruction. We cannot go into all possible instructions, but we will do with the basic and important ones:
  - Copy the value to some other address.
    - Go back to tape 1 to locate that address.
    - Copy the value into that address.
    - If more space is needed, or the new value uses less space, we will change the available space by shifting over.
      - Copy the entire nonblank content of tape 1 onto scratch tape.
      - Write the new value onto tape 1
      - Recopy the scratch tape back to tape 1, immediately right to the new value.
  - Add the value to the value of some other address.
    - Go back to tape 1 to locate that address.
    - Perform a binary addition of the value of that address and the value on tape 3.
    - If the result needs more space, shift contents on tape 1 to create enough space.
  - Take a value as an address and jump to it.
    - Simply copy tape 3 to tape 2.
- If the performed instruction is not a jump, add 1 to tape 2 and begin the instruction cycle.

---

### The Running Times of Computers and Turing Machines

**Theorem 8.17**: The Turing machine describe above can simulate $n$ steps, in $O(n^3)$ of its own steps, of a computer that

- has only instructions that increase the word length by at most 1
- has only instructions that a multitape TM can perform on words of length $k$ in $O(k^2)$ steps or less.

---

- The problem we want to handle by restriction 1 is the multiplication instruction.
  - Multiply a word by itself will double its length.
  - This will cause the length of words to grow exponentially.
  - In reality, this case will cause overflow and values are truncated.
  - We only allow words to increase its length by 1 at a time.
- The second restriction deals with all possible computer instructions that we do not cover.

---

**Proof**:

- The first tape starts with only the program of constant length $c$, occupying $d$ words.
- The occupied storage on tape 1 after $n$ steps is $O(n^2)$.
  - After $n$ steps, the computer cannot have created any words longer than $c+n$.
  - And the number of words are no more then $d+n$.
- Therefore, the execution of each instruction takes $O(n^2)$ steps.
  - Addressing, shifting over all needs only $O(n^2)$ time.
- Therefore, simulating $n$ steps needs $O(n^3)$ steps.

---

- Finally, we note that the simulation above use a multitape TM.
- We know that a basic TM can simulate $n$ steps of a multitape TM in $O(n^2)$ steps.
- Therefore, we can simulate a computer with a basic TM also in polynomial time.

**Theorem 8.18**: A computer of the type describe in Theorem 8.17 can be simulated for $n$ steps by one-tape Turing machine using at most $O(n^6)$ steps of the Turing machine.


Reference:  Introduction to Automata Theory, Languages, and Computation. John E. Hopcroft, Rajeev Motwani, Jeffrey D. Ullman.