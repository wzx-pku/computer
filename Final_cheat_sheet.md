### 1.输入输出与字符串处理
#### 输入处理
`str.split(sep=None) === str.split()` 一般情况下`split(sep='xxx')`
`info = list(map(int,input().split()))；info = [int(x) for x in input().split()]
#### 字符串处理
`str.title()`首字母大写（每个单词）  
`str.lower()/upper(）`每个字母小/大写  
`str.strip()`去除空格，`rstrip/lstrip`去掉尾部/头部的空格 
`str.find()`查找指定字符，注意如果有的话会返回第一个找到的，如果没有会返回-1而不是报错！
`str.zfill()`自动在前面补0补到所需总共位数
#### 格式化输出
`f-string print(f'{ans},{res:b(可省).af},{item:b}')`表示保留n位小数，表示右侧空格填充到b个字符宽度，'>''<'表示右对齐，左对齐
`print（xxx,sep/end='')`
`''.join(map(str,s))`将列表s中的元素无间隔连接
### 2.数学运算
`float('inf')`表示无穷大，在初始化某些边界值时可能有用
round不是严格意义上的四舍五入，遇到恰好.5会向偶数舍入。
floor和ceil 下整和上整
float的等于判断不能用“== ”，要用绝对值的差小于某个极小量
进制转化：bin,oct,hex 注意得到的是带前缀的字符串(2,8,16进制)
### 3.列表与字典
#### 列表（有序）
注意函数有无返回值：`list.sort(),list.reverse()
列表解析式：`[f(x) for x in list (if P(x))] ``
列表可以相加，相当于连接；对应`list.extend([] 或 list)`方法（append只能一次添加一个元素）
遍历列表的时候通常用for循环；若一边循环一边删除/添加元素。,必须的话建议改用while循环。
#### 字典（无序键值对）
按顺序遍历通常用`for x in sorted(dict.keys()/values())`
`dict.keys()/values()/items()`分别返回键/值/键值对列表
`dict.get(key,default=None)`查找指定key的value，如果key不存在不会报错而是返回给定的默认值
`keys=[key for key,value in d.items() if value==target]`根据值找到==所有==的键 `key=next((key for key,value in d.items() if value==target),None)`根据值找到==第一个==对应的键
#### 枚举enumerate
一般用法是`for i,x in enumerate(list)`,遍历list中的（下标，值）对
### I.DP
#### 1.背包问题
  如果背包恰好装满，则dp=[0]+[float('-inf')]
1. **01背包** --> 每个物品只能拿一次，选择放或不放，使得最后金额最大（滚动数组优化）
```python
def bag(V,n,cost,price):# V-总容量,n-物品种类个数,cost=[0,     ],price=[0,     ](注意建表的时候都有0)
    dp=[0]*(V+1)
    for i in range(1,n+1):           #每个物品
        for j in range(V,cost[i]-1,-1):    #逆向遍历每个容量
            dp[j]=max(dp[j],price[i]+dp[j-cost[i]])
    return dp[-1]
```
2. **完全背包** --> 每个物品可以拿无限次
```python
def bag(v,n,cost,price):
    dp = [0] * (v + 1)  # 初始化：如果不要求恰好装满，全部设为0[来源 17]
    for i in range(1, n + 1):  # 遍历每个物品
        for j in range(cost[i], v + 1):  # 正向遍历每个容量
            dp[j] = max(dp[j], dp[j - cost[i]] + price[i])
    return dp[-1]
```
3. **多重背包** --> 每个物品的个数有限制,把每个物品的个数拆成1 2 4等转化为01背包
```python
def bag(v,n,s,cost,price):#s=[0,   ]为每个物品的个数
    dp=[0]*(v+1)
    for i in range(1,n+1):
        k=1
        while s[i]>0:
            cnt=min(k,s[i])
            for j in range(V,cnt*cost[i]-1,-1):
                dp[j]=max(dp[j],cnt*price[i]+dp[j-cnt*cost[i]])
            s[i]-=cnt
            k*=2
    return dp[-1]
```
4. **二维费用背包** --> 有两种费用（e.g.重量和体积）两个分别由最大限制 01转化为二维
```python
def two_dimension_cost(n,V1,V2,cost1,cost2,price):
    dp=[[0]*(V2+1) for _ in range(V1+1)]
    for i in range(n):
        for c1 in range(V1-1,cost1[i]-1,-1):
            for c2 in range(V2-1,cost2[i]-1,-1):
                dp[c1][c2]=max(dp[c1][c2],dp[c1-cost1[i]][c2-cost2[i]]+price[i])
    return dp[-1][-1]
```
---
#### 2.整数分割问题
1. 把n划分为若干个正整数，**不考虑顺序** --> 完全背包
```python
def divide1(n):
    dp=[1]+[0]*n    #把0划分只有0这一种
    for i in range(1,n+1):           #每个数字
        for j in range(i,n+1):             #正向遍历每个容量（每个n）
            dp[j]+=dp[j-i]
    return dp[-1]
```
2. 把n划分为若干个正整数，**考虑顺序**
```python
def divide2(n):
    dp=[1]+[0]*n
    for i in range(1,n+1):           #每个容量（每个n）
        for j in range(1,i+1):             #每个可能划分出的数字
            dp[i]+=dp[i-j]
    return dp[-1]
```
3. 把n划分为若干个**不同的正整数**，不考虑顺序--> 01背包
```python
def divide3(n):
    dp=[1]+[0]*n
    for i in range(1,n+1):
        for j in range(n,i-1,-1):
            dp[j]+=dp[j-i]
    return dp[-1]
```
4. 把n划分为**k个正整数**，不考虑顺序
```python
#dp[n][k]:把n分成k组
def divide4(n,k):
    dp=[[0]*(k+1) for _ in range(n+1)]
    #每个数字分成1组都是1种
    for i in range(n+1):
        dp[i][1]=1
    for i in range(1,n+1):
        for j in range(2,k+1):
            #i<j时无法划分
            #i>=j时分为两种：若分组中有1，则为dp[i-1][j-1]
            #若无1，先把每组放进去1，则为dp[i-j][j]
            if i>=j:
                dp[i][j]=dp[i-1][j-1]+dp[i-j][j]
    return dp[n][k] #dp[-1][-1]
```
---
#### 4.设置多个dp数组+数学归纳思维
  设置dp1和dp2两个数组记录两种状态，一般定义dp1[i]为取s[i]，dp2[i]为不取s[i]
  
---
#### 5.Kadane算法
解决最大子数组和，即在一个一维整数数组中找到具有最大和的连续子数组
```python
**最大子数组和**
def kadane(s): #一维
    curr_max=total_max=s[0]
    for i in range(1,len(s)):
        curr_max=max(curr_max+s[i],s[i])
        total_max=max(total_max,curr_max)
    return total_max

**最大子矩阵和**
def kadane(s): #二维，压缩到一维数组
    curr_max=total_max=s[0]
    for i in range(1,len(s)):
        curr_max=max(curr_max+s[i],s[i])
        total_max=max(total_max,curr_max)
    return total_max
def max_sum_matrix(mat): #上下压缩
    max_sum=-float('inf')
    row,col=len(mat),len(mat[0])
    for top in range(row):
        col_sum=[0]*col
        for bottom in range(top,row):
            for c in range(col):
                col_sum[c]+=s[bottom][c]
            max_sum=max(max_sum,kadane(col_sum))
    return max_sum
```
---
### II.Dilworth Theory
>**最少单调链个数,最长反单调链长度**
>找===最长上升子序列的长度===，用left
>找===最长下降子序列===，先reverse，再用left
>如果是===不降===，用right
>如果是===不升===，先reverse，再用right
>看题目要求的最终结果==是否需要相同元素==的考虑，需要考虑用left，不需要用right
>bisect_left -->left  ； bisect_right -->right 
```python
from bisect import bisect_left,bisect_right
def d(s): #求最长上升子链长度
    lst=[]
    for i in s:
        pos=bisect_left(lst,i)
        if pos<len(lst):
            lst[pos]=i
        else:
            lst.append(i)
    return len(lst)
```
---
### III.PREFIX SUM  前缀和
#### 1.前缀和数组
用于处理**多次查询**从[l,r]的序列之和的问题
```python
s=[int(i) for i in input().split()]
prefix=[s[0]]+[0]*(len(s)-1)
for i in range(1,len(s)):
    prefix[i]=prefix[i-1]+s[i]
distance_l_r=prefix[r]-(prefix[l-1] if l-1>=0 else 0)
```
---
#### 2.二维前缀和
快速二维区域求和
```python
# 假设坐标范围是 [0, max_coord]
max_coord = 1024
size = max_coord + 1

# 构建二维前缀和数组（1-indexed，大小为 (size+1) x (size+1)）
prefix = [[0] * (size + 1) for _ in range(size + 1)]

# 填充前缀和数组，其中 val 是坐标 (i-1, j-1) 处的值
for i in range(1, size + 1):
    for j in range(1, size + 1):
        val = ...  # 获取 (i-1, j-1) 处的值，例如从字典 moskow.get((i-1, j-1), 0)
        # e.g.
	    # for _ in range(n):
        #     x, y, weight = map(int, input().strip().split())
        # if (x, y) not in moskow:
        #     moskow[(x, y)] = 0
        # moskow[(x, y)] += weight
        prefix[i][j] = val + prefix[i - 1][j] + prefix[i][j - 1] - prefix[i - 1][j - 1]
        
# 查询矩形区域 [x1, y1] 到 [x2, y2] 的和（坐标是 0-indexed）
def query(x1, y1, x2, y2): # 转换为 1-indexed 的前缀和坐标
    return prefix[x2 + 1][y2 + 1] - prefix[x1][y2 + 1] - prefix[x2 + 1][y1] + prefix[x1][y1]
```
---
#### 3.前缀和的特殊用法(哈希表)
使用prefix和prefix_map来记录已有的前缀和，从而===判断子串和为0的子串个数===；或找==相同前缀和数字出现的最远位置==
```python
#找出不重叠的和为0的子序列个数，一旦找到就将prefixed集合清空
t = int(input())
for _ in range(t):
    n = int(input())
    a = list(map(int, input().split()))
 
    prefix = 0
    prefixed = {0}
    cnt = 0
    for i in a:
        prefix += i
        if prefix not in prefixed:
            prefixed.add(prefix)
        else:
            cnt += 1
            prefix = 0
            prefixed={0}
    print(cnt)
```
---
### IV.SORTING
sort() --> 稳定的从小到大排序，如果列表存储的是多元元组，则依次按照每个元组的元素进行排序，且稳定
#### 1.归并排序 --> 递归
```python
def merge_sort(s):
    if len(s)<=1:
        return s
    mid=len(s)//2
    left=merge_sort(s[:mid])
    right=merge_sort(s[mid:])   #两次递归放在一起
    return merge(left,right)
def merge(l,r):
    ans=[]
    i=j=0
    while i<len(l) and j<len(r):
        if l[i]<r[j]:
            ans.append(l[i])
            i+=1
        else:
            ans.append(r[j])
            j+=1
    ans.extend(l[i:])
    ans.extend(r[j:])
    return ans
```
#### 2.快速排序 --> 递归，选基准
```python
def quick_sort(s):
    if len(s)<=1:
        return s
    base=s[0]
    left=[x for x in s[1:] if x<base]
    right=[x for x in s[1:] if x>=base]
    return quick_sort(left)+[base]+quick_sort(right)
```
---
#### 3.lambda函数 -->自行按照元组的元素顺序排序
```python
#按照第二个元素排序
s.sort(key=lambda x:x[1])
#按照第二个元素为首要升序排序，第一个元素为次要升序排序
s.sort(key=lambda x:(x[1],x[0]))
#按照第二个元素为首要降序排序，第一个元素为次要升序排序
s.sort(key=lambda x:(-x[1],x[0]))
#-----------------------------#
#如果想对数字按照**字典序组合**排序，得到最大最小整数，可以冒泡可以匿名
s=[9989,998]
#冒泡,转化为字符串
for i in range(len(s)-1):
    for j in range(len(s)-i-1):
        if str(s[j])+str(s[j+1])<str(s[j+1])+str(s[j]):
            s[j],s[j+1]=s[j+1],s[j]
#lambda函数
s=sorted(s,key=lambda x: str(x)*10,reverse=True)
#-----------------------------#
#对字典的**键值对**进行排序，与列表存储元组差不多
d={3:34,2:23,9:33,10:33}
dd=dict(sorted(d.items(),key=lambda x:(x[1],-x[0]))) #{2: 23, 10: 33, 9: 33, 3: 34}
#-----------------------------#
s.sort(key=lambda x:(px[a],qx[b]...)) #从前往后是有限排序的顺序，p/q为+—1来表示顺序/逆序
```
---
### V.SEARCHING
#### 1.dfs
防止递归深度过大，可以这样==调整递归深度==：
```python
import sys
sys.setrecursionlimit(1 << 30)
```
如果dfs内部有类似于dp数组==需要不断访问某些元素的值==的时候，除了==开空间创建一个dp==，还可以用==lru_cache==。注意书写位置
```python
from functools import lru_cache
@lru_cache(maxsize=2048) #或者更大，如None，考虑内存因素自行调整
def dfs():
```
1. 无回溯操作（原地修改）
```python
dx=[-1,0,1,-1,1,-1,0,1]
dy=[-1,-1,-1,0,0,1,1,1] #前后左右移动（这个包括了对角移动）
def dfs(x,y):
    m[x][y]='.'
    for k in range(8):
        nx=x+dx[k]
        ny=y+dy[k]
        if 0<=nx<=n-1 and 0<=ny<=s-1 and m[x][y]=='W':
            dfs(nx,ny)
```
2. 有回溯操作
```python
#输出所有可能的最短路径
vis = [[False]*n for _ in range(m)]
directions = [(-1,0),(1,0),(0,1),(0,-1)]
ans = [] #存储所有最短路径
dist = float('inf') #设最短路径目前是无穷大
def dfs(r,c,pre=[],step=0): #pre 储存当前路径
    if step>dist: #查看当前路径若比已知最短长，直接返回
        return
    if vis[r][c] or info[r][c]=='#': #info是指输入的地图二维数组
        return
    vis[r][c] = True
    pre.append((r,c)) #标记访问路径
    if r==end_r and c==end_c:
        if step==dist:
            ans.append(pre[:]) #要加入的是pre的copy，因为pre会变
            return
        elif step < dist:
            ans = [pre[:]]
            dist = step
            return
    for dr,dc in directions:
        if 0<=r+dr<m and 0<=c+dc<n:
            dfs(r+dr,c+dc,pre,step+1)
    vis[r][c] = False
    pre.pop() #回溯，“撤销选择”
```
---
#### 2.BFS
```python
from collections import deque
dx,dy=[0,-1,1,0],[-1,0,0,1]
def bfs(x,y,final): #从x,y出发，找目标值final
    q=deque() #创建队列
    q.append((x,y))
    inq=set() #集合用来记录已访问的位置
    inq.add((x,y))
    step=1
    while q:
        for _ in range(len(q)): #遍历这一层，可以不写这一行，但是写了更清晰
            x,y=q.popleft()
            for i in range(4):
                nx,ny=x+dx[i],y+dy[i]
                if s[nx][ny]==final: #可以加if 0<=nx<n and 0<=ny<m：检查边界
                    return step
                if 0<=nx<n and 0<=ny<m and s[nx][ny]==1 and (nx,ny) not in inq:
                    q.append((nx,ny))
                    inq.add((nx,ny))
        step+=1
    return None
```
---
#### 3.Dijkstra算法
解决单源最短路径问题，用于非负权图（最短路径下还要保证其他量的最大/小）
```python
import heapq
dx,dy=[0,-1,1,0],[-1,0,0,1]
def dijkstra(sx,sy,ex,ey):
    if s[sx][sy]=='#' or s[ex][ey]=='#':
        return 'NO'
    q=[]
    dist=[[float('inf')]*m for _ in range(n)]
    heapq.heappush(q,(0,sx,sy)) #(distance,x,y)
    dist[sx][sy]=0
    while q:
        curr,x,y=heapq.heappop(q) #heappop()
        if (x,y)==(ex,ey):
                return curr

        for i in range(4):
            nx,ny=x+dx[i],y+dy[i]
            if 0<=nx<n and 0<=ny<m and s[nx][ny]!='#':
                new=curr+abs(s[x][y]-s[nx][ny])
                if new<dist[nx][ny]:
                    heapq.heappush(q,(new,nx,ny)) #heappush()
                    dist[nx][ny]=new
    return 'NO'
```
---
### VI.DATA STRUCTURE
#### 1.STACK 栈（后进先出）
**辅助栈** -->查询栈内最大/最小值
```python
stack,min_so_far=[],[]
while True:
    try:
        s=input()
    except EOFError:
        break
    if s[-1].isdigit():
        n=int(s.split()[1])
        stack.append(n)
        if not min_so_far:
            min_so_far.append(n)
        else:
            min_so_far.append(min(min_so_far[-1],n))
    elif s=='pop' and stack:
        stack.pop()
        min_so_far.pop()
    elif s=='min' and stack:
        print(min_so_far[-1])
```
**单调栈**：
```python
s=list(map(int,input().split()))
stack=[]
ans=0
for i in range(len(s)):
    while stack and s[stack[-1]]<s[i]: #当栈不为空且当前考察的元素大于栈顶元素时
									   #此为单调递减栈，单调递增栈是'>'(求最大矩形面积)
    #找到数组中每个元素右边第一个更大的数
	    while stack and nums[i] > nums[stack[-1]]: 
	            index = stack.pop()
	            result[index] = nums[i]
	        stack.append(i) # 将当前元素的索引压入栈中
	    while stack: # 对于栈中剩余的元素，它们没有更大的元素
	        index = stack.pop()
	        result[index] = -1
	    return result
	    
	#接雨水问题
        curr=s[stack.pop()]
        if not stack: #左边没有更高的柱子
            break
        curr_w=i-stack[-1]-1
        curr_h=min(s[i]-curr,s[stack[-1]]-curr)
        ans+=curr_w*curr_h
    stack.append(i)
print(ans)
```
---
#### 2.HEAPQ
`import heapq`
**最小堆**(heapq)可以维护列表中的最小值并将其位置放在第一个，即heap[0]。如果想得到最大值，以负值形式存入。`a=heappop(s) #弹出最小元素` `heapq.heappush(heap,p) #添加元素（按照堆-分叉树-排列）`
且最小堆通常涉及到内部元素的删除，而内置函数无此操作，则会利用到**懒删除**操作，使用==字典记录已被删除的元素==，需要==取最小值时再一次性删除==。`from collections import defaultdict out=defaultdict(int)

---
### VII.INTERVAL PROBLEMS
1. 合并所有有交集的区间，返回最终个数--对左端点排序，不断更新右边界
```python
s.sort(key=lambda x:x[0])
cnt,ans,l,r=1,[],s[0][0],s[0][1]
for i in range(1,n):
    if s[i][0]<=r:
        r=max(r,s[i][1])
    else:
        ans.append([l,r])
        l,r=s[i][0],s[i][1]
        cnt+=1
print(cnt,ans)
```
2. 选择尽量多的无交集的区间，返回最大数量--对右端点排序
```python
s.sort(key=lambda x:x[1])
cnt,r=1,s[0][1]
for i in range(1,n):
    if s[i][0]<=r:
        continue
    cnt+=1
    r=s[i][1]
print(cnt)
```
3. 区间选点--取尽量少的点，使得每个区间内至少有一个点--对右端点排序
```python
k=int(input())
for _ in range(k):
    n=int(input())
    curr=0
    cnt=0
    sd=[]
    for _ in range(n):
        s,d=map(int,input().split())
        sd.append([s,d])
    sd.sort(key=lambda x:x[1])
    for i in range(0,n):
        if sd[i][0]>curr:
            curr=sd[i][1]
            cnt+=1
    print(cnt)
```
4. 区间覆盖--最少选择多少区间可以**覆盖**掉题中给出的这段目标区间--对左端点排序，从起点开始每次选最远的右端点
```python
def min_cameras(ranges):
    n=len(ranges)
    mx=max(ranges)
    curr=0
    num=0
    while curr<n:
        next=curr+ranges[curr]+1
        for i in range(max(0,curr-mx),min(n,curr+mx+1)):
            next=max(next,i+ranges[i]+1)
        num+=1
        curr=next
    return num
```
5. 区间分组--最少可以将这些区间分成多少组使得每个组内的区间互不相交--对左端点排序-转为事件（在排序时增加第二个元素）
```python
def min_host(n,ranges):
    events=[]
    for i in range(n):
        events.append((ranges[i][0],1)) #新添1，表示出现一个起点时主持人数加1
        events.append((ranges[i][1],-1)) #新添-1，表示出现一个终点时主持人数减1
        #最后统计主持人数聚集最多的个数，就是答案
    events.sort(key=lambda x:(x[0],x[1]))
    min_hosts=0
    curr=0
    
    for time,num in events:
        curr+=num
        min_hosts=min(min_hosts,curr)
    return min_hosts
```
---
### VIII.Other trivial things
#### 1.求解或判断质数
```python
#埃氏筛
primes = []
is_prime = [True]*N #假设全为素数
is_prime[0] = False;is_prime[1] = False
for i in range(1,N):
    if is_prime[i]:
        primes.append(i)
        for k in range(2*i,N,i): #用素数去筛掉它的倍数
            is_prime[k] = False
```
```python
#欧拉筛
def euler_sieve(n):
    primes = []
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False  # 0和1不是质数
    for i in range(2, n + 1):
        if is_prime[i]:
            primes.append(i)  # i 是质数
        for prime in primes:
            if i * prime > n:
                break
            is_prime[i * prime] = False
            # 如果 prime 是 i 的最小质因数，停止继续筛选
            if i % prime == 0:
                break
    return primes
```
---
#### 2.分解质因数
```python
def pFactors(n):
    from math import sqrt #sqrt()开方
    pFact, limit, check, num = [], int(sqrt(n)) + 1, 2, n
    for check in range(2, limit):
        while num % check == 0:
            pFact.append(check)
            num /= check
    if num > 1:
        pFact.append(num)
    return pFact
```
---
### BASIC GRAMMAR
#### 1.二分查找# （Binary Search）
在进行二分之前一般需要对列表进行排列。（最小化最大值，最大化最小值）
```python
def binary_search():
    l=0
    r=(s[-1]-s[0])//(c-1)
    while l<=r: 
        mid=(l+r)//2
        if can_reach(mid): #查是否可行
            l=mid+1 #尝试更大距离
        else:
            r=mid-1
    return r 
def can_reach(mid):
    cnt=1
    curr=s[0]
    for i in range(1,n):
        if s[i]-curr>=mid:
            cnt+=1
            curr=s[i]
    return cnt>=c
```
---
#### 2.排列组合
排列数：`permutations(list,r)`其中r默认是全排列，若`list=[1,2,3];r=2`，则会输出从列表任取两个数进行全排列的所有排列。
```python
from itertools import permutations #时间复杂度为n!
perms=permutations([1,2,3])  #此时perms是一个迭代器，需要用for取出，或者转成列表
for perm in perms:
    print(perm) #为元组
perms_list=list(permutations([1,2,3]))
print(perms_list)
```
组合数：`combinations(list,r)`第二个r参数必不可少。
```python
from itertools import combinations
combs=combinations([1,2,3],3)
for c in combs:
    print(c)
```
---
#### 3.zip函数
`zip()`将多个可迭代对象进行组合，成为一个一个的元组，返回值是一个zip对象，可以转为list或dict
```python
a=[1,2,3]
b=['n','m','l']
zipped=zip(a,b)
z_list=list(zipped)
z_dict=dict(zipped)
####还可以进行解压####
zipped = [('Alice', 25), ('Bob', 30), ('Charlie', 35)]
# 解压
names, ages = zip(*zipped)
print(names)  # ('Alice', 'Bob', 'Charlie')
print(ages)   # (25, 30, 35)
```
