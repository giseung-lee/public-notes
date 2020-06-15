---

layout : post
title : 알고리즘 - 위상정렬(Topological Sort)

---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}

## 위상정렬 - 순서 있는 노드 방문

---

&nbsp;위상 정렬 문제는 위상 정렬을 알고 있다면 비교적 수월하게 진행 할 수 있지만(문제에서 위상 정렬을 캐치하기 쉬우므로), 위상 정렬을 모르고 있다면 꽤나 애를 먹을 수 있는 문제입니다.

&nbsp;위상 정렬은 쉽게 말해 **방향 그래프에서의 순서 있는 노드 방문**입니다. RPG게임에서 흔히 볼 수 있는 스킬 트리나 대학에서의 선수 과목과 같이 노드들 간에 순서가 있는 그래프에서 사용됩니다. 

![Topological_Sort1.png]({{ imgurl }}/Topological_Sort1.png)

&nbsp;위와 같은 학습 로드맵이 있다고 가정합시다. 과목간의 우선순위를 지키면서 위의 모든 수업을 들을 수 있는 경우의 수는 아래와 같이 다섯 가지입니다. (아래와 같이 위상정렬로 방문한 노드의 순서를 위상 순서(Topology Order)라고 합니다.)

- Java - Spring - Oracle - JPA - Spring JPA
- Java - Oracle - JPA - Spring - Spring JPA
- Java - Oracle - Spring - JPA - Spring JPA
- Oracle - Java - Spring - JPA - Spring JPA
- Oracle - Java - JPA - Spring - Spring JPA

&nbsp;이렇게 단순한 위상정렬은 많은 가짓수가 나올 수 있습니다. 쉽게 문제를 낸다면  [https://www.acmicpc.net/problem/2252](https://www.acmicpc.net/problem/2252) 문제처럼 아무 위상 순서 중 하나를 구하라고 할 수도 있지만, 대부분의 문제는 추가 조건을 붙여 여러 위상 순서 중 한 가지의 위상 순서만을 요구합니다. 

&nbsp;[https://www.acmicpc.net/problem/1766](https://www.acmicpc.net/problem/1766) 이 문제는 위상 순서들 중 노드 번호가 사전순으로 앞서는 위상 순서를 요구합니다. [https://www.acmicpc.net/problem/2056](https://www.acmicpc.net/problem/2056) 이 문제는 노드를 하나의 작업으로 보고, 작업에 걸리는 시간을 배정했습니다. 공정의 공기를 계산하듯이 선행 관계가 없는 작업들은 동시에 진행할 수 있을 때, 최단 시간이 걸리는 위상 순서를 구하는 문제입니다.(구현이 점점 까다로워집니다.. 😥😥)



## 작동 방식

---

&nbsp;위상 정렬 문제는 문제의 세부 조건에 따라 구현 로직이 꽤 크게 달라집니다. 하지만 모든 위상 정렬의 세부 로직은 아래와 같은 큰 로직 안에서 이루어집니다.

- 각 노드 별로 진입 차수(선행 해야 하는 노드 수)를 구합니다.
- 모든 노드를 방문할 때 까지 반복합니다.
  - 진입 차수가 0인 노드(선행 할 것이 없는 노드)를 방문합니다.
  - 방문한 노드를 선행 노드로 가지고 있는 노드들의 진입 차수를 1 깎습니다.

&nbsp;위의 예시를 보며 설명드리겠습니다.

![Topological_Sort2.png]({{ imgurl }}/Topological_Sort2.png)

&nbsp;각 노드의 초기 진입 차수를 기록하고 진입차수가 0인 Java부터 시작합니다.

![Topological_Sort3.png]({{ imgurl }}/Topological_Sort3.png)

&nbsp;Java를 선행 과목으로 가지고 있던 JPA와 Spring의 진입 차수를 감소 시키고 다음 진입차수가 0인 Oracle을 방문합니다.(물론, Spring 부터 방문할 수도 있습니다.)

![Topological_Sort4.png]({{ imgurl }}/Topological_Sort4.png)

&nbsp;Oracle을 선행 과목으로 가지고 있던 JPA와 Spring JPA의 진입차수를 감소시킵니다. 다음으론 JPA를 방문합니다.

![Topological_Sort5.png]({{ imgurl }}/Topological_Sort5.png)

&nbsp;Spring JPA의 진입차수를 감소시키고 Spring에 방문합니다.

![Topological_Sort6.png]({{ imgurl }}/Topological_Sort6.png)

&nbsp;마지막으로 Spring JPA에 방문합니다.

![Topological_Sort7.png]({{ imgurl }}/Topological_Sort7.png)

&nbsp;우선 순위를 지키며 모든 노드에 방문했습니다.



## 예제1 - DFS를 이용한 구현

---

&nbsp;위상 정렬의 경우 앞서 말했다싶이 문제의 세부 조건에 따라 다양하게 구현이 가능합니다. 

&nbsp;가장 간단한 방법으론 DFS를 이용하는 것입니다. 앞서 말한 기본 동작과정과는 조금 다른 것 같지만 본질적으론 동일합니다. [https://www.cs.usfca.edu/~galles/visualization/TopoSortDFS.html](https://www.cs.usfca.edu/~galles/visualization/TopoSortDFS.html) 를 보시면 DFS를 이용한 동작 과정을 애니메이션으로 볼 수 있습니다.  가장 간단한 방법인 DFS는 진입 차수를 크게 활용하지도 않습니다.(아예 없이 수행할 수도 있습니다.)

&nbsp;진입 차수가 0인 노드부터 DFS를 수행 하되, 해당 노드에 연결된 후행 노드부터 방문하고 후행 노드들을 모두 방문한 뒤에 해당 노드를 방문합니다. 위의 예시를 보시겠습니다. 

```
5 6
Java Oracle Spring JPA SpringJPA
Oracle SpringJPA
Java JPA
Spring SpringJPA
Oracle JPA
Java Spring
JPA SpringJPA
```

&nbsp;위와 같은 입력을 줍니다. 첫 줄의 n, m은 노드 수, 간선 수입니다. 다음 줄은 노드 n개의 이름입니다. 다음부터 m개의 줄은 {선행, 후행} 노드입니다.

```python
# 노드 갯수, 간선 갯수 입력
n, m = map(int, input().split())
# 노드 입력
subjects = list(map(str, input().split()))

# 진입 차수 기록 dictionary
indegree = {};
# 간선 기록 dictionary
edges = {};
# 진입 차수 및 간선 초기화
for subject in subjects:
    indegree[subject] = 0
    edges[subject] = []

# 간선 입력
for _ in range(m):
    before, after = map(str, input().split())
    indegree[after] += 1;
    edges[before].append(after)
```

&nbsp;데이터를 입력받고 진입차수와 간선을 초기화하고, 간선을 입력 받습니다.

```python
# dfs
def dfs(edges, visited, result, key):
    if not visited[key]:
        # 연결된 후행 노드부터 방문
        for next_ in edges[key]:
            if not visited[next_]:
                dfs(edges, visited, result, next_)
        # 후행 노드를 모드 방문 후 현재 노드 기록
        visited[key] = True;
        result.append(key)
```

&nbsp;DFS는 위와 같은 재귀함수를 이용해 수행합니다. 후행 노드들로 먼저 DFS 재귀 함수를 호출하고, 모든 후행 노드들의 DFS가 끝나면 현재 노드를 위상 순서를 담을 result 배열에 추가합니다.

```python
# 방문 노드 기록할 딕셔너리(보통은 배열을 사용하나 노드를 index가 아니라 String으로 받는 바람에..)
visited = {}
for subject in subjects:
    visited[subject] = False
    
# 결과를 담을 배열
result = []

# 진입 차수가 0인 노드를 넣어 DFS 수행
for key, value in indegree.items():
    if value == 0:
        dfs(edges, visited, result, key)
```

&nbsp;방문 노드를 기록할 딕셔너리를 만듭니다.(보통 탐색에선 배열을 쓰는데 예시에서 노드를 index번호가 아니라 String으로 써버려서...) 결과를 담을 result 배열을 선언해주고 진입 차수가 0인 노드들을 넣어 DFS를 수행해줍니다.

```python
# 결과 출력
result.reverse()
print(result)

# >> ['Oracle', 'Java', 'Spring', 'JPA', 'SpringJPA']
```

&nbsp;result 배열엔 가장 깊은 depth부터 들어가 있습니다. 뒤집어주면 위상 순서를 확인할 수 있습니다.



## 예제2 - 진입차수 배열 이용

---

&nbsp;이번엔 처음 살펴본 동작 과정과 더 닮아 있는 구현입니다. 진입차수 배열과 다음에 방문할 노드를 관리할 큐를 사용하는 방법입니다. 

```python
# 노드 갯수, 간선 갯수 입력
n, m = map(int, input().split())
# 노드 입력
subjects = list(map(str, input().split()))

# 진입 차수 기록 dictionary
indegree = {};
# 간선 기록 dictionary
edges = {};
# 진입 차수 및 간선 초기화
for subject in subjects:
    indegree[subject] = 0
    edges[subject] = []
```

&nbsp;똑같이 입력을 받습니다.

```python
# 진입 차수 0인 노드를 관리할 큐
from queue import deque
indegree_zero_queue = deque()
# 큐에 초기 진입차수 0짜리 입력
for key, value in indegree.items():
    if value == 0:
        indegree_zero_queue.append(key)
```

&nbsp;방문할 노드를 관리할 큐를 초기화합니다. 해당 큐엔 진입차수가 0인 노드만 들어갈 것입니다.

```python
# 진입차수와 큐를 이용한 위상정렬 수행
result = []
while indegree_zero_queue:
    # 현재 노드를 위상 순서에 추가
    now = indegree_zero_queue.popleft()
    result.append(now)
    # 현재 노드의 후행 노드를 탐색
    for next_ in edges[now]:
        # 진입 차수를 1 감소 시키고, 감소된 진입 차수가 0이면 큐에 삽입
        indegree[next_] -= 1
        if indegree[next_] == 0:
            indegree_zero_queue.append(next_)
```

&nbsp;큐에 남은 노드가 없을 때까지(더 이상 방문해야할 노드가 없을 때까지) while문을 수행합니다. 노드 하나를 꺼내 해당 노드를 위상 순서에 추가합니다. 그 후, 해당 노드의 후행 노드들의 진입 차수를 깎고, 깎인 결과 진입차수가 0이 되었으면 방문예정 큐에 추가합니다.

```python
print(result)
# >> ['Java', 'Oracle', 'Spring', 'JPA', 'SpringJPA']
```

&nbsp;결과를 출력합니다. 예제1과는 다른 순서가 나왔지만, 그래도 위상 순서 중 하나입니다.

&nbsp;DFS를 이용한 방법은 재귀적으로 DFS를 구현하기만 하면 되기 때문에 구현이 쉽습니다. 하지만 다양한 문제의 조건에 따라 변형하기엔 힘듭니다. 진입 차수 배열을 이용하는 방법이 문제의 다양한 조건에 대응하기 쉬운 것 같습니다.