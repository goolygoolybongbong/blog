---
layout: single
classes: wide
categories: 알고리즘
title: "Programmers - 탐욕법(Greedy)"
tags: [Programmers]
---

1. 체육복 ( [https://programmers.co.kr/learn/courses/30/lessons/42862](https://programmers.co.kr/learn/courses/30/lessons/42862))

```
int solution(int n, vector<int> lost, vector<int> reserve) {
int answer = 0;

vector<int> student(n + 1, 1);

for (int l : lost)
student[l] -= 1;

for (int r : reserve)
student[r] += 1;

for (int i = 1; i < n + 1; i++) {
if (student[i] == 0) {
if (student[i - 1] >= 2) {
student[i - 1] -= 1;
student[i] += 1;
}
else if (student[i + 1] >= 2) {
student[i + 1] -= 1;
student[i] += 1;
}
}
if (student[i] != 0)
answer += 1;
}

return answer;
}
```

vector<int> student(n + 1, 1) 부분에서 마지막 부분을 고려하지 못해 벡터가 사이즈 이상 값을 참조하도록 하여 vector out of range 에러가 나야하는데 채점 시스템에서는 나지 않아 오류가 계속 틀렸었다.

배열의 크기를 신경쓰자 마지막과 첫 부분에 어떻게 탐색하는 지 주의하자.

1. 조이스틱 ( [https://programmers.co.kr/learn/courses/30/lessons/42860?language=cpp](https://programmers.co.kr/learn/courses/30/lessons/42860?language=cpp))

아니 왜 이걸 먼저 풀었지?

커서의 이동을 최소화 하기위해 이전에 값을 바꾼 것을 기억하며 추적할 필요가 있다고 생각하여 prensent_name이라는 vetor<int>에 저장하였다.

우연하게도 값을 바꾸고 -> 이동 -> 검증의 순으로 반복문을 돌린것이 답이 되었으며 왜 맞는지 이해가 잘 안됨.

left를 right 구하는 식은 지저분하나 주의깊게 살펴봐야함

**첫 번쨰 풀이**

```
int solution(string name) {
int answer = 0;
int cursor = 0;
vector<char> present_name(name.size(), 'A');
bool isCompleted = false;
while (!isCompleted) {
if ((name[cursor] - 'A') < ('Z' - name[cursor] + 1)) {
answer += name[cursor] - 'A';
}
else {
answer += 'Z' - name[cursor] + 1;
}
present_name[cursor] = name[cursor];
for (int i = 1; i < name.size(); i++) {
int right = (cursor + i) % name.size();
int left = (cursor - i < 0) ? (name.size() + cursor - i) :  (cursor - i);
if (name[right] != present_name[right]) {
cursor = right;
answer += i;
break;
}
else if (name[left] != present_name[left]) {
cursor = left;
answer += i;
break;
}
}
isCompleted = true;
for (int i = 0; i < name.size(); i++) {
if (name[i] != present_name[i]) {
isCompleted = false;
break;
}
}
}

return answer;
}
```

처음 풀이는 탐욕적으로 cursor를 움직여 가장 가까이 있는 문자를 먼저 처리하여 가로 움직임을 구현하였다. 하지만 CANAAAAANAN 같은 경우 탐욕적으로 움직이면 왼쪽을 먼저 가게되는데 가로 이동을 7번이 아닌 8번 하게되어 비 효율적으로 이동하게 된다.

**두 번째 풀이**

```
int solution(string name) {
int answer = 0;
// find minimum cursor length
vector<int> nonA;
for (int i = 0; i < name.size(); i++) {
if (name[i] != 'A') {
nonA.push_back(i);
if ((name[i] - 'A') < ('Z' - name[i] + 1)) {
answer += name[i] - 'A';
}
else {
answer += 'Z' - name[i] + 1;
}
}
}

priority_queue<int, vector<int>, greater<int>> cursorMove;
int Left, Right;
if (!nonA.empty()) {
for (int i = 0; i < nonA.size() - 1; i++) {
Right = nonA[i];
Left = name.size() - nonA[i + 1];
cursorMove.push(Right * 2 + Left);
cursorMove.push(Left * 2 + Right);
}
if(!cursorMove.empty())
answer += cursorMove.top();
}

return answer;
}
```

모든 A가 아닌 지점을 파악과 동시에 세로 이동 횟수를 구하고 최소 가로 이동의 길이를 구하면 된다.

1. 큰 수 만들기 ( [https://programmers.co.kr/learn/courses/30/lessons/42883](https://programmers.co.kr/learn/courses/30/lessons/42883))

**나의 풀이**

먼저 K까지의 수 중 가장 큰 수를 찾고 K 이후의 수를 순회하며

이전에 찾은 큰 수보다 작거나 같은 경우

큰 수를 답에 넣고 이전에 찾은 가장 큰 수 이후 부터 답을 찾은 순회 지점까지의 가장 큰 수를 찾는다.

큰 수보다 작은 수가 없거나 K 가 0이하인 경우 답에 나머지 숫자들을 추가한다.

```
string solution(string number, int k) {
string answer = "";
char big = '0';
int pos, start = 0;

for (int i = 0; i < k; i++) {
if (big < number[i]) {
big = number[i];
pos = i;
}
}

for (int i = k; i < number.size(); i++) {
if ((number[i] <= big) && (k > 0)) {
k -= (pos - start);
if (k >= 0) {
start = pos + 1;
answer.push_back(big);
big = '0';
for (int j = pos + 1; j <= i; j++) {
if (big < number[j]) {
big = number[j];
pos = j;
}
}
}
}
else {
k = 0;
answer.push_back(number[i]);
}
}

return answer;
}
```

**깔끔한 풀이**

K번째 이후의 모든 숫자를 answer 변수에 담은 뒤

K -1 부터 0 까지의 number의 수를 순회하며

순회의 해당 수가 answer의 수보다 큰 경우 알맞은 위치에 놓이도록 한다.

역방향으로 순회하고 값을 swap함으로 써 문제에서 요구하는 최적의 값에 접근하는 것이 관건

number[0 ~ k -1] 삭제될 숫자들의 구역

answer[0 ~ answer.size() - k] 답

미리 떼어낸 number 중 살릴 숫자를 answer에 넘겨주고

answer를 순회하며 삭제할 숫자를 넘겨 받는다.

```
string solution2(string number, int k) {
string answer = "";
answer = number.substr(k);
// substr : number[k ~ number.size() -1]
for (int i = k - 1; i >= 0; i--) {
int j = 0;
do {
if (number[i] >= answer[j]) {
char temp = answer[j];
answer[j] = number[i];
number[i] = temp;
j++;
cout << answer << endl;
}
else {
break;
}
} while (j < (number.size() - k));
}

return answer;
}
```

1. 구명보트 ( [https://programmers.co.kr/learn/courses/30/lessons/42885](https://programmers.co.kr/learn/courses/30/lessons/42885))

이와 비슷한 문제를 예전에도 푼 적이 있었는데 큰 사람부터 보트에 태운다는 개념을 떠올리지 못했다.

**나의 풀이**

사람들의 무게 순으로 정렬한 뒤 순회하며 작은 무게와 큰 무게가 짝을 이룰 수 있으면 한 배에 태워서 답을 1 올리고 왼쪽 인덱스와 오른쪽 인덱스를 하나씩 이동 시켰다.

**올바른 풀이**

정렬한 배열에서 큰 사람부터 보트에 태우고 작은 사람이 탈 수 있으면 같이 태워 두 인덱스가 같아지면 반복을 중지한다.

1. 단속카메라 ( [https://programmers.co.kr/learn/courses/30/lessons/42884](https://programmers.co.kr/learn/courses/30/lessons/42884))

손으로 구간을 여러개 그려보며 풀면 문제를 좀 더 쉽게 접근할 수 있다. 어떤 때에 카메라를 새로 설치하는지 주의깊게 관찰하고 간략화 가능한지 생각하자.

사고의 깊이를 늘려야 겠다.

**나의 풀이**

먼저 진입점을 기준으로 오름차순 정렬을 한 뒤

현재 구간에서 진입위치가 이전 차량의 진출위치보다 작거나 같으면 카메라를 늘리지 않고 중복 되게 설치가 가능하고

이전 차량의 진출 위치보다 진입 위치가 큰 경우 새 카메라를 설치해야한다.

```
int solution(vector<vector<int>> routes) {
int answer = 1;
sort(routes.begin(), routes.end());
int left = routes[0][0], right = routes[0][1];
for (int i = 1; i < routes.size(); i++) {
if (right >= routes[i][0]) {
if (routes[i][1] < right) {
right = routes[i][1];
}
}
else {
right = routes[i][1];
answer++;
}
}
return answer;
}
```

**더 짧고 직설적인 풀이**

진출 위치를 기준으로 오름차순 정렬한 뒤

새 진입위치가 진출 위치보다 큰 경우 카메라를 새로 설치하고 새로운 진출 한계점을 설정한다.

```
bool cmp(vector<int> a, vector<int> b) { return a[1] < b[1]; }

int solution2(vector<vector<int>> routes) {
int answer = 0;
int limit = -30001;
sort(routes.begin(), routes.end(), cmp);
for (int i = 0; i < routes.size(); i++) {
if (limit < routes[i][0]) {
answer++;
limit = routes[i][1];
}
}
return answer;
}
```

1. 섬 연결하기 ( [https://programmers.co.kr/learn/courses/30/lessons/42861?language=cpp](https://programmers.co.kr/learn/courses/30/lessons/42861?language=cpp))

**나의 풀이**

낮은 비용의 다리를 우선으로 연결하고 사이클이 형성되는지 확인하고 사이클이 없으면 연결하고 있으면 연결을 취소한다.

이 후 모든 섬이 연결되어있나 확인하는 함수를 호출하여 모든 섬의 연결이 확인되면 끝낸다.

**올바른 풀이**

섬을 연결할 떄 가장 낮은 비용의 다리를 최우선으로 연결하며 출발지는 관계없이 중복되지 않도록 목적지만을 비교 검사하면서 최소 비용만 합하면 된다.

1. 저울 ( [https://programmers.co.kr/learn/courses/30/lessons/42886?language=cpp](https://programmers.co.kr/learn/courses/30/lessons/42886?language=cpp))

1번째 풀이 37분 - **효율성 실패 n^2**

무게 1부터 만들수 없는 수가 나올때까지 작은 추부터 이용하여 무게를 만들어감

2번째 풀이 1시간 40분 n

누적해서 작은 추 부터 사용해서 만들 수 있는 범위를 넘어서면

작은 추 부터 누적하여 더하다가 새로운 추가 누적된 추의 합보다 크면 중간에 만들 수 없는 수가 존재한다.

```
int solution(vector<int> weight) {
int answer = 1;
sort(weight.begin(), weight.end());
reverse(weight.begin(), weight.end());
//  big-O : n
for (int w : weight) {
if (w > answer)
break;
answer += w;
}
// big-O : n^2
while (true) {
int n = answer;
for (int w : weight) {
if (w <= n) {
n -= w;
if (!n) {
break;
}
}
}
if (n)
return answer;
answer += 1;
}
return answer + 1;
}
```

```
for (int w : weight) {
if (w > answer)
break;
answer += w;
}
```