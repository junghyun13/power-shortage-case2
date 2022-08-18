# power-shortage-case2
# The first line of each test case is given the number of houses m and the number of streets n. Then n lines are given information about each road x, y, z, which means that there is a two-way road between houses x and y and the distance is z meters. A city is always in the form of a connected graph, and the sum of the distances of all roads in the city is less than 231 meters. At the end of the input, two zeros are given in the first line. Print the maximum cost savings on one line for each test case! 각 테스트 케이스의 첫째 줄에는 집의 수 m과 길의 수 n이 주어진다. 이어서 n개의 줄에 각 길에 대한 정보 x, y, z가 주어지는데, 이는 x번 집과 y번 집 사이에 양방향 도로가 있으며 그 거리가 z미터라는 뜻이다. 도시는 항상 연결 그래프의 형태이고, 도시상의 모든 길의 거리 합은 231미터보다 작다. 입력의 끝에서는 첫 줄에 0이 2개 주어진다. 각 테스트 케이스마다 한 줄에 걸쳐 절약할 수 있는 최대 비용을 출력해라!
# 3) 프림(Prim)
프림도 위에서 말한 최소 신장 트리를 구하는 알고리즘이다.
프림은 임의의 시작점에서 현재까지 연결된 정점들에서 연결되지 않은 정점들에 대해, 가장 가중치가 작은 정점을 연결하는 정점 선택 기반으로 동작한다.
프림의 핵심은 트리 집합을 단계적으로 확장하는 것이고, 
새로운 정점을 연결할 때마다 새로운 정점에서 갈 수 있는 아직 연결되지 않은 정점들에 대한 간선을 추가해줘야 한다.
Priority Queue를 이용한 최소 힙으로 구현할 수 있고, 다익스트라 알고리즘과 구현 방식이 유사하다.
전체 로직에 걸리는 시간은 VlogV + ElogV라고 할 수 있는데,
간선의 수는 정점의 수보다 많기 때문에 시간 복잡도에선 VlogV를 무시하여
O(ElogV)가 프림 알고리즘의 시간 복잡도가 된다. 
최소 힙을 사용하지 않는 경우 O(V^2)의 시간 복잡도를 가질 수 있지만,
최소 힙으로 구현하는 것이 어렵지 않다.
또한, 피보나치 힙을 이용하는 경우 O(E + VlogV)의 시간 복잡도가 가능하지만,
대부분의 경우 최소 힙이면 충분하니 여기까지만 알아보자.
//E : 간선의 개수
//V : 정점의 개
프림 알고리즘의 동작 방식
1. 임의의 정점을 시작점으로 선택한다.
2. 시작점에서 갈 수 있는 정점 중 가장 가중치가 작은 정점을 연결한다.
3-1. 2번 과정에서 시작점과 어떠한 정점들이 연결되었다.
3-2. 시작점과 연결된 정점들을 a집합이라 할 때, a집합에서 갈 수 있는 a집합에 속하지 않은 정점들에 대해 가중치가
     가장 작은 정점을 연결한다.
4. a집합의 크기가 늘어났다.(시작점을 포함한 a집합에 새로운 정점을 연결했다.) 위의 과정을 모든 정점이 연결될
   때까지 반복한다.
# python
import heapq
import sys
input = sys.stdin.readline
def prim(road_info, house, start):
    result = 0
    q = []
    heapq.heappush(q, (0, start))
    visited = [False] * house
    while q:
        cost, home = heapq.heappop(q) #가장 작은 비용의 도로를 pop하고, 비용을 누적,  연결된 노드를 방문처리
        if visited[home]:
            continue
        result += cost
        visited[home] = True
        for ncost, nhome in road_info[home]:
            if not visited[nhome]:
                heapq.heappush(q, (ncost, nhome))
    return result
while True:
    house, road = map(int, input().split())
    if house == 0 and road == 0:
        break
    total_cost = 0
    road_info = [[] for _ in range(house)]
    for _ in range(road):
        x, y, cost = map(int, input().split())
        road_info[x].append((cost, y))
        road_info[y].append((cost, x))
        total_cost += cost
    result = prim(road_info, house, 0)
    print(total_cost - result)
#input--> 7 11  0 1 7  0 3 5  1 2 8  1 3 9  1 4 7  2 4 5  3 4 15  3 5 6  4 5 8  4 6 9  5 6 11  0 0
#result--> 51
