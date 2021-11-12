*最长连续不下降子序列*

```c++
#include<iostream>

using namespace std;

#define MAXN 10000
int main(){
	int n;
	int a[MAXN];
	cin>>n;
	for(int i=1;i<=n;i++) scanf("%d",&a[i]);
	int now=1,ans=1;
	for(int i=2;i<=n;i++){
		if(a[i]>a[i-1]) now++;
		else now = 1;
		ans = max(now,ans);
	}
	cout<<ans<<endl;
} 
```



*最长不下降子序列*

```cpp
#include<iostream>
#include<cstring>
using namespace std;

#define MAXN 1000
#define INF 0x3f3f3f3f

int main(){
	int n;
	int a[MAXN];//存数组 
	int dp[MAXN];//dp[i]即长度为i的子序列的结尾数字 
	scanf("%d",&n); 
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
	}
	memset(dp,INF,sizeof(dp));
	for(int i=1;i<=n;i++){
		int *px=lower_bound(dp+1,dp+1+n,a[i]);//找到等于它或者小于它的位置 
		*px=a[i];//插入，如果原位置有数据则覆盖，因为同样长度则留结尾数字较小的那个 
	}
	int now=1;
	while(dp[now]!=INF) now++;
	cout<<now-1<<endl;
}
```

*最长公共子序列*

```
#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;

#define MAXN 1000

int dp[MAXN][MAXN];

int main() {
	dp[0][0]=dp[0][1]=dp[1][0]=0;
	string a,b;
	cin>>a>>b;
	int la=a.size()-1;
	int lb=b.size()-1;
	for(int i=0; i<la; i++) {
		for(int j=0; j<lb; j++) {
			if(a[i]==b[j]) {
				dp[i+1][j+1]=dp[i][j]+1;
			} else {
				dp[i+1][j+1]=max(dp[i+1][j],dp[i][j+1]);
			}
		}
	}
	cout<<dp[la][lb]<<endl;
}

```

*数位DP*

```
typedef long long ll;
int a[20];
ll dp[20][state];//不同题目状态不同
ll dfs(int pos,/*state变量*/,bool lead/*前导零*/,bool limit/*数位上界变量*/)//不是每个题都要判断前导零
{
    //递归边界，既然是按位枚举，最低位是0，那么pos==-1说明这个数我枚举完了
    if(pos==-1) return 1;/*这里一般返回1，表示你枚举的这个数是合法的，那么这里就需要你在枚举时必须每一位都要满足题目条件，也就是说当前枚举到pos位，一定要保证前面已经枚举的数位是合法的。不过具体题目不同或者写法不同的话不一定要返回1 */
    //第二个就是记忆化(在此前可能不同题目还能有一些剪枝)
    if(!limit && !lead && dp[pos][state]!=-1) return dp[pos][state];
    /*常规写法都是在没有限制的条件记忆化，这里与下面记录状态是对应，具体为什么是有条件的记忆化后面会讲*/
    int up=limit?a[pos]:9;//根据limit判断枚举的上界up;这个的例子前面用213讲过了
    ll ans=0;
    //开始计数
    for(int i=0;i<=up;i++)//枚举，然后把不同情况的个数加到ans就可以了
    {
        if() ...
        else if()...
        ans+=dfs(pos-1,/*状态转移*/,lead && i==0,limit && i==a[pos]) //最后两个变量传参都是这样写的
        /*这里还算比较灵活，不过做几个题就觉得这里也是套路了
        大概就是说，我当前数位枚举的数是i，然后根据题目的约束条件分类讨论
        去计算不同情况下的个数，还有要根据state变量来保证i的合法性，比如题目
        要求数位上不能有62连续出现,那么就是state就是要保存前一位pre,然后分类，
        前一位如果是6那么这意味就不能是2，这里一定要保存枚举的这个数是合法*/
    }
    //计算完，记录状态
    if(!limit && !lead) dp[pos][state]=ans;
    /*这里对应上面的记忆化，在一定条件下时记录，保证一致性，当然如果约束条件不需要考虑lead，这里就是lead就完全不用考虑了*/
    return ans;
}
ll solve(ll x)
{
    int pos=0;
    while(x)//把数位都分解出来
    {
        a[pos++]=x%10;//个人老是喜欢编号为[0,pos),看不惯的就按自己习惯来，反正注意数位边界就行
        x/=10;
    }
    return dfs(pos-1/*从最高位开始枚举*/,/*一系列状态 */,true,true);//刚开始最高位都是有限制并且有前导零的，显然比最高位还要高的一位视为0嘛
}
int main()
{
    ll le,ri;
    while(~scanf("%lld%lld",&le,&ri))
    {
        //初始化dp数组为-1,这里还有更加优美的优化,后面讲
        printf("%lld\n",solve(ri)-solve(le-1));
    }
}
```

​		HDOJ2089 不要62

统计区间 [a,b] 中不含 4 和 62 的数字有多少个。

```
#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;

int l,r;
const int MAXN = 25;
int dp[MAXN][2];//第二维记录前面一个是否为6
int a[MAXN];
int n,m;

int dfs(int pos,bool stat,int pre,bool limit){
    //limit记录之前是否都在上界，state记录之前的是不是6(记忆化作第二维)，pre记录上一个数字
	if(pos==-1) return 1;
	if(!limit&&dp[pos][stat]!=-1) return dp[pos][stat];
    int up = limit?a[pos]:9;//如果前面的都在上界则只能到a[pos]
    int ans = 0;
    for(int i=0;i<=up;i++){
        if(pre==6&&i==2) continue;//62的情况
        if(i==4) continue;
		ans += dfs(pos-1,i==6,i,limit&&i==a[pos]);
    }
	if(!limit) dp[pos][stat] = ans;
    return ans;
}

int solve(int x){
	//先按位转化到数组
    int px = 0;
    while(x){
		a[px++] = x%10;
        x/=10;
    }
    memset(dp,-1,sizeof(dp));//清空记忆化数组
    return dfs(px-1,0,-1,1);
}

int main(){
	while(cin>>l>>r){
        if(l==0&&r==0) break;
    	//cout<<solve(r)<<' '<<solve(l-1)<<endl;
        cout<<solve(r)-solve(l-1)<<endl;
    }
}
```



sum of log

```
+#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;
typedef long long LL;
int l,r;
const int MAXN = 32;
int dp[MAXN][2][2];//第二维记录前面一个是否为2
int a[MAXN],b[MAXN];
int n,m;
const int mod = 1e9 + 7;

int dfs(int pos,bool limitx, bool limity){
    //limit记录之前是否都在上界，state记录之前的是不是6(记忆化作第二维)，pre记录上一个数字
	if(pos==-1) return 1;
	if(dp[pos][limitx][limity]!=-1) return dp[pos][limitx][limity];
    int upa = limitx ? a[pos]:1;//如果前面的都在上界则只能到a[pos]
    int upb = limity ? b[pos]:1;//如果前面的都在上界则只能到b[pos]
    LL ans = 0;
    for (int i=0; i<=upa; ++i) {
        for (int j=0; j<=upb; ++j) {
            if (i && j) continue;
            ans=(ans+dfs(pos-1,limitx&&i==upa, limity&&j==upb)) % mod;
        }
    }
	dp[pos][limitx][limity] = ans;
    return ans;
}

LL solve(int x,int y){
	//先按位转化到数组
    memset(dp,-1,sizeof(dp));//清空记忆化数组
    memset(a,0,sizeof(a));
    memset(b,0,sizeof(b));
    int cntx = 0; int cnty = 0;
    while(x){ a[cntx++] = x&1; x>>=1;}
    while(y){ b[cnty++] = y&1; y>>=1;} 

    LL ans= 0;
    for (int i=0; i<max(cntx,cnty); ++i ){
        LL res=0;
        if (i<cntx)
            res=(res+dfs(i-1,i==cntx-1,i>=cnty)) % mod;
        if (i<cnty)
            res=(res+dfs(i-1,i>=cntx,i==cnty-1)) % mod;
        ans=(ans+res*(i+1)) % mod;
    }
    return ans;
}

int main(){
    ios::sync_with_stdio(false);
    cin.tie(0);cout.tie(0);
    int _;
	cin >> _;
    while (_--) {
        int x,y;
        cin >> x >> y;
        cout << solve(x,y) << "\n";
    }   
}
```



## 背包

```
//01背包
//一个价值为v,重量为w的物品最多取一个，
// C++ Version
for (int i = 1; i <= n; i++)
  for (int l = W; l >= w[i]; l--) f[l] = max(f[l], f[l - w[i]] + v[i]);
```

