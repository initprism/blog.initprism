+++
title = "3. Introduction to Lambda Calculus"
description = "The Power of Lambda"
date = 2021-06-09
[taxonomies]
tags = ["Lambda", "Calculus", "Functional"]
[extra]
banner = "haskell_logo.png"
katex  = true
+++

람다 계산에서 함수 덧셈, 곱셈, 거듭제곱이 Church 수\\((Church's \space numerals)\\)로 표현될 수 있음을 보았다. 이제 모든 게산 가능한(재귀적인) 함수가 람다 계산에서 표현될 수 있음을 보이도록 한다. 이를 위해 먼저 다른 종류의 수 체계를 사용할 것이다. 진리값과 조건문을 람다 계산에서 표현할 수 있다.

<!-- more --> 

</br>

# 3.1 Difinition.  
(i). \\(true \equiv K, \space false \equiv K_*.\\)  
(ii). 만약 \\(B\\)가 부울(Boolean)로 간주된다면, 즉 <b>참</b> 또는 <b>거짓</b>으로 표현되는 용어라면
\\[\text{if}\space B\space \text{then}\space P\space \text{else}\space Q\\]
위의 문장은 다음과 같이 표현할 수 있다.
\\[BPQ.\\]

# 3.2 Difinition.
\\(\Lambda\\)의 원소인 \\(M, N\\)에 대해\\((M, N \in \Lambda)\\) 다음과 같이 정의한다.
\\[[M, N] \equiv \lambda{z}. \text{if} \space z \space \text{then} \space M \space \text{else} \space N \quad (\equiv \lambda{z}.zMN) \\]
그러면,

$$
\begin{aligned}
[M, N]\bold{true} &\equiv M, \\\\
[M, N]\bold{false} &\equiv N, \\\\
\end{aligned}
$$

따라서 \\([M, N]\\)은 순서쌍으로 사용될 수 있으며, 생성 방법을 이용하여 Barendregt(1976)의 대체 자연수 표현을 사용할 수 있다.