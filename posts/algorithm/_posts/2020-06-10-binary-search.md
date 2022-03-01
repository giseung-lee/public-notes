---

layout: post
title: 알고리즘 - 이진탐색 문제풀이 잡기술

---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %}

## 이진탐색 풀이 프레임워크

---

&nbsp;이진 탐색 문제를 풀어봤다면 대부분의 이진탐색 문제는 아래와 같은 구조로 진행 되는 걸 알수 있습니다.

```python
def binary_search(input_data):
    left = 최소값
    right = 최대값

    while(left가 right보다 작을 때):
		mid = left와 right의 중간쯤
		if is_possible(input_data, mid):
            left = mid + 1
        else:
            right = mid - 1
            
def is_possible(input_data, mid):
    ...
    문제에 따라 많이 다름
    ...
    if ...:
        return True
   	else:
        return False
```

&nbsp;left와 right, mid를 잡고 mid일때 문제의 풀이가 가능하다면 left나 right를 mid로 옮기고 계속 반복하는 구조입니다. 문제에 따라 is_possible 함수는 상이하지만 binary_search 함수 부분은 거의 모든 문제에서 동일합니다.

&nbsp;그런데 개인적으로 이진 탐색 관련 문제를 여러개 풀다보니 +-1, <=, <, >, >= 처럼 한 끗 차이로 틀리고 애먹는 경우가 많았습니다.

&nbsp;`mid = (left+right)//2` 로 잡아야 하는지 `mid = (left+right)//2 +-1`로 잡아야 하는지...  `left, right`를 옮길 때 `mid`로 잡아야 하는지, `mid+-1`로 잡아야 하는 지...  `while ( left < right )`로 해야하는지 `while ( left <= right )`로 해야 하는지... 어떤 강의에서 이런식으로 잡으라고 했는데 다른 문제에 적용하면 틀리고..

&nbsp;문제 마다 고심에 고심을 해가며 문제 조건에 맞게 디테일을 조정하며 풀고 있었습니다. 그런데 매번 이진 탐색 문제 풀 때 마다 푸는 시간의 반정도를 디테일을 조정하는데 쓰고 있자니 화가나서 정리해두었습니다.



## 이진 탐색 문제 디테일 잡기술

---

&nbsp;10문제 남짓한 이진 탐색 문제들을 분석하면서 아래와 같은 이진 탐색 문제 잡기술을 마련했습니다. 

1. while ( **left<=right** )
2. mid = **( left + right ) // 2**
3. left = **mid + 1**
4. right = **mid - 1**
5. **중요**
   1. **가능한 최댓값 찾기 : return right**
   2. **가능한 최솟값 찾기 : return left**

&nbsp;디테일을 조정할 때 위의 원칙을 따르면 되는 것 같습니다! 프로그래머스와 백준의 이진탐색 문제에서 10개 조금 안되게 테스트 했는데 다 통과했습니다.

&nbsp;물론 안 되는 문제가 있을 수도 있습니다. 😅😅