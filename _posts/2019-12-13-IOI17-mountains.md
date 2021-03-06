---
title:  "IOI2017 Day 0 mountains"
date:   2019-12-13 03:38:00
categories:
- IOI
tags:
- IOI
- CCW
- DP
---

### 문제 링크
* https://oj.uz/problem/view/IOI17_mountains

### 문제 출처
* 2017 IOI Day0(연습세션) 1번

### 사용 알고리즘
* CCW
* DP

### 시간복잡도
* $O(N^2)$

### 풀이
먼저 오른쪽에 있는 $i$가 왼쪽에 있는 $j$를 볼 수 있는 조건을 먼저 생각해보면,<br>모든 $k(j < k < i)$에 대해 $ccw(j, k, i) ≥ 0$을 만족하는 경우에 $i$가 $j$를 볼 수 있습니다. 이는 그림을 그려보면 쉽게 알 수 있습니다.

$D(j, i) = [j, i]$ 구간의 답이라고 정의해서 답을 구해봅시다.

$i$를 고정시켜두고, $j$를 $i-1$부터 시작해 왼쪽으로 하나씩 이동하면서 dp table을 채울겁니다.<br>$j$를 $i-1$부터 1까지 보면서 $left$라는 변수를 하나 관리해줄건데, 이 변수는 $(j, i]$구간에서 $i$가 볼 수 있는 사람 중 가장 왼쪽의 있는 사람을 저장하고 있습니다.<br>만약 $ccw(j, left, i) ≥ 0$이라면 당연히  $i$는 $j$를 볼 수 있으며, 추가로 $(j, left)$사이에 있는 사람들은 $left$에 가려져서 안 보인다는 것까지 알 수 있습니다.<br>이 정보를 이용해 $ccw(j, left, i) ≥ 0$을 만족하는 $j$가 나올 때마다 $D(j+1, left-1)$을 더해가면서 dp table을 채워나갈 수 있습니다.

$left$는 sliding window 느낌으로 관리해줄 수 있으므로 $O(N^2)$에 문제를 해결할 수 있습니다.

### 전체 코드
```cpp
#include <bits/stdc++.h>
#define x first
#define y second
using namespace std;

typedef long long ll;
typedef pair<ll, ll> p;

int n;
vector<p> v;
int dp[2020][2020];

int ccw(int _a, int _b, int _c){
    p a = v[_a], b = v[_b], c = v[_c];
    ll res = a.x*b.y + b.x*c.y + c.x*a.y;
    res -= b.x*a.y + c.x*b.y + a.x*c.y;
    if(res > 0) return 1;
    if(res) return -1;
    return 0;
}

int main(){
    ios_base::sync_with_stdio(0); cin.tie(0);
    cin >> n; v.resize(n+1);
    for(int i=1; i<=n; i++) v[i].x = i, cin >> v[i].y;
    for(int i=1; i<=n; i++){
        dp[i][i] = dp[i-1][i] = 1;
        int left = i - 1, v = 0;
        for(int j=i-2; j>0; j--){
            if(ccw(j, left, i) >= 0){ // j \ left / i -> i에서 j 볼 수 있음
                v += dp[j+1][left-1]; // (j, left)는 left에 가려짐
                left = j; // sliding window
            }
            dp[j][i] = max(dp[j][i-1], v + dp[j][left-1] + 1);
        }
    }
    cout << dp[1][n];
}
```
