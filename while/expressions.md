---
layout: math
title: Expressions
nav_order: 1
mathjax: true
parent: The While Language
---

# Arithmetic Expressions

We formally define the language of *arithmetic expressions*, which we will use
in While programmes. We recall and extend their abstract syntax, then define
their semantics.

## Integer Literals

Before we move on to expressions, let us first define integer literals, which
will be useful. Note that we use $\mid$ for alternation in regular expressions
to avoid confusion between $+$ (the symbol for alternation) and $\texttt{+}$
(the plus symbol for use in arithmetic expressions).

<div class="defn" markdown="1">
An __integer literal__ is a string over the alphabet
$\\{ -, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 \\}$
that matches the following regular expression:

$$
  (\texttt{-} \mid \epsilon)(\texttt{0} \mid \texttt{1} \mid \texttt{2} \mid \texttt{3} \mid \texttt{4} \mid \texttt{5} \mid \texttt{6} \mid \texttt{7} \mid \texttt{8} \mid \texttt{9})^{+}
$$

</div>

We could formally define the semantics of integer literals, but we won't.
Instead, we will assume a function $⟦\cdot⟧^{\mathbb{Z}}$ over integer literals
and with value in $\mathbb{Z}$ that behaves exactly as you would expect.

For example:
- $⟦\texttt{42}⟧^{\mathbb{Z}} = 42$
- $⟦\texttt{-42}⟧^{\mathbb{Z}} = -42$
- $⟦\texttt{15485863}⟧^{\mathbb{Z}} = 15485863$

In handwriting, where font choice subtleties are less clear, we will use other
tricks to separate syntax from semantics (typically by underwriting syntactic
elements) and will often dispense with the explicit function application.

## Simple Arithmetic Expressions

We now have our extremely pedantic pants on.
This means we are ready to formally define the syntax and semantics of simple
arithmetic expressions: those you know and love, but without the dividy bits or
the exponenty bits.

### Syntax of Simple Arithmetic Expressions

<div class="defn" markdown="1">
A __simple arithmetic expression__ is a tree described by the following
grammar, where $\texttt{n}$ stands for integer literals, used as terminals.

$$
  A_1, A_2 ::= \texttt{n} \mid A_1\;\texttt{+}\;A_2 \mid A_1\;\texttt{-}\;A_2 \mid A_1\;\texttt{*}\;A_2
$$


For the purpose of understanding the expressions generated by this grammar as
trees, we view each integer literal as a constant, and each of the arithmetic
operators as binary.

We will use $A$ and indexed variants as variables that stand for arithmetic
expressions, and $\texttt{n}$ and indexed variants to stand for integer literals.

We denote the set of simple arithmetic expressions as $\mathcal{A}^{-}$.
</div>

Before we go further, it is important to note that symbols $\texttt{+}$,
$\texttt{-}$ and $\texttt{\*}$ in the definition above are part of the *syntax*
of expressions, they are *not* integer operations.

To avoid drowning in parentheses, we will here again follow some conventions:
operators are left-associative, and our multiplication operator binds more
tightly than the others (which have the same priority).

### Semantics of Simple Arithmetic Expressions

We can now give meaning to simple arithmetic expressions. Quite obviously,
the meaning of an expression will be a value in $\mathbb{Z}$.

<div class="defn" markdown="1">
We define the semantics of simple arithmetic expressions as a relation
$\Downarrow_{\mathcal{A}^{-}} \in \mathcal{A}^{-} \times \mathbb{Z}$, using the
following derivation rules.

$$
\begin{prooftree}
\AxiomC{}
\LeftLabel{$\rlnm{SVal}$}
\UnaryInfC{$\texttt{n} \Downarrow_{\mathcal{A}^{-}} ⟦\texttt{n}⟧^{\mathbb{Z}}$}
\end{prooftree}
\qquad\qquad
\begin{prooftree}
\AxiomC{$A_1 \Downarrow_{\mathcal{A}^{-}} v_1$}
\AxiomC{$A_2 \Downarrow_{\mathcal{A}^{-}} v_2$}
\LeftLabel{$\rlnm{SAdd}$}
\BinaryInfC{$A_1\;\texttt{+}\;A_2 \Downarrow_{\mathcal{A}^{-}} v_1 + v_2$}
\end{prooftree}
$$

<br/>

$$
\begin{prooftree}
\AxiomC{$A_1 \Downarrow_{\mathcal{A}^{-}} v_1$}
\AxiomC{$A_2 \Downarrow_{\mathcal{A}^{-}} v_2$}
\LeftLabel{$\rlnm{SSub}$}
\BinaryInfC{$A_1\;\texttt{-}\;A_2 \Downarrow_{\mathcal{A}^{-}} v_1 - v_2$}
\end{prooftree}
\qquad\qquad
\begin{prooftree}
\AxiomC{$A_1 \Downarrow_{\mathcal{A}^{-}} v_1$}
\AxiomC{$a_2 \Downarrow_{\mathcal{A}^{-}} v_2$}
\LeftLabel{$\rlnm{SAdd}$}
\BinaryInfC{$A_1\;\texttt{*}\;A_2 \Downarrow_{\mathcal{A}^{-}} v_1 \cdot v_2$}
\end{prooftree}
$$
</div>

Relation $\Downarrow_{\mathcal{A}^{-}}$ is in fact a total function (that is, there
is exactly one integer related to any given simple arithmetic expression): all
simple arithmetic expressions have meaning, and that meaning is unique. We can
prove this by structural induction. (Exercise sheet 5 will serve as a short
refresher for mathematical and structural induction—otherwise, check your
Logic/Introduction to Proofs, and Functional Programming notes from Year 1.)

<div class="defn" markdown="1">
$\Downarrow_{\mathcal{A}^{-}}$ is a total function.

For a simple arithmetic expression $A \in \mathcal{A}^{-}$ and an integer $v
\in \mathbb{Z}$, we write

$$
  ⟦A⟧^{\mathcal{A}^{-}} = v
$$

just if

$$
  A \Downarrow_{\mathcal{A}^{-}} v.
$$

</div>

**Proof**

By structural induction, we prove that, for any $A \in \mathcal{A}^{-}$ there
exists a unique $v \in \mathbb{Z}$ such that $A \Downarrow_{\mathcal{A}^{-}}
v$.

- Case $A = \texttt{n}$.
  Then we have $A \Downarrow_{\mathcal{A}^{-}} ⟦\texttt{n}⟧^{\mathbb{Z}}$ (and
  no other rule applies), which concludes the base case with
  $v = ⟦\texttt{n}⟧^{\mathbb{Z}}$
- Case $A = A_1\;\texttt{+}\;A_2$. By induction hypothesis, we must have:
  + A unique $v_1 \in \mathbb{Z}$ such that $A_1 \Downarrow_{\mathcal{A}^{-}} v_1$; and
  + A unique $v_2 \in \mathbb{Z}$ such that $A_2 \Downarrow_{\mathcal{A}^{-}} v_2$.

  Therefore, by rule $\rlnm{SAdd}$, we have
  $A_1\;\texttt{+}\;A_2 \Downarrow_{\mathcal{A}^{-}} v$ with $v = v_1 + v_2$.
  In addition, $\rlnm{SAdd}$ is the only rule that applies.

The other two cases are similar to the $\texttt{+}$ case.

**Note**
Had we known in advance that the meaning of an arithmetic expression was
entirely defined by the expression itself (but how would we have?!?!??), we
would have instead been able to equivalently define it as an inductively
defined function, using a notation that might remind some of you of things you
saw last year...

$$
\begin{align}
⟦\texttt{n}⟧^{\mathcal{A}}           & = ⟦\texttt{n}⟧^{\mathbb{Z}}\\
⟦A_1\;\texttt{+}\;A_2⟧^{\mathcal{A}} & = ⟦A_1⟧^{\mathcal{A}} + ⟦A_2⟧^{\mathcal{A}}\\
⟦A_1\;\texttt{-}\;A_2⟧^{\mathcal{A}} & = ⟦A_1⟧^{\mathcal{A}} - ⟦A_2⟧^{\mathcal{A}}\\
⟦A_1\;\texttt{*}\;A_2⟧^{\mathcal{A}} & = ⟦A_1⟧^{\mathcal{A}} \cdot ⟦A_2⟧^{\mathcal{A}}
\end{align}
$$

Because all of arithmetic expressions have _exactly_ one of the forms given by
the grammar, simply defining the behaviour of the function for each of these
forms is enough!

## Arithmetic Expressions

Arithmetic expressions in a programming language are _much_ more useful if
their result can be stored and recalled from memory. We extend our simple
arithmetic expressions with _variables_.

### Syntax of Arithmetic Expressions

<div class="defn" markdown="1">
An __arithmetic expression__ is a tree described by the following grammar,
where $\texttt{n}$ stands for integer literals, and $\texttt{x}$ stands for a
variable name (a string in $\mathsf{Var} =
\texttt{[a-z]}\texttt{[a-z0-9]}^{*}$; we will later remove some).

$$
  A_1, A_2 ::= \texttt{n} \mid \texttt{x} \mid A_1\;\texttt{+}\;A_2 \mid A_1\;\texttt{-}\;A_2 \mid A_1\;\texttt{*}\;A_2
$$

For the purpose of understanding the expressions generated by this grammar as
trees, we view each integer literal and variable as a constant, and each of the
arithmetic operators as binary.

We will use $A$ and indexed variants as variables that stand for arithmetic
expressions, and $\texttt{x}$ and indexed variants to stand for variables.
(Overriding the previous abbreviation: all our arithmetic expressions from now
on are non-simple.)

We denote the set of arithmetic expressions as $\mathcal{A}$.
</div>

We follow the same conventions as before when linearly representing arithmetic
expressions.

### Semantics of Arithmetic Expressions

The meaning of (full) arithmetic expressions is more complex than that of
simple arithmetic expressions (ah!). Indeed, they do not (and in general
cannot) simply have value in $\mathbb{Z}$: variables do not have intrinsic
value.

Full arithmetic expressions must therefore be interpreted in a _state_ a
mapping from variables to values in $\mathbb{Z}$.

<div class="defn" markdown="1">
A __state__ is a total function in
$\mathsf{State} = \mathsf{Var} \rightarrow \mathbb{Z}$.

Given a state $\sigma$, a variable $\texttt{x}$ and an integer value $v$, we
denote with $[\texttt{x} \mapsto v]\sigma$ the function that maps $\texttt{x}$
to $v$, and maps all $\texttt{x'} \neq \texttt{x}$ to $\sigma(\texttt{x'})$.

By convention, when a variable $\texttt{x}$ is not explicitly defined in a
given state $\sigma$, we will always take $\sigma(\texttt{x}) = 0$.
</div>

We can now give meaning to arithmetic expressions—the meaning of an arithmetic
expression is now a function from states to integer values. We could define it
as a relation first, then prove that it is a total function, but we won't
because we have better things to do.

<div class="defn" markdown="1">
We define the semantics of arithmetic expressions as a total function
$⟦\cdot⟧^{\mathcal{A}} \in \mathcal{A} \rightarrow \mathsf{State} \rightarrow
\mathbb{Z}$, inductively on the expression's structure.

$$
\begin{align}
⟦\texttt{n}⟧^{\mathcal{A}}(\sigma)           & = ⟦\texttt{n}⟧^{\mathbb{Z}}\\
⟦\texttt{x}⟧^{\mathcal{A}}(\sigma)           & = \sigma(\texttt{x})\\
⟦A_1\;\texttt{+}\;A_2⟧^{\mathcal{A}}(\sigma) & = ⟦A_1⟧^{\mathcal{A}}(\sigma) + ⟦A_2⟧^{\mathcal{A}}(\sigma)\\
⟦A_1\;\texttt{-}\;A_2⟧^{\mathcal{A}}(\sigma) & = ⟦A_1⟧^{\mathcal{A}}(\sigma) - ⟦A_2⟧^{\mathcal{A}}(\sigma)\\
⟦A_1\;\texttt{*}\;A_2⟧^{\mathcal{A}}(\sigma) & = ⟦A_1⟧^{\mathcal{A}}(\sigma) \cdot ⟦A_2⟧^{\mathcal{A}}(\sigma)
\end{align}
$$
</div>

## Syntax and Semantics of Boolean Expressions

We want our programmes to branch and loop, so we also need ways of talking
about the truth of some fact in some state. We define Boolean expressions.

<div class="defn" markdown="1">
A __boolean expression__ is a tree described by the following grammar. We
exclude strings $\texttt{true}$ and $\texttt{false}$ from the set of variable
names.

$$
  B, B_1, B_2 ::= \texttt{true} \mid \texttt{false} \mid \texttt{!}B \mid B_1\;\texttt{&&}\;B_2 \mid B_1\;\texttt{||}\;B_2 \mid A_1\;\texttt{=}\;A_2 \mid A_1\;\texttt{<=}\;A_2
$$

\texttt{true} and \texttt{false} are constants. \texttt{!} is a unary operator,
written in prefix form (before its operand). \texttt{&&}, \texttt{||},
\texttt{=}, and \texttt{<=} are binary operators.

We will use $B$ and indexed variants as variables that stand for arithmetic
expressions.

We denote the set of boolean expressions as $\mathcal{B}$.
</div>

As before, we take the convention that binary operators are left-associative.
Unary negation has highest precedence (and so negates the
smallest boolean expression that follows it).

<div class="defn" markdown="1">
We define the semantics of boolean expressions as a total function
$⟦\cdot⟧^{\mathcal{B}} \in \mathcal{B} \rightarrow \mathsf{State} \rightarrow
\mathbb{B}$, inductively on the expression's structure.

$$
\begin{align}
⟦\texttt{true}⟧^{\mathcal{B}}(\sigma)         & = \top\\
⟦\texttt{false}⟧^{\mathcal{B}}(\sigma)        & = \bot\\
⟦\texttt{!}B⟧^{\mathcal{B}}(\sigma)           & = ¬⟦B⟧^{\mathcal{B}}(\sigma)\\
⟦B_1\;\texttt{&&}\;B_2⟧^{\mathcal{B}}(\sigma) & = ⟦B_1⟧^{\mathcal{B}}(\sigma) \wedge ⟦B_2⟧^{\mathcal{B}}(\sigma)\\
⟦B_1\;\texttt{||}\;B_2⟧^{\mathcal{B}}(\sigma) & = ⟦B_1⟧^{\mathcal{B}}(\sigma) \vee ⟦B_2⟧^{\mathcal{B}}(\sigma)\\
⟦A_1\;\texttt{=}\;A_2⟧^{\mathcal{B}}(\sigma)  & = ⟦A_1⟧^{\mathcal{A}}(\sigma) = ⟦A_2⟧^{\mathcal{A}}(\sigma)\\
⟦A_1\;\texttt{<=}\;A_2⟧^{\mathcal{B}}(\sigma)  & = ⟦A_1⟧^{\mathcal{A}}(\sigma) <= ⟦A_1⟧^{\mathcal{A}}(\sigma)
\end{align}
$$
</div>
