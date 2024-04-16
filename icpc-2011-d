# ICPC-2011-D そして，いくつになった？ 解説

2024/04/16 21D8104014E 山田 拓哉

### 問題文

有名な一人遊びゲームの作家であるソリタリウス氏は，今日もまた新しいアイデアを思いついた． ソリタリウス氏の新しいゲームでは，さまざまな色と大きさの平らな円盤を使う．

まず最初に，全部の円盤をテーブル中央付近にばらまく． 別の円盤が上に重なっていない円盤の中に同じ色のものが 2 枚あれば，それらを同時に取り除くことができる． なお，円盤同士が 1 点で接しているだけなら，それらを重なっているとはみなさない．

<div align="center"><img src="https://judge.u-aizu.ac.jp/onlinejudge/IMAGE2/1175.png"></img><br/>図 D-1: テーブル上の7枚の円盤</div>

たとえば，図 D-1 の場合，まず最初に取り除けるのは 2 枚の黒い円盤だけである． これらを取り除くことで，今度は 2 枚の白い円盤を取り除けるようになる． しかし，グレーの円盤は決して取り除くことができない．

この問題では，与えられた円盤の色，大きさ，初期の位置関係から，最大何枚の円盤を取り除くことができるか計算するプログラムを作成して欲しい．

#### Input

入力は複数のデータセットからなり，それぞれが以下のような形式でテーブル上に円盤をばらまいた直後の状態を表す．

```
n
x1 y1 r1 c1
x2 y2 r2 c2
...
xn yn rn cn
```

最初の行の n は円盤の枚数を表す正整数である． 続く n 行は，それぞれが空白文字 1 個で区切られた 4 個の整数からなり，n 枚の円盤の色，大きさ，初期の位置関係を次のように表す．

- (xi, yi), ri, ci は i 番目の円盤の中心の xy 座標，半径，色番号をそれぞれ表し，
- i 番目の円盤が j 番目の円盤の上に重なるときには，必ず i < j が成り立つ．

色番号は 1 以上 4 以下であり，一つのデータセット中に同じ色の円盤は高々 6 枚しか出現しないと仮定して良い． また，円盤の中心の xy 座標は常に 0 以上 100 以下，半径は常に 1 以上 100 以下と仮定して良い．

入力の終わりは 1 個のゼロで示される．

#### Output

各データセットについて，取り除ける円盤の最大枚数をそれぞれ 1 行に出力しなさい．

#### Sample Input

```
4
0 0 50 1
0 0 50 2
100 0 50 1
0 0 100 2
7
12 40 8 1
10 40 10 2
30 40 10 2
10 10 10 1
20 10 9 3
30 10 8 3
40 10 7 3
2
0 0 100 1
100 32 5 1
0
```

#### Output for the Sample Input

```
2
4
0
```

### 解説

#### 使用アルゴリズム

- 深さ優先探索
- メモ化再帰

#### 状況整理

入力の条件を見ると、色番号が最大 4 種類、各色に対して円盤の数は最大 6 種類と各データセットにおける円盤の枚数が少ないことがわかり、次のことが言える。

- 全探索で円盤の取り方をすべて列挙できそう。

  円盤が実際にとれるかにかかわらず、各円盤に対して取るか取らないかの 2 通りの状態が最大で存在するので、円盤のとり方は`2^24/2 = 16,777,216/2 = 8,388,608`の状態がある。

  <div><img src="icpc-2011-d-1.jpg"></img><br>状態遷移の一例</div>

- 各状態のメモ化をして、計算量を改善する必要がある。

  円盤のとり方の状態を二回以上探索してしまうと、計算量がふえてしまうため。

#### 解法 O(1)

1. 現在の状態における、取り除いた円盤の数とそれまでの最大値を比較し、更新。

   ans と cnt の比較と更新: O (1)

2. 現在の状態に対して、一番上に積まれている円盤を取得。

   現在ある円盤の、組 (i, j) の選び方: O (n^2)

3. 一番上にある円盤の組の色が同じなら、円盤を取り除く。

   現在ある円盤の、組 (i, j) の選び方: O (n^2)

4. 状態をメモするための識別子を生成。

   円盤 i が取り除かれているかどうかの確認: O(n)

5. 次の状態へ遷移。

これを、各データセットに対して行えばよい。

各状態の総数 \* 一つの状態に対する計算量 = 8,388,688 \_ (24^3 + 24^2) = 10^10

### 回答例

```
/*
* 2024/04/16 21D8104014E 山田拓哉
*/

#include<bits/stdc++.h>
using namespace std;
#define rep(i, n) for (int i = 0; i < n; i++)

int n;
int ans;
// 円盤の取り除き方の候補は高々2^(4*6)通り
vector<bool> memo((1<<28), false);

struct Circle{
    double x, y, r;
    int c;
};

// 2点間の距離
double dist(int x1, int y1, int x2, int y2) {
    return sqrt((x1-x2)*(x1-x2) + (y1-y2)*(y1-y2));
}

// 円盤が重なっているかどうか
bool is_stack(Circle c1, Circle c2) {
    if (dist(c1.x, c1.y, c2.x, c2.y) < c1.r + c2.r) return true;
    return false;
}

// 各遷移状態を表す整数を生成
int gen_code(vector<bool> rm) {
    int code = 0;
    for (int i = 0; i < n; i++) {
        if (rm[i]) {
            code += (1<<i);
        }
    }
    return code;
}

void dfs(vector<Circle> cl, vector<bool> rm, int cnt) {
    if (cnt == n || ans == n) return;
    ans = max(ans, cnt);
    // 一番上にある円盤はtrue
    vector<bool> is_top(n, false);
    bool is_stacked;

    // 一番上にある円盤を取得
    for (int i = n-1; i >= 0; i--) {
        // すでに取り除かれている円盤はスキップ
        if (rm[i]) continue;
        is_stacked = false;
        for (int j = i-1; j >= 0; j--) {
            if (rm[j]) continue;
            if (is_stack(cl[i], cl[j])) {
                is_stacked = true;
                break;
            }
        }
        if (!is_stacked) {
            is_top[i] = true;
        }
    }


    for (int i = 0; i < n-1; i++) {
        for (int j = i+1; j < n; j++) {
            // 一番上にある円盤同士が同じ色の場合
            if (is_top[i] && is_top[j] && cl[i].c == cl[j].c) {
                if (cnt == n-2) {
                    ans = n;
                    return;
                }

                // 現在の深さの状態はかえない
                vector<bool> tmp_rm = rm;
                tmp_rm[i] = true;
                tmp_rm[j] = true;

                int code = gen_code(tmp_rm);
                if (!memo[code]) {
                    memo[code] = true;
                    dfs(cl, tmp_rm, cnt+2);
                }
            }
        }
    }
}

int main() {
    while(1) {
        cin >> n;
        if(n == 0) break;
        vector<Circle> cl(n);
        rep(i, n) {
            cin >> cl[i].x >> cl[i].y >> cl[i].r >> cl[i].c;
        }
        // 初期化
        ans = 0;
        memo = vector<bool>((1<<28), false);
        vector<bool> rm(n, false);
        dfs(cl, rm, 0);
        cout << ans << endl;
    }
}
```

### 関連サイト

[AIZU ONLINE JUDGE( 問題を解きたい人用 )](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1175&lang=jp)
