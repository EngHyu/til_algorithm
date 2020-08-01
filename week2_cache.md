# 문제
https://programmers.co.kr/learn/courses/30/lessons/17680
캐시 교체 알고리즘 중 하나인 `LRU`(Least Recently Used)를 사용하여 주어진 크기의 캐시에서 배열의 각 요소 Hit/Miss에 따른 총 실행 시간을 구하는 문제입니다.

# 오답
```py
def solution(cacheSize, cities):
    answer = 0
    lists = []
    for city in cities:
        city = city.lower()
        if city in lists:
            answer += 1
            lists.remove(city)
            lists.append(city)
            continue

        answer += 5
        if len(lists) < cacheSize:
            lists.append(city)
        else:
            lists = lists[1:] + [city]
    return answer
```

# 로직 1
- 캐시는 리스트로 구현했습니다.
- 도시가 캐시에 있으면(Hit) 실행 시간은 1 증가합니다. 해당 도시를 리스트의 맨 마지막으로 옮깁니다.
- 없으면(Miss) 다시 두 가지 경우로 나뉩니다. 캐시가 꽉 차지 않았으면 리스트의 맨 마지막에 추가하고, 꽉 찼다면 맨 앞 요소를 제거한 뒤 도시를 캐시 맨 뒤에 추가합니다.

# 정답
```py
def solution(cacheSize, cities):
    answer = 0
    lists = []
    ''' 추가된 부분 '''
    if cacheSize == 0:
        return 5 * len(cities)
    ''' 추가된 부분 '''
    for city in cities:
        city = city.lower()
        if city in lists:
            answer += 1
            lists.remove(city)
            lists.append(city)
            continue

        answer += 5
        if len(lists) < cacheSize:
            lists.append(city)
        else:
            lists = lists[1:] + [city]
    return answer
```

# 로직 2
- 캐시 크기가 0일 경우에 대한 예외 처리
- 테스트 케이스에도 캐시 크기 0인 경우가 있는데 왜 통과했는지 모르겠습니다..

# 문제점 
- `for ~ in ...`이나 `remove`의 경우 선형적으로 탐색 => 시간 복잡도 O(n)
- 효율성 테스트가 없지만, `dict`나 다른 자료구조를 사용하면 실행 시간을 더 줄일 수 있을 걸로 예상합니다.

# 캐시
캐시는 CPU 내부에 위치한 L1 캐시와 00 L2 캐시가 있습니다. 캐시는 DRAM으로 이루어져 있으며, 때문에 더 빠릅니다.

캐시는 빠르지만 비용이 비싸기 때문에 보통 보조기억장치(SSD, HDD)보다 용량이 작습니다. 최대의 효율을 보이기 위해선 캐시를 효율적으로 사용할 수 있어야 하며, 어떤 데이터가 자주 사용될 지 추측하는 과정을 통해 다양한 캐시 교체 알고리즘이 탄생하였습니다.

대표적인 캐시 교체 알고리즘에는 `FIFO`(First In First Out), `OPT`(OPTimal Page Replacement), `LRU`(Least Recently Used), `LFU`(Least Frequently Used), `MFU`(Most Frequently Used), `SCR`(Second Chance Replacement), `Random`이 있습니다.

`FIFO`는 단순히 큐라고 생각하면 되고, `OPT` 알고리즘은 앞으로 가장 적게 사용될 것을 선택하는 방식입니다. `OPT` 알고리즘은 재현 작업 같은, 실행 순서가 모두 정해진 상태에서만 가능한 알고리즘이기 때문에 대부분의 경우 실현 불가능합니다. 다른 페이지 교체 알고리즘의 성능을 측정하기 위해 사용합니다.

`LRU`는 과거 가장 오랫동안 사용되지 않은 캐시를 선택하여 제거하고, 이는 counter를 통해 각 캐시가 사용된 시점을 저장하거나, 요소에 임의 접근 가능하도록 만든 queue로 구현 가능합니다.

`LFU`는 자주 참조되는 데이터만 유지하는 방식인데, `Approximated LRU`와 유사합니다. 모리스 카운터(=확률적 카운터)를 사용하여 액세스 빈도를 계산하고 시간이 지나면서 카운터가 감소합니다. 

`MFU`는 가장 많이 사용된 페이지가 앞으로는 사용되지 않을 것이라는 가정으로 교체되며, `SCR`은 reference bit을 두어 교체를 1회 유예하고 결과적으로 `LRU`와 비슷한 효과를 냅니다.

특별한 경향성이 나타나지 않는다면 `Random` 알고리즘을 사용하는 것도 방법입니다.