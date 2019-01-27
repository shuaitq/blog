---
title: "Noip模板大集合"
date: 2016-01-30
draft: false
tags: ["OI", "Dijkstra", "Floyd", "SPFA", "宏", "并查集", "归并排序", "快速幂", "最大公因数", "最小公倍数", "模运算", "筛法", "读入优化", "质数", "高精度"]
# categories: ["docs", "shortcodes", "index"]
author: "Tom Smith"
---

会有一些之前已经发过文章的算法，这里只是在最后一次Noip前进行一次模板总结，为最后一次Noip做准备~~（带去考场，误）~~。本人水平有限大家看到特别不爽的地方还是忍忍吧。

下面马上就开始了：

# 首先是宏模板

```C++
#include <cstdio>
#include <cstdlib>
#include <algorithm>
#include <iostream>
#include <cmath>
#include <string>
#include <cstring>
#include <queue>
#include <map>
#include <vector>
#include <ctime>
#include <bitset>
#define FOR(i,s,e) for(int i=s;i<e;i++)
#define REP(i,s,e) for(int i=s;i<=e;i++)
#define ROF(i,s,e) for(int i=s;i>e;i--)
#define PER(i,s,e) for(int i=s;i>=e;i--)
#define FI(a) freopen(a,"r",stdin)
#define FO(a) freopen(a,"w",stdout)
#define MAX(a,b) ((a)>(b)?(a):(b))
#define MIN(a,b) ((a)>(b)?(b):(a))
#define ABS(a) ((a)>0?(a):(-(a)))
#define INF 0x3f3f3f3f
#define SPEEDUP ios::sync_with_stdio(false)
#define N 
using namespace std;
int main(void){
	return 0;
}
```

# 读入优化模板
## 正整数版

```C++
template<typename T>
void read(T &a){
	char temp=getchar();
	while(temp<'0'||temp>'9'){
		temp=getchar();
	}
	a=temp-'0';
	temp=getchar();
	while(temp>='0'&&temp<='9'){
		a=a*10+temp-'0';
		temp=getchar();
	}
}
```

## 实数版

```C++
template<typename T>
void read(T &a){
	char temp=getchar();
	bool sign=false;
	while(temp<'0'||temp>'9'){
		if(temp=='-'){
			sign=true;
		}
		temp=getchar();
	}
	a=temp-'0';
	temp=getchar();
	while(temp>='0'&&temp<='9'){
		a=a*10+temp-'0';
		temp=getchar();
	}
	if(sign){
		a=-a;
	}
}
```

# 快速幂

```C++
int fastpow(int a,int b,int k){
	int ans=1;
	while(b){
		if(b&1){
			ans*=a;
			ans%=k;
		}
		b=b>>1;
		a*=a;
		a%=k;
	}
	return a;
}
```

# 并查集

```C++
#define SIZE 100
int father[SIZE];
void start(){
	for(int i=0;i<SIZE;i++){
		father[i]=i;
	}
}
int getfather(int x){
	if(father[x]==x){
		return x;
	}else{
		return father[x]=getfather(father[x]);
	}
}
void add(int x,int y){
	int xx=getfather(x);
	int yy=getfather(y);
	father[xx]=yy;
}
```

# 最大公因数，最小公倍数

```C++
int gcd(int a,int b){
	if(b==0){
		return a;
	}else{
		return gcd(b,a%b);
	}
}
int lcm(int a,int b){
	return a/gcd(a,b)*b;
}
```

# 最短路SPFA

```C++
#define SIZE 100
int dis[SIZE];
bool vis[SIZE];
struct way{
	int to,num;
};
vector<way> v[SIZE];
int spfa(int s,int e){
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	dis[s]=0;
	vis[s]=true;
	deque<int> q;
	q.push_back(s);
	while(!q.empty()){
		int temp=q.front();
		q.pop_front();
		for(int i=0;i<v[temp].size();i++){
			if(dis[v[temp][i].to]>dis[temp]+v[temp][i].num){
				dis[v[temp][i].to]=dis[temp]+v[temp][i].num;
				if(!vis[v[temp][i].to]){
					vis[v[temp][i].to]=true;
					if(!q.empty()){
						if(dis[v[temp][i].to]>dis[q.front()]){
							q.push_back(v[temp][i].to);
						}else{
							q.push_front(v[temp][i].to);
						}
					}else{
						q.push_back(v[temp][i].to);
					}
				}
			}
		}
		vis[temp]=false;
	}
	return dis[e];
}
```

# 弗洛伊德

```C++
#define SIZE 100
int dis[SIZE][SIZE];
void floyd(){
	for(int k=0;k<SIZE;k++){
		for(int i=0;i<SIZE;i++){
			for(int j=0;j<SIZE;j++){
				dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]);
			}
		}
	}
}
```

# 弗洛伊德最小环

```C++
#define N 100
int dis[N][N],ma[N][N];
int floyd(){
	int mi;
	for(int k=0;k<N;k++){
		for(int i=0;i<k;i++){
			for(int j=i+1;j<k;j++){
				mi=min(mi,dis[i][j]+ma[i][k]+ma[k][j]);
			}
		}
		for(int i=0;i<N;i++){
			for(int j=0;j<N;j++){
				dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]);
			}
		}
	}
	return mi;
}
```

# 迪杰斯特拉

```C++
#define SIZE 100
struct way{
	int to,num;
};
vector<way> v[SIZE];
int dis[SIZE];
int dijkstra(int s,int e){
	typedef pair<int,int> P;
	memset(dis,0x3f,sizeof(dis));
	dis[s]=0;
	priority_queue<P,vector<P>,greater<P> > q;
	q.push(P(0,s));
	while(!q.empty()){
		P p=q.top();
		q.pop();
		int temp=p.second;
		if(dis[temp]<p.first){
			continue;
		}
		for(vector<way>::iterator it=v[temp].begin();it!=v[temp].end();it++){
			if(dis[it->to]>dis[temp]+it->num){
				dis[it->to]=dis[temp]+it->num;
				q.push(P(dis[it->to],it->to));
			}
		}
	}
	return dis[e];
}
```

# 高精度

只敲了加法和乘法，减法和加法差不多，除法用减法模拟就好了。

```C++
#define LEN 100
struct Big{
	int num[LEN];
	Big(){
		clear();
	}
	Big(char *a){
		start(a);
	}
	Big(const Big &a){
		for(int i=0;i<LEN;i++){ 
			num[i]=a.num[i]; 
		} 
	} 
	void clear(){ 
		memset(num,0,sizeof(num)); 
		num[0]=1; 
	} 
	void read(){ 
		char word[LEN]; 
		scanf("%s",word); 
		start(word); 
	} 
	void start(char *a){ 
		num[0]=strlen(a); 
		for(int i=num[0];i>0;i--){
			num[i]=a[num[0]-i]-'0';
		}
	}
	void print(){
		for(int i=num[0];i>0;i--){
			printf("%d",num[i]);
		}
		printf("\n");
	}
	bool big(Big &a){
		if(num[0]>a.num[0]){
			return true;
		}else if(num[0]<a.num[0]){ 
			return false; 
		} 
		for(int i=num[0];i>0;i--){
			if(num[i]>a.num[i]){
				return true;
			}else if(num[i]<a.num[i]){
				return false;
			}
		}
		return true;
	}
	void minmin(){
		int i=1;
		while(num[i]==0){
			num[i]=9;
			i++;
		}
		num[i]--;
		if(!num[num[0]]){
			num[0]--;
		}
	}
	void plusplus(){
		int i=1;
		while(num[i]==9){
			num[i]=0;
			i++;
		}
		num[i]++;
		if(num[num[0]+1]){
			num[0]++;
		}
	}
}a;
void swap(Big &a,Big &b){
	Big c=a;
	a=b;
	b=c;
}
Big add(Big &a,Big &b){
	Big c;
	c.num[0]=max(a.num[0],b.num[0]);
	for(int i=1;i<=c.num[0];i++){
		c.num[i]+=(a.num[i]+b.num[i])%10;
		c.num[i+1]=(a.num[i]+b.num[i])/10;
	}
	if(c.num[c.num[0]+1]){
		c.num[0]++;
	}
	return c;
}
Big mul(Big &a,Big &b){
	Big c;
	for(int i=1;i<a.num[0];i++){
		for(int j=1;j<b.num[0];j++){
			c.num[i+j-1]+=a.num[i]*b.num[j];
			c.num[i+j]+=c.num[i+j-1]/10;
			c.num[i+j-1]%=10;
		}
	}
	c.num[0]=a.num[0]+b.num[0];
	if(c.num[c.num[0]]){
		c.num[0]--;
	}
	return c;
}
```

# 质数试除法

```C++
bool prime(int a){
	int temp=sqrt(a)+0.5;
	for(int i=2;i<=temp;i++){
		if(a%i==0){
			return false;
		}
	}
	return true;
}
```

# 质数筛法

```C++
#define N 100
bool flag[N+5];
void prime(){
	int temp=sqrt(N)+0.5;
	for(int i=2;i<=temp;i++){
		if(!flag[i]){
			for(int j=i*2;j<=N;j+=i){
				 flag[j]=true;
			}
		}
	}
}
```

# 模运算法则

```C++
(a+b)%c=(a%c+b%c)%c
(a-b)%c=(a%c-b%c+c)%c
(a*b)%c=(a%c*b%c)%c
(a^b)%c=((a%c)^b)%c
```