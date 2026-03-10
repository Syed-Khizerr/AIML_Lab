# AIML_Lab
#A* with grid

import heapq

def heuristic(a, b):
    return abs(a[0] - b[0]) + abs(a[1] - b[1])

def astar(grid, start, goal):
    rows, cols = len(grid), len(grid[0])
    open_list = []
    heapq.heappush(open_list, (0, start))
    

    came_from = {}
    g = {start: 0}
    
    directions = [(0,1),(1,0),(0,-1),(-1,0)]
    
    while open_list:
        _, current = heapq.heappop(open_list)
    
        if current == goal:
            path = []
            while current in came_from:
                path.append(current)
                current = came_from[current]
            path.append(start)
            return path[::-1]
    
        for d in directions:
            neighbor = (current[0]+d[0], current[1]+d[1])
    
            if not (0 <= neighbor[0] < rows and 0 <= neighbor[1] < cols):
                continue
            if grid[neighbor[0]][neighbor[1]] == 1:
                continue
    
            temp_g = g[current] + 1
    
            if neighbor not in g or temp_g < g[neighbor]:
                came_from[neighbor] = current
                g[neighbor] = temp_g
                f = temp_g + heuristic(neighbor, goal)
                heapq.heappush(open_list, (f, neighbor))
    
    return None


grid = [
[0,1,0,0,0,0],
[0,0,0,1,1,0],
[0,0,0,1,0,0],
[0,1,1,0,0,0],
[0,1,0,0,0,0],
[0,0,1,0,0,0]
]

start = (0,0)
goal = (5,5)

path = astar(grid,start,goal)

print("Path:",path)







#8 Puzzle Problem

import heapq

def heuristic(state, goal):
    count = 0
    for i in range(9):
        if state[i] != 0 and state[i] != goal[i]:
            count += 1
    return count


def get_neighbors(state):
    neighbors = []
    zero = state.index(0)

    moves = {
        0:[1,3], 1:[0,2,4], 2:[1,5],
        3:[0,4,6], 4:[1,3,5,7], 5:[2,4,8],
        6:[3,7], 7:[4,6,8], 8:[5,7]
    }
    
    for m in moves[zero]:
        new = list(state)
        new[zero], new[m] = new[m], new[zero]
        neighbors.append(tuple(new))
    
    return neighbors


def solve(start, goal):
    pq = []
    heapq.heappush(pq,(heuristic(start,goal),start,[]))
    visited=set()

    while pq:
        f,state,path = heapq.heappop(pq)
    
        if state == goal:
            return path
    
        visited.add(state)
    
        for n in get_neighbors(state):
            if n not in visited:
                g = len(path)+1
                h = heuristic(n,goal)
                heapq.heappush(pq,(g+h,n,path+[n]))
    
    return None


start = (1,2,3,4,5,6,7,0,8)
goal  = (1,2,3,4,5,6,7,8,0)

solution = solve(start,goal)

if solution:
    for s in solution:
        print(s)
else:
    print("No solution")







#CSP UNIQUE VALUES

def is_valid(assignment, value):
    return value not in assignment.values()


def backtrack(variables, domains, assignment):
    

    if len(assignment) == len(variables):
        return assignment
    
    var = None
    for v in variables:
        if v not in assignment:
            var = v
            break
    
    for value in domains[var]:
        if is_valid(assignment, value):
            assignment[var] = value
    
            result = backtrack(variables, domains, assignment)
            if result:
                return result
    
            assignment.pop(var)
    
    return None


variables = ['X1','X2','X3']

domains = {
'X1':[1,2,3],
'X2':[1,2,3],
'X3':[1,2,3]
}

solution = backtrack(variables, domains, {})

if solution:
    print("Solution:",solution)
else:
    print("No solution")







#CSP GRAPH COLORING

def is_safe(node, color, graph, colors):
    for neighbor in range(len(graph)):
        if graph[node][neighbor] == 1 and colors[neighbor] == color:
            return False
    return True


def graph_coloring(graph, m, colors, node):

    if node == len(graph):
        return True
    
    for color in range(1, m+1):
    
        if is_safe(node, color, graph, colors):
    
            colors[node] = color
    
            if graph_coloring(graph, m, colors, node+1):
                return True
    
            colors[node] = 0
    
    return False


graph = [
[0,1,1,1],
[1,0,1,0],
[1,1,0,1],
[1,0,1,0]
]

m = 3
colors = [0]*len(graph)

if graph_coloring(graph, m, colors, 0):
    print("Color assignment:", colors)
else:
    print("No solution")

#MINIMAX

import math

def minimax(curDepth, nodeIndex, maxTurn, scores, targetDepth):

    if (curDepth == targetDepth):
        return scores[nodeIndex]
    
    if (maxTurn):
        return max(
            minimax(curDepth + 1, nodeIndex * 2, False, scores, targetDepth),
            minimax(curDepth + 1, nodeIndex * 2 + 1, False, scores, targetDepth)
        )
    
    else:
        return min(
            minimax(curDepth + 1, nodeIndex * 2, True, scores, targetDepth),
            minimax(curDepth + 1, nodeIndex * 2 + 1, True, scores, targetDepth)
        )


scores = [3, 5, 2, 9, 12, 5, 23, 23]

treeDepth = math.log(len(scores), 2)

print("The optimal value is:", minimax(0, 0, True, scores, treeDepth))



#AB Pruning

MAX, MIN = 1000, -1000

def minimax(depth, nodeIndex, maximizingPlayer, values, alpha, beta):

    if depth == 3:
        return values[nodeIndex]
    
    if maximizingPlayer:
        best = MIN
    
        for i in range(0,2):
    
            val = minimax(depth + 1, nodeIndex * 2 + i, False, values, alpha, beta)
    
            best = max(best, val)
            alpha = max(alpha, best)
    
            if beta <= alpha:
                break
    
        return best
    
    else:
        best = MAX
    
        for i in range(0,2):
    
            val = minimax(depth + 1, nodeIndex * 2 + i, True, values, alpha, beta)
    
            best = min(best, val)
            beta = min(beta, best)
    
            if beta <= alpha:
                break
    
        return best


values = [3,5,6,9,1,2,0,-1]

print("The optimal value is:", minimax(0,0,True,values,MIN,MAX))
