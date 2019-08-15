
# 组合

---

[toc]

---

## 一、简单组合

>**问题描述**：给定两个整数n和k，返回1..n中所有可能的k个数的组合。

>**示例**：输入：n=4，k=2；输出：[[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]

1. **方法一**：

```
        public void getCombine(List<List<Integer>> ans,LinkedList<Integer> path,int n,int k,int cur){
            if(cur==n+1)//隐含着path相应临界条件，递归出口
                return;
            path.add(cur);
            if(path.size()==k){     //注意将答案输出的时机
                ans.add(new LinkedList(path));
            }
            getCombine(ans,path,n,k,cur+1);
            path.removeLast();
            getCombine(ans,path,n,k,cur+1);
        }
        public List<List<Integer>> combine(int n, int k){
            List<List<Integer>> ans=new LinkedList();
            getCombine(ans,new LinkedList<Integer>(),n,k,1);
            return ans;
        }
```

2. **方法二**：回溯法

>回溯法是一种通过遍历所有可能成员来寻找可行解的算法。若候选不是可行解(或者至少不是最后一个解)，回溯法会在前一步进行一些修改以舍弃该候选，换而言之，回溯并再次尝试。

这是一个回溯法函数，它将第一个添加到组合中的数和现有的组合作为参数。backtrack(first,curr)

1. 若组合完成添加到输出中。
2. 遍历从first到n的所有整数。
     * 将整数i添加到现有组合curr中。
     * 继续向组合中添加更多整数：backtrack(i+1,curr)
     * 将i从curr中移除，实现回溯

>**代码一**

```
        public void backtrack(int first,LinkedList<Integer> curr,int k,int n){
            if(curr.size()==k)
                output.add(new LinkedList(curr));
            for(int i=first;i<=n;++i) {
                curr.add(i);
                backtrack(i+1, curr,k,n);
                curr.removeLast();
            }
        }
        public List<List<Integer>> combine(int n, int k){
            List<List<Integer>> output = new LinkedList();
            backtrack(1,new LinkedList<Integer>(),k,n);
            return output;
        }
```

>**代码二**：代码一的优化，剪枝

    ```
        //p可以声明成一个栈
        //i的极限值满足：n-i+1=(k-pre.size())。
        //[关键]n-i+1是闭区间[i,n] 的长度。
        //k-pre.size()是剩下还要寻找的数的个数。
        private void findCombinations(List<List<Integer>> ans,int n,int k,int index,Stack<Integer> p){
            if(p.size()==k){
                result.add(new ArrayList(p));
                return;
            }
            for (int i=index;i<=n-(k-p.size())+1;i++){
                p.push(i);
                findCombinations(ans,n,k,i+1,p);
                p.pop();
            }
        }
        public List<List<Integer>> combine(int n,int k){
            List<List<Integer>> ans=new ArrayList();
            if(n<=0||k<=0||n<k){
                return ans;
            }
            findCombinations(ans,n,k,1,new Stack<>());
            return ans;
        }
    ```

3. **方法三**：字典序(二进制排序)组合

>主要思路是以字典序的顺序获得全部组合。

1. 将nums初始化为从1到k的整数序列。将n+1添加为元素末尾，起到“哨兵”作用。将指针设为列表的开头j=0。
2. while(j<k)
   * 将nums中的前k个元素添加到输出中，换而言之，除了“哨兵”之外的全部元素。
   * 找到nums中的第一个满足nums[j]+1!=nums[j+1]的元素，并将其加一。nums[j]++以转到下一个组合。

    ```
        public List<List<Integer>> combine(int n, int k) {
            LinkedList<Integer> nums = new LinkedList<Integer>();
            for(int i=1;i<k+1;++i)
                nums.add(i);//将nums初始化为从1到k的整数序列
            nums.add(n+1);//将n+1添加为元素末尾，起到“哨兵”作用
            List<List<Integer>> output=new ArrayList();
            int j=0;
            while(j<k) {
                output.add(new LinkedList(nums.subList(0,k)));
                j=0;
                while((j<k)&&(nums.get(j+1)==nums.get(j)+1))
                    nums.set(j,j++ +1);
                nums.set(j,nums.get(j) +1);
            }
            return output;
        }
    ```

## 二、组合总和I

>**问题描述**：给定一个**无重复元素的数组candidates**和一个目标数target，找出candidates中所有可以使数字和为target的组合。candidates中的**数字可以无限制重复被选取**。

>**说明**：所有数字(包括target)都是正整数。解集不能包含重复的组合。

>**示例**：输入：candidates=[2,3,5], target=8；所求解集为：[[2,2,2,2],[2,3,3],[3,5]]

1. **方法一**：回溯法

**代码一**：简单回溯，思路比较好理解。参数比较复杂，实际上total这个参数可以去掉。

```
    public void getCombine(List<List<Integer>> ans,int total,
        LinkedList<Integer> path,int []nums,int target,int begin){
        if(total>target)
            return;
        if(total==target){
            ans.add(new LinkedList(path));
            return;
        }
        for(int i=begin;i<nums.length;i++){
            path.add(nums[i]);
            getCombine(ans,total+nums[i],path,nums,target,i);
            //同一元素可以重复使用，故参数应该是i而不是i+1
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum(int[] candidates, int target){
        List<List<Integer>> ans=new LinkedList();
        Arrays.sort(candidates);
        getCombine(ans,0,new LinkedList<Integer>(),candidates,target,0);
        return ans;
    }
```

**代码二**：代码一的优化版本，时间和空间消耗相较于代码一会更少。

>对于该题来讲，数组无需排序，因为该数组不含重复数字。

```
    public void getCombine(List<List<Integer>> ans,
        LinkedList<Integer> path,int []nums,int target,int begin){
        if(target<0)
            return;
        if(target==0){
            ans.add(new LinkedList(path));
            return;
        }
        for(int i=begin;i<nums.length;i++){
            path.add(nums[i]);
            getCombine(ans,path,nums,target-nums[i],i);
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum(int[] candidates, int target){
        List<List<Integer>> ans=new LinkedList();
        getCombine(ans,new LinkedList<Integer>(),candidates,target,0);
        return ans;
    }
```

1. **方法二**：回溯法(排序去重)

```
    public void getCombine(List<List<Integer>> ans,
        LinkedList<Integer> path,int []nums,int target,int begin){
        if(target==0){
            ans.add(new LinkedList(path));
            return;
        }
        for(int i=begin;i<nums.length&&target-nums[i]>=0;i++){
            path.add(nums[i]);
            getCombine(ans,path,nums,target-nums[i],i);
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum(int[] candidates, int target){
        List<List<Integer>> ans=new LinkedList();
        Arrays.sort(candidates);//必不可少
        getCombine(ans,new LinkedList<Integer>(),candidates,target,0);
        return ans;
    }
```

## 三、组合总和II

>**问题描述**：给定一个数组candidates和一个目标数target，找出candidates中所有可以使数字和为target的组合。candidates中的每个数字在每个组合中只能使用一次。

>**说明**：所有数字(包括target)都是正整数。解集不能包含重复的组合。

>**示例**：输入：candidates=[10,1,2,7,6,1], target=8；所求解集为：[[1,7],[1,2,5],[2,6],[1,1,6]]

1. **方法一**：寻找子集，判断子集的和是否为target。超出时间限制

```
    //判断函数，检测前面相同的数据是否已经被选择
    public boolean isSelect(int[] nums,LinkedList<Integer> path,int end){
        int sum=0;
        for(int i=0;i<end;i++){
            if(nums[i]==nums[end])
                sum++;
        }
        for(int i=0;i<path.size();i++){
            if(path.get(i)==nums[end])
                sum--;
        }
        if(sum==0)
            return true;
        return false;
    }
    public void getCombine(List<List<Integer>> ans,LinkedList<Integer> path,int []nums,int target,int cur){
        if(cur==nums.length)//这里的临界条件给的好像有点问题
            return;
        if(isSelect(nums,path,cur)==true){
            path.add(nums[cur]);
            int sum=0;
            for(int i=0;i<path.size();i++)
                sum=sum+path.get(i);//求存放答案链表的结果，增加了时间复杂度
            if(sum==target)
                ans.add(new LinkedList(path));//和为n且元素个数为k的组合添加进ans中
            getCombine(ans,path,nums,target,cur+1);
            path.removeLast();//在这里退出后path还剩1，出现错误，使[1,2,5]、[1,7]出现了两次
        }
        getCombine(ans,path,nums,target,cur+1);
    }
    public List<List<Integer>> combinationSum2(int[] candidates, int target){
        List<List<Integer>> ans=new LinkedList();
        LinkedList<Integer> path=new LinkedList();
        Arrays.sort(candidates);
        getCombine(ans,path,candidates,target,0);
        return ans;
    }
```

2. **方法二**：回溯法

```
    //判断函数，判断前面相同的数据是否已经被选择
    public boolean isSelect(int[] nums,LinkedList<Integer> path,int end){
        int sum=0;
        for(int i=0;i<end;i++){
            if(nums[i]==nums[end])
                sum++;
        }
        for(int i=0;i<path.size();i++){
            if(path.get(i)==nums[end])
                sum--;
        }
        if(sum==0)
            return true;
        return false;
    }
    public void getCombine(List<List<Integer>> ans,LinkedList<Integer> path,int []nums,int target,int begin){
        if(target==0){
            ans.add(new LinkedList(path));
            return;
        }
        for(int i=begin;i<nums.length&&target-nums[i]>=0;i++){
            if(isSelect(nums,path,i)==true){
                path.add(nums[i]);
                getCombine(ans,path,nums,target-nums[i],i+1);
                path.removeLast();
            }
            if(i>begin&&nums[i]==nums[i-1])
                continue;
            path.add(nums[i]);
            getCombine(ans,path,nums,target-nums[i],i+1);
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum2(int[] candidates,int target){
        List<List<Integer>> ans=new LinkedList();
        Arrays.sort(candidates);
        getCombine(ans,new LinkedList<Integer>(),candidates,target,0);
        return ans;
    }
```

## 四、组合总和III

>**问题描述**：找出所有相加之和为n的k个数的组合。组合中只允许含有1-9的正整数，并且每种组合中不存在重复的数字。

>**说明**：所有数字(包括target)都是正整数。解集不能包含重复的组合。

>**示例**：输入：k=3,n=9；所求解集为：[[1,2,6],[1,3,5],[2,3,4]]

1. **方法一**：递归回溯

* 递归终止条件：数组中包含k个数，如果和为n则加入结果集，否则直接返回终止递归
* 递归过程：循环遍历1-9，将新数字加入临时数组中进入下一层递归，出来后再将其移除
* 回溯的关键在于：添加和移除，保证所有可能性都遍历到，整体结构和栈类似

    **代码一**：写法较为简洁，时间复杂度低于代码二的
    ```
        public void traceBack(int k,int n,int sum,int begin,List<List<Integer>> ans,LinkedList<Integer> list){
            if(k==0){
                if(n==sum)
                    ans.add(new ArrayList(list));
                return;
            }
            for(int i=begin;i<10;i++){
                list.add(i);
                traceBack(k-1,n,sum+i,i+1,ans,list);
                list.removeLast();
            }
        }
        public List<List<Integer>> combinationSum3(int k, int n){
            List<List<Integer>> ans=new ArrayList<>();
            traceBack(k,n,0,1,ans,new LinkedList<>());
            return ans;
        }
    ```

    **代码二**：本方法较为麻烦，回溯处理的没有代码一表现好
    ```
        public void getCombine(List<List<Integer>> ans,LinkedList<Integer> path,int k,int n,int cur){
            if(cur==10||path.size()==k)
                return;
            path.add(cur);
            int sum=0;
            for(int i=0;i<path.size();i++)
                sum=sum+path.get(i);
            int len=path.size();
            if(len==k&&sum==n){
                ans.add(new LinkedList(path));
            }
            if(sum<n&&len<k){
                getCombine(ans,path,k,n,cur+1);
            }
            path.removeLast();
            getCombine(ans,path,k,n,cur+1);
        }
        public List<List<Integer>> combinationSum3(int k,int n){
            List<List<Integer>> ans=new LinkedList();
            getCombine(ans,new LinkedList<Integer>(),k,n,1);
            return ans;
        }
    ```
