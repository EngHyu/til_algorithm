# Insertion sort
`Insertion Sort`는 작은 수의 요소를 정렬할 때 효율적인 정렬 알고리즘입니다. `Insertion Sort`는 현재 값을 key에 저장, key-1 ~ 0을 key와 비교, key 값이 더 작으면 비교 대상을 한 칸 뒤로 보냅니다. 자기랑 같거나 보다 작은 값을 만나면 순회를 멈추고 그 뒤에 key를 끼워넣습니다. 이를 코드로 옮기면 다음과 같습니다.
![pseudo_insertion_sort](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FduTFpj%2FbtqFPGOYUEl%2FQGvB3V4YhJWJPoVmWeqKT1%2Fimg.png)
```cpp
void insertionSort(int* arr, int len) {
    int key, i, j;
    for (j=1; j<len; j++) {
        key = arr[j];
        i = j-1;
        while (i > -1 && arr[i] > key) {
            arr[i+1] = arr[i];
            i--;
        }
        arr[i+1] = key;
    }
}
```
## 관련 문제
p22 Exercies 2.1-3

검색 알고리즘을 구하여라:

입력값: n개 값의 시퀀스 A = (a_1, a_2, ..., a_n)와 값 v
출력값: v = A[i]를 만족하는 인덱스 i 혹은 v가 A 안에 없을 경우 NIL

linear search에 대한 코드를 작성하세요.
```cpp
int linearSearch(int* arr, int len, int value) {
  for (int i=0; i<len; i++) {
    if (arr[i] == value)
      return i;
  }
  return -1; // for NIL
}
```
# divide-and-conquer 접근법
insertion sort에는 점진적인 접근법을 사용하였습니다: A[0]부터 A[j-1]까지의 부분 배열을 사용하여 A[j]를 알맞은 공간에 삽입해 정렬된 A를 만들었죠.
이번에는 "divide-and-conquer" 접근법을 사용하여 insertion sort보다 worst case 시간 복잡도가 작은 정렬 알고리즘을 구현해보겠습니다. divide-and-conquer의 장점은 실행 시간이 쉽게 결정된다는 것입니다.

divide-and-conquer는 다음 세 가지 과정을 거칩니다.

- Divide: 문제를 같은 작업을 수행하는 여러 개의 작은 문제로 쪼갭니다.
- Conquer: 재귀용법을 통해 작은 문제를 해결합니다. 작게 나뉜 문제가 충분히 작다면, 순차적으로 푸는 것도 가능합니다.
- Combine: 작은 문제에 대한 답을 모아 본래 문제에 대한 답을 도출합니다.

## merge sort
merge sort는 divide-and-conquer를 따르는 대표적인 알고리즘입니다.

- Divide: n개의 요소로 이루어진 시퀀스를 n/2개의 요소를 가진 두 개의 서브 시퀀스로 나눕니다.
- Conquer: 두 개의 서브 시퀀스를 merge sort를 사용해 재귀적으로 정렬합니다.
- Combine: 정렬된 두 서브 시퀀스를 합쳐 정렬된 시퀀스를 만들어냅니다.

merge sort 알고리즘의 핵심은 Combine 과정입니다. 두 개의 서브 시퀀스를 합치기 위해 MERGE(A, p, q, r)을 호출합니다. A는 배열, p, q, r은 p <= q < r인 인덱스입니다. 시퀀스 A는 A[p..q], A[q+1..r]로 나뉘어 있고 merge를 통해 A[p..r]이 된다고 가정합니다.

MERGE는 두 정렬된 서브 시퀀스의 첫 번째 요소부터 비교하며 더 작은 요소를 시퀀스에 추가하고 서브 시퀀스에선 제거합니다. 한 서브 시퀀스가 모두 비면, 다른 서브 시퀀스를 모조리 합치고 MERGE를 종료합니다. MERGER는 n = r - p + 1인 O(n)의 시간 복잡도를 가집니다. 다음은 MERGE, MERGE-SORT 코드입니다.
![pseudo_merge](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbW7zBA%2FbtqFONOOvI1%2FB4mckn4wjHagWB8VjoPudK%2Fimg.png)
![pseudo_merge_sort](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FekxcbI%2FbtqFQyJIcOi%2F7An1MXFdpEkUZTqx4hNLA0%2Fimg.png)
```cpp
void merge(int* arr, const int p, const int q, const int r) {
    int i;
    int n1 = q - p + 1;
    int L[n1+1];
    
    for (i=0; i<n1; i++) {
        L[i] = arr[p + i];
    }
    L[n1] = INF;
    
    
    int j;
    int n2 = r - q;
    int R[n2+1];
    
    for (i=0; i<n2; i++) {
        R[i] = arr[q + i + 1];
    }
    R[n2] = INF;
    
    i = 0;
    j = 0;
    for (int k=p; k<r+1; k++) {
        if (L[i] <= R[j]) {
            arr[k] = L[i];
            i++;
        }
        else {
            arr[k] = R[j];
            j++;
        }
    }
}

void mergeSort(int* arr, const int &p, const int &r) {
    if (p >= r) {
        return;
    }
    int q = (p + r) / 2;
    mergeSort(arr, p, q);
    mergeSort(arr, q+1, r);
    merge(arr, p, q, r);
}
```
알고리즘이 재귀 구조를 포함하고 있을 때, 일반적으로 점화식으로 실행 시간을 나타냅니다.

문제의 크기가 충분히 작다면, 나열하는 게 낫습니다. 코드를 나열하여 해결할 때의 시간 복잡도는 O(1)입니다.

문제를 a개의 작은 문제로 나눌 수 있고, 각각의 작은 문제가 원래 문제의 1/b의 크기를 차지한다고 할 때 시간 복잡도는 다음과 같습니다:

aO(n/b) + D(n) + C(n), D(n) to divide problem, C(n) to conquer problem

그래서 다음과 같은 식이 완성됩니다:
![general recurrence](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F4b4UY%2FbtqFOMWJaeI%2FNj5HZjbtS0CND4veagMyhk%2Fimg.png)
merge sort에서 a = b = 2이므로 식은 다음과 같이 변합니다.
![merge sort recurrence](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmBLiN%2FbtqFQybUwy7%2FNgdHhbiaPzIMQYnbnafVK0%2Fimg.png)
# 문제를 풀어보자!
[점프와 순간이동](https://programmers.co.kr/learn/courses/30/lessons/12980) - 프로그래머스 난이도 2

[배열 합치기](https://www.acmicpc.net/problem/11728) - 백준 정답 비율 44.263%