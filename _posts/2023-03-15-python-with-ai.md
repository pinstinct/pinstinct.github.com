---
layout: post
title: 이제 시작이야 컴퓨팅 사고력으로 인공지능까지 파이썬
categories: book
tags: [book, python, ai]
excerpt: 책을 읽고 정리한 내용입니다.
---

[이제 시작이야! 컴퓨팅 사고력으로 인공지능까지 파이썬](http://www.yes24.com/Product/Goods/106540957) 책을 읽고 정리한 내용입니다.


## 컴퓨팅 사고력

- 지네트 윙 교수는 컴퓨팅 사고력을 크게 '추상화(abstraction)'와 '자동화(automation)'의 두 가지 과정으로 언급하였다. 추상화란 복잡한 문제의 공통적이고 핵심적인 부분을 인식하여 통합할 수 있는 사고능력이다. 그리고, 자동화란 반복되는 일을 자동으로 처리되도록 하는 것이다. 추상화란 실세계의 복잡한 상황이나 문제를 해결하기 위한 형태로 단순하게 표현하는 사고과정을 의미한다. 컴퓨터를 이용하여 문제를 자동화하기 위해서는 문제를 단순화해야 한다. 자동화란 추상화 과정에서 만들어진 알고리즘을 바탕으로 프로그래밍 언어로 표현함으로써 반복되는 작업을 자동으로 처리되도록 하는 것이다.

- 추상화
  - 문제분해(problem decomposition): 복잡한 과정이나 문제 상황을 처리 가능한 작은 문제로 분해한다. 실세계에서 발생하는 문제들은 일반적으로 매우 복잡하다. 따라서 복잡한 문제를 작은 크기의 문제로 분해해야 보다 효율적으로 해결할 수 있다.
    - 문제해결과정의 특징을 보면 순차적이고 절차적이라는 것을 알 수 있다. 이렇게 순차적이고 절차적인 문제들이 복잡하게 얽혀서 나타날 때, 문제분해를 통해서 구조화하고 각각의 부분 문제들을 해결한다면 복잡하고 큰 문제들도 효율적으로 해결할 수 있게 된다.
  - 패턴인식(pattern recognition): 부문 문제들 중에서 규칙이나 패턴을 찾는다.
    - 패턴을 찾는 방법: 박복되는 규칙성 찾기, 대상 간의 공통점 혹은 유사성 찾기
  - 좁은 의미의 추상화(abstraction): 문제 해결에 관련 없는 세부사항은 제외하고 핵심적이고 중요한 정보를 추출하여 단순하게 표현한다.
    - 컴퓨터에서 추상화는 자동화하기 위한 필수 요소를 찾아내야 한다.
    - 추상화의 종류는 절차 추상화, 데이터 추상화, 제어 추상화가 있다. 절차 추상화는 프로그램의 대략적인 수행 흐름을 설계하는 것이다. 데이터 추상화는 구현 시 변수, 리스트, 배열 등의 데이터 구조로 표현될 데이터를 설계하는 것이다. 제어 추상화는 특정 조건이나 규칙에 따른 분기를 계획하고 설계하는 것이다.
  - 알고리즘(algorithms): 문제 해결과정을 프로그래밍의 구조에 의해 구조적으로 표현한다. 알고리즘은 어떤 문제를 해결하기 위한 절차나 방법을 의미한다. 알고리즘을 작성할 때 우리가 중요하게 생각해야 하는 것은 순서대로 자세하게 표현해야 한다는 것이다.
    - 알고리즘은 다양할 수 있으나 효율적이어야 한다. 효율성을 확인하기 위해 시간복잡도와 공간복잡도를 사용한다. 최근에는 주기억장치인 RAM의 용량이 커졌기 때문에 공간복잡도의 중요성이 상대적으로 줄어들고 있다.
    - 알고리즘을 표현하는 방법에는 자연어, 순서도, 의사코드, 프로그래밍 언어가 있다.
    - 알고리즘을 작성하기 위한 구조는 순차, 선택, 반복의 세 가지 구조가 있다.
- 자동화: 설계된 알고리즘을 바탕으로 프로그래밍의 언어로 구현한다.

- 기술적 숙달을 목적으로 하는 것이 아니라 문제를 컴퓨팅으로 해결하는 과정을 컴퓨팅 사고력이라고 할 수 있다.

- 컴퓨터 과학에서 문제는 해결해야 하는 과제를 말하고, 목표 상태와 현재 상태의 차이를 일컫는다. 이 차이를 해결하는 과정이 바로 문제 해결이다. 


## 컴퓨터과학의 기초

- 기계어는 명령어를 0과 1로 표현한다. 이는 컴퓨터의 CPU가 명령을 처리할 때 사용하는 언어로 이진수로 되어 있다.
- 이러한 기계어를 사람의 언어로 알아보기 쉽게 표현하는 언어가 어셈블리어(assembly langauge)이다. 어셈블리어는 기계어와 1대 1로 대응한다.
- 컴파일은 C언어, 파스칼 등으로 구현된 프로그램 코드를 어셈블리 코드 혹은 기계어 등 다른 언어로 변환해 주는 프로그램을 지칭한다.

- 아스키 코드(ASCII code)는 문자를 디지털로 표현하기 위해 사용한다. 한 글자에 대해 8비트를 할당하여 영어 대문자, 소문자, 숫자, 기호 등을 8비트 숫자들과 매칭해서 나타낸 코드들이다. 그러나 아스키 코드는 128자만 표현할 수 있으므로 한자나 한글과 같은 언어를 다 표현할 수 없다는 문제점이 있다. 따라서 아스키코드로는 다 표현할 수 없는 세계의 다양한 언어를 표현하기 위해 한 글자를 16비트(2바이트)로 표현한 유니코드(unicode)가 등장했다.

- 이미지를 디지털로 표현하는 방식에는 비트맵 방식과 벡터 방식이 있다. 비트맵 방식은 픽셀들이 모여 이미지를 구성하는 형태로 이미지를 확대할 경우 픽셀이 흐려지는 계단 현상(엘리어싱 효과)이 일어날 수 있다. 반면, 벡터 방식은 이미지에 필요한 선의 길이, 색상, 좌표 값 등을 수식으로 표현하는 방식으로 이미지를 나타낼 수 있다.
- 해상도는 하나의 이미지를 몇 개의 픽셀로 표현했는가에 따라서 달라진다. 즉, 정해진 규격 안에 픽셀이 많을수록 화질은 좋아진다.

- 아날로그 데이터를 컴퓨터는 숫자로 바꿔야하기 때문에 샘플링을 거쳐 디지털 데이터로 만든다. 샘플링을 할 때 잡음에 영향을 주는 샘플링 비율(sampling rate)과 음의 섬세함에 영향을 주는 샘플링 사이즈(sampling size/bit rate)에 따라 음질의 차이가 난다.
- 샘플링 비율은 음파를 1초에 몇 번 측정해서 데이터화 했는가에 의해 결정된다. 보통 44.1kHz 정도면 CD음질 정도다. 
- 샘플링 사이즈는 측정된 소리의 전체 규모 및 최대값을 의미한다. 16bit 정도면 CD음질 정도다.

- 함수는 가장 작은 단위의 프로그램으로 정의된다.

- 자료구조(data structure)란 컴퓨터에서 처리하고자 하는 데이터를 효율적으로 관리하기 위해 다양한 형태로 구조화시키는 것이다. 컴퓨터에서도 유동적이고 수많은 데이터를 상황에 따라 스택, 큐, 리스트, 그래프, 트리 등의 다양한 형태로 구조화 한다.
  - 배열(array)은 같은 유형의 변수들로 이루어진 집합이다. 배열은 메모리 공간에 연속적으로 저장되기 때문에 배열의 기준 주소를 알면 배열의 모든 항목을 찾을 수 있다. 배열은 한번 생성되면 변경하기가 비교적 어렵지만 구조가 간단하고 성능이 우수하기 때문에 많이 사용한다.
  - 리스트(list)는 항목들을 순서대로 저장한 선형구조로 되어 있다. 데이터를 추가하거나 삭제할 경우 메모리의 빈 공간을 허용하지 않으므로 배열보다 더 효율적인 메모리 활용이 가능하다.
  - 그래프(graph)는 노드(node)와 그 노드를 간선(edge)으로 연결한 구조이다. 따라서 연결되어 있는 객체 간의 관계를 표현할 수 있다. 실행활에서 지도, 지하철 노선도의 최단 경로, 도로 연결 등의 상황을 그래프로 표현할 수 있다.
  - 트리(tree)는 그래프의 한 종류로 노드와 간선으로 이루어진 자료구조이지만, 그래프와 다르게 트리는 하나의 루트 노드를 갖는다. 그리고 이 루트 노드는 0개 이상의 자식 노드를 가지며 그 자식 노드도 0개 이상의 자식 노드를 가지며 반복적으로 정의된다. 또한, 그래프와 다르게 사이클(cycle)이 없는 하나의 연결 그래프(connected graph)라고 할 수 있다.
  - 최소신상트리(minimum spanning tree)란 간선에 가중치를 고려하여 최소 비용을 선택하는 것을 말한다. 통신망, 경로 설정, 도로망, 유통망에서의 길이, 구축 비용, 전송시간 등을 최소로 구축하려는 경우에 적용할 수 있다. 최소신상트리는 다음과 같은 특징을 가진다.
    - 간선을 연결할 때 사이클이 생겨서는 안 된다.
    - 간선 위에 있는 가중치들의 합은 최소가 되어야 한다.
    - n개의 노드를 가지는 그래프에서 간선의 개수는 반드시 (n-1)개이어야 한다.
  - 탐욕적 방법(greedy method)을 이용하면 최소신장트리를 구현할 수 있다. 탐욕적 방법은 결정을 해야 할 때마다 그 순간에 가장 좋다고 생각되는 것을 선택하여 최종적인 해답에 도달하는 방법으로 모든 노드를 최소 비용으로 연결하여 최적 해답을 구하는 것이다.
  - 크루스칼(kruskal) 알고리즘은 무조건 최소 간선만을 선택하는 방법으로 최소신장트리를 구현한다.
    - 그래프의 간선들을 가중치의 오름차순으로 정렬한다.
    - 정렬된 간선 리스트에서 가장 낮은 가중치를 먼저 선택한다. (사이클을 형성하는 간선은 제외한다.)
    - n개의 노드를 가지는 그래프에서 간선의 개수가 (n-1)개가 될 때까지 연결한다.
    - 해당 간선을 현재의 최소 신장 트리에 표시하고 해당 가중치를 모두 더한다.

- 정렬이란 일정한 순서로 데이터나 요소들을 나열하는 알고리즘이다.
  - 버블정렬(bubble sort)은 인접한 두 개의 데이터의 크기를 비교하여 큰 수를 뒤쪽으로 가도록 위치를 바꾸어가며 정렬하는 알고리즘이다. 이러한 방법은 가장 간단하지만 비효율적인 편인다.
  - 선택정렬(selection sort)은 첫 번째 자리에 가장 작은 데이터를 넣고 두 번째 자리에 그 다음 가장 작은 데이터를 선택해서 넣는 방식을 배열이 끝날 때까지 반복한다.
  - 삽입정렬(insert sort)은 배열의 한 원소인 key라는 값을 알맞은 자리에 삽입하는 방식이다. 
  - 퀵정렬(quick sort)은 전체 리스트를 2개 부분으로 나누고 각각의 부분 리스트를 다시 퀵 정렬하는 분할-정복방법을 사용한다. 피벗(pivot)이라고 부르는 리스트의 기준 값보다 작은 데이터는 모두 피벗의 왼쪽으로 옮기고 피벗보다 큰 데이터는 모두 오른쪽으로 옮긴다. 결과적으로 피벗을 중심으로 왼쪽에 존재하는 데이터들은 피벗보다 작은 값들이고 오른쪽은 비벗보다 큰 값들로 구성된다. 이 상태에서 피벗을 제외한 왼쪽 리스트와 오른쪽 리스트를 각각 다시 같은 방식으로 정렬하게 되면 전체 리스트가 정렬된다.

- 순차 탐색(sequential search)은 선형 탐색(linear search)이라고 부르며 데이터 배열에서 처음부터 끝까지 차례대로 비교하여 원하는 데이터를 찾아내는 알고리즘이다. 이 순차 탐색은 단순하지만 비효율적이라는 단점을 지니고 있다.
- 이진 탐색(binary search)은 순서대로 데이터가 정렬된 배열을 전제로 하며 한번 비교를 거칠 때마다 탐색 범위가 반(1/2)으로 줄어들기 때문에 이진탐색이라고 부른다. 기본적으로 배열이 순서대로 정렬되어 있어야 한다.

## 인공지능

- 지능이란 외부 현상을 보고 듣고 느끼는 등의 오감을 통해 **인식**한 것을 바탕으로 **추론**하며 **행동**할 수 있는 능력을 의미한다.

- 머신러닝은 컴퓨터가 데이터로부터 스스로 규칙을 만들어 내며 학습하는 방식이다. 또한, 딥러닝은 머신 러닝의 다양한 분야 중 한 분야로써 연속된 층을 통해 학습하는 방식인 초기 인공신경망을 기반으로 주어진 데이터를 스스로 학습한다.

- 2006년 제프리 힌터는 가중치의 초기값을 제대로만 설정하면 깊은 신경망 학습도 가능하다는 것을 증명했다. 즉, 신경망을 학습시키기 전에 적은 층으로 학습을 먼저 해서 더 나은 초기값을 얻었는데, 이것을 사전학습이라고 하고 이것을 본 신경망 학습에 투입시키는 것이다. 이때부터는 딥러닝이라는 용어를 사용하게 된다.

- 인공지능의 접근 방법은 인간의 지식 생성 과정을 적용한 '지식 기반 방법'과 지능 생성의 원리 기반의 '학습(데이터) 기반 방법'이 있다. 즉 지식 기반 방법은 인간 전문가의 지식을 명시적인 지식과 규칙으로 작성하여 이를 토대로 추론하는 방식인 전문가 시스템 등의 지능형 시스템을 구현하는 방식이다. 반면, 데이터 기반 방법은 인간이 다양한 경험에 의해 학습하듯이 수많은 데이터를 입력시켜 컴퓨터가 스스로 학습하도록 함으로써 지식과 모델을 찾아내어 인공지능 시스템을 구현하는 방식이다.
  - 지식 기반 인공지능: 비교적 정확한 분류 및 예측이 가능하다는 장점이 있으나, 전문가가 모든 상황의 규칙을 다 제시하기 어렵다는 점과 여러 상황에 의해 규칙이 바뀔 경우 새롭게 알고리즘을 수정해야하는 과정을 반복해야 하는 단점이 있다.
  - 데이터 기반 인공지능: 인간이 가진 두뇌와 최대한 비슷하게 모방하는 것에서 출발하자는 연결주의로부터 시작된다. 신경망의 기본 구조와 작동원리를 모방한 것이 인공신경망의 시작이었다. 데이터 기반 인공지능은 해당 알고리즘에 많은 정보(빅데이터)를 제시하여 훈련(training, 데이터를 예측하거나 분류하기 위한 특징이나 주요 요소를 찾아 모델링 하는 과정)시키고, 일부 데이터로 테스트(test, 학습에 사용한 기존 데이터가 아닌 새로운 데이터를 제시하여 인공지능의 성능을 확인)한 결과가 높은 정확도를 가진다면 사용 가능하게 된다. 또한, 사용된 데이터 예측 결과는 다시 업데이트됨으로써 인공지능의 성능이 더 좋아지게 된다. 데이터 기반의 인공지능은 계속적으로 업데이트 되는 데이터를 통한 학습으로 인해 규칙의 변화에 민감하게 반응할 수 있다. 즉, 별도의 추가 작업 없이 새로운 데이터의 변화에도 새로운 데이터를 인식할 수 있다. 다만, 학습 결과의 정확도를 높이기 위해서는 충분한 데이터가 요구된다. 이런 학습 기반 인공지능 기술은 현재의 머신러닝과 딥러닝 기술의 흐름으로 이어졌다.

  - 인공지능에서의 입력은 다양한 입력장치나 센서를 이용하여 수집된 이미지, 측정 데이터, 글자, 비디오 등의 정제된 데이터의 모음인 데이터 셋(data set)을 통해 컴퓨터에 전달된다. 또한, 인공지능의 처리과정은 다양한 인공지능 학습 알고리즘을 통한 모델링 과정이라고 할 수 있다. 마지막으로 출력은 기존의 전통적인 프로그램이 방식에서 제공한 정확한 정답이나 결정된 산출물이기보다는 결과에 대한 예측값이라고 할 수 있다. 
  - 입력되는 데이터셋이 누구에 의해 수집된 데이터이며, 어떻게 수집된 데이터냐에 따라서 다른 결과를 예측하여 제공해 주게 된다. 따라서 제공되는 데이터셋은 인공지능 알고리즘의 성능과 공정성 등에서 매우 중요한 역할을 한다.
  - 편향 데이터란 어떤 것은 선호하고 다른 것에 우선 순위를 둔 데이터이거나 일부가 배제된 데이터를 의미한다. 즉, 데이터를 어떻게 수집하였는지, 누가 수집하였는지, 어떻게 들어온 데이터인지에 따라 데이터가 변질될 수 있으며 그 결과는 편향된 예측을 가져올 수 있다.
    - 상호작용 편향(confirmation bias): 사람들의 상호작용에 의해 만들어질 때 누락될 수 있는 부분으로써 무의식적으로 자신의 믿음과 일치하는 방향으로 수집하여 데이터 처리과정에 영향을 미친다. 예를 들어, 신발이라는 것을 그림으로 표현할 경우, 대부분의 사람들이 하이힐은 그리지 않기 때문에 하이힐을 제시했을 경우 신발이라고 인식하지 못 할 수 있다.
    - 잠재적 편향(automation bias): 어쩔 수 없는 상황에서의 데이터, 직업과 성별 연결 편향을 의미한다. 이러한 데이터는 실제 현실을 반영하지만 부정적 편향을 강화할 수 있다. 예를 들어, 과학자나 의사 등의 일부 직업에 실제로 남성이 많은 경우, 해당 직업에 대한 데이터가 남성을 가르킬 경우가 많을 수 있다.
    - 선택 편향(selection bias): 조사 대상으로 선정된 표본이 대표성이 결여되어 생기는 편향을 의미한다. 예를 들어 특정 지역에서 데이터를 수집한 경우 그 인공지능 시스템은 해당 지역에 대한 문화, 음식, 학력 등의 편향이 존재할 수 있다. 예를 들어, 대한민국에서만 식생활에 대한 데이털르 생성하고 모델을 만든다면 이는 대한민국에 대한 편향이 발생할 것이다.

- 머신러닝
  - 지도 학습(supervised learning): 사람이 알고 있는 지식에 기반을 두어 각 데이터에 레이블(lable), 즉 정답을 알려주며 컴퓨터를 학습시키는 방식이다. 지도 학습은 데이터와 과제 유형에 따라 예측을 위한 회귀와 두 레이블의 구분을 위한 분류의 두 가지 방식으로 나누어 볼 수 있다.
    - 분류(classification): 각각의 이미지에 레이블(정답)을 붙여 학습시킨다. 이때 사용하는 데이터 셋을 훈련용 셋(training set)이라고 한다. 이러헥 훈련된 컴퓨터에게 이젠 기존에 훈련용 셋에서 사용하지 않았던 새로운 이미지(테스트 셋, test set)를 입력하여 그 성능을 테스트해볼 수 있다. 테스트 셋에 의해 정확도를 확인해 볼 수 있으며, 이때 원하는 만큼의 정확도를 나타내지 않는다면 더 많은 훈련이 필요하게 될 것이다.
    - 회귀(regression): 회귀는 연속된 숫자 값에서 해당 데이터가 어느 숫자 값을 가질지에 대해 예측하기 위해 사용한다. 예를 들어 특정 물건의 광고 노출 횟수가 많을수록 판매량이 증가한다고 했을 때, 광고 횟수를 3배 늘렸을 경우 판매량이 얼마나 늘어날지 예측할 수 있도록 하는 것이다.
  - 비지도 학습(unsupervised learning): 주어딘 데이터에 대해 레이블(정답)을 제공하지 않고 학습시키는 방식을 의미한다. 따라서 인공지능이 스스로가 데이터 간의 패턴을 찾아 분류 요소를 찾고 비슷한 유형의 데이터끼리 묶이도록 하는 방법이다. 비지도 학습은 데이터를 비슷한 특징을 가진 자료끼리 묶는 군집화(clustering)을 가능하게 한다. 분류는 데이터를 일정한 기준에 따라 직선으로 분류하는 것이지만, 클러스터링은 유사성에 따라 몇 개의 몇 개의 그룹으로 묶는 것을 말한다. 이런 비지도 학습의 클러스터링 방식은 사람이 분류기준을 명확히 제시하기 어려운 목표 마케팅, 추천 시스템, 데이터 마이닝, 고객 세분화 등의 분야에 적용할 수 있다. 가장 대표적인 클러스터링 방식은 K-means로 이것은 주어진 데이터를 사용자가 지정한 K개의 군집으로 나누는 방법을 의미한다. 군집을 나누는 방법은 각 군집에 포인트를 할당하고 반복적인 계산을 통해 각 포인트들의 거리나 밀도를 계산하여 평균 좌표 값들의 중심을 찾는 방식이다. 
  - 강화 학습(reinforcement learning): 주어진 입력에 대한 출력, 즉 정답 행동이 주어지지 않는다. 대신 일련의 행동의 결과에 대해 보상(reward)이 주어지게 되며, 시스템은 이러한 보상을 이요해 학습을 실행한다. 게임, 알파고, 로봇 관절제어 등에 적용한다.

  - 알고리즘은 무엇에 최적화 되었는지에 따라서 그 목적이 달라진다. 특정인을 위한 알고리즘은 때때로 다른 사람들에게는 유용하지 않을 수 있다. 다양한 이해관계자를 고려하거나 영향력이 가장 큰 이해관계자를 고려한 알고리즘을 개발해야 할 수도 있다.