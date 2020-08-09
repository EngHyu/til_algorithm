# 다리를 지나는 트럭
[문제 from 프로그래머스](https://programmers.co.kr/learn/courses/30/lessons/42583)

## 문제 설명
트럭 여러 대가 강을 가로지르는 일 차선 다리를 정해진 순으로 건너려 합니다. 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지 알아내야 합니다. 트럭은 1초에 1만큼 움직이며, 다리 길이는 bridge_length이고 다리는 무게 weight까지 견딥니다.

※ 트럭이 다리에 완전히 오르지 않은 경우, 이 트럭의 무게는 고려하지 않습니다.

예를 들어, 길이가 2이고 10kg 무게를 견디는 다리가 있습니다. 무게가 [7, 4, 5, 6]kg인 트럭이 순서대로 최단 시간 안에 다리를 건너려면 다음과 같이 건너야 합니다.

경과 시간 | 다리를 지난 트럭 | 다리를 건너는 트럭 | 대기 트럭
---|---|---|---
0 | [] | [] | [7,4,5,6]
1~2 | [] | [7] | [4,5,6]
3 | [7] | [4] | [5,6]
4 | [7] | [4,5] | [6]
5 | [7,4] | [5] | [6]
6~7 | [7,4,5] | [6] | []
8 | [7,4,5,6] | [] | []

따라서, 모든 트럭이 다리를 지나려면 최소 8초가 걸립니다.

solution 함수의 매개변수로 다리 길이 bridge_length, 다리가 견딜 수 있는 무게 weight, 트럭별 무게 truck_weights가 주어집니다. 이때 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지 return 하도록 solution 함수를 완성하세요.

## 제한 조건
- bridge_length는 1 이상 10,000 이하입니다.
- weight는 1 이상 10,000 이하입니다.
- truck_weights의 길이는 1 이상 10,000 이하입니다.
- 모든 트럭의 무게는 1 이상 weight 이하입니다.

## 입출력 예
bridge_length | weight | truck_weights | return
---|---|---|---
2 | 10 | [7,4,5,6] | 8
100 | 100 | [10] | 101
100 | 100 | [10,10,10,10,10,10,10,10,10,10] | 110

## [출처](http://icpckorea.org/2016/ONLINE/problem.pdf)

※ 공지 - 2020년 4월 06일 테스트케이스가 추가되었습니다.

## 풀이
```py
def solution(bridge_length, weight, truck_weights):
    answer = 0
    sum_of_bridge = 0
    bridge = [0] * bridge_length # 다리를 표현한다. bridge의 길이는 항상 bridge_length를 유지
    
    # queue의 각 job에 대해
    for truck in truck_weights:
        # worker capability 업데이트
        sum_of_bridge -= bridge.pop(0)
        
        # 새로운 job 시작 가능
        if sum_of_bridge + truck <= weight:
            # capability 업데이트
            sum_of_bridge += truck
            # do job
            bridge.append(truck)
            # time++
            answer += 1
            continue

        # 새로운 job 시작 불가
        # job 끝날 때까지 대기
        while sum_of_bridge + truck > weight:
            # capability update
            sum_of_bridge -= bridge.pop(0)
            # idle state
            bridge.append(0)
            # time++
            answer += 1

        # capability update
        sum_of_bridge += truck
        # do job
        bridge.append(truck)
        # time++
        answer += 1
        
    # 더이상 추가할 job 없음
    # 기존 job이 끝날 때까지 time++
    answer += bridge_length
    return answer
```

## 유의할 점
- `bridge[start:end]`와 같은 식으로 슬라이스 할 경우 리스트가 임시 변수로 복사되기 때문에 `.append(val)`, `.pop()`, `.pop(idx)`보다 성능이 떨어진다.
- `sum(list)`, `len(list)`의 time complexity는 `O(n)`이므로, 각각 `sum_of_bridge`, `bridge_length`로 관리하였다.
- 기본 로직은 동일하지만 이런 쪼잔한 걸로 타임아웃 거는 건 치사하지 않나...