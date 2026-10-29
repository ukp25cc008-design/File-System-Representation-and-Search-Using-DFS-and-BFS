# File-System-Representation-and-Search-Using-DFS-and-BFS
#include <stdio.h>
#include <string.h>

#define MAX 50
#define NAME_LEN 32

int  adj[MAX][MAX];          
char names[MAX][NAME_LEN];   
int  n;                    
int  visited[MAX];

int findIndex(const char *name)
{
    for (int i = 0; i < n; i++)
        if (strcmp(names[i], name) == 0)
            return i;
    return -1;
}

void displayMatrix(void)
{
    printf("\nAdjacency Matrix:\n%-18s", "");
    for (int j = 0; j < n; j++)
        printf("%-4d", j);
    printf("\n");
    for (int i = 0; i < n; i++) {
        printf("%2d %-15s", i, names[i]);
        for (int j = 0; j < n; j++)
            printf("%-4d", adj[i][j]);
        printf("\n");
    }
}

/* ---------------- Breadth-First Search ---------------- */
int bfs(int start, int target)
{
    int queue[MAX], front = 0, rear = 0;
    int found = 0;

    memset(visited, 0, sizeof(visited));
    visited[start] = 1;
    queue[rear++] = start;

    printf("BFS Traversal Order: ");
    while (front < rear) {
        int cur = queue[front++];
        printf("%s ", names[cur]);

        if (cur == target) {          /* stop once the file is reached */
            found = 1;
            break;
        }
        for (int v = 0; v < n; v++) {
            if (adj[cur][v] && !visited[v]) {
                visited[v] = 1;
                queue[rear++] = v;
            }
        }
    }
    printf("\n");
    return found;
}

/* ---------------- Depth-First Search (recursive) ---------------- */
int dfsVisit(int cur, int target)
{
    visited[cur] = 1;
    printf("%s ", names[cur]);

    if (cur == target)
        return 1;

    for (int v = 0; v < n; v++)
        if (adj[cur][v] && !visited[v])
            if (dfsVisit(v, target))
                return 1;             /* stop descending once found */
    return 0;
}

int dfs(int start, int target)
{
    memset(visited, 0, sizeof(visited));
    printf("DFS Traversal Order: ");
    int found = dfsVisit(start, target);
    printf("\n");
    return found;
}

int main(void)
{
    int e;
    char a[NAME_LEN], b[NAME_LEN], startName[NAME_LEN], targetName[NAME_LEN];

    printf("Enter number of nodes (files/directories): ");
    if (scanf("%d", &n) != 1 || n <= 0 || n > MAX) {
        printf("Invalid number of nodes.\n");
        return 1;
    }

    printf("Enter the names of %d nodes:\n", n);
    for (int i = 0; i < n; i++)
        scanf("%31s", names[i]);

    memset(adj, 0, sizeof(adj));
    printf("Enter number of edges: ");
    scanf("%d", &e);
    printf("Enter %d edges (parent child):\n", e);
    for (int i = 0; i < e; i++) {
        scanf("%31s %31s", a, b);
        int u = findIndex(a), v = findIndex(b);
        if (u == -1 || v == -1) {
            printf("Skipping invalid edge: %s -> %s\n", a, b);
            continue;
        }
        adj[u][v] = 1;                /* directory u contains v */
    }

    displayMatrix();

    printf("\nEnter start node: ");
    scanf("%31s", startName);
    printf("Enter file to search: ");
    scanf("%31s", targetName);

    int start  = findIndex(startName);
    int target = findIndex(targetName);

    if (start == -1) {
        printf("\nStart node '%s' does not exist.\n", startName);
        return 1;
    }

    printf("\n--- Searching for '%s' starting from '%s' ---\n\n",
           targetName, startName);

    /* If the file isn't a node at all, target = -1 never matches,
       so both traversals visit everything reachable and report not found. */
    int bfsFound = bfs(start, target);
    printf("BFS Result: File '%s' %s\n\n", targetName,
           bfsFound ? "FOUND" : "NOT FOUND");

    int dfsFound = dfs(start, target);
    printf("DFS Result: File '%s' %s\n", targetName,
           dfsFound ? "FOUND" : "NOT FOUND");

    return 0;
}
