---
layout: single
classes: wide
categories: 알고리즘
title: "16988 Baaaaaaaaaduk2 (Easy)"
tags: [baekjoon]
---

### [문제 링크](https://www.acmicpc.net/problem/16988)<br>

## 내 풀이

```cpp
#include <iostream>
#include <vector>
#include <cstring>
#include <algorithm>
#include <queue>

using namespace std;

int m[20][20];
int mark[20][20];
int N, M;

vector<pair<int, int>> z;
int dr[4] = { 1, -1, 0, 0 };
int dc[4] = { 0, 0, 1, -1 };

int eat(vector<pair<int, int>> candi)
{
    int ret = 0;
    for (pair<int, int> c : candi)
    {
        m[c.first][c.second] = 1;
    }

    memset(mark, 0, sizeof(mark));

    for (pair<int, int> ca : candi)
    {
        deque<pair<int, int>> a;
        int tret = 0, piv;

        for (int i = 0; i < 4; i++)
        {
            int nr = ca.first + dr[i];
            int nc = ca.second + dc[i];
            piv = 0;
            tret = 0;
            if (nr < 0 || nr >= N || nc < 0 || nc >= M || mark[nr][nc] || m[nr][nc] != 2)
                continue;

            a.push_back({ nr, nc });
            tret += 1;

            while (!a.empty())
            {
                int r = a.front().first;
                int c = a.front().second;
                a.pop_front();
                mark[r][c] = 1;

                for (int i = 0; i < 4; i++)
                {
                    int nnr = r + dr[i];
                    int nnc = c + dc[i];

                    if (nnr < 0 || nnr >= N || nnc < 0 || nnc >= M || mark[nnr][nnc] || m[nnr][nnc] == 1)
                    {
                        continue;
                    }

                    if (!m[nnr][nnc])
                    {
                        piv = 1;
                        continue;
                    }
                    mark[nnr][nnc] = 1;
                    a.push_back({ nnr, nnc });
                    tret += 1;
                }
            }
            if (!piv)
                ret += tret;
        }
    }

    for (pair<int, int> c : candi)
    {
        m[c.first][c.second] = 0;
    }

    return ret;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    cin >> N >> M;

    for (int r = 0; r < N; r++) for (int c = 0; c < M; c++)
    {
        cin >> m[r][c];
        if (m[r][c] == 0)
        {
            z.push_back({ r, c });
        }
    }

    vector<int> t(z.size() - 2, 0);
    t.push_back(1);
    t.push_back(1);

    int ret = 0;

    do
    {
        vector<pair<int, int>> candi;
        for (int i = 0; i < t.size(); i++)
        {
            if (t[i])
            {
                candi.push_back(z[i]);
                if (candi.size() == 2)
                    break;
            }
        }
        ret = max(ret, eat(candi));
    } while (next_permutation(t.begin(), t.end()));

    cout << ret << "\n";
}
```

## 다른 사람 풀이

1개만 놓아서 잡을 수 있는 적돌 그룹과 2개를 놓아 잡을 수 있는 적 돌그룹을 기록하여 빠르게 최소값을 찾아내는 알고리즘

### 테크닉 분석

- BFS함수에서 이미 탐색을 마친 적돌 위치에는 3을 넣어 탐색한 위치를 표시하였다.
- 나는 mark배열을 만들고 조회하도록하여 필요없는 배열을 생성하였다.
- 가능하면 3을 넣거나 -1을 넣어 BFS탐색을 해보자.
- 1개의 구멍만 막으면 먹을 수 있는 돌 위치를 저장하고 해당 위치에 돌의 결과값을 음수로 저장한다.
- 구멍에 저장된 음수가 나중에 다른 적 그룹의 구멍과 겹치게 되면 먹는 돌 점수가 합산 계산되게 한다

```cpp
#include<iostream>
#include<vector>
#include<queue>
#include<algorithm>
#define point pair<int,int> 
using namespace std;
struct needOneStone{
    int value;
    point P;
    needOneStone(int v, point p) : value(v), P(p){}
    
    bool operator<(needOneStone a) const {
        return value < a.value;
    }
};

struct needTwoStone{
    int value;
    point P1;
    point P2;
    needTwoStone(int v, point p1, point p2) : value(v), P1(p1), P2(p2){}
};
vector<point> V0;
vector<needTwoStone> V2;

void BFS(int x, int y, int ** table){
    int numberOfPoints = 1;
    vector<point> v;
    table[y][x] = 3;
    queue<point> q;
    q.emplace(x,y);
    int dx[4] = {0, 1, 0, -1};
    int dy[4] = {1, 0, -1, 0};
    while ( !q.empty() ){
        point p = q.front();
        q.pop();
        for ( int d = 0; d < 4; d++ ){
            int newX, newY;
            newX = p.first + dx[d];
            newY = p.second + dy[d];
            if ( table[newY][newX] == 2 ){
                q.emplace(newX,newY);
                table[newY][newX] = 3;
                numberOfPoints++;
            }
            else if ( table[newY][newX] <= 0 && v.size() <= 2){
                bool checkDuplication = false;
                for( auto p_v : v ){
                    if ( p_v.first == newX && p_v.second == newY )
                        checkDuplication = true;
                }
                if (!checkDuplication)
                    v.emplace_back(newX,newY);
            }
        }
    }
    if ( v.size() == 1 ){
        if(table[v[0].second][v[0].first] ==  0)
            V0.emplace_back(v[0].first,v[0].second);
        table[v[0].second][v[0].first] -= numberOfPoints;
    }
    else if ( v.size() == 2 ){
        for( auto v2: V2)
            if(v2.P1 == v[0] && v2.P2 == v[1] || v2.P1 == v[1] && v2.P2 == v[0] ){
                numberOfPoints+=v2.value;
            }
        V2.emplace_back(numberOfPoints,v[0],v[1]);
    }
}

void findEnemy(int row, int column, int** table){
    for ( int y = 1; y <= column; y++){
        for ( int x = 1; x<= row; x++){
            if (table[y][x] == 2){
                BFS(x,y,table);
            }
        }
    }
}

// 1개 짜리 2개 조합 최대값
int find2min(int** table){
    int min1=0, min2=0;
    for(int i=0; i<V0.size(); i++){
        int value = table[V0[i].second][V0[i].first];
        if(min1 > value){
            if(min2 > value){
                min1 = min2;
                min2 = value;
            }
            else{
                min1 = value;
            }
        }
    }
    return min1+min2;
}

int findMax(int** table){
    int maxValue = -find2min(table); // 1개짜리 2개 조합 최대값
    if(V2.size()!=0) 
        for( auto v2: V2 ){
            maxValue = max(maxValue,v2.value-table[v2.P1.second][v2.P1.first]-table[v2.P2.second][v2.P2.first]);
            //table[v2.P1.second][v2.P1.first]
            //table[v2.P2.second][v2.P2.first]
            // 1개 짜리로 막힐 수 있는 가능성;!!
        }
    return maxValue;
}

int main(){
    int row, column;
    cin >> column >> row;
    int** table = (int**) malloc ( sizeof(int*) * (column+2) );
    for ( int y = 0; y <= column+1; y++){
        table[y] = (int*) malloc ( sizeof(int) * (row+2) );
        for ( int x = 0; x <= row+1; x++){
            if ( x == 0 || x == row+1 || y == 0 || y == column+1 )
                table[y][x] = 1;
            else{
                cin >> table[y][x];
            }
        }
    }
    findEnemy(row, column, table);
    cout << findMax(table);

}
```

```cpp
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second
typedef pair<int,int> pii;
int dx[4] = {1,-1,0,0};
int dy[4] = {0,0,1,-1};
int board[1002][1002];
bool vis[1002][1002];
int score1[1002][1002]; // score1[x][y] : (x,y)에 착수함으로서 딸 수 있는 돌의 수
pair<pii, pii> forced[1000002]; // forced[i] : i번째 그룹이 2군데 비어있을 경우 그 두 곳을 저장하는 변수
int area[1000002]; // i번째 그룹의 크기
int n,m;
int main(){
  ios::sync_with_stdio(0);
  cin.tie(0);
  scanf("%d%d",&n,&m);
  for(int i = 0; i < n; i++)
    for(int j = 0; j < m; j++)
      scanf("%d",&board[i][j]);
  int mark = 1e9; // 연결된 돌 주변에 빈 칸임을 나타내기 위해 사용하는 마크
  int idx = 3; // group idx
  vector<pair<pair<int,int>, pair<int,int> > > cand; // 2개를 두는 후보군
  for(int i = 0; i < n; i++){
    for(int j = 0; j < m; j++){
      if(vis[i][j] or board[i][j] != 2) continue;      
      pair<int,int> moves[2]; // 해당 영역을 메꾸기 위해 둬야하는 곳
      int movenum = 0; // (i,j)와 연결된 돌을 메꾸기 위해 둬야하는 곳의 수
      mark++;
      idx++;
      forced[idx] = {% raw %}{{-1,-1},{-1,-1}}{% endraw %};
      queue<pair<int, int> > Q;
      Q.push(make_pair(i,j));
      vis[i][j] = true;  
      while(!Q.empty()){
        area[idx]++;
        pair<int, int> cur = Q.front(); Q.pop();
        board[cur.X][cur.Y] = idx;              
        for(int dir = 0; dir < 4; dir++){
          int nx = cur.X+dx[dir];
          int ny = cur.Y+dy[dir];
          if(nx < 0 or nx >= n or ny < 0 or ny >= m) continue;
          if(board[nx][ny] != 1 and board[nx][ny] != 2 and board[nx][ny] != mark and board[nx][ny] != idx){ // board[nx][ny]가 1, 2, mark, idx가 아닐 경우 메꾸기 위해 둬야하는 곳이다.
            board[nx][ny] = mark;
            if(movenum > 2) continue; // 어차피 막아야하는 곳이 2개를 넘어 의미없는 영역일 경우에는 넘어감
            moves[movenum++] = make_pair(nx,ny);
          }
          if(vis[nx][ny] or board[nx][ny] != 2) continue;
          Q.push(make_pair(nx,ny));
          vis[nx][ny] = true;
        }
      }
      if(movenum == 1){
        score1[moves[0].X][moves[0].Y] += area[idx];
      }
      else if(movenum == 2){
        if(moves[0] > moves[1]) swap(moves[0], moves[1]);
        cand.push_back(make_pair(moves[0],moves[1]));        
        forced[idx] = {moves[0],moves[1]};
      }
    }
  }
  int ans = 0;
  // 1. 2개의 착수가 독립적으로 그룹을 죽일 경우
  int mx1 = 0, mx2 = 0;
  for(int i = 0; i < n; i++){
    for(int j = 0; j < m; j++){
      if(mx1 < score1[i][j]){
          mx2 = mx1;
          mx1 = score1[i][j];
      }
      else if(mx2 < score1[i][j]) mx2 = score1[i][j];
    }
  }
  ans = mx1+mx2;
  // 2. 2개의 착수가 그룹을 죽일 때 같이 쓰이는 경우
  for(int i = 0; i < cand.size(); i++){    
    pair<pair<int,int>, pair<int,int> > cur = cand[i];
    pair<int,int> mv1 = cand[i].X, mv2 = cand[i].Y;
    int val = score1[mv1.X][mv1.Y]+score1[mv2.X][mv2.Y];
    int occur[4] = {-1,-1,-1,-1};
    for(int dir = 0; dir < 4; dir++){
      int nx = mv1.X+dx[dir];
      int ny = mv1.Y+dy[dir];
      if(nx < 0 or nx >= n or ny < 0 or ny >= m) continue;
      if(board[nx][ny] == 0 or board[nx][ny] == 1 or board[nx][ny] >= 1e9) continue; // mark가 놓여있을 경우
      bool isused = 0;
      for(int dd = 0; dd < dir; dd++) if(board[nx][ny] == occur[dd]) isused = 1;
      if(isused) continue;
      int idx = board[nx][ny]; // (nx, ny)가 속한 그룹
      occur[dir] = idx;
      if(cur == forced[idx]) val += area[idx];
    }
    ans = max(ans,val);
  }
  printf("%d",ans);
}
```