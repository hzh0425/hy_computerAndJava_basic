### 第 220场周赛

#### 1.[删除子数组的最大得分](https://leetcode-cn.com/problems/maximum-erasure-value/)

![image-20201220195724922](https://gitee.com/zisuu/picture/raw/master/img/20201220195724.png)



**题目分析**

这题有很明确的题意:寻找一个窗口,没有重复数字,和为所有窗口的最大值

所以很明显可以用滑动窗口解题

```c++
class Solution {
public:
int maximumUniqueSubarray(vector<int>& nums) {
    int ans=0;
    set<int> st;
    int temp=0;
    int left=0,right=0;
    while(right<nums.size())
    {
        int curR=nums[right++];
        if(st.find(curR)==st.end())
        {
            temp+=curR;
            ans=max(ans,temp);
            st.insert(curR);
        }
        else
        {
            while(curR!=nums[left])
            {
                temp-=nums[left];
                st.erase(st.find(nums[left]));
                left++;
            }
            //这里不用再删除区间里相同的元素了,因为curR并没有被加到temp中
            left++;
        }
    }
    return ans;
}
};
```

#### 2.[跳跃游戏 VI](https://leetcode-cn.com/problems/jump-game-vi/)

![image-20201220202010091](https://gitee.com/zisuu/picture/raw/master/img/20201220202010.png)

##### 解法一

这道题无非就是dp

那么很明显，每次我们只需要找到dp[i - 1]、dp[i - 2]、...、dp[i - k]的最大值，然后加上当前元素的大小，就可以得到dp[i]。

但是简单的dp会超时!!!!!

于是,我们想到,可以用线段树来求区间的最大值,并且求完dp[i]之后,要注意修改线段树的区间值

```c++
#线段树模板    
class NumArray {
public:
    #define ls k<<1
    #define  rs k<<1|1
    vector<int> t;
    vector<int> nums;
    int size;
    NumArray(vector<int> nums) {
        size=nums.size();
        t=vector<int>(size*4+1,0);
        this->nums=nums;
        build(1,0,size-1);
    }
    void update_point(int p,int v)
    {
        update(1,0,size-1,p,v);
    }
    int getMax(int i,int j)
    {
        return query_max(1,0,size-1,i,j);
    }
    //////////////////////////////////////
    void pushUp(int k)
    {
        t[k]=max(t[ls],t[rs]);
    }
    void build(int k,int l,int r)
    {
        if(l==r)
        {
            t[k]=nums[l];
        }
        else
        {
            int m=l+(r-l)/2;
            build(ls,l,m);
            build(rs,m+1,r);
            pushUp(k);
        }
    }
    int query_max(int k,int l,int r,int L,int R)
    {
        if (l>=L&&r<=R)
            return t[k];
        else
        {
            int left=INT_MIN,right=INT_MIN;
            int m=l+(r-l)/2;
            if(L<=m)
                left=query_max(ls,l,m,L,R);
            if(R>m)
                right=query_max(rs,m+1,r,L,R);
            return max(left,right);
        }
    }
    void update(int k,int l,int r,int p,int v)
    {
        if(l==r)
        {
            t[k]=v;
        }
        else
        {
            int m=l+(r-l)/2;
            if(p<=m)
                update(ls,l,m,p,v);
            else if(p>m)
                update(rs,m+1,r,p,v);
            pushUp(k);
        }
    }
};


int maxResult(vector<int>& nums, int k) {
    int size=nums.size();
    if(size==0)
        return 0;
    NumArray numArray(nums);
    vector<int> dp(size+1,0);
    dp[0]=nums[0];
    numArray.update_point(0,dp[0]);
    //对于每个点,求其之前的[i-k,i-1]区间中的最大值
    //求完之后更新线段树
    for(int i=1;i<size;i++)
    {
        int left=i-k>=0?i-k:0;
        int right=i-1;
        dp[i]=numArray.getMax(left,right)+nums[i];
        numArray.update_point(i,dp[i]);
        cout<<dp[i]<<" ";
    }
    cout<<endl;
    return dp[size-1];
}

```

##### 解法二

上面的解法我们用到了线段树,但是效率并不高

现在我们用同样的思路,只是线段树变成了单调栈

也即我们维护一个单调递减栈,那么区间[i-k,i-1]的最大值就是栈头



```c++
class Solution {
public:
int maxResult(vector<int>& nums, int k) {
    int ans=0;
    deque<int> dq;
    int size=nums.size();
    vector<int> dp(size,0);
    dp[0]=nums[0];
    dq.push_back(0);
    for(int i=1;i<size;i++)
    {
        //向右滑动,删除i-k-1
        if(i>k&&i-k-1==dq.front())dq.pop_front();
        int cur=nums[i];
        dp[i]=dp[dq.front()]+cur;
        //单调递减的逻辑
        while(!dq.empty()&&dp[dq.back()]<dp[i])dq.pop_back();
        dq.push_back(i);
    }
    return dp[size-1];
}
};
```

#### 3.[检查边长度限制的路径是否存在](https://leetcode-cn.com/problems/checking-existence-of-edge-length-limited-paths/)

![image-20201220204403986](https://gitee.com/zisuu/picture/raw/master/img/20201220204404.png)

![image-20201220204417345](https://gitee.com/zisuu/picture/raw/master/img/20201220204417.png)

**并查集 + 离线思维**

「离线」的意思是，对于一道题目会给出若干询问，而这些询问是全部提前给出的，也就是说，你不必按照询问的顺序依次对它们进行处理，而是可以按照某种顺序（例如全序、偏序（拓扑序）、树的 DFS 序等）或者把所有询问看成一个整体（例如整体二分、莫队算法等）进行处理。

与「离线」相对应的是「在线」思维，即所有的询问是依次给出的，在返回第 kk 个询问的答案之前，不会获得第 k+1k+1 个询问。

实际上，力扣平台上几乎所有的题目都是「离线」的，即一次性给出所有的询问。但在大部分情况下，我们按照下标顺序处理这些询问是没有问题的，也就是用「在线」的思维在「离线」的场景下解决问题。然而对于本题而言，我们必须按照一定的顺序处理 \textit{queries}queries 中的询问，否则会使得时间复杂度没有保证。

本题需要的前置技能点为「并查集」，如果读者没有掌握，可以尝试查阅相关资料进行学习。

![image-20201220210001363](https://gitee.com/zisuu/picture/raw/master/img/20201220210001.png)

```c++
class UF{

private:
    vector<int> parent;

public:
    UF(int n) : parent(n){
        for(int i = 0 ; i < n ; i ++)
            parent[i] = i;
    }

    int find(int p){
        if(p != parent[p])
            parent[p] = find(parent[p]);
        return parent[p];
    }

    bool isConnected(int p, int q){
        return find(p) == find(q);
    }

    void unionElements(int p, int q){

        int pRoot = find(p), qRoot = find(q);

        if(pRoot == qRoot) return;

        parent[pRoot] = qRoot;
    }
};

class Solution {
public:
 vector<bool> distanceLimitedPathsExist(int n, vector<vector<int>>& edgeList, vector<vector<int>>& queries) {

        // 对所有的边按照权值排序
        sort(edgeList.begin(), edgeList.end(), [](const vector<int>& e1, const vector<int>& e2){
           return e1[2] < e2[2];
        });

        // 对于所有的 query，把这个 query 的索引 i 添加进去。
        // 之后会对 query 排序，需要使用 query 原来的索引
        for(int i = 0; i < queries.size(); i ++)
            queries[i].push_back(i);
            
        // 对所有 query 按照 limit 进行排序
        sort(queries.begin(), queries.end(), [](const vector<int>& q1, const vector<int>& q2){
            return q1[2] < q2[2];
        });

        UF uf(n); // 并查集
        vector<bool> res(queries.size());
        int ep = 0; // ep 表示当前处理到的边的位置
        
        // 按照 limit 从小到大，依次处理每一个 query
        for(int i = 0; i < queries.size(); i ++){

            // 将还没有添加的，小于当前 query 的 limit 的边进行添加
            while(ep < edgeList.size() && edgeList[ep][2] < queries[i][2])
                uf.unionElements(edgeList[ep][0], edgeList[ep][1]), 
                ep ++;
            
            // 添加以后，看对于这个 query 的 p 和 q，是否连通
            // 注意，答案要放到 res[queries[i][3]] 中，而不是 res[i] 中
            // 因为 queries 数组被排过序
            res[queries[i][3]] = uf.isConnected(queries[i][0], queries[i][1]);
        }
        return res;
    }

};

```

