#数据结构&杂项

	#include<bits/stdc++.h> 
	using namespace std;
	#define _for(i, a, b) for(int i = a; i <= b; i++)
	#define _memset(a, b) memset(a, b, sizeof a)
	#define endl '\n'
	#define IOS 	std::ios::sync_with_stdio(false); cin.tie(0), cout.tie(0);
	typedef long long ll;
	typedef long long LL;
	typedef pair<int, int> PII;
	const int N = 1e5 + 5;
	const int INF = 0x3f3f3f3f;



	int main(){
	    IOS
	    return 0;
	}


#子集枚举

真子集枚举

	for (int s = (S - 1) & S; s; s = (s - 1) & S)

大小为k的子集的枚举

	template<typename T>
	void subset(int k, int n, T&& f) {
	    int t = (1 << k) - 1;
	    while (t < 1 << n) {
	        f(t);
	        int x = t & -t, y = t + x;
	        t = ((t & ~y) / x >> 1) | y;
	    }
	}

#线性筛

	void init(int n){
	    for(int i = 2; i <= n; i++){
	        if(!st[i]) prime[++cnt] = i;
	        for(int j = 1; prime[j] * i <= n; j++){
	            st[prime[j] * i] = 1;
	            if(i % prime[j] == 0) break;
	        }
	    }
	}

#组合数初始化（优化版）

	void init(){
	    pre[0] = 1;
	    for(int i = 1; i <= mod; i++) pre[i] = pre[i - 1] * i % mod;
	    inv[mod] = qmi(pre[mod], mod - 2);
	    inv[mod - 1] = qmi(pre[mod - 1], mod - 2) % mod;
	    for(int i = mod - 2; i >= 0; i--) inv[i] = inv[i + 1] * (i + 1) % mod;
	    inv[0] = 1;
	}
	
	ll qmi(ll a, ll b){
	    ll res = 1;
	    while(b){
	        if(b & 1) res = res * a % mod;
	        b >>= 1;
	        a = a * a % mod;
	    }
	    return res;
	}
	
	ll C(ll m, ll n){
	    if(m > n) return 0;
	    ll a = pre[n], b = inv[m], c = inv[n - m];
	    ll t = pre[n] * inv[m] % mod;
	    t = t * inv[n - m] % mod;
	    return t;
	}


#线段树


带add与mul懒标记的线段树
	
	struct{
		int l, r, sum, add, mul;
	}

	void build(int u, int l, int r){
	    tr[u].r = r, tr[u].l = l;
	    tr[u].add = 0, tr[u].mul = 1;
	    if(l == r){
	        tr[u].sum = arr[l];
	        return ;
	    }
	    int mid = l + r >> 1;
	    build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
	    pushup(u);
	}
	
	LL que(int u, int l, int r){
	    if(tr[u].r <= r && tr[u].l >= l){
	        return tr[u].sum;
	    }
	    int mid = tr[u].r + tr[u].l >> 1;
	    LL ans = 0;
	    pushdown(u);
	    if(l <= mid) ans = (ans + que(u << 1, l, r)) % mod;
	    if(r > mid) ans = (que(u << 1 | 1, l, r) + ans) % mod;
	    return ans;
	}
	
	void modify(int u, int l, int r, int add, int mul){
	    if(tr[u].r <= r && tr[u].l >= l){
	        tr[u].sum = (tr[u].sum * mul % mod + add * (LL)(tr[u].r - tr[u].l + 1)) % mod;
	        tr[u].add = (tr[u].add * mul + add) % mod;
	        tr[u].mul = tr[u].mul * mul % mod;
	    }
	    else{
	        int mid = tr[u].l + tr[u].r >> 1;
	        pushdown(u);
	        if(l <= mid) modify(u << 1, l, r, add, mul);
	        if(r > mid) modify(u << 1 | 1, l, r, add, mul);
	        pushup(u);
	    }
	}
	
	void pushup(int u){
	    tr[u].sum = (tr[u << 1].sum + tr[u << 1 | 1].sum) % mod;
	}
	
	void pushdown(int u){
	    eval(u << 1, tr[u].add, tr[u].mul);
	    eval(u << 1 | 1, tr[u].add, tr[u].mul);
	    tr[u].add = 0, tr[u].mul = 1;
	}
	
	void eval(int u, int add, int mul){
	    tr[u].sum = (tr[u].sum * mul + add * (tr[u].r - tr[u].l + 1)) % mod;
	    tr[u].add = (tr[u].add * mul + add) % mod;
	    tr[u].mul = tr[u].mul * mul % mod;
	}

#树状数组

![](aa.png)

	int n;
	int a[1005], tr[1005]; //对应原数组和树状数组

	int lowbit(int x){
	    return x & -x;
	}
	
	void updata(int i, int k){    //在i位置加上k
	    while(i <= n){
	        tr[i] += k;
	        i += lowbit(i);
	    }
	}
	
	int getsum(int i){        //求A[1 - i]的和
	    int res = 0;
	    while(i > 0){
	        res += tr[i];
	        i -= lowbit(i);
	    }
	    return res;
	}


# 打印二进制的形式
	int c = -315;
	bitset<16> y(c);
	cout << y << '\n';

# random_shuffle(v.begin(), v.end());
