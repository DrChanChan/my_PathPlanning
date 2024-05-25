import random

def probabilistic_path(graph, start_node, goal_node, probabilities):
    current_node = start_node
    path = [current_node]
    total_weight = 0
    
    while current_node != goal_node:
        neighbors = list(graph[current_node].keys())
        if len(neighbors) == 0:
            break
        
        neighbors = [node for node in neighbors if node not in path]
        if not neighbors:
            break
        
        # Create a list of probabilities for the neighbors
        neighbor_probabilities = [probabilities[(current_node, neighbor)] for neighbor in neighbors]
        
        # Choose next node based on weighted probabilities
        next_node = random.choices(neighbors, weights=neighbor_probabilities, k=1)[0]
        total_weight += graph[current_node][next_node]
        path.append(next_node)
        current_node = next_node
    
    return path, total_weight

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

def update_probabilities(path, probabilities, increment):
    for i in range(len(path) - 1):
        edge = (path[i], path[i + 1])
        reverse_edge = (path[i + 1], path[i])
        
        probabilities[edge] += increment
        probabilities[reverse_edge] += increment

# Define the graph
graph = {
    'A': {'B': 5.6, 'D': 6.8},
    'B': {'A': 5.6, 'C': 4.3, 'E': 6.5},
    'C': {'B': 4.3, 'D': 5.6, 'F': 5.8, 'G': 7},
    'D': {'A': 6.8, 'C': 5.6, 'F': 6.5},
    'E': {'B': 6.5, 'G': 5.2},
    'F': {'D': 6.5, 'C': 5.8, 'G': 5.5},
    'G': {'E': 5.2, 'C': 7, 'F': 5.5}
}

# Initialize probabilities for each edge
probabilities = {}
for node in graph:
    for neighbor in graph[node]:
        probabilities[(node, neighbor)] = 0.5  # Initial probability for each edge

# Print initial probabilities
print("Initial probabilities:")
for edge, prob in probabilities.items():
    print(f"{edge}: {prob:.6f}")

# Example usage
start_node = 'A'
goal_node = 'G'
path, path_weight = probabilistic_path(graph, start_node, goal_node, probabilities)
total_weight = total_edge_weight(graph)

print(f"\nPath from {start_node} to {goal_node}: {path}")
print(f"Weight of the path: {path_weight}")
print(f"Total weight of all edges: {total_weight}")

# Update probabilities if path weight is less than total edge weight
if path_weight < total_weight:
    update_probabilities(path, probabilities, increment=0.1)

print("\nUpdated probabilities:")
for edge, prob in probabilities.items():
    print(f"{edge}: {prob:.6f}")

