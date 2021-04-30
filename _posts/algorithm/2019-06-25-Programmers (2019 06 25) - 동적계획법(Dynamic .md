---
layout: single
classes: wide
categories: 알고리즘
title: "Programmers - 동적계획법(Dynamic Programming)"
tags: [Programmers]
---

1. N으로 표현( [https://programmers.co.kr/learn/courses/30/lessons/42895](https://programmers.co.kr/learn/courses/30/lessons/42895))

**나의 풀이**

첫 번째, 수를 만들고 사칙연산을 미리 계산해 재귀 호출하였다. 4 17 (4/4)+(4*4) 덧샘예외발생

두 번째, 역폴란드표기법식으로 캐싱하면 위의 문제가 해결될거라 생각했는데 너무 복잡함

**올바른 풀이**

먼저 기반 수 N, NN, ..., NNNNNNNN 을 만들고 해당 수를 숫자 갯수를 기준으로 나뉜 배열에 저장하고 결과값 해쉬 키 최소길이 값인 cache에 저장한다.

1. 타일 장식물( [https://programmers.co.kr/learn/courses/30/lessons/43104](https://programmers.co.kr/learn/courses/30/lessons/43104))

**나의 풀이**

```
long long solution(int N) {
if (N == 1) return 4;
if (N == 2) return 6;
long long answer = 0;
vector<long long> t(N);
t[0] = t[1] = 1;
for (int i = 2; i < N; i++) {
t[i] = t[i - 1] + t[i - 2];
}
answer = (t[N - 1] + t[N - 2]) * 2 + (t[N - 2] + t[N - 3]) * 2;
return answer;
}
```

**개선**

```
answer = 4*t[N-1] + 2*t[N-2];
```

1. 정수 삼각형( [https://programmers.co.kr/learn/courses/30/lessons/43105](https://programmers.co.kr/learn/courses/30/lessons/43105))

**나의 풀이**

삼각형의 cache의 행마다 양 끝값을 먼저 구하고 이전 행의 올 수 있는 최대값과 합쳐서 행의 최대값을 구한다.

```
int solution(vector<vector<int>> triangle) {
int answer = 0;
vector<vector<int>> cache(triangle.size());
cache[0].push_back(triangle[0][0]);
for (int i = 1; i < triangle.size(); i++) {
cache[i].resize(i + 1);
// 양 끝 계산
cache[i][0] += cache[i - 1][0] + triangle[i][0];
cache[i][i] += cache[i - 1][i - 1] + triangle[i][i];
// 중간 계산
for (int j = 1; j < i; j++) {
cache[i][j] += max(cache[i - 1][j], cache[i - 1][j - 1]) +  triangle[i][j];
}
}
for (int a : cache.back())
if (a > answer)
answer = a;
return answer;
}
```

1. 등굣길( [https://programmers.co.kr/learn/courses/30/lessons/42898](https://programmers.co.kr/learn/courses/30/lessons/42898))

**나의 풀이**

BFS를 이용해 주변 지역을 먼저 이동하며 경로의 수와 경로의 길이를 계산하며 이동하여 최소길이를 유지하며 경로의 수를 파악하였다.

```
int solution(int m, int n, vector<vector<int>> puddles) {
int answer = 0;
const int DIV = 1000000007;
vector<vector<pair<int, int>>> map(n + 1, vector<pair<int, int>>(m + 1,  make_pair(numeric_limits<int>::max(), 0)));
for (vector<int> p : puddles) {
map[p[1]][p[0]].first = -1;
map[p[1]][p[0]].second = 0;
}
map[0][0].first = 0;
map[0][0].second = 1;
deque<vector<int>> que;

que.push_back(vector<int>{1, 1, 0, 0});
while (!que.empty()) {
vector<int> t = que.front();
que.pop_front();
if (!t[0] || !t[1] || t[0] > m || t[1] > n || map[t[1]][t[0]].first  == -1) {
continue;
}
else if (map[t[3]][t[2]].first + 1 > map[t[1]][t[0]].first) {
continue;
}
else if (map[t[3]][t[2]].first + 1 == map[t[1]][t[0]].first) {
map[t[1]][t[0]].second = (map[t[1]][t[0]].second +  map[t[3]][t[2]].second) % DIV;
continue;
}
else {
map[t[1]][t[0]].first = map[t[3]][t[2]].first + 1;
map[t[1]][t[0]].second = map[t[3]][t[2]].second;
que.push_back(vector<int>{t[0] + 1, t[1], t[0], t[1]});
que.push_back(vector<int>{t[0], t[1] + 1, t[0], t[1]});
que.push_back(vector<int>{t[0] - 1, t[1], t[0], t[1]});
que.push_back(vector<int>{t[0], t[1] - 1, t[0], t[1]});
}
}
answer = map[n][m].second;
return answer;
}
```

**다른사람 풀이**

항상 오른쪽과 아래로만 이동한다는 가정하에 타일을 이동하며 위와 왼쪽 블럭의 경로수만 더하면 쉽게 구해진다.

```
#include <string>
#include <vector>
#include <queue>
#define DIVISION 1000000007
using namespace std;

bool isPuddle[100][100];
int map[100][100];

int solution(int m, int n, vector<vector<int>> puddles) {
for(vector<int> elem : puddles){
isPuddle[elem[1]-1][elem[0]-1] = true;
}
bool flag = true;
for(int i=0; i<m; i++){
if(isPuddle[0][i]) flag=false;
if(flag) map[0][i] = 1;
}

flag = true;
for(int i=0; i<n; i++){
if(isPuddle[i][0]) flag = false;
if(flag) map[i][0] = 1;
}

for(int i=1; i<n; i++){
for(int j=1; j<m; j++){
if(!isPuddle[i][j])
map[i][j] = (map[i][j-1] + map[i-1][j]) % DIVISION;
}
}

int answer = map[n-1][m-1];
return answer;
}
```

1. 카드게임( [https://programmers.co.kr/learn/courses/30/lessons/42896](https://programmers.co.kr/learn/courses/30/lessons/42896))

**나의 처음 풀이**

왼쪽 카드와 오른쪽 카드의 인덱스를 2차원 배열을 만들고 큐를 사용한 BFS를 이용해 인접한 카드를 우선적으로 탐색을 한 후 경로마다 점수를 기록하며 최대 점수를 반환하게 하였다. 하지만 모든 정점을 방문하기에 2000X2000개의 배열을 모두 방문하기에는 시간이 너무 오래걸린다.

```
int solution(vector<int> left, vector<int> right) {
int answer = 0;
vector<vector<int>> cache(left.size(), vector<int>(right.size()));
deque<vector<int>> que;
que.push_back(vector<int>{0, 0, 0, 0});
while (!que.empty()) {
vector<int> t = que.front();
que.pop_front();
if (t[0] == left.size() || t[1] == right.size() || cache[t[2]][t[3]]  < answer)
continue;
if (left[t[0]] > right[t[1]]) {
if (cache[t[0]][t[1]] < (cache[t[2]][t[3]] + right[t[1]])) {
cache[t[0]][t[1]] = (cache[t[2]][t[3]] + right[t[1]]);
que.push_back(vector<int>{t[0], t[1] + 1, t[0],  t[1]});
}
}
else {
if (cache[t[0]][t[1]] < cache[t[2]][t[3]]) {
cache[t[0]][t[1]] = cache[t[2]][t[3]];
}
que.push_back(vector<int>{t[0] + 1, t[1], t[0], t[1]});
que.push_back(vector<int>{t[0] + 1, t[1] + 1, t[0], t[1]});
}
answer = max(answer, cache[t[0]][t[1]]);
}
return answer;
}
```

**개선한 풀이**

인터넷에서 찾은 카드의 상태에 따라 값이 변함 = DFS, 최대값 출력 = DP라는 힌트를 활용하여 DFS와 DP를 이용하여 코드를 작성하였다. 점수를 얻을 수 있을때는 점수를 얻고 이외에는 2개의 상황 중 최대값을 얻을 수 있는 지점의 점수를 가져오도록 한다. 솔직히 풀어놓고 왜 풀리는지 아직도 완전히 잘 이해가 되지 않는다.

```
vector<vector<int>> cache;
vector<int> LEFT, RIGHT;
int lim;

int dp(int L, int R) {
if (L == lim || R == lim)
return 0;

int& ret = cache[L][R];

if (ret != -1)
return ret;

if (LEFT[L] > RIGHT[R]) {
ret = RIGHT[R] + dp(L, R + 1);
}
else {
ret = max(dp(L + 1, R), dp(L + 1, R + 1));
}

return ret;
}

int solution(vector<int> left, vector<int> right) {
int answer = 0;
LEFT = left;
RIGHT = right;
lim = left.size();
cache.resize(lim, vector<int>(lim, -1));

answer = dp(0, 0);
return answer;
}
```

1. 도둑질 ( [https://programmers.co.kr/learn/courses/30/lessons/42897](https://programmers.co.kr/learn/courses/30/lessons/42897))

**나의 풀이**

최대값을 구하기 위해 DP를 이용하며, 상태에 따라 값이 변하므로 DFS를 이용하였다. 처음 집을 방문한 경우와 그렇지 않은 경우를 생각하여 2번씩 순회하여 풀었으나 cache를 재설정하여 약간의 시간을 소요하게 됨

```
int dp(int pos) {
if (pos >= MONEY.size())
return 0;

int& ret = cache[pos];

if (ret != -1)
return ret;

ret = max(dp(pos + 1), dp(pos + 2) + MONEY.at(pos));

return ret;
}

int solution(vector<int> money) {
int answer = 0;
MONEY.assign(money.begin(), money.end());

int t = MONEY[0];
MONEY.pop_front();
cache.resize(MONEY.size());
for (int& c : cache)
c = -1;
int t1 = dp(0);

MONEY.pop_back();
MONEY.push_front(t);
for (int& c : cache)
c = -1;
int t2 = dp(0);
answer = max(t1, t2);
return answer;
}
```

**개선한 풀이**

재귀적으로 풀지 않고 반복문으로 두 가지 경우의 배열을 만들어 앞에서 부터 차례대로 더하며 풀 수도 있다. 이 경우에는 cache를 재설정을 하지 않아 더 빠르게 풀 수 있다.

```
int solution(vector<int> money) {
int answer = 0;

vector<int> dp1;
vector<int> dp2;

dp1.reserve(money.size());
dp2.reserve(money.size());

dp1.push_back(money[0]);
dp1.push_back(max(money[0], money[1]));

dp2.push_back(0);
dp2.push_back(money[1]);

for (int i = 2; i < money.size() - 1; i++) {
int val = max(dp1[i - 2] + money[i], dp1[i - 1]);
dp1.push_back(val);
}

int maxIdx = money.size() - 1;
dp1.push_back(max(dp1[maxIdx - 2], dp1[maxIdx - 1]));

for (int i = 2; i < money.size(); i++) {
int val = max(dp2[i - 2] + money[i], dp2[i - 1]);
dp2.push_back(val);
}

return max(dp1[money.size() - 1], dp2[money.size() - 1]);
}
```

1. 서울에서 경산까지 ( [https://programmers.co.kr/learn/courses/30/lessons/42899](https://programmers.co.kr/learn/courses/30/lessons/42899)) : 57분

**나의 풀이**

재귀를 사용한 DP를 이용해 cache의 인덱스를 도시와 누적시간으로 하여 최대값을 저장하였다.

```
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

vector<vector<int>> cache;
vector<vector<int>> TRAVEL;
int Klim;

int dp(int city, int accu) {
if (accu > Klim)
return -987645321;
if (city >= TRAVEL.size())
return 0;

int& ret = cache[city][accu];

if (ret != -1)
return ret;

ret = max(dp(city + 1, accu + TRAVEL[city][0]) + TRAVEL[city][1],
dp(city + 1, accu + TRAVEL[city][2]) + TRAVEL[city][3]);

return ret;
}

int solution(int K, vector<vector<int>> travel) {
int answer = 0;

Klim = K;
TRAVEL = travel;
cache.resize(travel.size(), vector<int>(K + 1, -1));
answer = dp(0, 0);

return answer;
}
```

**다른 풀이**

반복문으로 2차원 배열을 순회하며 다음 도시에 금액을 저장하며 최대 값을 찾는다.

```
#include <string>
#include <vector>

using namespace std;
int d[101][100001]; //d[i][j] : 도시 i까지 j이내의 시간에 이동하면서 벌 수 있는 최대 금액

int visit[101][100001];
int max(int a, int b)
{
return a > b ? a : b;
}

int solution(int K, vector<vector<int>> travel)
{
int answer = 0;
visit[0][0] = 1;
for (int city = 0; city < travel.size(); city++)
{
for (int accu = 0; accu <= K; accu++)
{
if (visit[city][accu] == 0)
continue;
if (accu + travel[city][0] <= K)
{
// 최대금액[다음도시][누적 + 도보시간] = 최대값(최대금액[다음도시][누적 + 도보시간] (자기자신), 금액[현재도시][누적] + 현재도시 도보모집액)
d[city + 1][accu + travel[city][0]] = max(d[city + 1][accu + travel[city][0]], d[city][accu] + travel[city][1]);
// 방문여부[다음도시][누적 + 도보시간] = 1
visit[city + 1][accu + travel[city][0]] = 1;
}
if (accu + travel[city][2] <= K)
{
d[city + 1][accu + travel[city][2]] = max(d[city + 1][accu + travel[city][2]], d[city][accu] + travel[city][3]);
visit[city + 1][accu + travel[city][2]] = 1;
}
}
}
for (int i = 0; i <= K; i++)
{
answer = max(answer, d[travel.size()][i]);
}
return answer;
}
```