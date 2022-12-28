+++
title = "윈도우에서 쉘코드 분석"
description = "first article"
date = 2022-10-22
aliases = ["/posts/analysis_of_shellcode_under_windows.html"]
draft = true
[taxonomies]
tags = ["쉘코드", "취약점"]
[extra]
banner = "hello.jpg"
+++

## 쉘코드란..
1996년 Aleph One이 Underground에 투고한 논문 [Smashing The Stack For Fun And Profit](https://inst.eecs.berkeley.edu/~cs161/fa08/papers/stack_smashing.pdf)은 리눅스 시스템에서 스택의 구조와 스택 기반 버퍼 오버플로우를 사용하는 방법에 대해 자세하게 설명하는데, 쉘을 얻기 위해 프로세스 코드 조각을 이식하는 방법을 보여주고 이식된 프로세스를 '쉘코드'라고 부른다.  
나중에 사람들은 버퍼 오버플로우 공격 과정에서 삽입된 코드를 지칭하기 위해서 특수한 용어인 쉘코드를 단순히 일률적으로 사용했는데, 이 코드는 장난 목적으로 메시지 박스를 표시하거나 공격 목적으로 중요한 파일을 삭제 또는 수정하거나 데이터를 훔치거나 트로이 목마를 업로드 혹은 실행하거나 파괴적인 목적으로 데이터를 포맷할 수 있다.  
사실 쉘코드의 적용 범위도 이제 매우 넓어지고 일부 원격 제어 프로그램도 스스로를 쉘코드 형태로 만들 수 있다. 이것이 오버플로우 공격 이후에 악의적인 행위를 하는 코드 조각이라는 것만 이해하면 된다. (여기서 언급하는 쉘코드 모두 오버플로우와 관련된 쉘코드 이다.)
 
<!-- more -->