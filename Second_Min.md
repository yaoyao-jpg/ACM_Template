// Problem: 次小生成树
// Contest: AcWing
// URL: https://www.acwing.com/problem/content/description/358/
// Memory Limit: 512 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
#define Endl endl
#define ENDL endl
#define _memset(a, i) memset(a, i, sizeof a)
#define _for(i, a, b) for(int i = a; i <= b; i++)
typedef long long LL;
typedef long long ll;
typedef pair<int, int> PII;
const LL mod = 1;
const int N = 3e5 + 10;
const int INF = 0x3f3f3f3f;
inline LL lowbit(int x){return x & -x;};

int head[N], ne[N * 2], wei[N * 2], edge[N * 2], idx;
int par[N][20], dep[N], d1[N][20], d2[N][20];
int n, m;
struct edge{
    int u, v, w;
    bool in = false;
    bool operator < (const edge &e) const{
        return w < e.w;
    }
} e[N * 2];
int fa[N]; //kruskal
int dd[N * 2];
int q[N];

void add(int u, int v, int w);
int find(int u);
void dfs(int u, int fa);
int lca(int a, int b, int w);
ll kruskal();
void bfs();

int main(){
    ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);   //就不能用scanf printf  putchar  puts了
    cin >> n >> m;
    _memset(head, -1);
    _for(i, 0, m - 1){
        int u, v, w;
        cin >> u >> v >> w;
        e[i].u = u, e[i].v = v, e[i].w = w;
    }
    ll ss = kruskal();
    // dfs(1, -1);
    _memset(dep, 0x3f3f3f3f);
    dep[0] = 0, dep[n] = 1;
    // bfs();
    dfs(n, -1);
    ll ans = 1e18;
    _for(i, 0, m - 1){
        if(!e[i].in){
            int u = e[i].u, v = e[i].v, w = e[i].w;
            int t = lca(u, v, w);
            ans = min(ans, ss + w - t);
        }
    }
    cout << ans << endl;
    return 0;
}

void add(int u, int v, int w){
    edge[idx] = v, ne[idx] = head[u], wei[idx] = w, head[u] = idx++;
}

int find(int u){
    if(fa[u] == u) return u;
    else return fa[u] = find(fa[u]);
}

ll kruskal(){
    sort(e, e + m);
    _for(i, 0, n) fa[i] = i;
    ll ans = 0;
    _for(i, 0, m - 1){
        int pu = find(e[i].u), pv = find(e[i].v), w = e[i].w;
        if(pu != pv){
            ans += (ll)w;
            fa[pu] = pv;
            e[i].in = true;
            add(e[i].u, e[i].v, w), add(e[i].v, e[i].u, w);
        }
    }
    return ans;
}

int lca(int a, int b, int w){
    if(dep[a] < dep[b]) swap(a, b);
    int cnt = 0;
    for(int k = 16; k >= 0; k--){
        if(dep[par[a][k]] >= dep[b]){
            dd[++cnt] = d1[a][k], dd[++cnt] = d2[a][k];
            a = par[a][k];
        }
    }
    if(a != b){
        for(int k = 16; k >= 0; k--){
            if(par[a][k] != par[b][k]){
                dd[++cnt] = d1[a][k], dd[++cnt] = d2[a][k];
                dd[++cnt] = d1[b][k], dd[++cnt] = d2[b][k];
                a = par[a][k], b = par[b][k];
            }
        }
        dd[++cnt] = d1[a][0], dd[++cnt] = d2[a][0];
        dd[++cnt] = d1[b][0], dd[++cnt] = d2[b][0];
        a = par[a][0], b = par[b][0];
    }
    int dist1 = -INF, dist2 = -INF;
    _for(i, 1, cnt){
        if(dd[i] > dist1) dist2 = dist1, dist1 = dd[i];
        else if(dd[i] > dist2 && dd[i] < dist1) dist2 = dd[i];
    }
    if(w > dist1) return dist1;
    if(w > dist2) return dist2;
    return 0;
}

void bfs() {
    q[0] = n;
    int hh = 0, tt = 0;
    while (hh <= tt) {
        int t = q[hh ++];
        for (int i = head[t]; i != -1; i = ne[i]) {
            int j = edge[i];
            if (dep[j] > dep[t] + 1) {
                dep[j] = dep[t] + 1;  // bfs更新层次
                q[++ tt] = j;  // 宽度优先
                par[j][0] = t;  // 更新一下 fa(i, 0);
                d1[j][0] = wei[i], d2[j][0] = -INF;  // 更新一下d1, d2
                // 只有一条边不存在次长路

                // 都已经更新到这个点了, 之前路径上的距离也都算出来了
                // 所以都可以更新出d1, d2 fa,
                for (int k = 1; k <= 16; k ++) {  // 开始跳
                    int anc = par[j][k - 1];  // 这anc是跳一半的位置
                    par[j][k] = par[anc][k - 1];  // 求得跳2^k之后的位置

                    // ddd存储的是j开始跳一半的数据
                    // d1[j][k - 1] 跳一般的最大边
                    // d2[j][k - 1] 跳一半的次大边
                    // d1[anc][k - 1]  跳另一半的最大边
                    // d2[anc][k - 1]  跳另一半的次大边
                    int ddd[4] = {d1[j][k - 1], d2[j][k - 1], 
                                       d1[anc][k - 1], d2[anc][k - 1]};

                    d1[j][k] = d2[j][k] = -INF;  // 整条路径的最大值和次大值先初始化为-INF
                    for (int u = 0; u < 4; u ++) {
                        // 更新一下最大值和次大值
                        int d = ddd[u];
                        if (d > d1[j][k]) d2[j][k] = d1[j][k], d1[j][k] = d;
                        else if (d != d1[j][k] && d > d2[j][k]) d2[j][k] = d;
                    }
                }
            }
        }
    }
}

void dfs(int u, int fa){
    for(int i = head[u]; ~i; i = ne[i]){
        int v = edge[i];
        if(v != fa){
            dep[v] = dep[u] + 1;        //tm这里写错了，艹
            par[v][0] = u;
            d1[v][0] = wei[i], d2[v][0] = -INF;
            _for(k, 1, 16){
                int anc = par[v][k - 1];
                par[v][k] = par[anc][k - 1];
                int ddd[4] = {d1[v][k - 1], d2[v][k - 1], d1[anc][k - 1], d2[anc][k - 1]};
                d1[v][k] = d2[v][k] = -INF;
                for(int it = 0; it < 4; it++){
                    int d = ddd[it];
                    if(d > d1[v][k]) d2[v][k] = d1[v][k], d1[v][k] = d;
                    else if(d != d1[v][k] && d > d2[v][k]) d2[v][k] = d;
                }
            }
            dfs(v, u);
        }
    }
}