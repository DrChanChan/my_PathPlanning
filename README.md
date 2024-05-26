import random

def proba_path(graph, start_node, goal_node, probabilities):
    current_node = start_node
    path = [current_node]
    path_total_weight = 0
    
    while current_node != goal_node:
        neighbors = list(graph[current_node].keys())
        
        if len(neighbors) == 0:
            break
        
        neighbors = [node for node in neighbors if node not in path]
        
        if not neighbors:
            break
        
        neighbor_proba = [probabilities[(current_node, neighbor)] for neighbor in neighbors]
        next_node = random.choices(neighbors, weights=neighbor_proba, k=1)[0]
        
        path_total_weight += graph[current_node][next_node]
        
        path.append(next_node)
        current_node = next_node
        
    return path, path_total_weight

def update_probabilities(path, proba, increment):
    for i in range(len(path) - 1):
        edge = (path[i], path[i+1])
        reverse_edge = (path[i+1], path[i])
        proba[edge] += increment
        proba[reverse_edge] += increment

def total_edge_weight(graph):
    total_weight = 0
    visited_edges = set()
    
    for node in graph:
        for neighbor, weight in graph[node].items():
            edge = tuple(sorted((node, neighbor)))
            if edge not in visited_edges:
                visited_edges.add(edge)
                total_weight += weight
    
    return total_weight


#하이퍼파라미터
graph = {
    'A': {'B': 5.6, 'D': 6.8},
    'B': {'A': 5.6, 'C': 4.3, 'E': 6.5},
    'C': {'B': 4.3, 'D': 5.6, 'F': 5.8, 'G': 7},
    'D': {'A': 6.8, 'C': 5.6, 'F': 6.5},
    'E': {'B': 6.5, 'G': 5.2},
    'F': {'D': 6.5, 'C': 5.8, 'G': 5.5},
    'G': {'E': 5.2, 'C': 7, 'F': 5.5}
}

Initial_probabilities = 0.5
Initial_comparison_weight = total_edge_weight(graph)
increment = 0.05
start_node = 'A'
goal_node = 'G'

probabilities = {}

for node in graph:
    for neighbor in graph[node]:
        probabilities[(node, neighbor)] = Initial_probabilities
        
#간선의 초기 확률 확인
print("Initial probabilities:")
for edge, prob in probabilities.items():
    print(f"{edge}: {prob:.2f}")
    
#초기 비교 가중치
print(f"Initial_comparison_weight: {Initial_comparison_weight}")   
    
for _ in range(20):
    path, path_total_weight = proba_path(graph, start_node, goal_node, probabilities)

    if path_total_weight < Initial_comparison_weight:
        update_probabilities(path, proba=probabilities, increment=increment)
        
          
    #경로의 노드들 출력 && 경로의 가중치 합 출력
    print(f"\nPath from {start_node} to {goal_node}: {path}")
    print(f"Weight of the path: {path_total_weight}")
    weight_of_the_paths = [(path_total_weight)]
    
    Initial_comparison_weight = path_total_weight

min_weight = min(weight_of_the_paths)

print("\nUpdated probabilities:")
for edge, prob in probabilities.items():
    print(f"{edge}: {prob:.3f}")
    
print(f"Min_weight: {min_weight}")

