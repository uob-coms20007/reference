---
layout: math
title: Operational Semantics (I)
nav_order: 3
mathjax: true
parent: Semantics
---

# Operational Semantics (I)

Operational semantics is an alternative approach to denotational semantics that emphasis the steps taken during the execution of the program.
We will construct an operational semantics for While programs, building on the denotational semantics for arithmetic and Boolean expressions.

Recall the grammar of programs (i.e. statements) is given as follows:

<div class="defn" markdown="1">
A __statement__ is an element of the following grammar:

$$
  S \to \mathsf{skip} \mid x \leftarrow A \mid S; S \mid \mathsf{if}\ B\ \mathsf{then}\ S\ \mathsf{else}\ S \mid \mathsf{while}\ B\ \mathsf{do}\ S
$$

where $A$ stands for any arithmetic expression and $B$ stands for any Boolean expression.
</div>

As with denotational semantics, we will work with the abstract syntax tree of expressions rather than the string that produced them, using $S$ to refer to the set of statements.
Therefore, we needn't consider parentheses or curly braces as part of this grammar.

The type of operational semantics that we will employ is sometimes called _big-step_ operational semantics.
This framework involves defining a relation $S,\, \sigma \Downarrow \sigma'$ which says that the program $S$ in the initial state $\sigma$ executes leaving a final state $\sigma'$.
We are using an _infix_ notation for this relation; equally, we could have written ${\Downarrow} \subseteq S \times \mathsf{State} \times \mathsf{State}$.
The name big-step semantics refers to the fact that this relation doesn't expose any intermediary states of the computation - only the initial state $\sigma$ and the final state $\sigma'$.

If you squint, it may seem that this is awfully similar to the denotational setup except we're using a relation instead of a function and the result is a state rather than a value.
The difference lies in the way these relations/functions are defined.
Recall that the denotational semantics was given by _recursion_ over the expression.
For the operational semantics, we will define the relation _inductively_.

## Inference Systems & Derivations

Already in this course, we have met some inductive defined sets.
In particular, the natural numbers are inductively defined as are the set of arithmetic/Boolean expressions.

Recall the grammar defining the natural numbers:

$$
  N \to 0 \mid N + 1
$$

Another way of describing this set is as the _least_ set $N$ such that: (1) $0 \in N$; and, (2) if $n \in N$ is a natural number, then $n + 1 \in N$ is also a natural number.
Both of these clauses can be thought of as _rules_ for generating the inhabitants of our set.
In particular, the first rule says that $0$ is included, and the second rule allows us to generate new instances from existing ones.

Other inductively defined sets work similarly, consisting of a set of rules for building up the set of instances by combining and modifying existing instances.
Sometimes, to be more precise, we will refer to the rules from which an inductively defined set is generated as _inference rules_.

> #### An Aside
> You might ask why it has to be the _least_ set in particular.
> Actually, if this were not specified, then you could include quite unnatural numbers such as $-1$.
> Although these values aren't specifically generated by the grammar, they don't violate the previous description as long as we also include $-1 + 1$ and $-1 + 1 + 1$ etc. to satisfy the last clause.

<div class="defn" markdown="1">
  An __inference rules__ is a logical statement of the form:

  $$
    \dfrac
    {P_1 \quad \cdots \quad P_n}
    {Q}
  $$

  That is interpreted as the statement: if $P_1$ and $P_2$ and ... and $P_n$ are all true, then $Q$ is also true.
  We refer to each $P_1$, ..., $P_n$ as _premises_ and to $Q$ and the _conclusion_.
</div>

Using this notation for inference rules, we express the definition of the natural numbers as follows:

$$
  \begin{array}{cc}
    \dfrac
    {}
    {0 \in N}

    &
    \dfrac
    {n \in N}
    {n + 1 \in N}
  \end{array}
$$

When undefined symbols (e.g. "$n$") appear in the premises and conclusion of an inference rule, they are _universally quantified_.
That means the rule applies for all concrete instances.
For example, the second inference rule allows us to conclude that $0 + 1 \in N$ given that $0 \in N$ where we have instantiated the symbol $n$ for the concrete value $0$.
These symbols are referred to as _metavariables_.
To write down these concrete instance of inference rules - referred to as _inferences_ -- we use the same notation, e.g.:

$$
  \begin{array}{cc}
    \dfrac
    {0 \in N}
    {0 + 1 \in N}

    &
    \dfrac
    {0 + 1 \in N}
    {0 + 1 + 1 \in N}
  \end{array}
$$

One of the nice features of this notation is that multiple inferences can be chained together to present a consistent argument.
For example, we can combine the above two inferences and the inference rule for $0 \in N$ to crete the following argument:

$$
  \dfrac
  {
    \dfrac
    {
      \dfrac
      {}
      {0 \in N}
    }
    {0 + 1 \in N}
  }
  {0 + 1 + 1 \in N}
$$

These compound inferences are referred to as _derivations_.

<div class="defn" markdown="1">
  An __derivation__ is a tree where each node 

  $$
    \dfrac
    {P_1 \quad \cdots \quad P_n}
    {Q}
  $$

  That is interpreted as the statement: if $P_1$ and $P_2$ and ... and $P_n$ are all true, then $Q$ is also true.
  We refer to each $P_1$, ..., $P_n$ as _premises_ and to $Q$ and the _conclusion_.
</div>

## Skip, Assignment, and Composition

In the same way that two aforementioned rules are used to define the natural numbers inductively, we will use similar rules to define the operational semantics of While programs.
That is, we will define the relation ${\Downarrow} \subseteq S \times \mathsf{State} \times \mathsf{State}$ as the least set to satisfy certain rules that describe the behaviour of each language construct.
Let's start with the rules for skip, assignment, and composition:

  - The skip statement $\mathsf{skip}$ does not affect the program state.
    Therefore, if the program $\mathsf{skip}$ starts in the state $\sigma$, it will end in the state $\sigma$.
    We characterise this behaviour as the inference rule:

    $$
      \dfrac
      {}
      {\mathsf{skip},\, \sigma \Downarrow \sigma}
    $$

    Here we are using our convenient infix notation for the relation $\Downarrow$, but this rule should be read as the logical statement: for all states $\sigma \in \mathsf{State}$, $(\mathsf{skip},\, \sigma,\, \sigma) \in {\Downarrow}$.

    Notice the metavariable $\sigma$ that refers to a placeholder for an arbitrary state, rather than any particular state.
    This is a parameter of the rule that can instantiated with a particular state of interest to get concrete instances.
    For example, the rule allows us to conclude that $\mathsf{skip},\, [x \mapsto 2] \Downarrow [x \mapsto 2]$ where the metavariable $\sigma$ is instantiated with the concrete state $[x \mapsto 2]$.

  - The assignment statement $x \leftarrow e$ executes in a given state $\sigma$ by evaluating the arithmetic expression $e$ in this state, and updating the state so that $x$ receives this value.

    As with the $\mathsf{skip}$ statement, the behaviour of this statement can be described without making reference to other statements as it is not a compound statement (i.e. it is a base case).
    Therefore, we give the inference rule for its execution directly.

    $$
      \dfrac
      {}
      {x \leftarrow e,\, \sigma \Downarrow \sigma[x \mapsto \llbracket e \rrbracket_A(\sigma)]}
    $$

    where the notation $\sigma[x \mapsto n]$ refers to the state that results from updating the value assigned to $x$ to be $n$.

    Here, the variable $x$, the arithmetic expression $e$, and the state $\sigma$ are metavariables that are universally quantified by this rule.
    Logically, it corresponds to the statement: for all variables $x \in \mathsf{Var}$, arithmetic expressions $e \in A$, and states $\sigma \in \mathsf{State}$, $x \leftarrow e,\, \sigma \Downarrow \sigma[x \mapsto \llbracket e \rrbracket_A(\sigma)]$.
     <!-- (where the infix notation can be replaced by an explicit inclusion). -->

    As with the rule for skip statements, we can instantiate these metavariables with a concrete variable, a concrete arithmetic expression, and a concrete state to get concrete instances of this rule.
    For example, the rule implies that $x \leftarrow 1,\, [x \mapsto 2] \Downarrow [x \mapsto 1]$ where we have instantiated the rule with the variable $x$, the arithmetic expression $1$, and the state $[x \mapsto 2]$.
  
  - The next example we will look at is the rule governing the operational semantics of composition $s_1 ; s_2$.
    In this case, the behaviour of the compound statement is precisely the behaviour of $s_1$ followed by the behaviour of the statement $s_2$.
    Therefore, the rule for this construct depends on the operational semantics of the two sub-statements $s_1$ and $s_2$.
    The rule is as follows:

    <!-- If $s_1,\, \sigma \Downarrow \sigma'$ and $s_2,\, \sigma' \Downarrow \sigma''$, then -->

    $$
      \dfrac
      { s_1,\, \sigma \Downarrow \sigma'
          \quad s_2,\, \sigma' \Downarrow \sigma''
      }
      {s_1; s_2,\, \sigma \Downarrow \sigma''}
    $$

    This rule says that if the relation $\Downarrow$ already includes the tuples $(s_1,\, \sigma,\, \sigma')$ and $(s_2,\, \sigma',\, \sigma'')$, then it must also include the triple $(s_1; s_2,\, \sigma,\, \sigma'')$.
    Notice that the form of this rule is very similar to the inductive rule for natural numbers in that new instances are generated from existing instance, i.e. if $n \in N$, then $n+1 \in N$.

    As with the two previous rules, the rule applies for all statements $s_1,\, s_2 \in S$ and all states $\sigma,\, \sigma',,\, \sigma'' \in \mathsf{State}$ - these are the rules metavariables.
    In order to use this rule, however, we need not only to instantiate metavariables but to find inhabitants of the $\Downarrow$ that determine the behaviour of the statements $s_1$ and $s_2$.
    Once we have done so, we can drive a new inhabitant describing the behaviour of the compound statement $s_1 ; s_2$.
    For example, if we already know that $x \leftarrow 2,\, [x \mapsto 1] \Downarrow [x \mapsto 2]$ and $x \leftarrow 3,\, [x \mapsto 2] \Downarrow [x \mapsto 3]$, then we can combine these facts to conclude that:
    $
      x \leftarrow 2; x \leftarrow 3,\, [x \mapsto 1] \Downarrow [x \mapsto 3]
    $

    In order to justify our assumptions $x \leftarrow 2,\, [x \mapsto 1] \Downarrow [x \mapsto 2]$ and $x \leftarrow 3,\, [x \mapsto 2] \Downarrow [x \mapsto 3]$, we will have to apply more rules; in this case, the assignment rules...

As we can see some rules, such as the rules for skip and assignment, can be used in isolation and do not depend on the behaviour of other statements.
Other rules require us to already know the behaviour of some statements in order to characterise their behaviour.
These two types of rules behave much like the base case and inductive cases of the natural numbers.
And, as with the natural numbers or other grammars, we can apply inductive cases multiple times to derive more complex instances.

### A Worked Example

Let us consider the program $x \mapsto 1; y \mapsto 2$ and compute the behaviour of this program according to our operational semantics starting in the initial state $[]$ (i.e. where all variables are zero).
Specifically, we are asking what states $\sigma$ are there (if any) such $x \mapsto 1; y \mapsto 2,\, [] \Downarrow \sigma$.

We will find such a strategy by first noticing that, at some point, we must use the rule for composition.
That is, $x \mapsto 1; y \mapsto 2,\, [] \Downarrow \sigma$ could only be true if we found another state $\sigma'$ such that $x \mapsto 1,\, [] \Downarrow \sigma'$ and $y \mapsto 2,\, \sigma' \Downarrow \sigma$.
This observation allows us to break down our problem into sub-problems.
If our example program was composed of more than two assignment statements, we would have to repeat this process.

Next, we turn to the sub-problems and see which inference rules can be used to solve them.
The first sub-problem requires us to find a state $\sigma'$ such that $x \mapsto 1,\, [] \Downarrow \sigma'$.
As before, we can see that only one rule is applicable - the rule for assignment.
The rule for assignment tells us that $\sigma'$ must be the state $[x \mapsto 1]$.
Now we may finally solve for $\sigma$ by considering the second sub-problem: $y \mapsto 2,\, \sigma' \Downarrow \sigma$.
We know that $\sigma'$ is $[x \mapsto 1]$ and thus the rule for assignment tell us that $\sigma$ is $[x \mapsto 1,\, y \mapsto 2]$.
This gives us a final state of the program.

Could there be any other final states $\sigma$ such that $x \mapsto 1; y \mapsto 2,\, [] \Downarrow \sigma$?


<!-- <div class="defn" markdown="1">
  An __inference rules__ is a logical statement of the form:

  $$
    \dfrac
    {P_1 \quad \cdots \quad P_n}
    {Q}
  $$

  That is interpreted as the statement: if $P_1$ and $P_2$ and ... and $P_n$ are all true, then $Q$ is also true.
  We refer to each $P_1$, ..., $P_n$ as _premises_ and to $Q$ and the _conclusion_.

  When undefined symbols (e.g. "$x$" or "$e$" or "$\sigma$") appear in the statements $P_1$, $P_2$, etc. or in $Q$ we assume they are _metavariables_ unless specified otherwise.
  These are universally quantified in the sense that the rule applies for all concrete instances.
</div> -->
<!-- 
Using this new notation, we can rephrase our rules for skip, assignment, and composition as follows:

$$
  \begin{array}{ccc}
    \dfrac
    {}
    {\mathsf{skip},\, \sigma \Downarrow \sigma}

    &
    \dfrac
    {}
    {x \leftarrow e,\, \sigma \Downarrow \sigma[x \mapsto \llbracket e \rrbracket_A(\sigma)]}

    &
    \dfrac
    {
      s_1,\, \sigma \Downarrow \sigma' \quad
      s_2,\, \sigma' \Downarrow \sigma''
    }
    {s_1; s_2,\, \sigma \Downarrow \sigma''}
  \end{array}
$$ -->

<!-- Remember that we will still use metavariables when presenting inference rules in this form.
That is, any undefined symbol appearing in an inference rule is universally quantified so that the rule applies for all appropriate instances. -->

<!-- Let us revisit the earlier example of the behaviour $x \mapsto 1; y \mapsto 2,\, [] \Downarrow [x \mapsto 1,\, y \mapsto 2]$.
This fact was derived from three inferences (i.e. instances of inference rules): 

  - Using the assignment rule: $\dfrac{}{x \mapsto 1,\, [] \Downarrow [x \mapsto 1]}$

  - Using the assignment rule: $\dfrac{}{y \mapsto 2,\, [x \mapsto 1] \Downarrow [x \mapsto 1,\, y \mapsto 2]}$

  - Using the composition rule: $\dfrac{x \mapsto 1,\, [] \Downarrow [x \mapsto 1] \qquad y \mapsto 2,\, [x \mapsto 1] \Downarrow [x \mapsto 1,\, y \mapsto 2]}{x \mapsto 1; y \mapsto 2,\, [] \Downarrow [x \mapsto 1,\, y \mapsto 2]}$ -->


## Derivations

Writing down the application of these rules can get tiresome, so we will now move to a more convenient notation.
One of the nice things about our notation for inference rules, is it allows us to combine these inferences together to present a single argument.
We do so by replacing premises with the inference used to justify them.
This builds up a tree of inferences - referred to as a _derivation tree_.

The derivation tree for our previous example is given as follows:

$$
  \dfrac
  {
    \dfrac
    {}
    {x \mapsto 1 [] \Downarrow [x \mapsto 1]}

    \quad

    \dfrac
    {}
    {y \mapsto 2,\, [x \mapsto 1] \Downarrow [x \mapsto 1,\, y \mapsto 2]}
  }
  {x \mapsto 1; y \mapsto 2,\, [] \Downarrow [x \mapsto 1,\, y \mapsto 2]}
$$

Each node in the tree corresponds to a statement, in this case regarding the behaviour of programs.
Additionally, we label nodes in the tree with the inference rules used to justify them.
The children of a node are the premises of that particular inference.