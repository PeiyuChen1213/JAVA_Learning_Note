# Pratical

深度优先搜索

```c++
#include <iostream>
#include <map>
#include <queue>
#include <vector>
using namespace std;
bool isVisited(char node, vector<char>& visited)
{
    for (int i = 0 ; i<visited.size(); i++)
        {
            if (visited[i] == node)
            {
                return true;
            }
        }
    return false;
}

void dfs(map<char, vector<char> >& graph, char start, vector<char>& visited)
{
    if (!isVisited(start, visited))
    {
        visited.push_back(start);
        cout << start << "->";
        for (int i = 0; i<graph[start].size(); i++)
        {
            if (!isVisited(graph[start][i], visited))
            {
                dfs(graph, graph[start][i], visited);
            }
        }
    }
}

void bfs(map<char, vector<char> >& graph, char start, vector<char>& visited)
{
    queue<char> queue;
    visited.push_back(start);
    queue.push(start);
    while (!queue.empty())
    {
        char node = queue.front();
        queue.pop();
        cout << node << "->";
        for ( int i = 0; i<graph[node].size(); i++)
            {
                if (!isVisited(graph[node][i], visited))
                {
                    queue.push(graph[node][i]);
                    visited.push_back(graph[node][i]);
                }
            }
    }
}

int main()
{
    map<char, vector<char> > graph;
    graph['0'].push_back('1');
    graph['1'].push_back('@');
    graph['1'].push_back('4');
    graph['@'].push_back('e');
    graph['3'].push_back('1');
    graph['4'].push_back('2');
    graph['4'].push_back('3');

    vector<char> visited;
    dfs(graph, '0', visited);
//    bfs(graph, '0', visited);

    return 0;
}

```

冒泡排序

```c++
void bubbleSort(vector<int>& nums) {
    int n = nums.size();

    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (nums[j] > nums[j + 1]) {
                swap(nums[j], nums[j + 1]);
            }
        }
    }
}
```

二分查找

```cpp
int binarySearch(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return -1;  // 目标元素不存在
}
```

```
collaborative filtering utilizes user interactions and community recommendations to make predictions, capturing complex patterns and hidden user-item relationships. Secondly, collaborative filtering is more flexible and scalable when dealing with limited data for new items or users, as it relies on the collective behaviour of users to address the "cold start
```

