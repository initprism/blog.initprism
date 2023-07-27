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
명령형 프로그래밍이 익숙한 개발자가 함수형 프로그래밍을 처음 만났을 때, 계산의 흐름이 잘못된 순서로 수행되는 것처럼 느낄 가능성이 있다. 특히, 재귀는 목표를 달성하기 위해 반대로 수행되는 것 같은 과정으로 묘사되기도 하며, 일반적으로 왼쪽에서 오른쪽으로 진행되는 것이 자연스러운 방식처럼 보이지만 리스트는 종종 오른쪽에서 왼쪽으로 처리되는 것처럼 보인다. 함수 합성을 포함하는 수학적 표현은 역시 일반적으로 오른쪽에서 왼쪽으로 이해된다. 
\\[head\ =\ foldr\ (\ll)\ ⊥\ \bold{where}\ x \ll y = x \\\]
개념적으로 \\(foldr\\\)의 평가는 오른쪽에서 왼쪽으로, 첫 번째 요소를 만난 후에는 중단된다.
$$\begin{aligned}
head\ (x:xs) &= foldr\ (\ll)\ ⊥\ (x:xs)\\\\
             &= x\ \ll foldr\ (\ll)\ ⊥\ xs\\\\
             &= x
\end{aligned}$$
하스켈은 필요할 때만 평가가 수행되는 지연 언어이기 때문에 \\(\ll\\\)의 두 번째 인자에 대한 실행은 필요하지 않다.  
가끔은 순회하는 방향이 중요한데, 예를 들어 \\(concat\\\)의 두 가지 정의를 생각해보자
$$\begin{aligned}
concat_{1}&, cocnat_{2} :: [[a]] \rightarrow [a]\\\\
cocnat_{1}& = foldr\ (\oplus)\ []\\\\
cocnat_{2}& = foldl\ (\oplus)\ []
\end{aligned}$$
모든 유한 리스트 \\(xs\\\)에 대해 \\(concat_{1}\ xs = concat_{2}\ xs\\\)인데, 어떤 정의가 더 나을까?  
많은 문서 더미가 있는 테이블을 상상하며, 올바른 순서가 유지되도록 문서 더미를 하나의 큰 파일로 조합해야 한다고 생각해보자. 직관적으로 두 번째 문서는 첫 번째 문서 아래에 있어야 하고 세 번째 문서는 두 번째 문서 밑에 있어야 할 것이다. 왼쪽에서 오른쪽으로, 첫 번째 문서를 집어들고, 두 번째 문서 위에 올려놓고, 결합한 문서 더미를 다시 세 번째 문서 위에 올려놓는 작업을 할 수 있을 것이다. 또는 오른쪽에서 왼쪽으로, 마지막 문서 위에 끝에서 두 번째 파일을 올려놓고 다시 끝에서 세 번째 파일을 그 위에 올려 놓을 수도 있다. 왼쪽에서 오른쪽으로 방법은 큰 문서 더미를 들어 올려 마지막 더미에 올려야 하는 단계에서 더 무거운 연산을 수반하지만, 오른쪽에서 왼쪽으로의 방법에서는 각 단계에서 하나의 문서만 올리는 것으로 해결된다. 따라서 \\(concat_{1}\\\)은 잠재적으로 \\(concat_{2}\\\)보다 더 효율적인 방법이다.  
이동 방향은 알고리즘 설계의 또 다른 개념인 [온라인 알고리즘](https://en.wikipedia.org/wiki/Online_algorithm)의 개념과도 관련이 있다. 온라인 알고리즘은 처음부터 전체 리스트를 사용하지 않고 리스트를 처리하는 알고리즘이다. 대신, 리스트는 잠재적으로 무한한 값의 스트림으로 간주된다. 결과적으로, 주어진 스트림에 대한 문제를 해결하기 위한 온라인 알고리즘은 스트림의 모든 접두사에 대한 문제를 해결해야 한다. 이는 스트림이 왼쪽에서 오른쪽으로 처리되어야 함을 의미한다. 반대로 오프라인 알고리즘은 시작할 전체 리스트가 주어지고 원하는 순서로 처리할 수 있는 알고리즘이다. 온라인 알고리즘은 일반적으로 하스켈에서 \\(scanl\\\)로 정의할 수 있다.
$$\begin{aligned}
scanl& :: (b \rightarrow a \rightarrow b) \rightarrow b \rightarrow [a] \rightarrow [b]\\\\
scanl&\ f\ e\ = [e]\\\\
scanl&\ f\ e\ (x:xs) = e\ : scanl\ f\ (f\ e\ x\)\ xs
\end{aligned}$$
> \\(scanl\ (⊕)\ e\ [x, y, z, ...] = [e,\ e ⊕ x,\ (e ⊕ x) ⊕ y,\ ((e ⊕ x) ⊕ y) ⊕ z, ...]\\\)  
> 무한 리스트를 \\(scanl\\\)에 적용하면 무한 리스트를 생성할 수 있다.

## Inductive and recursive definitions
대부분 함수가 재귀를 사용하지만, 재귀의 특성은 함수마다 다르다. \\(map,\ filter\\\) 및 \\(foldr\\\)은 구조적 재귀를 사용한다. 즉, 재귀는 빈 리스트 \\([]\\\)와 연결 생성자 \\((:)\\\)에서 빌드된 리스트 구조를 따른다. 빈 리스트에 대한 구조 하나와 \\(x:xs\\\)에 대한 또 다른 재귀 구조가 있다. 여기선 그러한 정의를 귀납적 정의라고 부를 것이다. 대부분의 귀납적 정의는 \\(foldr\\\)의 인스턴스로 표현될 수 있다.  
다음은 리스트의 모든 순열을 반환하는 \\(perms_{1}\\\) 함수의 귀납적 정의이다.
$$\begin{aligned}
perms_{1}&\ [] = [[\]]\\\\
perms_{1}&\ (x:xs) = [zs\ |\ ys \leftarrow perms_{1}\ xs,\ zs \leftarrow inserts\ x\ ys]
\end{aligned}$$
비어 있지 않은 리스트의 순열은 리스트 꼬리의 각 순열을 취하여 첫 번째 요소를 삽입할 수 있는 모든 방법을 반환하여 얻는다. 함수 \\(inserts\\\)는 다음과 같이 정의된다.
$$\begin{aligned}
inserts& :: a \rightarrow [a] \rightarrow [[a]]\\\\
inserts&\ x\ [] = [[x]]\\\\
inserts&\ x\ (y:ys) = (x:y:ys) : map\ (y:)\ (inserts\ x\ ys)
\end{aligned}$$
> \\(perms_{1} \\\)는 명시적인 재귀가 아닌 \\(foldr\\\)을 사용하여 정의할 수도 있다.
> \\[perms_{1} = foldr\ step\ [[]]\ \bold{where}\ step\ x\ xs\ = concatMap\ (inserts\ x)\ xs\\\]
> \\(concatMap\\\)은 \\(map\\\) 과 \\(concat\\\)의 합성 함수이다.  
> $$\begin{aligned}
> concatMap& :: (a \rightarrow [b]) \rightarrow [a] \rightarrow [b]\\\\
> concatMap&\ f = concat \cdot map\ f
> \end{aligned}$$
> 따라서 \\(perms_{1}\\)의 정의는 다음과 같이 더 간략하게 표현할 수 있다.
> \\[perms_{1} = foldr\ (concatMap \cdot inserts) [[]] \\\]

다음으로 순열을 생성하는 재귀적인 방법을 알아보자.
$$\begin{aligned}
perms_{2}&\ [] = [[]]\\\\
perms_{2}&\ xs = [x:zs\ |\ (x, ys) \leftarrow picks\ xs,\ zs \leftarrow perms_{2}\ ys]\\\\
\end{aligned}$$

\\(picks\\\) 함수는 가능한 모든 방법으로 리스트에서 임의의 요소를 선택하여 요소와 나머지 요소를 모두 반환한다
$$\begin{aligned}
picks& :: [a] \rightarrow [(a, [a])]\\\\
picks&\ = []\\\\
picks&\ (x:xs) = (x, xs) : [(y, x:ys) | (y, ys) \leftarrow picks\ x]
\end{aligned}$$
\\(perms_{2}\\\) 함수는 비어 있지 않은 리스트의 임의의 요소를 첫 번째 요소로 선택하고, 나머지 리스트의 순열을 따라 순열을 계산한다.

> \\(perms_{2}\\\)함수는 list comprehension을 사용하지만, 동등한 방법으로 
> $$\begin{aligned}
> perms_{2}&\ [] = [[]]\\\\
> perms_{2}&\ xs = concatMap\ subperms\ (picks\ xs)\\\\
> &\bold{where}\ subperms\ (x, ys) = map\ (x:)\ (perms_{2}\ ys)
> \end{aligned}$$ 
> 작성 할 수 있으며 등식 추론 및 실행 시간 분석에 도움이 된다.

순열, 분할 또는 하위 시퀀스와 같은 기본 조합 함수 정의의 재귀적 또는 귀납적인 스타일은 서로 다른 종료의 최종 알고리즘으로 이어진다. 예를 들어, 분할정복 알고리즘은 일반적으로 재귀적이지만, 탐욕 및 축소 알고리즘은 일반적으로 귀납적이다. 하나의 동일한 문제에 대해 다른 알고리즘이 있을 수 있음을 이해하려면 문제에 사용된 기본 함수의 정의로 돌아가서 이들을 다르게 정의할 수 있는지 확인해야 한다. 예를 들어 \\(perms_{1}\\\)의 귀납적 정의는 삽입 정렬로 이어지고 \\(perms_{2}\\\)의 재귀적 정의는 선택 정렬로 이어진다. 함수형 알고리즘 디자인의 핵심은 정의가 다른 하나 이상의 해결방법이 있기 때문에 문제에 대한 여러 솔루션이 생긴다는 것이다.  
함수형 프로그래밍은 임의로 긴 계산을 정의하기 위해 재귀에만 의존하지만, 명령형 프로그래밍은 다양한 종류의 루프를 사용할 수 있다. 그러나 하스켈에서도 루프를 정의하고 사용할 수 있다.
$$\begin{aligned}
until& :: (a \rightarrow Bool) \rightarrow (a \rightarrow a) \rightarrow a \rightarrow a\\\\
until&\ p\ f\ x = \bold{if}\ p\ x\ \bold{then}\ x\ \bold{else}\ until\ p\ f\ (f\ x)
\end{aligned}$$
결과가 어떤 조건을 만족할 때까지 함수를 반복적으로 값에 적용하는 함수를 재귀적으로 정의한다.  
\\(until\\\) 함수를 사용해서 \\(while\\\) 함수를 정의할 수도 있다.
\\[while\ p\ = until\ (not \cdot p)\\\]

## Fusion
효율적인 알고리즘을 구성하는 강력한 기술은 두 가지 계산을 하나의 계산으로 융합하는 능력에 있다.
$$\begin{aligned}
&map\ f \cdot map\ g\ &=&\ map\ (f \cdot g)\\\\
&concatMap\ f \cdot map\ g &=&\ concatMap\ (f \cdot g)\\\\
&foldr\ f\ e \cdot map\ g &=&\ foldr\ (f \cdot g)\ e
\end{aligned}$$
첫 번째 등식은 리스트의 모든 요소에 하나의 함수를 적용한 다음, 결과의 모든 요소에 두 번째 함수를 적용하는 2단계 과정을 두 함수의 구성이 각 요소에 적용되는 1단계 순회로 대체될 수 있음을 나타낸다. 두 번째 등식은 첫 번째 등식의 인스턴스이고, 세 번째 등식은 두 순회를 단일 순회로 대체 할 수 있는 또 다른 예이다.

# Accumulating and tupling
때로는 단순한 정의를 효율적으로 만들어야 한다. 다소 인위적인 예를 하나 들어보자. 정수 리스트의 리스트인 \\(xss\\\)가 주어지고 총합이 양수이면 xss의 가장 짧은 접두사를 연결하고 만약 합이 양수가 아니면 전체 리스트를 연결한다.
$$\begin{aligned}
collapse& [[1], [-3], [2, 4]] &=& [1]\\\\
collapse& [[-2, 1], [-3], [2, 4]] &=& [-2, 1, -3, 2, 4]\\\\
collapse& [[-2, 1], [3], [2, 4]] &=& [-2, 1, 3]\\\\
\end{aligned}$$
\\(collapse\\)를 정의하는 간단한 방법은 첫 번째 인수에 필요한 접두사를 누적하는 함수를 사용하는 것이다.
$$\begin{aligned}
&collapse :: [[Int]] \rightarrow [Int]\\\\
&collapse\ xss = help\ []\ xss\\\\
&help\ xs\ xss = \bold{if}\ sum\ xs > 0\ ||\ null\ xss\ \bold{then}\ xs\ \bold{else}\ help\ (xs ++ head\ xss)\ (tail\ xss)
\end{aligned}$$
\\(collapse\\\) 함수는 \\(help\\\) 함수의 첫 번째 인수(누적 매개변수)에서 각 리스트가 구성되므로 합은 항상 처음부터 다시 계산된다. 각각의 리스트에 합을 튜플링하는 방식으로 \\(collapse\\\)를 수정해 보자.
$$\begin{aligned}
&collapse\ xss\ &=&\ help\ (0, [])\ (labelsum\ xss)\\\\
&labelsum\ xss\ &=&\ zip\ (map\ sum\ xss)\ xss\\\\
&help\ (s, xs)\ xss &=&\ \bold{if}\ s > 0\ ||\ null\ xss\ \bold{then}\ xs\ \bold{else}\ help\ (cat\ (s, xs)\ (head\ xss))\ (tail\ xss)\\\\
&cat\ (s, xs)\ (t, ys) &=&\ (s + t, ++ ys)
\end{aligned}$$
각 리스트는 합과 쌍을 이루고 이 쌍은 계산을 통해 유지된다. 수정된 \\(help\\\) 함수의 정의에는 합을 구하는 계산이 없고 \\(labelsum\\\)에만 있다. 그러나 \\(cat\\\) 함수의 정의에는 \\(+\\\) 연산이 하나 있다. 최악의 경우, 이러한 합을 구하는 계산의 비용은 입력의 전체 길이에서 선형이지만, 이전의 정의에서는 2차 함수였다.  
\\(collapse\\\) 함수의 나머지 문제는 \\(cat\\\) 함수의 \\(++\\\) 연산이다. 연결은 왼쪽에서 오른쪽으로 수행된다. 예를 들어,
$$\begin{aligned}
collapse\ &[[-5,3], [-2], [-4], [-4, 1] = \\\\
  &((([] ++ [-5, 3]) ++ [-2]) ++ [-4]) ++ [-4, 1]
\end{aligned}$$
이것은 리스트를 연결하는 비효율적인 방법이다. 문제를 해결하는 방법은 \\(help\\\) 정의의 누적 리스트를 누적 함수로 대체하는 것이다. 

$$\begin{aligned}
&collapse\ xss &=&\ (help\ (0, id)\ (labelsum\ xss))\ []\\\\
&help\ (s, f)\ xss &=&\ \bold{if}\ s > 0\ ||\ null\ xss\  \bold{then}\ f\ \bold{else}\ help\ (s + t, f \cdot (xs\ ++))\ (tail\ xss)\ \\\\ 
&\quad&& \bold{where}\ (t, xs) =\ head\ xss
\end{aligned}$$
계산이 끝나면 누적 함수가 다음과 같이 빈 리스트에 적용된다. 
$$\begin{aligned}
collapse&\ [[-5,3], [-2], [-4], [-4, 1]]\\\\
&= (([−5, 3]++) \cdot ([−2]++) \cdot ([−4]++) \cdot ([−4, 1]++))\ []\\\\
&= [−5, 3] ++ ([−2] ++ ([−4] ++ ([−4, 1] ++ [ ])))
\end{aligned}$$
이제 연결이 오른쪽에서 왼쪽으로 진행되고 더 효율적이다. 효율적인 연결을 달성하기 위해 누적 함수를 사용하는 이러한 트릭의 요점은 튜플링인데, 관심 있는 값을 함께 튜플링하고 계산을 통해 갱신함으로써 계산을 더 효율적으로 만들 수 있다. 실제로 튜플링은 함수의 값을 저장하여 한 번 이상 계산하는 아이디어의 간단한 버전이다. 메모이제이션은 동적 프로그래밍 알고리즘에서 더 자세히 다룰 것이다. 일반적으로 항상은 아니지만, 이러한 튜플링 최적화는 코드를 어지럽히고 모호하게 만들 수 있기 때문에 효율적인 알고리즘 설계의 마지막 단계에 맡길 것이다. 성급한 최적화는 프로그래밍에서 모든 악의 근원이다. 