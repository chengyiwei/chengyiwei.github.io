---
title: 「NOIP模拟赛 捌」题解
author: Martian148
avatar: 'https://www.z4a.net/images/2020/03/18/49552014.png'
authorLink: chengyiwei.github.io
authorAbout: 一只OI届的小混混
authorDesc: 一只OI届的小混混
categories: 题解
comments: true
date: 2020-02-19 19:43:23
tags:
keywords:
description:
photos: 'http://seopic.699pic.com/photo/40150/3529.jpg_wh1200.jpg'
---

## A.Digits

### 题面

 给定$n$令$i=\overline{x_{m}\cdots x_{1}x_{0} },n-i=\overline{y_{m}\cdots y_{1}y_{0} }$其中$x_i,y_i$分别是$i,n-i$的十进制表示，令$f(i)=\sum_{i=0}^m x_i\times y_i$其中$x_i,y_i$都为0，求$\sum_{i=0}^n f(i) \bmod 10^9+7$

### sol

看到这么大的数据范围，自然而然就想到了数位DP，定义$F[i][j]$表示考虑完第$i-1$位，第$i$位是否被借位了，$j$是$0/1$。$g[i][j]$表示前$i-1$数字的个数$j$同样表示是否有被借位。

于是转移方程就出来了 $F[i+1][flg]+=F[i][s]+g[i ][s] * j *(l+flg*10-j)$

$g[i+1][flg]+=g[i][s]$

$g$数组累计数字个数。

显然答案就是$F[m+1][0]$，$m$是数字的个数。

### code

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
const int TT=1e9+7;
LL T,N,a[20],m,F[20][2],g[20][2];
LL read(){
	LL ret=0,f=1;char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')f=-f;ch=getchar();}
	while(ch<='9'&&ch>='0')ret=ret*10+ch-'0',ch=getchar();
	return ret*f;
}
int main(){
	T=read();
	while(T--){
		N=read();
		m=-1;memset(a,0,sizeof a);memset(F,0,sizeof F);memset(g,0,sizeof g); 
		while(N){a[++m]=N%10;N/=10;}
		F[0][0]=0;g[0][0]=1;
		for(int i=0;i<=m;i++)
			for(int s=0;s<2;s++){
				int l=a[i]-s;
				for(int j=0;j<=9;j++){
					int flg=(l<j);
					(F[i+1][flg]+=F[i][s]+g[i][s]*j*(l+flg*10-j))%=TT;
					(g[i+1][flg]+=g[i][s])%=TT;
				}
			}
		printf("%lld\n",F[m+1][0]);
	}
}
```



## B.Brew

### 题面

 有一以酿酒闻名的村庄，呈直线型分布，也就是说$n$ 户人家的地址都在一条数轴上以坐标标出 $x_i$，现在村里要建筑$K$座酿酒厂，同样分布在数轴上。定义建造的代价是每家到最近的酿酒厂的距离之和。求最小的代价。

$1\leq n,K\leq 100000,1\leq x_i\leq 10^6$

### sol

此题不晓得怎么做，贴一个CXR大佬的代码

### code

```cpp
#include<bits/stdc++.h>
#define max(x,y) ((x)>(y)?(x):(y))
#define min(x,y) ((x)<(y)?(x):(y))
#define uint unsigned int
#define LL long long
#define ull unsigned long long
#define swap(x,y) (x^=y,y^=x,x^=y)
#define abs(x) ((x)<0?-(x):(x))
#define INF 1e18
#define Inc(x,y) ((x+=y)>=MOD&&(x-=MOD))
#define N 100000
#define GetCost(l,r) (f[l]+GetSum((l)+1,r)+Cost)
#define Sum(l,r) (sum[r]-sum[(l)-1])
using namespace std;
LL n,m,Cost,a[N+5],sum[N+5],f[N+5],g[N+5];
class FIO
{
    private:
        #define Fsize 100000
        #define tc() (FinNow==FinEnd&&(FinEnd=(FinNow=Fin)+fread(Fin,1,Fsize,stdin),FinNow==FinEnd)?EOF:*FinNow++)
        #define pc(ch) (FoutSize<Fsize?Fout[FoutSize++]=ch:(fwrite(Fout,1,FoutSize,stdout),Fout[(FoutSize=0)++]=ch))
        LL f,FoutSize,OutputTop;char ch,Fin[Fsize],*FinNow,*FinEnd,Fout[Fsize],OutputStack[Fsize];
    public:
        FIO() {FinNow=FinEnd=Fin;}
        inline void read(LL &x) {x=0,f=1;while(!isdigit(ch=tc())) f=ch^'-'?1:-1;while(x=(x<<3)+(x<<1)+(ch&15),isdigit(ch=tc()));x*=f;}
        inline void read_char(char &x) {while(isspace(x=tc()));}
        inline void read_string(string &x) {x="";while(isspace(ch=tc()));while(x+=ch,!isspace(ch=tc())) if(!~ch) return;}
        inline void write(LL x) {if(!x) return (void)pc('0');if(x<0) pc('-'),x=-x;while(x) OutputStack[++OutputTop]=x%10+48,x/=10;while(OutputTop) pc(OutputStack[OutputTop]),--OutputTop;}
        inline void write_char(char x) {pc(x);}
        inline void write_string(string x) {register LL i,len=x.length();for(i=0;i<len;++i) pc(x[i]);}
        inline void end() {fwrite(Fout,1,FoutSize,stdout);}
}F;
inline LL GetSum(LL l,LL r)
{
    if(l>r) return 0;
    register LL mid=l+r>>1,ln=mid-l+1,rn=r-mid+1;
    return (Sum(mid,r)-rn*a[mid])+(ln*a[mid]-Sum(l,mid));
}
class Class_Monotone_queue//单调队列
{
    private:
        struct key
        {
            LL S,L,R;//L和R记录区间，S记录上次的转移点
            key(LL x=0,LL y=0,LL z=0):S(x),L(y),R(z){}
        }data[N+5];
        LL H,T;
    public:
        inline void Clear() {data[H=T=1]=key(0,1,n);}//清空
        inline bool empty() {return H>T;}//判断队列是否为空
        inline key Front() {return data[H];}//返回队首
        inline key Back() {return data[T];}//返回队尾
        inline void PushBack(key x) {data[++T]=x;}//在队尾加入一个元素
        inline void PopFront() {++H;}//弹出队首
        inline void PopBack() {--T;}//弹出队尾
        inline void Push(LL x)//加入一个新的元素
        {
            register LL lst=-1,l,r,mid;
            while(!empty())//只要队列不为空
            {
                if(GetCost(data[T].S,data[T].L)>GetCost(x,data[T].L)) {lst=data[T].L,PopBack();continue;}//如果原先的斜率大于当前的斜率，就弹出队尾，并跳过当前循环
                for(mid=(l=data[T].L)+(r=data[T].R)>>1;l<=r;mid=l+r>>1) GetCost(data[T].S,mid)>GetCost(x,mid)?r=mid-1:l=mid+1;//二分求出最早的从当前状态转移要优于原先状态转移的时刻
                if(l<=data[T].R) data[T].R=(lst=l)-1;//更新
                break;//退出循环
            }
            if(~lst) PushBack(key(x,lst,n));//如果有值，将其加入队列
            if(!empty()&&++data[H].L>data[H].R) PopFront();//如果队首所表示区间为空，则将其弹出                  
        }
}q;
inline bool check(LL C)//求出额外代价为C时的最优答案以及对应造酿酒厂的个数是否小于等于m
{
    Cost=C,q.Clear();//清空数组
    for(register LL i=1;i<=n;++i)//斜率优化DP 
        f[i]=GetCost(q.Front().S,i),g[i]=g[q.Front().S]+1,q.Push(i);//计算出最优答案以及对应造酿酒厂的个数，然后将当前的i加入单调队列
    return g[n]<=m;//如果g[n]≤m则返回true
}
int main()
{
    register LL i,j,l,r,mid;
    for(F.read(n),F.read(m),i=1;i<=n;++i) F.read(a[i]);
    for(sort(a+1,a+n+1),i=1;i<=n;++i) sum[i]=sum[i-1]+a[i];
    for(mid=(l=0)+(r=INF)>>1;l<=r;mid=l+r>>1) check(mid)?r=mid-1:l=mid+1;//WQS二分
    return check(l),F.write(f[n]-l*m),F.end(),0;//输出答案
}
```

## C.QTree

### 题面

 一张由$n$ 个点，$n$ 条边构成的无重边无自环的带权无向连通图，需要支持以下操作： 

-  操作一：对与结点$u$距离不超过$k$的点（包括$u$）的权值加上d。
-  操作二：询问与结点$u$距离不超过$k$的点（包括$u$）的权值和。

初始权值为$0$

$n\leq 100000,m\leq 100000,0\leq k\leq 2,-100\leq d \leq 100$

### sol

这道题有$n$条边和$n$条边，也就是说这个图是一个环加若干棵树，对于一棵树。我们要修改的是$val[u],val[fa[u]],val[fa[fa[u]]],val[fa[son[u]]],val[son[u]],val[son[son[u]]]$

对于$val[u],val[fa[u]],val[fa[fa[u]]]$是确定的，我们直接单点修改即可，我们发现每个u的$son[u],son[son[u]]$的BFS序是靠在一起的，于是我们可以预处理出每个u和$son[u],son[son[u]]$和范围，对于修改兄弟节点，就修改$fa[u]$的儿子的区间.

最后把树拓展到树加环上就好了。

### code

```cpp
#include<bits/stdc++.h>
#define SET 1,1,N
#define V(x) (Sum(SET,bfn[x],bfn[x]))
#define C(x,d) (Change(SET,bfn[x],bfn[x],d))
#define S(x) (Sum(SET,Lson[x],Rson[x]))
#define CS(x,d) (Change(SET,Lson[x],Rson[x],d))
using namespace std;
typedef long long ll;
inline void write(ll x){x<0?putchar('-'),x=-x:x=x;int num[100]={0};num[0]+=!x;while (x) num[++num[0]]=x%10,x/=10;for (int i=num[0];i;i--) putchar(num[i]+'0');puts("");}
inline int read(){
	int Res=0,f=1;char ch=getchar();
	while (ch>'9'||ch<'0') f=(ch=='-'?-f:f),ch=getchar();
	while (ch>='0'&&ch<='9') Res=Res*10+ch-'0',ch=getchar();
	return Res*f;
}
namespace MFS{
	int fa[100005];
	inline void init(int N){for (int i=1;i<=N;i++) fa[i]=i;}
	inline int getfa(int x){return fa[x]==x?x:fa[x]=getfa(fa[x]);}
	inline bool merge(int x,int y){x=getfa(x);y=getfa(y);if (x!=y) fa[x]=y;return x==y;}
}
int N,M;
int tot,lnk[100005],son[200005],nxt[200005],bfn[100005],fa[100005],bfs;
int Lson[100005],Rson[100005],Lsson[100005],Rsson[100005],LNeb[100005],RNeb[100005];
bool lop[100005];
bool DFS(int x,int End,int fa){
	if (x==End) return lop[x]=1;
	for (int i=lnk[x];i;i=nxt[i]){
		if (son[i]==fa) continue;
		if (DFS(son[i],End,x)) RNeb[x]=son[i],LNeb[son[i]]=x,lop[x]=1;
	}
	return lop[x];
}
inline void add(int x,int y){son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;}
queue<int>Que;
struct ST{
	ll x,lazy;
}T[400005];
inline void pushdown(int x,int L,int R){
	ll &l=T[x].lazy;int mid=(L+R)>>1;
	if (l) T[x<<1].lazy+=l,T[x<<1|1].lazy+=l,T[x<<1].x+=l*(mid-L+1),T[x<<1|1].x+=l*(R-mid),l=0;
}
inline void Change(int p,int L,int R,int l,int r,int data){
	if (l==2e9||r==-2e9) return ;
	if (l<=L&&R<=r){T[p].lazy+=data;T[p].x+=data*(R-L+1);return ;}
	int mid=(L+R)>>1;pushdown(p,L,R);
	if (l<=mid) Change(p<<1,L,mid,l,r,data);
	if (mid<r) Change(p<<1|1,mid+1,R,l,r,data);
	T[p].x=T[p<<1].x+T[p<<1|1].x;
}
inline ll Sum(int p,int L,int R,int l,int r){
	if (l==2e9||r==-2e9) return 0;
	if (l<=L&&R<=r) return T[p].x;
	int mid=(L+R)>>1;pushdown(p,L,R);ll Res=0;
	if (l<=mid) Res+=Sum(p<<1,L,mid,l,r);
	if (mid<r) Res+=Sum(p<<1|1,mid+1,R,l,r);
	return Res;
}
inline ll calcNeb(int u,bool son,bool neb){
	ll val=V(LNeb[u])+V(RNeb[u]);
	if (son) if (LNeb[u]!=RNeb[u]) val+=S(LNeb[u])+S(RNeb[u]);else val+=S(LNeb[u]);
	if (neb){
		if (LNeb[LNeb[u]]==RNeb[u]) return val;
		if (LNeb[LNeb[u]]==RNeb[RNeb[u]]) return val+V(LNeb[LNeb[u]]);
		return val+V(LNeb[LNeb[u]])+V(RNeb[RNeb[u]]);
	}
	return val;
}
inline void changeNeb(int u,bool son,bool neb,int d){
	C(LNeb[u],d),C(RNeb[u],d);
	if (son) if (LNeb[u]!=RNeb[u]) CS(LNeb[u],d),CS(RNeb[u],d);else CS(LNeb[u],d);
	if (neb){
		if (LNeb[LNeb[u]]==RNeb[u]) return ;
		if (LNeb[LNeb[u]]==RNeb[RNeb[u]]) C(LNeb[LNeb[u]],d);
		else C(LNeb[LNeb[u]],d),C(RNeb[RNeb[u]],d);
	}
	return ;
}
int main()
{
	N=read();M=read();MFS::init(N);
	for (int i=1;i<=N;i++){
		int x=read(),y=read();
		if (MFS::merge(x,y)) LNeb[x]=y,RNeb[y]=x,DFS(x,y,0);
		add(x,y);add(y,x);Lson[i]=Lsson[i]=2e9;Rson[i]=Rsson[i]=-2e9;
	}
	for (int i=1;i<=N;i++) if (lop[i]) Que.push(i),bfn[i]=++bfs;
	while (Que.size()){
		int Now=Que.front();Que.pop();
		for (int i=lnk[Now];i;i=nxt[i]){
			if (bfn[son[i]]) continue;
			fa[son[i]]=Now;Que.push(son[i]);bfn[son[i]]=++bfs;
			Lson[Now]=min(Lson[Now],bfs);Lsson[fa[Now]]=min(Lsson[fa[Now]],bfs);
			Rson[Now]=max(Rson[Now],bfs);Rsson[fa[Now]]=max(Rsson[fa[Now]],bfs);
		}
	}
	for (int i=1;i<=M;i++){
		int op=read(),u=read(),k=read(),d;ll Ans=0;
		if (op==1){
			d=read();
			if (!k) C(u,d);
			if (k==1){
				C(u,d);CS(u,d);
				if (lop[u]) changeNeb(u,0,0,d);
				else C(fa[u],d);
			}
			if (k==2){
				CS(u,d);Change(SET,Lsson[u],Rsson[u],d);
				if (lop[u]) C(u,d),changeNeb(u,1,1,d);
				else{
					C(fa[u],d);CS(fa[u],d);
					if (lop[fa[u]]) changeNeb(fa[u],0,0,d);
					else C(fa[fa[u]],d);
				}
			}
		}
		else{
			if (!k) Ans=V(u);
			if (k==1){
				Ans=V(u)+S(u);
				if (lop[u]) Ans+=calcNeb(u,0,0);
				else Ans+=V(fa[u]);
			}
			if (k==2){
				Ans=S(u)+Sum(SET,Lsson[u],Rsson[u]);
				if (lop[u]) Ans+=V(u),Ans+=calcNeb(u,1,1);
				else {
					Ans+=V(fa[u])+S(fa[u]);
					if (lop[fa[u]]) Ans+=calcNeb(fa[u],0,0);
					else Ans+=V(fa[fa[u]]);
				}
			}
			write(Ans);
		}
	}
	return 0;
}
```



