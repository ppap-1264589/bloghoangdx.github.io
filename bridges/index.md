---
layout: default
title: Khớp cầu
use_math: true
---

# Bài toán
Tìm cạnh cầu và đỉnh khớp

Nhập n là số đỉnh, m là số cạnh, u - v là các cặp cạnh trong đồ thị

Cho biết các cạnh cầu và đỉnh khớp

# Code

```cpp
#include <bits/stdc++.h>
#define up(i,a,b) for (int i = (a); i <= (b); i++)
#define pii pair<int, int>
#define f first
#define s second
using namespace std;

const int maxn = 1e5 + 10;
vector<int> a[maxn];
int n,m;

int low[maxn];
int num[maxn];
int par[maxn];
int tdfs;

vector<pii> bridge;
vector<int> joint;
int root;

void DFS(int u){
    int isolated = (u != root);
    low[u] = num[u] = ++tdfs;
    for (int v : a[u]){
        if (v == par[u]) continue;
        if (!num[v]){
            par[v] = u;
            DFS(v);
            low[u] = min(low[u], low[v]);
            if (low[v] > num[u]){ //Để ra ngoài
                bridge.push_back({min(u, v), max(u, v)});
            }
            if (low[v] >= num[u]) ++isolated;
        }
        else low[u] = min(low[u], num[v]);
    }
    if (isolated > 1) joint.emplace_back(u);
}

signed main (){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    #define Task "A"
    if (fopen(Task".inp", "r")){
        freopen(Task".inp", "r", stdin);
        freopen(Task".out", "w", stdout);
    }

    cin >> n >> m;

    up(i,1,m){
        int u,v;
        cin >> u >> v;
        a[u].emplace_back(v);
        a[v].emplace_back(u);
    }

    up(i,1,n) if (!num[i]){
        root = i;
        DFS(i);
    }

//    cout << bridge.size() << "\n";
//    for (auto x : bridge){
//        cout << x.f << " " << x.s << "\n";
//    }

    cout << joint.size() << "\n";
    for (auto x : joint) cout << x << " ";

    return 0;
}
```

# Notes:

## Chú ý 1:
Giả sử ta để điều kiện kiểm tra xem (u, v) có phải cạnh cầu hay không ra ngoài điều kiện if (!num[v]) như sau:
```cpp
for (int v : a[u]){
    if (v == par[u]) continue;
    if (!num[v]){
        par[v] = u;
        DFS(v);
        low[u] = min(low[u], low[v]);
        if (low[v] >= num[u]) ++isolated;
    }
    else low[u] = min(low[u], num[v]);

    if (low[v] > num[u]){ //Để ra ngoài
        bridge.push_back({min(u, v), max(u, v)});
    }
}
```

Thì kết quả bài toán vẫn đúng

### Lý do:

Việc đặt điều kiện kiểm tra cạnh cầu ra bên ngoài điều kiện if (!num[v]), tương đương với việc ta xét cả hai trường hợp cung xuất hiện tại đỉnh u:

1. cung xuôi (v là đỉnh chưa thăm trong quá trình DFS từ u đến v)
2. cung ngược (v là đỉnh đã thăm trong quá trình DFS từ u đến v) 

Theo thiết kế cây DFS, một cung ngược không bao giờ là cạnh cầu, chỉ có cung xuôi mới có khả năng trở thành cạnh cầu.

Do đó, việc đặt điều kiện kiểm tra cung xuôi (u-v) là cạnh cầu ra bên ngoài giống không ảnh hưởng đến tính đúng đắn của thuật toán.

Chỉ là mình phải check nhiều trường hợp thừa thãi hơn thôi

[Quay lại trang chủ](../)
