# my_PathPlanning


import random

# 원래 그래프(간선의 비용이 있는 그래프)
graph = {
    'A' : {'B': 5.6, 'D': 6.8},
    'B' : {'A': 5.6, 'C': 4.3, 'E': 6.5},
    'C' : {'B': 4.3, 'D': 5.6, 'F': 5.8, 'G': 7},
    'D' : {'A': 6.8, 'C': 5.6, 'F': 6.5},
    'E' : {'B': 6.5, 'G': 5.2},
    'F' : {'D': 6.5, 'C': 5.8, 'G': 5.5},
    'G' : {'E': 5.2, 'C': 7, 'F': 5.5}
}

def initialize_probabilities(graph):
    prob_graph = {}
    for node, edges in graph.items():
        num_edges = len(edges)
        equal_prob = 1 / num_edges if num_edges > 0 else 0
        prob_graph[node] = {edge: equal_prob for edge in edges}
    return prob_graph

def update_probabilities(prob_graph, path, increase_prob):
    for i in range(len(path) - 1):
        current_node = path[i]
        next_node = path[i + 1]
        
        if next_node in prob_graph[current_node]:
            prob_graph[current_node][next_node] += increase_prob
        
        # Normalizing probabilities so that they sum up to 1
        total = sum(prob_graph[current_node].values())
        for key in prob_graph[current_node]:
            prob_graph[current_node][key] /= total

prob_graph = initialize_probabilities(graph)

def select_next_node(current_node, prob_graph, visited):
    next_nodes = [node for node in prob_graph[current_node].keys() if node not in visited]
    if not next_nodes:
        return None
    probabilities = [prob_graph[current_node][node] for node in next_nodes]
    return random.choices(next_nodes, probabilities)[0]

def dfs(current_node, end_node, path, total_cost, visited, paths, graph):
    if current_node == end_node:
        paths.append((path.copy(), total_cost))
        return
    for next_node in graph[current_node]:
        if next_node not in visited:
            visited.add(next_node)
            path.append(next_node)
            dfs(next_node, end_node, path, total_cost + graph[current_node][next_node], visited, paths, graph)
            path.pop()
            visited.remove(next_node)

def find_paths(start_node, end_node, prob_graph, graph, total_cost_threshold, num_paths):
    paths = []
    global total_cost  # Use the global total_cost variable
    while len(paths) < num_paths:
        current_paths = []
        dfs(start_node, end_node, [start_node], 0, {start_node}, current_paths, graph)
        for path, cost in current_paths:
            if cost < total_cost_threshold:
                update_probabilities(prob_graph, path)
                total_cost = cost  # Update the total cost
                paths.append((path, cost))
                if len(paths) >= num_paths:
                    break
    return paths

def total_edge_cost(graph):
    total_cost = 0
    visited_edges = set()
    for node, edges in graph.items():
        for neighbor, cost in edges.items():
            if (node, neighbor) not in visited_edges and (neighbor, node) not in visited_edges:
                total_cost += cost
                visited_edges.add((node, neighbor))
    return total_cost

# 전체 간선 비용의 총 합 계산
total_cost = total_edge_cost(graph)
print("전체 간선 비용의 총 합:", total_cost)

# A에서 G까지 경로를 5개 구하고 각 경로의 총 비용을 계산
num_paths = 5
start_node = 'A'
end_node = 'G'
increase_prob = 0.5
paths = find_paths(start_node, end_node, prob_graph, graph, total_cost, num_paths=num_paths)

# 결과 출력
for i, (path, cost) in enumerate(paths):
    print(f"경로 {i+1}: {path}, 총 비용: {cost}")
print("갱신된 전체 간선 비용의 총 합:", total_cost)
    
    
print("\n확률 그래프(각 간선의 확률):")
for node, edges in prob_graph.items():
    print(f"{node}: {edges}")
