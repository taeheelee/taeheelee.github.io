---
layout: post
title: 알고리즘 - 시간&공간 복잡도
date: '2021-05-09'
subtitle: 한정된 자원(시간과 메모리)을 효율적으로 써먹자
---

개인적인 소망 중 하나로 컴퓨터 성능이 엄청엄청 좋아져서 알고리즘이 중요하지 않은 세상이 왔으면 했다.
"컴퓨터 성능이 좋아졌으니 알고리즘 공부를 대충해도 되지 않을까?"

그런데 아쉽게도 가면 갈수록 알고리즘의 중요성이 더 높아졌다.
빅데이터, 인공지능이 떠오르면서 처리해야 할 정보의 양이 증가했기 때문인 것 같다.
처리할 정보의 양이 적을 때에는 별 차이가 없지만, 양이 많을 때는 아래처럼 처리 시간이 엄청 크게 늘어난다.

> 입력 대비 처리시간
알고리즘 A는 n^2의 처리시간을 필요로 하고,
알고리즘 B는 2^n의 처리시간을 필요로 할 때 처리시간은 아래처럼 <u>기하급수적</u>으로 증가한다.
n=1일 때, n^2 = 1초, 2^n = 2초
n=10일 때, n^2 = 100초, 2^n = 1,024초
n=100일 때, n^2 = 10,000초, 2^n = 1,267,650,600,228,229,401,496,703,205,376초


그러면 알고리즘은 어떻게 좋고 나쁨을 판단할 수 있을까.
그 기준은 시간 복잡도, 공간 복잡도이다.

시간 복잡도(time complexity)는 알고리즘의 실행 시간이 얼마나 걸리는지를 나타내고
공간 복잡도(space complexity)는 알고리즘의 메모리 사용량이 어느정도인지를 나타낸다.

알고리즘 문제를 풀다보면 제한시간 얼마에 제공하는 메모리 양은 얼마라고 알려주는 경우가 있는데,
앞서 말했듯이 알고리즘을 판단하는 기준이 <u>시간과 공간</u>이기 때문이다.

시간 복잡도에 대해 이야기를 하면 컴퓨터가 연산하는 횟수는 알고리즘의 종류에 따라, 그리고 입력 n에 따라 유동적이다.
즉 컴퓨터 연산의 횟수는 n의 함수이고, T(n)으로 나타낼 수 있다.

T(n) = n^2 + n + 1 이라는 시간 복잡도 함수가 있다고 할 때
n=1,000일 때 두 번째 항 n이 전체에서 차지하는 비율은 고작 **0.1%**다.
따라서, 보통 시간 복잡도 함수를 고려할 때는 최고차항만을 고려한다.
**빅오 표기법**은 T(n)의 두 번째, 세 번째 항처럼 불필요한 정보를 빼고, 알고리즘 비교를 쉽게 하기 위해 시간 복잡도를 표시하는 방법이다.


일반적으로 알고리즘을 평가할 때는 아래의 기본 가정을 따른다.

기본가정
1. 알고리즘의 효율성은 최악의 경우를 기본으로 한다.
    최악의 수행시간은 해당 알고리즘의 수행시간에 대한 상한이 되기에, 그보다 오래걸리지 않음을 확신할 수 있다.
2. 최악의 경우가 빈번하게 나타나는 알고리즘이 있다.
    ex) 검색알고리즘에서 검색 결과가 없는 경우 = 탐색을 끝까지 해야한다.
3. '평균적인 경우'가 최악의 경우만큼 안좋은 상황일 때가 있다
    ex) n개의 숫자를 골라 삽입정렬을 하는 케이스
4. 각각의 연산은 동일한 시간 내에 수행된다.
    ex) 덧셈, 뺄셈, 나눗셈 등


빅오 표기법 외에도 빅오메가, 빅세타 표기법 등이 있지만 일반적으로 빅오 표기법을 사용하므로 이쯤에서 간략하게 정리를 한다.
