#最大流最小割
#Dinic

拆点思想：比如某一个点只能经过一次，则将之拆成了两个点，头和尾，头到尾的距离设定为1，用网络流跑则保证了这个点只经过一次

	#include<bits/stdc++.h> 
	using namespace std;
	#define _for(i, a, b) for(int i = a; i <= b; i++)
	#define _memset(a, b) memset(a, b, sizeof a)
	typedef long long ll;
	typedef long long LL;
	typedef pair<int, int> PII;
	const int N = 2e5 + 5;
	const int inf = 0x3f3f3f3f;
	
	int n, m, s, t;
	int head[N], ne[N], edge[N], wei[N], idx;
	int cur[N], dep[N];
	
	void add(int u, int v, int w);
	ll dfs(int u, ll limit);
	bool bfs();
	ll dinic();
	
	int main(){
		std::ios::sync_with_stdio(false);
		cin.tie(0), cout.tie(0);	
	    _memset(head, -1);
	    cin >> n >> m >> s >> t;
	    _for(i, 1, m){
	        int a, b, c;
	        cin >> a >> b >> c;
	        add(a, b, c);
	        add(b, a, 0);
	    }
	    cout << dinic() << endl;
	    return 0;
	}
	
	void add(int u, int v, int w){
	    edge[idx] = v, ne[idx] = head[u], wei[idx] = w, head[u] = idx++;
	}
	
	ll dfs(int u, ll limit){
	    int low = 0;
	    if(u == t) return limit;
	    for(int i = head[u]; ~i && low < limit; i = ne[i]){
	        cur[u] = i;
	        int v = edge[i];
	        if(wei[i] > 0 && dep[v] == dep[u] + 1){
	            ll t = dfs(v, min(limit - low, (ll)wei[i]));
	            if(!t) dep[v] = -1;
	            wei[i] -= t, wei[i ^ 1] += t;
	            low += t;
	        }
	    }
	    return low;
	}
	
	bool bfs(){
	    _memset(dep, -1);
	    queue<int> q;
	    q.push(s), cur[s] = head[s], dep[s] = 0;
	    while(!q.empty()){
	        int u = q.front();
	        q.pop();
	        for(int i = head[u]; ~i; i = ne[i]){
	            int v = edge[i];
	            if(wei[i] > 0 && dep[v] == -1){
	                int v = edge[i];
	                cur[v] = head[v];
	                dep[v] = dep[u] + 1;
	                if(v == t) return 1;
	                q.push(v);
	            }
	        }
	    }
	    return 0;
	}
	
	ll dinic(){
	    ll max_flow = 0;
	    while(bfs()){
	        int tem;
	        while(tem = dfs(s, inf)) max_flow += tem;
	    }
	    return max_flow;
	}


#费用流 spfa+EK

	#include<bits/stdc++.h> 
	using namespace std;
	#define _for(i, a, b) for(int i = a; i <= b; i++)
	#define _memset(a, b) memset(a, b, sizeof a)
	#define endl '\n'
	#define IOS 	std::ios::sync_with_stdio(false); cin.tie(0), cout.tie(0);
	typedef long long ll;
	typedef long long LL;
	typedef pair<int, int> PII;
	const int N = 5e4 + 5;
	const int INF = 0x3f3f3f3f;
	
	int n, m, S, T;
	int head[N], ne[N << 1], wei[N << 1], cost[N << 1], edge[N << 1], idx;
	int pre[N], incf[N], d[N];
	bool st[N];
	
	void add(int u, int v, int w, int c);
	bool spfa();
	void EK(int &flow, int &cost_sum);
	
	int main(){
	    IOS
	    cin >> n >> m >> S >> T;
	    _memset(head, -1);
	    _for(i, 1, m){
	        int u, v, w, c;
	        cin >> u >> v >> w >> c;
	        add(u, v, w, c);
	    }
	    int flow, cos;
	    EK(flow, cos);
	    cout << flow << ' ' << cos << endl;
	    return 0;
	}
	
	void add(int u, int v, int w, int cos){
	    edge[idx] = v, ne[idx] = head[u], wei[idx] = w, cost[idx] = cos, head[u] = idx++;
	    edge[idx] = u, ne[idx] = head[v], wei[idx] = 0, cost[idx] = -cos, head[v] = idx++;
	}
	
	bool spfa(){
	    _memset(d, INF);
	    _memset(incf, 0);
	    queue <int> q;
	    q.push(S);
	    d[S] = 0, incf[S] = INF;
	    while(q.size()){
	        int u = q.front();
	        q.pop();
	        st[u] = false;
	        for(int i = head[u]; ~i; i = ne[i]){
	            int v = edge[i];
	            if(wei[i] && d[v] > d[u] + cost[i]){
	                d[v] = d[u] + cost[i];
	                pre[v] = i;
	                incf[v] = min(wei[i], incf[u]);
	                if(!st[v]){
	                    q.push(v);
	                    st[v] = true;
	                }
	            }
	        }
	    }
	    return incf[T] > 0;
	}
	
	void EK(int &flow, int &cost_sum){
	    flow = cost_sum = 0;
	    while(spfa()){
	        int t = incf[T];
	        flow += t, cost_sum += t * d[T];
	        for(int i = T; i != S; i = edge[pre[i] ^ 1])    // pre记录的是线段，所以要该线段的反向线段的终点
	            wei[pre[i]] -= t, wei[pre[i] ^ 1] += t;
	    }
	}

#树的直径

#Solution 1
通过两遍dfs求得  第一次dfs从任意起点开始求得树的直径的其中一个端点，第二次从该端点开始搜直径的另外一个端点

	#include<bits/stdc++.h>
	using namespace std;
	const int N = 1e5 + 5;
	
	int n;
	int head[N], edge[N << 1], ne[N << 1], idx;
	int dis[N], c; // c代表直径的某一端点
	
	void dfs(int u, int par);
	void add(int u, int v);
	
	int main(){
	    memset(head, -1, sizeof head);
	    cin >> n;
	    for(int i = 1; i < n; i++){
	        int u, v;
	        add(u, v), add(v, u);
	    }
	    dfs(1, 0);
	    cout << c << ' ';
	    dis[c] = 0, dfs(c, 0);
	    cout << c << endl << dis[c] << endl;
	    return 0;
	}
	
	void dfs(int u, int par){
	    for(int i = head[u]; ~i; i = ne[i]){
	        int v = edge[i];
	        if(v == par) continue;
	        dis[v] = dis[u] + 1;
	        if(dis[v] > dis[c]) c = v;
	        dfs(v, u);
	    }
	}

#Solution 2
树形DP，可以求带权值的树的直径

	void dfs(int u, int fa){
	    for(int i = head[u]; ~i; i = ne[i]){
	        int v = edge[i];
	        if(v == fa) continue;
	        dfs(v, u);
	        diameter = max(diameter, d[u] + d[v] + wei[i]);
	        d[u] = max(d[u], d[v] + wei[i]);
	    }
	}

#LCA

两种方法都来自GCPC


大部分情况，倍增法和tarjan是一样的，但是倍增法可以用来求两点到齐公共祖先的路径上的路径最大值


#Solution 1 by Tarjan

	#include<bits/stdc++.h> 
	using namespace std;
	#define _for(i, a, b) for(int i = a; i <= b; i++)
	#define _memset(a, b) memset(a, b, sizeof a)
	#define endl '\n'
	#define IOS 	std::ios::sync_with_stdio(false); cin.tie(0), cout.tie(0);
	typedef long long ll;
	typedef long long LL;
	typedef pair<int, int> PII;
	// const int N = 2e3 + 5;
	// const int M = 1e6 + 5;
	const int N = 1e3 + 5;
	const int M = 1e6 + 50;
	const int INF = 0x3f3f3f3f;
	
	int h, w;
	int gra[N][N];
	int q;
	string str[N];
	int head[M], ne[M << 2], edge[M << 2], idx;
	vector<PII> que[M];
	int ans[M];
	int par[M];
	int dist[M];
	int st[M];
	
	void add(int u, int v);
	int find(int x);
	void dfs(int u, int fa);
	void tarjan(int u);
	
	int main(){
	    IOS
	    _memset(head, -1);
	    cin >> h >> w;
	    getline(cin, str[0]);
	    _for(i, 0, h){
	        getline(cin, str[i]);  
	        if(i == 0) continue;
	        for(int j = 2; j <= 2 * w - 2; j += 2){
	            int ha1, ha2;
	            ha1 = (i - 1) * w + j / 2, ha2 = (i - 1) * w + j / 2 + 1;
	            if(str[i][j] == '|') continue;
	            else add(ha1, ha2), add(ha2, ha1);
	        }
	        for(int j = 1; j <= 2 * w - 1; j += 2){
	            int ha1, ha2;
	            ha1 = (i - 1) * w + (j + 1) / 2, ha2 = i * w + (j + 1) / 2;
	            if(str[i][j] == '_') continue;
	            else add(ha1, ha2), add(ha2, ha1);
	        }
	    }
	    cin >> q;
	    int px, py;
	    cin >> px >> py;
	    _for(i, 2, q){
	        int tx, ty;
	        cin >> tx >> ty;
	        int ha1 = (tx - 1) * w + ty, ha2 = (px - 1) * w + py;
	        que[ha1].push_back({ha2, i});
	        que[ha2].push_back({ha1, i});
	        px = tx, py = ty;
	    }
	    _for(i, 1, h * w){
	        par[i] = i;
	    }
	    dfs(1, -1);
	    tarjan(1);
	    ll res = 0;
	    _for(i, 1, q){
	        res += ans[i];
	    }
	    cout << res << endl;
	    return 0;
	}
	
	void add(int u, int v){
	    edge[idx] = v, ne[idx] = head[u], head[u] = idx++;
	}
	
	int find(int x){
	    if(par[x] != x) par[x] = find(par[x]);
	    return par[x];
	}
	
	void dfs(int u, int fa){
	    for(int i = head[u]; ~i; i = ne[i]){
	        int v = edge[i];
	        if(v == fa) continue;
	        dist[v] = dist[u] + 1;
	        dfs(v, u);
	    }
	}
	
	void tarjan(int u){
	    st[u] = 1;
	    for(int i = head[u]; ~i; i = ne[i]){
	        int v = edge[i];
	        if(!st[v]){
	            tarjan(v);
	            par[v] = u;
	        }
	    }   
	    for(auto tem : que[u]){
	        int v = tem.first, id = tem.second;
	        if(st[v] == 2){
	            int anc = find(v);
	            ans[id] = dist[u] + dist[v] - dist[anc] * 2;
	        }
	    }
	    st[u] = 2;
	}

#Solution 2 by 倍增

	#include<bits/stdc++.h> 
	using namespace std;
	#define _for(i, a, b) for(int i = a; i <= b; i++)
	#define _memset(a, b) memset(a, b, sizeof a)
	#define endl '\n'
	#define IOS 	std::ios::sync_with_stdio(false); cin.tie(0), cout.tie(0);
	typedef long long ll;
	typedef long long LL;
	typedef pair<int, int> PII;
	// const int N = 50;
	// const int M = 250;
	const int N = 505;
	const int M = N * N * 2;
	const int INF = 0x3f3f3f3f;
	
	int m, n, q;
	int hei[N * N];
	struct ee{
	    int u, v;
	    int w;
	} e[M];
	int cnt;
	int belong[N * N];
	int head[M], ne[M * 2], edge[M * 2], wei[M * 2], idx;
	int dep[N * N], fa[N * N][100], ma[N * N][100];
	int dd;
	
	void add(int u, int v, int w);
	void bfs(int root);
	int lca(int a, int b);
	bool cmp(ee a, ee b);
	void kruskal();
	int find(int u);
	
	int main(){
	    IOS
	    cin >> m >> n >> q;
	    _memset(head, -1);
	    _for(i, 1, m)
	        _for(j, 1, n){
	            int ha = (i - 1) * n + j;
	            belong[ha] = ha;
	            cin >> hei[ha];
	        }
	    dd = (int)(log(n * m) / log(2)) + 1;
	    _for(i, 1, m){
	        _for(j, 1, n){
	            int ha = (i - 1) * n + j, har = (i - 1) * n + j + 1, had = i * n + j;
	            if(j + 1 <= n)
	                e[++cnt] = {ha, har, max(hei[ha], hei[har])};
	            if(i + 1 <= m)
	                e[++cnt] = {ha, had, max(hei[ha], hei[had])};
	        }
	    }
	    sort(e + 1, e + 1 + cnt, cmp);
	    kruskal();
	    bfs(1);
	    // _for(i, 0, idx - 1){
	    //     cout << 
	    // }
	    _for(i, 1, q){
	        int x1, x2, y1, y2;
	        cin >> x1 >> y1 >> x2 >> y2;
	        int ha1 = (x1 - 1) * n + y1, ha2 = (x2 - 1) * n + y2;
	        if(ha1 == ha2) cout << hei[ha1] << endl;
	        else cout << lca(ha1, ha2) << endl;
	    }
	    return 0;
	}
	
	bool cmp(ee a, ee b){
	    return a.w < b.w;
	}
	
	void kruskal(){
	    _for(i, 1, cnt){
	        int u = e[i].u, v = e[i].v, w = e[i].w;
	        int fu = find(u), fv = find(v);
	        if(fu != fv){
	            belong[fu] = fv;
	            add(u, v, w), add(v, u, w);
	        }
	    }
	}
	
	int find(int u){
	    if(u != belong[u]) belong[u] = find(belong[u]);
	    return belong[u];
	}
	
	void add(int u, int v, int w){
	    edge[idx] = v, wei[idx] = w, ne[idx] = head[u], head[u] = idx++;
	}
	
	void bfs(int root){
	    _memset(dep, INF);
	    dep[0] = 0, dep[root] = 1;
	    queue<int> q;
	    q.push(root);
	    while(q.size()){
	        int u = q.front();
	        q.pop();
	        for(int i = head[u]; ~i; i = ne[i]){
	            int v = edge[i];
	            if(dep[v] > dep[u] + 1){
	                q.push(v);
	
	                dep[v] = dep[u] + 1;
	                ma[v][0] = wei[i];
	                fa[v][0] = u;
	                _for(k, 1, dd){
	                    fa[v][k] = fa[fa[v][k - 1]][k - 1];
	                    ma[v][k] = max(ma[v][k - 1], ma[fa[v][k - 1]][k - 1]);
	                }
	            }
	        }
	    }
	}
	
	int lca(int a, int b){
	    int ans = 0;
	    if(dep[a] > dep[b]) swap(a, b);
	    for(int i = dd; i >= 0; i--){
	        if(dep[fa[b][i]] >= dep[a]){
	            ans = max(ans, ma[b][i]);
	            b = fa[b][i];
	        }
	    }
	    if(a == b) return ans;
	    for(int i = dd; i >= 0; i--){
	        if(fa[a][i] != fa[b][i]){
	            ans = max(ans, ma[a][i]);
	            ans = max(ans, ma[b][i]);
	            a = fa[a][i], b = fa[b][i];
	        }
	    }
	    ans = max(ans, ma[a][0]);
	    ans = max(ans, ma[b][0]);
	    return ans;
	}


#树的重心

Definition：对于树上的每一个点，计算其所有子树中最大的子树节点数，这个值最小的点就是这棵树的重心。

#性质

	1. 以树的重心为根时，所有子树的大小都不超过整棵树大小的一半。
	
	2. 树中所有点到某个点的距离和中，到重心的距离和是最小的；如果有两个重心，那么到它们的距离和一样。
	
	3. 把两棵树通过一条边相连得到一棵新的树，那么新的树的重心在连接原来两棵树的重心的路径上。
	
	4. 在一棵树上添加或删除一个叶子，那么它的重心最多只移动一条边的距离。
	

	#include<bits/stdc++.h>
	using namespace std;
	const int N = 1e5 + 5;
	
	int size[N], weight[N], centroid[2];
	int n;
	int head[N], ne[N << 1], edge[N << 1], idx;
	
	void add(int u, int v);
	void findCentroid(int cur, int fa);
	
	int main(){
	    memset(head, -1, sizeof head);
	    cin >> n;
	    for(int i = 1; i < n; i++){
	        int u, v;
	        cin >> u >> v;
	        add(u, v), add(v, u);
	    }
	    int root = 1;
	    findCentroid(root, -1);
	    return 0;
	}
	
	void add(int u, int v){
	    edge[idx] = v, ne[idx] = head[u], head[u] = idx++;
	}
	
	void findCentroid(int u, int fa){
	    size[u] = 1;
	    weight[u] = 0;
	    for(int i = head[u]; ~i; i = ne[i]){
	        int v = edge[i];
	        if(v == fa) continue;
	        findCentroid(v, u);
	        size[u] += size[v];
	        weight[u] = max(weight[u], size[v]);
	    }
	    weight[u] = max(weight[u], n - size[u]);
	    if(weight[u] <= n / 2){
	        centroid[centroid[0] != 0] = u;
	        
	    }
	}

#拓扑排序

	int n, m;
	int head[N], ne[N], edge[N], idx;
	bitset <N> bi[N];
	vector <int> topo;
	int in[N];
	
	void add(int u, int v);
	void t_sort();
	
	int main(){
	    ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);   //就不能用scanf printf  putchar  puts了
		_memset(head, -1);
	    cin >> n >> m;
		_for(i, 1, m){
			int a, b;
			cin >> a >> b;
			add(a, b);
			in[b]++;
		}
		t_sort();
	    return 0;
	}
	
	void add(int u, int v){
		edge[idx] = v, ne[idx] = head[u], head[u] = idx++;
	}
	
	void t_sort(){
		queue <int> q; //如果求字典序最小/最大的拓扑排序，则需要将此处改为priority_queue
		_for(i, 1, n) if(!in[i]) q.push(i);
		while(!q.empty()){
			int u = q.front();
			q.pop();
			topo.push_back(u);
			for(int i = head[u]; ~i; i = ne[i]){
				int v = edge[i];
				in[v]--;
				if(!in[v]) q.push(v);
			}
		}
	}

#最小生成树

#Kruskal & Prim








#次小生成树
Implement by LCA

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
	    _memset(dep, 0x3f3f3f3f);
	    dep[0] = 0, dep[n] = 1;
	    bfs();
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



#Dijkstra求最短路

	//要求所有边权都是正值
	#include <bitsdc++.h>
	using namespace std;
	
	const int N = 150050;
	
	typedef pair <int, int> PII;
	
	int head[N], edge[N * 2], ne[N * 2], wei[N * 2], idx;
	int n, m;
	int dist[N], st[N];
	
	void add(int a, int b, int w);
	int dijsktra(void);
	
	int main(){
	    cin >> n >> m;
	    memset(head, -1, sizeof head);
	    for(int i = 1; i <= m; i++){
	        int a, b, w;
	        cin >> a >> b >> w;
	        add(a, b, w);
	    }
	
	    printf("%d\n", dijsktra());
	    return 0;
	}
	
	void add(int a, int b, int w){
	    edge[idx] = b, wei[idx] = w, ne[idx] = head[a], head[a] = idx++;
	}
	
	int dijsktra(void){
	    memset(dist, 0x3f3f3f3f, sizeof dist);
	    dist[1] = 0;
	    priority_queue <PII, vector <PII>, greater <PII>> heap;
	    heap.push({0, 1});
	    while(!heap.empty()){
	        auto tem = heap.top();
	        heap.pop();
	        int dis = tem.first, ver = tem.second;
	        if(st[ver] == 1)
	            continue;
	        st[ver] = 1;
	        for(int i = head[ver]; i != -1; i = ne[i]){
	            int j = edge[i];
	            if(dist[j] > dist[ver] + wei[i]){
	                dist[j] = dist[ver] + wei[i];
	                heap.push({dist[j], j});
	            }
	        }
	    }
	    if(dist[n] == 0x3f3f3f3f)
	        return -1;
	    else
	        return dist[n];
	}

#SPFA求最短路

	#include <bitsdc++.h>
	using namespace std;
	
	const int N = 2020, M = 10050, INF = 0x3f3f3f3f;
	
	int head[N], edge[M], ne[M], wei[M], idx;
	int n, m;
	int dist[N], cnt[N], st[N];
	
	int spfa(void);
	void add(int a, int b, int w);
	
	int main(){
	    cin >> n >> m;
	    memset(head, -1, sizeof head);
	    for(int i = 1; i <= m; i++){
	        int a, b, w;
	        cin >> a >> b >> w;
	        add(a, b, w);
	    }
	    if(spfa() == 1)   
	        printf("No\n");
	    else
	        printf("Yes\n");
	    return 0;
	}
	
	void add(int a, int b, int w){
	    edge[idx] = b, wei[idx] = w, ne[idx] = head[a], head[a] = idx++;
	}
	
	int spfa(void){
	    memset(dist, INF, sizeof dist);
	    dist[1] = 0;
	    queue <int> q;
	    for(int i = 1; i <= n; i++){        //因为这个负环未必存在于从1-n的路径上，所以一开始就需要加入所有的边  
	        st[i] = true;
	        q.push(i);
	    }
	    while(!q.empty()){
	        int tem = q.front();
	        q.pop();
	        st[tem] = 0;
	        for(int i = head[tem]; i != -1; i = ne[i]){
	            int j = edge[i];
	            if(dist[j] > dist[tem] + wei[i]){
	                cnt[j] = cnt[tem] + 1;
	                dist[j] = dist[tem] + wei[i];
	                if(cnt[j] >= n)
	                    return 0;
	                if(st[j] == 0){
	                    q.push(j);
	                    st[j] = 1;
	                }
	            }
	        }
	    }
	    return 1;
	}


#图的绝对中心
图的绝对中心 可以存在于一条边上或某个结点上，该中心到所有点的最短距离的最大值最小。

	bool cmp(int a, int b) { return val[a] < val[b]; }
	
	void Floyd() {
	  for (int k = 1; k <= n; k++)
	    for (int i = 1; i <= n; i++)
	      for (int j = 1; j <= n; j++) d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
	}
	
	void solve() {
	  Floyd();
	  for (int i = 1; i <= n; i++) {
	    for (int j = 1; j <= n; j++) {
	      rk[i][j] = j;
	      val[j] = d[i][j];
	    }
	    sort(rk[i] + 1, rk[i] + 1 + n, cmp);
	  }
	  int ans = INF;
	  // 图的绝对中心可能在结点上
	  for (int i = 1; i <= n; i++) ans = min(ans, d[i][rk[i][n]] * 2);
	  // 图的绝对中心可能在边上
	  for (int i = 1; i <= m; i++) {
	    int u = a[i].u, v = a[i].v, w = a[i].w;
	    for (int p = n, i = n - 1; i >= 1; i--) {
	      if (d[v][rk[u][i]] > d[v][rk[u][p]]) {
	        ans = min(ans, d[u][rk[u][i]] + d[v][rk[u][p]] + w);	// p是v离的最远的点
	        p = i;
	      }
	    }
	  }
	}

![](cfys.png)

#Tarjan For SCC 求强连通分量
	
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
	const int N = 1e3;
	inline LL lowbit(int x){return x & -x;};
	
	int head[N], ne[N * 2], edge[N * 2], idx;
	int n, m;
	bool stack_in[N];
	int scc_id[N], scc_cnt, tcnt, out[N], in[N];
	int dfn[N], low[N];
	stack <int> ss;
	
	void add(int u, int v);
	void tarjan(int u);
	
	int main(){
	    ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);
	    cin >> n;
	    _memset(head, -1);
	    _for(i, 1, n){
	        int v;
	        while(cin >> v && v){
	            add(i, v);
	        }
	    }
	    _for(i, 1, n)
	        if(!dfn[i])
	            tarjan(i);
	    _for(u, 1, n){
	        for(int i = head[u]; ~i; i = ne[i]){
	            int v = edge[i];
	            if(scc_id[u] != scc_id[v]){
	                out[scc_id[u]]++, in[scc_id[v]]++;
	            }
	        }
	    }
	    int sta = 0, des = 0;
	    _for(i, 1, scc_cnt){
	        if(!in[i]) sta++;
	        if(!out[i]) des++;
	    }
	    cout << sta << endl;
	    if(scc_cnt == 1) cout << 0 << endl;
	    else cout << max(sta, des) << endl;	// 需要添加几条新有向边才能使得对于任意一个点都能到达图上任意一点
											//（有向边取法：从out = 0的点接到in = 0党的点）
	    return 0;
	}
	
	void add(int u, int v){
	    edge[idx] = v, ne[idx] = head[u], head[u] = idx++;
	}
	
	void tarjan(int u){
	    dfn[u] = low[u] = ++tcnt;
	    ss.push(u);
	    in[u] = true;
	    for(int i = head[u]; ~i; i = ne[i]){
	        int v = edge[i];
	        if(!dfn[v]){
	            tarjan(v);
	            low[u] = min(low[u], low[v]);
	        }
	        else if(in[v]){
	            low[u] = min(low[u], dfn[v]);
	        }
	    }
	    if(low[u] == dfn[u]){
	        ++scc_cnt;
	        while(ss.top() != u){
	            int t = ss.top();
	            ss.pop();
	            scc_id[t] = scc_cnt;
	            in[t] = false;
	        }
	        scc_id[u] = scc_cnt;
	        in[u] = false;
	        ss.pop();
	    }
	}

