### 1.对一含*的串打分

![image-20210103171445620](https://gitee.com/zisuu/picture/raw/master/img/20210103171458.png)

```
#include<bits/stdc++.h>
using namespace std;
#include<stdio.h>

int main(){
    int sum=0;  
    int ans=0;  
    char ch;
    while(scanf("%c",&ch) != EOF){
        if(ch == '*'){
            ans++;
            sum+=ans;
        }else{
            ans=0;
        }
    }
    printf("%d\n",sum);
}


```



### 2.n的多种拆分

![image-20210103171714176](https://gitee.com/zisuu/picture/raw/master/img/20210103171714.png)

这题重要的是回朔的递归树要画清楚:



```
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

/**
 * @author hzh
 * @version 1.0
 * @date 2021/1/1 17:46
 */
public class Main {
    static int target;
    private static void printList(List<Integer> arr) {
        System.out.print(target+"=");
        for (Integer k : arr) {
            System.out.print(k + "+");
        }
    }

    private  static void dfs(int n, List<Integer> arr, int begin) {
        if (n == 1) {
            printList(arr);
            System.out.println(1);
        } else {
            for (int i = begin; i <=n / 2; i++) {
                arr.add(i);
                dfs(n - i, arr, i);
                arr.remove(arr.size() -1);
            }
            if(n!=target){
                printList(arr);
                System.out.println(n);
            }
        }
    }
    public static void main(String[] args) {
        int n;
        List<Integer> list = new ArrayList<Integer>();
        Scanner scanner=new Scanner(System.in);
        n=scanner.nextInt();
        target=n;
        dfs(n,list,1);
    }
}
```

### 3.走迷宫

![image-20210103171747298](https://gitee.com/zisuu/picture/raw/master/img/20210103171747.png)

```
#include <bits/stdc++.h>
using namespace std;
int vis[10][10];
int block[10][10];
int direction [2][2]={1,0,0,1};
int n, m, num=0;
int isOk(int x1, int y1)/*判断该点能不能走*/
{
    if(x1 <= n && x1 >= 0 && y1 <= m && y1 >= 0 && !vis[x1][y1] && !block[x1][y1])
        return 1;
    else return 0;
}
void dfs(int x1, int y1)
{
    vis[x1][y1] = 1;
    if(x1 == n && y1 == m)
    {
        num++;
        return ;
    }
    int j;
    for(auto direct:direction)
    {
        int x=x1+direct[0];
        int y=y1+direct[1];
        if(isOk(x,y)){
            dfs(x,y);
            vis[x][y]=0;
        }
    }
}
int main()
{
        int  i, j;
         n=9;
         m=9;
        memset(vis, 0, sizeof(vis));
        num = 0;
        int x,y;
        int size;
        cin>>size;
        for(int i=0;i<size;i++){
            cin>>x>>y;
            block[x][y]=1;
        }
        dfs(0, 0);
        cout<<num;
}
```

### 4.快速幂

![image-20210103171827470](https://gitee.com/zisuu/picture/raw/master/img/20210103171827.png)

输入样例 1 

```
1
31
70
91
473
512
811
953
0
```

输出样例 1

```
0
6
8
9
11
9
13
12
```

迭代加深算法:

```

import java.io.IOException;
import java.util.*;

/**
 * @author hzh
 * @version 1.0
 * @date 2021/1/1 17:46
 */
public class Main {
    static int vis[]=new int[3000];
    static boolean flag=false;
    static int maxDeep =0;
    static int n;
    static  int limit=2000;
    static void iidfs(int cur)
    {
        int t;
        if(cur> maxDeep) return;
        if((vis[cur]<<(maxDeep -cur))<n)
            return;
        if(flag)
            return;
        if(vis[cur]==n)
        {
            flag=true;
            return;
        }
        for(int i=1;i<=cur;i++)
        {
            if(flag) return;
            t=vis[cur]+vis[i];
            if(t>0 && t<limit)
            {
                vis[cur+1]=t;
                iidfs(cur+1);
            }
            t=vis[cur]-vis[i];
            if(t>0 && t<limit)
            {
                vis[cur+1]=t;
                iidfs(cur+1);
            }
        }
    }
    public static void main(String[] args) throws IOException {
        Scanner scanner=new Scanner(System.in);
        while(scanner.hasNextInt()){
            n=scanner.nextInt();
            if(n==0)break;
            maxDeep =0;flag=false;
            vis[1]=1;
            while(!flag){
                maxDeep++;
                iidfs(1);
            }
            System.out.println(maxDeep -1);
        }
    }
}
```

### 5.木棍

![image-20210103171911287](https://gitee.com/zisuu/picture/raw/master/img/20210103171911.png)

```
import java.util.Arrays;
import java.util.Scanner;

/**
 * @author hzh
 * @version 1.0
 * @date 2021/1/1 17:46
 */
public class Main {

    public static void main(String[] args) {
        int m,n;
        int inf=0x3f3f3f3f;
        int ans=inf;
        Scanner scanner=new Scanner(System.in);
        m=scanner.nextInt();n=scanner.nextInt();
        int [] arr=new int[m+1];
        int st[][]=new int[n+1][m+1];
        arr[0]=0;
        for(int i=1;i<=m;i++){
            arr[i]=scanner.nextInt();
        }
        Arrays.sort(arr);
        for(int i=0;i<=n;i++)
            Arrays.fill(st[i],inf);
        st[0][0]=0;
        for(int i=1;i<=n;i++)
        {
            int minValue=st[i-1][i*2-2];
            for(int j=i*2;j<=m;j++){
                minValue=Math.min(minValue,st[i-1][j-2]);
                st[i][j]= minValue + (arr[j] - arr[j - 1]) * (arr[j] - arr[j - 1]);
                if(i==n)
                    ans=Math.min(ans,st[i][j]);
            }
        }
        System.out.println(ans);
    }

}
```

### 6.泡利不相容

![image-20210103171947394](https://gitee.com/zisuu/picture/raw/master/img/20210103171947.png)

```
import java.io.IOException;
import java.util.*;

/**
 * @author hzh
 * @version 1.0
 * @date 2021/1/1 17:46
 */
public class Main {
    public static void main(String[] args) throws IOException {
        Scanner scanner=new Scanner(System.in);
        double m;
        double ans=0;
        m=scanner.nextDouble();
        if(m==1) System.out.println("100.00%");
        else if(m==2) System.out.println("50.00%");
        else if(m==3) System.out.println("66.67%");
        else{
            ans= getMaxm(m)+2;
            System.out.printf("%.2f",ans*100/m);
            System.out.print("%");
        }
    }
    static int getMaxm(double n)
    {
        if(n<=4)return 0;
        if(n%2==0){
            return getMaxm(n/2)+ getMaxm(n/2+1)+1;
        }else{
            return 2* getMaxm((n+1)/2)+1;
        }
    }
}
```

### 7.密文解码

![image-20210103172008594](https://gitee.com/zisuu/picture/raw/master/img/20210103172008.png)

```
#include<bits/stdc++.h>
using namespace std;
int main()
{
    int m,n;
    char d;
    cin>>n>>m;
    getchar();
    d=getchar();
    getchar();
    string str;
    getline(cin,str);
    vector<char> v;
    int ans=0;
    m=m%26;
    //每n个一组,将头元素放入v
    for(int i=0;i<str.length();i++)
    {
        char ch=str[i];
        if(ch>='A'&&ch<='Z'||ch>='a'&&ch<='z')
        {
            if(ch>='A'&&ch<='Z')ch=ch-'A'+'a';
            if(ans==0){
                if(d=='l'){
                    ch=(char)('a'+(ch-'a'-m+26)%26);
                }else{
                    ch=(char)('a'+(ch-'a'+m)%26);
                }
                ans=(ans+1)%n;
                cout<<ch;
            }
            else ans=(ans+1)%n;
        }
    }
}
```



### 8.24点游戏

![image-20210103183320826](https://gitee.com/zisuu/picture/raw/master/img/20210103183320.png)

```
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Scanner;

/**
 * @author hzh
 * @version 1.0
 * @date 2021/1/1 17:46
 */
public class Main {
    static boolean compareStr(String s1,String s2){
        int a=0,b=0;
        for(int i=0;i<s1.length();i++)
        {
            char ch=s1.charAt(i);
            if(ch=='('||ch==')')continue;;
            if(ch=='+'||ch=='-'||ch=='*'||ch=='/')break;
            a=a*10+(ch-'0');
        }
        for(int j=0;j<s2.length();j++)
        {
            char ch=s2.charAt(j);
            if(ch=='('||ch==')')continue;;
            if(ch=='+'||ch=='-'||ch=='*'||ch=='/')break;
            b=b*10+(ch-'0');
        }
        if(a<b)return true;
        else if(a==b)return s1.length()<s2.length();
        else return false;
    }
    static String turn(String s1,String s2,char t){
        if(compareStr(s1,s2)){
            return s1+t+s2;
        }else
            return s2+t+s1;
    }
    static boolean dfs(ArrayList<Double>nums,ArrayList<String> str)
    {
        if(nums.size()==1)
        {
            if(Math.abs(nums.get(0)-24)<1e-6){
                System.out.println(str.get(0));
                return true;
            }else{
                return false;
            }
        }
        for(int i=0;i<nums.size();i++)
        {
            for(int j=0;j<nums.size();j++)
            {
                if(i==j)
                    continue;
                ArrayList<String> strTemp=new ArrayList<>();
                ArrayList<Double> temp=new ArrayList<>();
                double n1=nums.get(i);
                double n2=nums.get(j);

                String str1=str.get(i);
                String str2=str.get(j);
                for(int k=0;k<nums.size();k++)
                {
                    if (k!=i&&k!=j)
                    {
                        temp.add((double)nums.get(k));
                        strTemp.add(str.get(k));
                    }
                }
                for(int type=0;type<4;type++)
                {

                    if (type == 0) {
                        temp.add(n1 + n2);
                        strTemp.add(turn(str1,str2,'+'));
                    }
                    if (type == 1) {
                        temp.add(n1 - n2);
                        strTemp.add(str1+"-"+str2);
                    }
                    if (type == 2) {
                        temp.add(n1 * n2);
                        if(str1.contains("+")||str1.contains("-"))
                            str1="("+str1+")";
                        if(str2.contains("+")||str2.contains("-"))
                            str2="("+str2+")";
                        strTemp.add(turn(str1,str2,'*'));
                    }
                    if (type == 3) {
                        if (n2 != 0) {
                            temp.add(n1 / n2);
                            if(str1.contains("+")||str1.contains("-"))
                                str1="("+str1+")";
                            if(str2.contains("+")||str2.contains("-"))
                                str2="("+str2+")";
                            strTemp.add(str1+"/"+str2);
                        } else
                            continue;
                    }
                    if(dfs(temp,strTemp))
                        return true;
                    temp.remove(temp.size()-1);
                    strTemp.remove(strTemp.size()-1);
                }
            }
        }
        return false;
    }
    public static void main(String[] args) {
        int n;
        ArrayList<Integer> temp=new ArrayList<>();
        ArrayList<Double> num = new ArrayList<Double>();
        ArrayList<String> strlist=new ArrayList<>();
        Scanner scanner=new Scanner(System.in);
        for(int i=0;i<4;i++)
        {
            n=scanner.nextInt();
            temp.add(n);
        }
        Collections.sort(temp,(a1,a2)->a1-a2);
        for(int i=0;i<4;i++)
        {
            n=temp.get(i);
            num.add((double) n*1.0);
            strlist.add(String.valueOf(n));
        }
        boolean isOk=dfs(num,strlist);
        if(!isOk)
            System.out.println("There is no solution.");
    }
}
```

