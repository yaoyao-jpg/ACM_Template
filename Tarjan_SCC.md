	// Problem: 学校网络
	// Contest: AcWing
	// URL: https://www.acwing.com/problem/content/369/
	// Memory Limit: 64 MB
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
	    ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);   //就不能用scanf printf  putchar  puts了
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
	    else cout << max(sta, des) << endl;
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