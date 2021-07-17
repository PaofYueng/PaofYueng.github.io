---
title: Kuangbin带你飞[基础DP]
date: 2019-10-01 23:10:28
tags: 
- 动态规划
- ACM
mathjax: true
---

# Kuangbin带你飞[基础DP]

## 前言

> Kuangbin带你飞的日常题单

<!-- more -->		

## 题目

### [HDU 1024 Max Sum Plus Plus](http://acm.hdu.edu.cn/showproblem.php?pid=1024)

#### 题意

> 给定一个长度为n的序列，分成m段不相交叉的子段，使得他们的和最大。

#### 题解

​		首先可以用简单的二维DP数组实现。即$dp[i][j]$表示前j个元素的组成i个字段的最大和，字段数的转移有两种一是取下第j个元素作为第i个字段的延续，二是让第j个元素自成一个字段，于是可得转移方程：$${dp[i][j]=max\lbrace dp[i][j-1]+a[j],dp[i-1][t]+a[j]\rbrace(t\leq j)}$$                                                                           

​		我们可以存下$dp[i-1][t]$的最大值优化，具体步骤为通过m次循环省去第一维的空间，在第i次循环内$max[j]$表示为前j个元素中最大的i段字段和。

$${dp[j]=max\lbrace dp[j-1]+a[j],Max[j-1]+a[j]\rbrace \\Max[j]=max\{Max[j-1],dp[j]\}}$$

#### 代码

```c++
#include <iostream>
#include <algorithm>
#include <cstring>
using namespace std;
const int MAX=1e6+10;
const int INF=-1e9;
int a[MAX],dp[MAX],Max[MAX];
int m,n,mmax; 
int main(){
    while(cin>>m>>n){
        for(int i=0;i<n;i++) cin>>a[i];
        memset(dp,0,sizeof(dp));
        memset(Max,0,sizeof(Max));
        for(int i=0;i<m;i++){
            mmax=INF;
            for(int j=i;j<n;j++){
                dp[j]=max(dp[j-1]+a[j],Max[j-1]+a[j]);
                Max[j-1]=mmax;//Max[j-1]在一次循环内只会用到一次故可以直接更新为下次循环做准备
                mmax=max(mmax,dp[j]);
            }
        }
        cout<<mmax<<endl;
    }
}
```



### [HDU 1029 Ignatius and the Princess IV](http://acm.hdu.edu.cn/showproblem.php?pid=1029)

#### 题意

> 给你n个数字，请你找出出现至少(n+1)/2次的数字。

#### 题解

​		可以直接排序后统计完成此题，也可以通过线性完成。方法为：记录下当前数字m与次数ct，每当读取数据k，若k=m则让ct++反之ct--，若$ct \leq 0$ 则更新m为k。原理是记载至少出现$\frac{n+1}{2}$的数字那么平均情况下至少每两个数出现一次此数字。

#### 代码

​	

```c++
#include <iostream>
using namespace std;
int n,m,ct;
int main()
{
    while(cin>>n)
    {
        int time=0;
        while(n--)
        {
            cin>>m;
            if(time<=0)
                ct=m;
            m==ct?++time:--time;
        }
        cout<<ct<<endl;
    }
    return 0;
}
```

### [HDU 1069 Monkey and Banana](http://acm.hdu.edu.cn/showproblem.php?pid=1069)

#### 题意

> 给n种物品，每种物品都有无限个，每个物品都有长宽高。将其排列为一堆，上面的物品的长宽必须严格小于下方物品的长宽，求最大高。

#### 题解

​		由于物品可旋转，每个物品都可衍生出六种不同的形态，将其分开储存。建立一维数组$dp[i]$，i表示为第i种物体为顶时的最大高度。

#### 代码

```c++
#include <iostream>
#include <algorithm> 
#include <cstring>
using namespace std;
struct block{
    int x,y,z;
}b[1000];
bool cmp(block a,block b){
    if(a.x=b.x)return a.y<b.y;
    return a.x<b.x; 
}
int dp[1000];
int main(){
    int n,t=0;
    while(cin>>n&&n){
        int ct=0;
        memset(b,0,sizeof(b));
        for(int i=0;i<n;i++){
            int x,y,z;
            cin>>x>>y>>z;
            b[ct].x=x;b[ct].y=y;b[ct++].z=z;
            b[ct].x=x;b[ct].y=z;b[ct++].z=y;
            b[ct].x=y;b[ct].y=x;b[ct++].z=z;
            b[ct].x=y;b[ct].y=z;b[ct++].z=x;
            b[ct].x=z;b[ct].y=y;b[ct++].z=x;
            b[ct].x=z;b[ct].y=x;b[ct++].z=y;
        }
        sort(b,b+ct,cmp);
        for(int i=0;i<ct;i++)dp[i]=b[i].z; 
        for(int i=0;i<ct;i++)
            for(int j=0;j<i;j++)
                if(b[i].x>b[j].x&&b[i].y>b[j].y)
                    dp[i]=max(dp[i],dp[j]+b[i].z);
        int ans=0;
        for(int i=0;i<ct;i++)ans=max(ans,dp[i]);
        cout<<"Case "<<++t<<": maximum height = "<<ans<<endl;
    }
}

```

### [POJ 1015 Jury Compromise](http://poj.org/problem?id=1015)

#### 题意

> 给n个人，每个人有甲乙两方不同的评价，分值由0~20。让你选出m个人，试两方评价差尽可能小的情况下总评价尽可能高。($n \leq 200,m \leq 20$)

#### 题解

​		让问题失去后效性的想法是让评价差作为状态的一环，但评价差可能为负数，因此我们需要让评价差这一维度映射到0~400。接着是n个人中选m个人的问题，若让m循环在n外那么就有可能出现一个人反复选择的问题。这么想我们就已经得出了结论：$$dp[i][j]=max\{dp[i-1][j-sub[k]]+sum[k],dp[i][j]\}$$。其次我们必须由可行状态出发求解，因此我们需要给不可行状态标记，避免由其产生解。最后加上三维数组记录下路径即可。

#### 代码

```c++
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<vector>
using namespace std;
const int maxn=801;
int n,m,dp[maxn][maxn],d[maxn],v[maxn],id[maxn];
vector<int> path[21][801];  

int main(){
	int cas=1;
	while(scanf("%d%d",&n,&m)&&(n||m)){
		memset(dp,-1,sizeof dp);
		for(int i=0;i<m;++i)//清空vector  
            for(int j=0;j<801;++j)  
                path[i][j].clear();
		for(int i=0;i<n;i++){
			int t1,t2;
			scanf("%d%d",&t1,&t2);
			d[i]=t1-t2;
			v[i]=t1+t2;
		}
		int fix=20*m;//映射评价差
		dp[0][fix]=0;//唯一初始可行状态
		for(int k=0;k<n;k++)
			for(int i=m-1;i>=0;i--){
				for(int j=0;j<2*fix;j++){
					if(dp[i][j]<0)continue;//避免不可行状态
					if(dp[i+1][j+d[k]]<=dp[i][j]+v[k]){
						dp[i+1][j+d[k]]=dp[i][j]+v[k];
						path[i+1][j+d[k]]=path[i][j];//记录路径
						path[i+1][j+d[k]].push_back(k);
					}
				}
			}
		int i;
        for(i = 0; dp[m][fix+i] == -1 && dp[m][fix-i] == -1; i++);
        int temp = (dp[m][fix+i] > dp[m][fix-i]) ? i : -i;
        int sumD = ( dp[m][fix+temp] + temp )/2;
        int sumP = ( dp[m][fix+temp] - temp )/2;
        printf( "Jury #%d\n", cas++ );
        printf( "Best jury has value %d for prosecution and value %d for defence:\n", sumD,sumP);
        for( i=0; i < m; i++ )
            printf( " %d", path[m][fix+temp][i]+1);
        printf( "\n\n" );

	}
}

```

## 结语

> 还没刷完就别放屁了。