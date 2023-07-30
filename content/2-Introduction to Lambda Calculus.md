+++
title = "2. Introduction to Lambda Calculus"
description = "Conversion"
date = 2021-05-10
[taxonomies]
tags = ["Lambda", "Calculus", "Functional"]
[extra]
banner = "haskell_logo.png"
katex  = true
+++

이장에서는 \\(\lambda-\\)계산을 형식적으로 소개한다. \\(\lambda-\\)계산은 수학과 컴퓨터 과학등 다양한 분야에서 중요한 개념으로 사용되는 형식 언어이다. \\(\lambda-\\)계산은 알론조 처치(Alonzo Church)에 의해 1930년대 초에 개발되었으며, 이론적인 컴퓨팅과 함수의 표현에 매우 유용하다.  

\\(\lambda-\\)계산은 변수, 함수 추상화, 그리고 함수 적용을 사용하여 구성된다. 변수들의 무한한 집합을 \\(V=\lbrace{v, v', v''..\rbrace}\\)라고 가정하고, 이 변수들을 이용하여 \\(\lambda-\\)계산의 식(\\(\lambda-term\\))을 형성한다. 함수 추상화는 람다 기호(\\(\lambda\\))와 변수를 사용하여 정의하며, 함수 적용은 괄호를 이용하여 표현한다.  
\\(\lambda-\\)계산의 특성과 활용 방법에 대해 이해함으로써, \\(\lambda-\\)계산이 어떻게 다양한 분야에서 중요한 도구로 사용되는지와 \\(\lambda-\\)계산의 응용 가능성과 기본 원리들에 대한 이해를 갖출 수 있을 것이다.  

</br>

<!-- more --> 

# 2.1. Definition.
\\(\lambda-\\)식\\((\lambda-term)\\)의 집합(표기법: \\(\Lambda\\))은 무한한 변수들의 집합 \\(V = \lbrace{v, v', v'',..\rbrace}\\)을 사용하여 적용(application)과 함수 추상화(abstraction)을 통해 구성된다.

$$
\begin{aligned}
x \in V &\rArr x \in \Lambda, \\\\
M,N \in \Lambda &\rArr (MN) \in \Lambda, \\\\
M \in \Lambda, x \in V &\rArr (\lambda{x}M) \in \Lambda. \\\\
\end{aligned}
$$

BNF(Backus-Naur Form)로는 다음과 같이 표현할 수 있다.
$$
\begin{aligned}
variable &\Coloneqq \lq{v}\text{\textquoteright} \space | \space variable \space \lq'\text{\textquoteright} \\\\
\lambda-term &\Coloneqq varialble \space | \space \lq{(}\text{\textquoteright} \space \lambda-term \space \lambda-term \space \lq{)}\text{\textquoteright} \space | \space \lq{(}\text{\textquoteright} \space \lambda-term \space \lambda-term \space \lq{)}\text{\textquoteright} \\\\
\end{aligned}
$$

# 2.2. Example.
다음은 \\(\lambda-\\)식의 예시이다.
\\[v';\\]
\\[(v'v);\\]
\\[(\lambda{v}(v'v));\\]
\\[((\lambda{v}(v'v))v'');\\]
\\[(((\lambda{v}(\lambda{v'}(v'v)))v'')v''')\\]

# 2.3. Convention.
(i). \\(x, y, z,...\\)는 임의의 변수를 나타낸다. \\(M, N, L,...\\)은 임의의 \\(\lambda-\\)식을 나타내며, 가장 바깥 쪽의 괄호는 생략한다.  
(ii). \\(M \equiv N\\)은 \\(M\\)과 \\(N\\)이 동일한 \\(\lambda-\\)식이거나 서로의 bound 변수를 변경하여 얻을 수 있음을 나태낸다. 예를들어,  

$$
\begin{aligned}
(\lambda{xy})z &\equiv (\lambda{xy})z; \\\\
(\lambda{xx})z &\equiv (\lambda{yy})z; \\\\
(\lambda{xx})z &\not \equiv z; \\\\
(\lambda{xx})z &\not \equiv (\lambda{xy})z. \\\\
\end{aligned}
$$

(iii). 약어로서 다음을 사용한다.
\\[FM_1 ··· M_n \equiv (··((FM_1)M_2) ··· M_n)\\] 
그리고
\\[\lambda{x_1} ··· x_n.M \equiv \lambda{x_1}(\lambda{x_2}(···(\lambda{x_n}(M)))).\\]
2.2의 예시는 이제 다음과 같이 작성될 수 있다.
\\[y;\\]
\\[yx;\\]
\\[\lambda{x}.yx;\\]
\\[(\lambda{x}.yx)z;\\]
\\[(\lambda{xy}.yx)zw.\\]

참고로 \\(\lambda{x}.yx\\)는 \\((\lambda{x}(yx))\\)이며, \\(((\lambda{x.y})x)\\)가 아니다.  

# 2.4. Definition.  
(i). \\(\lambda-\\)식 \\(M\\)의 자유 변수\\((free \space variable)\\) 집합을 \\(FV(M)\\)으로 다음과 같이 귀납적으로 정의한다.

$$
\begin{aligned}
FV(x) &= {x}; \\\\
FV(MN) &= FV(M) \cup FV(N); \\\\
FV(\lambda{x}.M) &= FV(M) - \lbrace x \rbrace. \\\\
\end{aligned}
$$

\\(M\\) 내의 변수가 자유 변수가 아닌 경우 해당 변수는 묶여(bound)있다. 주목할 점은 변수가 묶여있는 경우 해당 변수가 \\(\lambda\\)의 범위(scope) 아래에 나타난다.

(ii). \\(\lambda-\\)식 \\(M\\)이 닫혀있는 경우, 즉 \\(FV(M) = \emptyset \\)인 경우, 이를 닫힌 \\(\lambda-\\)식(\\(closed \space \lambda-term\\) 또는 \\(combinator\\))라고 한다. 닫힌 \\(\lambda-\\)식의 집합은 \\(\Lambda^{o}\\)로 표기 된다.

(iii). \\(\lambda-\\)식 \\(M\\)에서 \\(x\\)의 자유 등장(free occurrences)에 \\(N\\)을 대체한 결과를 \\(M[x := N]\\)으로 다음과 같이 정의한다. 

$$
\begin{aligned}
x[x := N] &\equiv N; \\\\
y[x := N] &\equiv y, \quad \text{if} \space x \not \equiv y; \\\\
(M_1M_2)[x := N] &\equiv (M_1[x := N])(M_2[x := N]); \\\\
(\lambda{y}.M_1)[x := N] &\equiv \lambda{y}.(M_1[x := N]). \\\\
\end{aligned}
$$

# 2.5. Example.
다음의 \\(\lambda-\\)식을 고려해보자. 
\\[\lambda{xy}.xyz\\]
이때 \\(x\\)와 \\(y\\)는 묶인 변수(bound variables)이며, \\(z\\)는 자유 변수(free variable)이다. 또한, \\(\lambda-\\)식 \\(\lambda{xy}.xxy\\)는 닫힌 \\(\lambda-\\)식 이다.

# 2.6. Variable Conention.
\\(M_1,..., M_n\\)이 특정 수학적 문맥(예: 정의, 증명)에서 나타난다면, 이러한 \\(\lambda-\\)식들에서 모든 묶인 변수는 자유 변수와 다른 변수로 선택된다.  
주목할 점은 정의 2.4의 (iii)에서 '\\(y \not = x \\)이고 \\(y \not \in FV(N)\\)'을 명시할 필요가 없다는 것이다. 변수 관례에 따라 이 조건이 만족된다.  

이제 \\(\lambda-\\)계산을 형식적인 이론으로 소개할 수 있다.

# 2.7. Definition.
(i). \\(\lambda-\\)계산의 주요 공리 체계는 다음과 같다.
\\[(\lambda{x}.M)N = M[x := N]; \space \forall \space M, N \in \Lambda \qquad (\beta)\\]
(ii). 또한 논리적(lgical) 공리와 규칙이 있다.
- 동일성(Equality):

$$
\begin{aligned}
M &= N; \\\\
M = N &\Rightarrow N = M; \\\\
M = N, N = L &\Rightarrow M = L. \\\\
\end{aligned}
$$

- 일치성 규칙(Compatibility rules):

$$
\begin{aligned}
M = M' &\Rightarrow MZ = M'Z; \\\\
M = M' &\Rightarrow ZM = ZM'; \\\\
M = M' &\Rightarrow \lambda{x}.M = \lambda{x}.M'. \qquad (\zeta) \\\\
\end{aligned}
$$

(iii). \\(\lambda-\\)계산에서 \\(M = N\\)이 증명가능하다면, 때때로 \\(\lambda \vdash M = N\\)으로 표기할 수 있다.  
일치성 규칙의 결과로, 모든 \\(\lambda-\\)식 문맥에서 하위식을 동일한 식으로 대체할 수 있다.
\\[M = N \Rightarrow \boxed{⋅⋅⋅M⋅⋅⋅} = \boxed{⋅⋅⋅N⋅⋅⋅}.\\]
예를 들어, \\((\lambda{y}.yy)x = xx\\) 이므로, 다음과 같이 표현할 수 있다.
\\[\lambda \vdash \lambda{x}.x((\lambda{y}.yy)x)x = \lambda{x}.x(x.x)x. \\]

# 2.8. Remark.
묶인 변수들의 이름이 다른 식들을 동일한 것으로 취급하였다. 또 다른 방법은 다음과 같은 공리 체계를 \\(\lambda-\\)계산에 추가하는 것이다.
\\[\lambda{x}.M = \lambda{y}.N[x := y], \quad \text{provided that } y \text{ does not occur in } M. \qquad (\alpha)\\]

우리는 문법적 수준에서 이러한 동일성을 정의하는 이론을 선호한다. 이러한 동일성은 우리의 머릿속에서 이루어지며, 종이 위에서 직접적으로 표현되지 않는다. \\(\lambda-\\)계산의 구현에 있어서는 이러한 \\(\alpha-\\)변환을 다루어야 한다.

# 2.9. Lemma.
\\[\lambda \vdash (\lambda{x_1} ⋅⋅⋅ x_n.M)X_1 ⋅⋅⋅ X_n = M[x_1 := X_1] ⋅⋅⋅ [x_n := X_n]. \\]
<b>증명.</b>  
공리 \\((\beta)\\)에 의해 다음이 성립한다. 
\\[(\lambda{x_1}.M)X_1 = M[x_1 := X_1].\\]
\\(n\\)에 대한 귀납법을 사용하여 결과를 얻는다. \\(\quad \square \\)

# 2.10. Example (Standard combinators).
다음과 같이 combinator를 정의 한다.

$$
\begin{aligned}
\bold{I} &\equiv \lambda{x}.x; \\\\
\bold{K} &\equiv \lambda{xy}.x; \\\\
\bold{K_*} &\equiv \lambda{xy}.y; \\\\
\bold{S} &\equiv \lambda{xyz}.xz \(yz). \\\\
\end{aligned}
$$

그런 다음, 보조정리 2.9에 의해 다음과 같은 방정식을 얻을 수 있다.

$$
\begin{aligned}
\bold{I}M &= M; \\\\
\bold{K}MN &= M; \\\\
\bold{K_*}MN &= N; \\\\
\bold{S}MNL &= ML(NL). \\\\
\end{aligned}
$$


# 2.11. Example.
\\(\exist G \space \forall X\space GX = XXX \\)(\\(\lambda \vdash  GX = XX\\)를 만족하는 \\(G \in \Lambda \\)가 존재한다). 실제로, \\(G \equiv \lambda{x}.xxx\\)로 두면 요구사항을 만족한다.  
재귀 방정식은 특별한 기술을 요구하는데, 다음 결과는 \\(\lambda-\\)계산에서 재귀를 표현하는 한 가지 방법을 제공한다.

# 2.12. FIXEDPOINT THEOREM.
(i). \\(\forall F \space \exist X \space FX = X.\\)(모든 \\(F \in \Lambda\\)에 대해 \\(\lambda \vdash FX = X\\)를 만족하는 \\(X \in \Lambda\\)가 존재)

(ii). 고정점 조합자 \\(Y\\)가 존재한다.
\\[\bold{Y} \\equiv \lambda{f}.(\lambda{x}.f(xx))(\lambda{x}.f(xx))\\]
이러한 조합자는 다음과 같은 특성을 가진다.
\\[\forall F \space F(\bold{Y}F) = \bold{Y}F. \\]

<b>증명.</b>  
\\(W \equiv \lambda{x}.F(xx)\\) 와 \\(X \equiv WW\\)를 정의한다. 그러면,
\\[X \equiv WW \equiv (\lambda{x}.F(xx))W = F(WW) \equiv FX.\\]


# 2.13. Example.
(i). \\(\exist G \space \forall X \space GX = \bold{S}GX.\\) 일 때
$$
\begin{aligned}
\forall X \space GX = \bold{S}GX &\Leftarrow Gx = \bold{S}Gx \\\\
&\Leftarrow G = \lambda{x}.\bold{S}Gx \\\\
&\Leftarrow G = (\lambda{gx}.\bold{S}gx)G \\\\
&\Leftarrow G \equiv \bold{Y}(\lambda{gx}.\bold{S}.gx).
\end{aligned}
$$
또한 \\(G \equiv YS\\)로 정할 수도 있다.  

(ii). \\(\exist G \space \forall X \space GX= GG: \text{take} \space G \equiv Y(\lambda{gx}.gg)\\).  
람다 계산법에서는 숫자를 정의하고 숫자 함수를 표현할 수 있다.

# 2.14. Definition.
(i). \\(F^{n}(M)\\)은 \\(F \in \Lambda\\)와 \\(n \in \N\\)에 대해 다음과 같이 재귀적으로 정의한다.
$$
\begin{aligned}
F^{0}(M) &\equiv M; \\\\
F^{n+1}(M) &\equiv F(F^{n}(M)). \\\\
\end{aligned}
$$

(ii). Church 수(\\(Church \space numerals\\)) \\(\space \bold{c}_0, \bold{c}_1, \bold{c}_2, ...\\)는 다음과 같이 정의된다.
\\[\bold{c}_n \equiv \lambda{fx}.f^{n}(x).\\]

# 2.15. Proposition.
$$
\begin{aligned}
\bold{A} _+ &\equiv \lambda{xypq}.xp(ypq); \\\\
\bold{A} _∗ &\equiv \lambda{xyz}.x(yz) \\\\
\bold{A} _{exp} &\equiv \lambda{xy}.yx.
\end{aligned}
$$

\\(\forall \space n, m \in \N \\)에 대해 다음이 성립한다.  
(i). \\(\bold{A} _ +{\bold{c} _ n}{\bold{c} _ m} = \bold{c} _ {n+m} .\\)  
(ii). \\(\bold{A} _ {\*}{\bold{c} _ n}{\bold{c} _ m} = \bold{c} _ {n*m} .\\)  
(iii). \\(\bold{A} _ {exp}{\bold{c} _ n}{\bold{c} _ m} = \bold{c} _ {(n^m)} , except \space for \space m = 0\\)  

# 2.16. Lemma.  
(i). \\((\bold{c}_n{x})^m(y) = x^{n*m}(y).\\)  
(ii). \\((\bold{c}_n)^m(x) = \bold{c} _ (n^m)(x), \space for \space m > 0.\\)

<b>증명.</b>  
(i). \\(m\\)에 대한 귀납법을 사용한다. \\(m = 0\\)인 경우, \\(LHS = y = RHS\\) 이므로 성립한다. 이제 \\(m\\)에 대해 (i)가 성립한다고 가정한다(귀납 가정). 그러면,

$$
\begin{aligned}
(\bold{c} _ {n}x)^{m+1}(y) &= \bold{c} _ {n}x((\bold{c} _{n}x)^m(y)) \\\\
&= \bold{c} _ {n}x(x^{n*m}(y)) \\\\
&= x^n(x^{n\*m}(y)) \\\\
&\equiv x^{n+n\*m}(y) \\\\
&\equiv x^{n\*(m+1)}(y). \\\\
\end{aligned}
$$

(ii). \\(m > 0\\)에 대한 귀납법을 사용한다. \\(m = 1\\)인 경우, \\(LHS \equiv c_{n}x \equiv RHS\\)이므로 성립한다. 이제 \\(m\\)에 대해 (ii)가 성립한다고 가정한다(귀납 가정). 그러면,

$$
\begin{aligned}
(\bold{c} _ n)^{m+1}(x) &= \bold{c} _ n((\bold{c} _ n)^m(x)) \\\\
&= \bold{c} _n(\bold{c} _ {(n^m)}(x)) \\\\
&= \lambda{y}.(\bold{c} _ {(n^m)}(x))^n(y) \\\\
&= \lambda{y}.x^{{n^m}\*n}(y) \quad \text{by (i),} \\\\
&= \bold{c} _ {(n^{m+1})}x. \\\\
\end{aligned}
$$
