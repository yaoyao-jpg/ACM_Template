	// Problem: 可达性统计
	// Contest: AcWing
	// URL: https://www.acwing.com/problem/content/166/
	// Memory Limit: 256 MB
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
	inline int read(){
		int X=0; bool flag=1; char ch=getchar();
		while(ch<'0'||ch>'9') {if(ch=='-') flag=0; ch=getchar();}
		while(ch>='0'&&ch<='9') {X=(X<<1)+(X<<3)+ch-'0'; ch=getchar();}
		if(flag) return X;
		return ~(X-1);
	}
	typedef pair<int, int> PII;
	const LL mod = 1;
	const int N = 3e4 + 10;
	inline LL lowbit(int x){return x & -x;};
	
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
		for(int i = n - 1; i >= 0; i--){
			int u = topo[i];
			bi[u][u] = 1;
			for(int i = head[u]; ~i; i = ne[i]){
				int v = edge[i];
				bi[u] |= bi[v];
			}
		}
		_for(i, 1, n)
			cout << bi[i].count() << endl;
	    return 0;
	}
	
	void add(int u, int v){
		edge[idx] = v, ne[idx] = head[u], head[u] = idx++;
	}
	
	void t_sort(){
		queue <int> q;
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