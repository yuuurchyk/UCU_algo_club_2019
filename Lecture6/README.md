# Lecture 6: Graph Theory

# Before we start
All the topics from today lecture are well covered on [e-maxx](http://e-maxx.ru/algo/).

Please, fill free to use it as a reference.

# What's a graph?
# Types of graphs
* directed / undirected
* connected / disconnected
* finite / infinite
* simple / multigraph / pseudo graph
* weighted / not weighted
* bipartite
* tree, forest

Read more on [geeksforgeeks](https://www.geeksforgeeks.org/graph-types-and-applications/).

In the problem statements the type of graph is always clarified and explained. However, you should remember the key properties of the main types of graphs.

# Indexation

0 or 1 indexation (more on the lecture).

# How to store the graph?

1. Adjacency list
2. Adjacency matrix
3. List of edges

![Adjacency list representation](https://www.kodefork.com/media/uploads/articles/2019/06/23/graph-ajacency-list-cpp.jpg)
![Matrix representation](https://4.bp.blogspot.com/-8Lz-3aCkhbk/Ww_rpugRewI/AAAAAAAAAJ0/yPWySl96IEsnU7WQppU0aBWYj9VzYIpbQCLcBGAs/s1600/Graph%2BAdjacency%2BMatrix%2B1.jpg)

Every option has its pros and cons. Think about the next tasks for each representation:
* get degree of the vertex
* for particular ```i``` and ```j``` know whether edge ```(i, j)``` exists
* iterate over all the edges
* iterate over neighbors of a particular vertex

# Connectivity checking, Depth First Search

See dfs theory on [e-maxx](https://e-maxx.ru/algo/dfs).

![Depth first search](https://he-s3.s3.amazonaws.com/media/uploads/9fa1119.jpg)

```cpp
const int kMaxN = 1000000;

int n, m;

vector<int> g[kMaxN];
bool visited[kMaxN];

void dfs(int v){
    visited[v] = true;
    for(const auto &to: g[v])
        if(!visited[to])
            dfs(to);
}

void dfsNonRecursive(int startv){
    stack<int> s;
    s.push(startv);
    visited[startv] = true;

    while(!s.empty()){
        int v = s.top();
        s.pop();
        for(const auto &to: g[v])
            if(!visited[to]){
                visited[to] = true;
                s.push(to);
            }
    }
}

int main(){
    cin >> n >> m;

    for(int i = 0; i < m; ++i){
        int from, to; cin >> from >> to;
        g[from].push_back(to);
        g[to].push_back(from);
    }

    dfs(1);
    // or
    dfsNonRecursive(1);

    // now if visited[i] == true, then i is in the same component as vertex 1

    return 0;
}
```

Asymptotics: ```O(n + m)``` where n - number of vertices, m - number of edges in a graph.

# Shortest path, bfs

**Bfs** - same as non-recursive **dfs**, but ```queue``` instead of ```stack```.

See theory on [e-maxx](https://e-maxx.ru/algo/bfs).

Solves the problem of shortest path in unweighted graph between 1 vertex and all the others.

Asymptotics: ```O(n + m)``` where n - number of vertices, m - number of edges in a graph.

# Color of the vertex
If we need to find number of connected components, we use ```color[kMaxN]``` array instead of ```visited[kMaxN]```.

Then in dfs we 'color' the node:

```cpp
int color[kMaxN];

void dfs(int v, int currentcolor){
    color[v] = currentcolor;

    for(const auto &to: g[v])
        if(color[to] == 0)
            dfs(to, currentcolor);
}

int main(){
    // read the graph

    int components_num = 0;
    for(int i = 1; i <= n; ++i)
        if(color[i] == 0){
            ++components_num;
            dfs(i, components_num);
        }
    // HORRAY
}
```

# Topological Sort

See theory on [e-maxx](https://e-maxx.ru/algo/topological_sort).

Directed dependency graph (i.e. prerequisites for the courses).

![Topological Sort](https://he-s3.s3.amazonaws.com/media/uploads/d6be27e.png)

Find the ordering of the vertices (i.e. course order): ```1->2->3->4->5```

```cpp
const int kMaxN = 1000000;

int n, m;
int indegree[kMaxN];
vector<int> g[kMaxN];

int main(){
    cin >> n >> m;
    
    for(int i = 0; i < m; ++i){
        int from, to; cin >> from >> to;
        ++indegree[to];
        g[from].push_back(to);
    }

    vector<int> topological_sort;
    queue<int> q;
    
    for(int i = 1; i <= n; ++i)
        if(indegree[i] == 0)
            q.push(i);
    
    while(!q.empty()){
        int v = q.front();
        q.pop();

        topological_sort.push_back(v);

        for(const auto &to: g[v]){
            --indegree[to];
            if(indegree[to] == 0)
                q.push(to);
        }
    }
}
```

# Problems

**Problem:** [Interesting Vertices](https://codeforces.com/gym/102348/problem/B?locale=en)

**Problem:** [C, Coolest Ski Route](https://codeforces.com/gym/102021)

**Problem:** [No Link, Cut Tree](http://codeforces.com/problemset/gymProblem/101484/F)

# Check whether a graph contains a cycle

See theory on [e-maxx](http://e-maxx.ru/algo/finding_cycle).

Problem: given an undirected graph, find whether it is acyclic. If the graph contains a cycle, print any one.

Dfs approach with modifications:
1. We store a parent for each node (```p[i]``` means from which node did we come in ```i```?)
2. When enter the node, paint it ```gray```
3. When exit the node, paint it ```black```
4. If we visit a gray node, which is not a parent for the current node - we have found a cycle
5. We can restore the cycle by going to parents (more on the lecture)

```cpp

const int kMaxN = 1000000;

vector<int> g[kMaxN];
int parent[kMaxN];
int color[kMaxN];

const int black = 1;
const int gray = 2;

int cycle_start;
int cycle_end;

bool dfs(int v, int p){
    color[v] = gray;
    parent[v] = p;

    for(const auto &to: g[v]){
        if(parent[v] == to)
            continue;
        if(color[to] == gray){
            cycle_start = to;
            cycle_end = v;
            return true;
        }

        if(dfs(to, v))
            return true;
    }

    color[v] = black;
    return false;
}

int main(){
    // read the graph ...

    for(int i = 1; i <= n; ++i)
        if(color[i] == 0)
            if(dfs(i, i))
                break;
    
    if(cycle_start == 0){
        cout << "Acyclic" << endl;
        return 0;
    }
    else{
        cout << "Has Cycle" << endl;

        vector<int> cycle;
		cycle.push_back (cycle_st);
		for (int v = cycle_end; v != cycle_st; v = p[v])
			cycle.push_back (v);
		cycle.push_back (cycle_st);
		reverse (cycle.begin(), cycle.end());

        for(const auto &it: cycle)
            cout << it << " ";
        cout << endl;
        return 0;
    }
}

```

# Floyd-Warshall Algorithm

See theory on [e-maxx](http://e-maxx.ru/algo/floyd_warshall_algorithm).

Problem: given undirected weighted graph with ```n``` vertices.

For every pair ```(i, j)``` find the minimum distance between vertex ```i``` and ```j``` in a graph.

Let ```d[n][n]``` will be the adjacency matrix of our graph.

```cpp
for (int k=0; k<n; ++k)
    for (int i=0; i<n; ++i)
        for (int j=0; j<n; ++j)
            d[i][j] = min (d[i][j], d[i][k] + d[k][j]);
```

Complexity: **O(n<sup>3</sup>)**

Why does it work? (More on the lecture)

Special cases:
* float weights
* negative weights (exponential growth rate for cycles of negative length)

# Dijkstra Algorithm

See theory on [e-maxx](https://e-maxx.ru/algo/dijkstra).

**Problem:** given a directed/undirected weighted graph with non-negative weights.
For fixed vertex ```s``` find the shortest distance between ```s``` and any other node in the graph.

Strategy:
1. Mark each node as unvisited
2. Assign a shortest distance to each node (0 for ```s```, infinity for any other)
3. While there are unvisited nodes, pick an unvisited node with the <i>smallest</i> distance
4. Go through its adjacent edges and modify the distances to neighbors (edge relaxation)
5. Mark the node as visited

![Dijkstra visualization](https://upload.wikimedia.org/wikipedia/commons/5/57/Dijkstra_Animation.gif)

## Complexity?

Depends on the complexity of picking the **smallest unvisited node** and **edge relaxation**.

## Straightforward approach (e-maxx)

Picking smallest unvisited vertex - just iterate over all vertices.

This gives you ```O(n * n + m)``` (```n``` phases with ```O(n)``` picking of the smallest vertex + ```O(m)``` to relaxate across all edges).

## Set implementation
(to be discussed on the lecture)

```cpp
#include <vector>
#include <set>

using namespace std;

const int kMaxN = 1000000;
const int kInf = 1000000000;

int n, m;
int start_node;

vector<pair<int, int>> g[kMaxN]; // <neightbour_node, weight>
int d[kMaxN];
bool visited[kMaxN];

void dijkstra(){
    for(int i = 1; i <= n; ++i)
        d[i] = kInf;
    d[start_node] = 0;
    
    set<pair<int, int>> s; // HAZARD <d[i], i>
    s.insert(make_pair(d[start_node], start_node));

    while(!s.empty()){
        int v = s.begin()->second;
        s.erase(s.begin());

        if(visited[v])
            continue;
        visited[v] = true;

        for(const auto &it: g[v]){
            int to = it.first;
            int w = it.second;

            if(visited[to])
                continue;
            if(d[to] <= d[v] + w)
                continue;
            
            s.erase(make_pair(d[to], to));
            d[to] = d[v] + w;
            s.insert(make_pair(d[to], to));
        }
    }
}

int main(){
    // read the graph
    // ...

    int start_node = ...; // starting node

    dijkstra();
}
```

Complexity: ```O(nlogn + mlogn)```

## Implementation comparison
Straightforward implementation has better complexity when graph is close to complete (**O(n<sup>2</sup>)** vs **O(n<sup>2</sup>logn)** in set implementation).

However on practice graphs are sparse and set implementation works quite well.

## Problems

**Problem:** [Home by trains](https://www.e-olymp.com/en/problems/750)

**Problem:** [C, Buggy Robot](http://codeforces.com/gym/101291)