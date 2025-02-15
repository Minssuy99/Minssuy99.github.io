---
title: "Graph and Tree"
description: Algorithm Coding Test
author: Minssuy99
date: 2025-02-16 01:30:00 +0900
categories: [Lecture, Coding Test]
tags: [Cpp, Algorithm, Coding Test]
pin: false
math: true
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/2wBDAAIBAQIBAQICAgICAgICAwUDAwMDAwYEBAMFBwYHBwcGBwcICQsJCAgKCAcHCg0KCgsMDAwMBwkODw0MDgsMDAz/2wBDAQICAgMDAwYDAwYMCAcIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAz/wAARCAAEABQDASIAAhEBAxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAn/xAAUEAEAAAAAAAAAAAAAAAAAAAAA/8QAFgEBAQEAAAAAAAAAAAAAAAAAAAUH/8QAFBEBAAAAAAAAAAAAAAAAAAAAAP/aAAwDAQACEQMRAD8AsAAqMvAAf//Z
  # alt:
media_subpath: '/assets/img/posts/Lecture/Coding_Test'
---

<!----------------------북마크---------------------------->

## _**그래프**_
---

![image](graph.jpg)

### _**개념**_

여러 개의 노드와 간선으로 연결된 구조를 그래프 라고 한다.

### _**정점**_

정점 `vertex` 또는 노드 `node` 라고 불린다.

그래프에서 한 점을 의미한다.

### _**간선**_

정점과 정점을 연결하는 선을 간선 `edge` 라고 한다.

방향이 있는 간선을 `Directed Edge` 라고 부르며, 다른 말로 `arc` 라고 부르기도 한다.

### _**차수**_

정점에 연결된 간선의 수를 차수 `Degree` 라고 한다.

* `Out Degree` : 노드로 부터 다른 노드로 나가는 간선
* `In Degree` : 다른 노드로 부터 들어오는 간선

### _**가중치**_

간선에 할당되는 비용을 의미한다.

<br>

## _**트리**_
---

![image](tree.jpg)

### _**개념**_

부모노드와 자식노드로 이루어진 계층적인 구조를 가진 순환이 없는 무방향 그래프 자료구조이다.

간선의 수는 노드 수 `- 1` 이다.

$$
\begin{equation}  
  E = V - 1  
  \label{eq:tree_edges}  
\end{equation}  
$$

### _**루트 노드**_

트리의 최상위에 있는 노드이다.

### _**내부 노드**_

루트노드와 리프노드 사이에 있는 노드이다.

### _**리프 노드**_

자식노드가 없는 노드이다.

<br>

![image](node_height_depth.jpg)

### _**깊이**_

루트노드에서 특정 노드까지 최단거리를 깊이 `Depth` 라고 한다.

### _**높이**_

루트노드에서 리프노드까지의 거리 중 가장 긴 거리를 높이 `height` 이라고 한다.

<br>

## _**이진 트리**_
---

![image](binary_trees.jpg)

### _**개념**_

각 노드의 자식 수가 `2` 개 이하로 구성되어 있는 트리이다.

### _**정이진 트리**_

자식 노드가 `0` 또는 `2` 개인 이진트리이다.


### _**완전 이진 트리**_

왼쪽에서 부터 채워져 있는 이진트리이다.

마지막 레벨을 제외한 나머지 레벨은 노드가 모두 채워져있다.

### _**변질 이진 트리**_

모든 부모노드는 하나의 자식노드만을 가지는 이진 트리이다.

### _**포화 이진 트리**_

모든 레벨의 노드가 꽉 차 있는 이진 트리이다.

<br>

![image](balanced_tree.jpg)

### _**균형 이진 트리**_

모든 노드에 대해, 특정 노드의 좌측 서브트리와 우측 서브트리의 높이차이가 `1` 이하인 이진 트리이다.

탐색, 삽입, 삭제 등의 연산에서 시간 복잡도가 `O(log n)` 으로 보장된다.

<br>

## _**이진 탐색 트리**_
---

![image](binary_search_tree.jpg)

### _**개념**_

이진 트리의 일종으로, 모든 노드에 대해서 오른쪽 하위 트리에는 해당 노드보다 큰 값을 가지는 노드가 있고

왼쪽 하위 트리에는 해당 노드 기준으로 작은 값을 가지는 노드로 구성되어 있는 트리이다.

<br>

탐색, 삽입, 삭제, 수정 모두 `O(logN)` 의 시간복잡도를 가진다.

단, 우측 그림과 같이 선형적인 모습으로 삽입되어 있다면 `O(n)` 의 시간복잡도를 가진다.

이진 탐색 트리에서 발전된 트리로는 `AVL 트리` , `레드 블랙 트리` 가 있다.

<br>

## _**Reference**_
---

* [yoongrammer - 이진 탐색 트리](https://yoongrammer.tistory.com/71)
* [youhyeoneee.log's velog - 이진 트리와 이진 탐색 트리](https://velog.io/@youhyeoneee/%EC%9D%B4%EC%A7%84-%ED%8A%B8%EB%A6%AC%EC%99%80-%EC%9D%B4%EC%A7%84-%ED%83%90%EC%83%89-%ED%8A%B8%EB%A6%AC)
* [wikipedia - Binary Tree](https://ko.wikipedia.org/wiki/%EC%9D%B4%EC%A7%84_%ED%8A%B8%EB%A6%AC)