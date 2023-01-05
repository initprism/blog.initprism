+++
title = "1. Haskell Algorithm Design"
description = "Functional programming"
date = 2022-11-28
[taxonomies]
tags = ["Haskell", "Algorithm", "Functional programming"]
[extra]
banner = "lambda_symbol.png"
katex  = true
+++

무엇이 좋은 알고리즘을 만드는가? 이 질문에 대한 답은 좋은 <b>요리책을 만드는 것</b>이 무엇인지에 대한 질문만큼 답도 많다.
요리법이 명확하고 따라 하기 쉬운가? 요리법이 표준적이고 잘 알려진 기술을 사용하는가? 혹은 잘 알려진 재료를 사용하는가? 준비 시간이 합리적인가?
냄비, 프라이팬이 많고 큰 부엌 공간이 필요한가? 등등. 어떤 사람들은 이 질문을 받았을 때 요리법에서 가장 중요한 것은 요리가 매력적인지 아닌지라고 말하는데,
이것이 함수형 알고리즘을 표현할 때 염두에 두어야 할 점이다.  
여기서는 함수형 부엌에서 매력적인 알고리즘을 위한 좋은 요리법을 설계하는데 필요한 재료를 검토하고 효율성을 분석하는데 필요한 도구를 설명한다.
우리가 선택한 함수형 언어는 하스켈이고 재료는 하스켈의 함수이다.  

<!-- more -->  
- 이러한 재료와 기술을 결합하는 것을 검토한다. 하지만 이것은 하스켈에 대한 소개가 아니라는 것에 유의하길 바란다.
주요 목적은 독자에게 친숙한 영역 또는 최소한 독자가 편안하게 읽을 수 있는 영역의 개요를 설명하는 것이다. 
- 효율성, 특히 알고리즘 실행 시간에 관하여 이야기하며 공간 효율성의 문제를 완전히 무시할 것이다. 왜냐하면 함수형 프로그램을 실행하는 것은 상당히 많은
부엌 공간을 차지할 수 있다. 함수형 표현을 평가할 때 사용되는 [공간을 제어하는 방법](https://wiki.haskell.org/Performance/Strictness)이 있지만 여기서는 언급하지 않기로 한다.
실행시간을 명시하기 위해 점근적 표기법을 검토하고, 재귀 함수의 실행 시간을 결정하기 위해 본질적으로 재귀 함수인 반복 관계가 점근적 추정을 제공하는 방법을 탐구한다.
- 대칭 리스트, 랜덤 액세스 리스트, 순수 함수형 배열에 대해 소개한다. 대부분 알고리즘을 효율적으로 만드는 데 필요한 데이터 구조에 대해 논의하겠지만, 이 세 가지는
특정 응용 프로그램을 염두에 두지 않고 논의할 수 있는 일관을 형성한다.

## Types and funcions
사용하는 대부분의 함수는 하스켈의 Standard Prelude 또는 Data.List 라이브러리를 사용하며 Boolean, Char, String 및 수치 자료형 같은 단순 유형만 사용할 것이다.
가독성을 향상하기 위해 type synonym을 사용하고, newtype 선언을 사용할 것이다. 필요한 경우 Eq, Ord, Num과 같은 간단한 타입 클래스를 사용하지만
새로운 클래스는 정의하지 않는다.  
하스켈은 두 종류의 정수인 Int, Integer와 그리고 두 종류의 부동소수점인 Float, Double을 포함하여 많은 종류의 수치 자료형을 제공한다. 
Int의 요소는 64비트 컴퓨터에서 [\\(\-2^{63},2^{63}\\)) 범위로 제한되지만 하스켈 컴파일러(GHC)는 [\\(\-2^{29},2^{29}\\)) 범위만 [요구](https://hackage.haskell.org/package/base-4.17.0.0/docs/GHC-Int.html)한다. Integer의 요소는 제한이 없다. Float 및 Double에서 제공하는 부동 소수점은 한두 곳에서 비율을 구하는 연산을 제외하고서는 거의 사용하지 않을 것이다.
하스켈은 자연수 타입이 없지만, Nemeric.Natural 라이브러리는 임의 정밀도 타입을 제공한다. 대신, 여기서는 자연수 타입을 type synonym을 사용하여 정의할 것이다.  
> GHC 문서에는 "Prelude.Integer가 무제한 크기의 부호 있는 정수를 제공하는 것처럼 무한한 크기의 부호 없는 정수를 제공하는 Natural 유형을 추가하는 것이 매우 자연스러울 것이지만, 수요가 없기 때문에 아직은 하지 않는다”고 말했다

\\[\bold{type}\ Nat = Int\\]

하스켈은 Nat의 요소가 자연수라는 제약 조건을 적용할 수 없기 때문에 type synonym을 사용한다. 예를 들어 Prelude에 정의된 리스트의 길이는 Int의 음이 아닌 요소이기 때문에
\\(length :: [a] \rightarrow Nat \\) 라고 단언할 수 있다.
하스켈은 또한 Data.Word 라이브러리에서 비부호형 정수를 제공한다. Word의 요소는 비부호형 정수이며 64비트 시스템에서 \\(0 \leqslant n < 2^{64} \\) 범위의 자연수 n을 나타낼 수 있다.
그렇지만 \\(\bold{type}\ Nat = Word\\)를 정의하는 것은 단순히 \\(length :: [a] \rightarrow Nat \\)을 단언할 수 없기 때문에 불편할 것이다.  

가장 중요한 것은 리스트를 처리하는 기본 기능이다. 이 중 가장 유용한 함수는 \\(map, filter, fold\\) 이며, 다음의 \\(map\\) 함수의 정의를 보자.

$$\begin{aligned}
map& :: (a \rightarrow b) \rightarrow [a] \rightarrow [b]&\\\\
map&\ f\ [] = []\\\\
map&\ f\ (x:xs) = f\ x\ :\ map\ f\ xs\\\\
\end{aligned}$$
> \\(map\\)은 첫 번째 인수인 함수를 두 번째 인수인 리스트의 모든 요소에 적용한다.

다음의 \\(filter\\) 함수의 정의를 보자.
$$\begin{aligned}
filter& :: (a \rightarrow Bool) \rightarrow [a] \rightarrow [a]&\\\\
filter&\ p\ [] = []\\\\
filter&\ p\ (x:xs) = \bold{if}\ p\ x\ \bold{then}\ x:filter\ p\ xs\ \bold{else}\ filter\ p\ xs\\\\
\end{aligned}$$
> \\(filter\\)는 리스트를 필터링하여 주어진 조건을 만족하는 요소만 유지한다.

리스트에는 다양한 \\(fold\\) 함수가 있으며 중요한 두 가지는 \\(foldr\\)과 \\(foldl\\) 함수이다. 다음의 \\(foldr\\) 함수의 정의를 보라.
$$\begin{aligned}
foldr& :: (a \rightarrow b \rightarrow b) \rightarrow b \rightarrow [a] \rightarrow b&\\\\
foldr&\ f\ e\ [] = e\\\\
foldr&\ f\ e\ (x:xs) = f\ x\ (foldr\ f\ e\ xs)\\\\
\end{aligned}$$
> \\(foldr\\)는 값 \\(e\\)로 시작하고 이항 연산자 \\(⊕\\)를 사용하여 리스트를 오른쪽에서 왼쪽으로 접으며 단일 값으로 축소한다.  
> \\(foldr\ (⊕)\ e\ [x, y, z]\ = x ⊕ (y ⊕ (z ⊕ e))\\)  

\\(foldl\\)은 리스트를 왼쪽에서 오른쪽으로 접는다.
$$\begin{aligned}
foldl& :: (b \rightarrow a \rightarrow b) \rightarrow b \rightarrow [a] \rightarrow b&\\\\
foldl&\ f\ e\ [] = e\\\\
foldl&\ f\ e\ (x:xs) = foldl\ f\ (f\ e\ x)\ xs
\end{aligned}$$
> \\(foldl\ (⊕)\ e\ [x, y, z]\ = ((e ⊕ x) ⊕ y) ⊕ z\\)  
> \\(foldl\\)은 유한한 리스트에서만 잘 정의된 값을 반환한다. 무한한 리스트에서는 \\(foldl\\) 평가는 절대 종료되지 않는다. 

\\(foldl\\\)에 대해 \\(foldr\\)을 사용하여 정의할 수있다.
\\[foldl\ f\ e = foldr\ (flip\ f)\ e\ ·\ reverse\\]

여기서 \\(flip\\)은 prelude에 정의된 함수이다.
$$\begin{aligned}
flip& :: (a \rightarrow b \rightarrow c) \rightarrow b \rightarrow a \rightarrow c\\\\
flip&\ f\ x\ y = f\ y\ x\\\\
\end{aligned}$$

> 선형 시간 내에 리스트를 반전시킬 수 있기 때문에 이 정의는 점근적으로 전자만큼 빠르다. 하지만 입력에 대해 반전시키고 접는 것에 대한 두 번의 순회가 필요하다. 

## Processing Lists

---
<b> 작성 중 2022.12.14 </b>