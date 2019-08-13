
# 子集

---
[toc]

---

>**回溯法**：回溯法的关键在于**不适合就退回上一步**，然后**通过约束条件减少时间复杂度**。

## 一、不含重复元素的子集

>**问题描述**：给定一组不含重复元素的整数数组nums，返回该数组所有可能的子集（幂集）。**说明**：解集不能包含重复的子集。

>**示例**：输入: nums = [1,2,3]；输出:[ [3], [1], [2], [1,2,3], [1,3],  [2,3], [1,2], [ ] ]

**方法一**：逐个选择输出

>注意：后退时不要再重复输出。为解决这一问题，在函数中添加一个havePrint参数，当是0时是后退情况；当是1时即输出。事实上也可以对此部分代码进行优化省去这个参数，具体代码见下面注释部分。

```
    public void print_subsets(List<List<Integer>> ans,int[] nums,Stack<Integer> path,int cur,int havePrint){
        if(cur==0)
            Arrays.sort(nums);
        if(havePrint==1)
            ans.add(new ArrayList(path));//先写入结果
        if(cur==nums.length)
            return;
        path.push(nums[cur]);//压栈
        print_subsets(ans,nums,path,cur+1,1);
        path.pop();//弹栈，回溯法一定要退回到原来的情况
        print_subsets(ans,nums,path,cur+1,0);

        // if(cur==0){
        //     ans.add(new ArrayList(path));
        //     Arrays.sort(nums);
        // }
        // if(cur==nums.length)
        //     return;
        // path.push(nums[cur]);//压栈
        // ans.add(new ArrayList(path));//没有标记符号的写法
        // print_subsets(ans,nums,path,cur+1,1);
        // path.pop();//弹栈
        // print_subsets(ans,nums,path,cur+1,0);
    }
    public List<List<Integer>> subsets(int[] nums){
        List<List<Integer>> ans=new LinkedList();
        Stack<Integer> path=new Stack();//用来存储子集
        print_subsets(ans,nums,path,0,1);
        return ans;
    }
```

**方法二**：位向量法，全部确定完之后再输出

>描述：设置一个数组，取值只能是0或者1，取1时即选择了这个元素，以此方法即可确定所有子集。

```
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> ans=new LinkedList();
        int []flag=new int[nums.length];//标记数组
        print_subsets(ans,flag,nums,0);
        return ans;
    }
    //位向量法
    public void print_subsets(List<List<Integer>> ans,int []flag,int[] nums,int cur){
        if(cur==0)//初始化排序
            Arrays.sort(nums);
        List<Integer> tem=new LinkedList();//添加子集
        if(cur==nums.length){
            for(int i=0;i<nums.length;i++){
                if(flag[i]==1)
                    tem.add(nums[i]);
            }
            ans.add(tem);
            return;
        }
        flag[cur]=1;
        print_subsets(ans,flag,nums,cur+1);
        flag[cur]=0;
        print_subsets(ans,flag,nums,cur+1);
    }
```

**方法三**：二进制和位运算

>集合的每个元素，都可以选或者不选，用二进制和位运算

```
    public static List<List<Integer>> binaryBit(int[] nums) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        for (int i = 0; i < (1 << nums.length); i++) {
            List<Integer> sub = new ArrayList<Integer>();
            for (int j = 0; j < nums.length; j++)
                if (((i >> j) & 1) == 1) sub.add(nums[j]);
            res.add(sub);
        }
        return res;
    }
```

## 二、含有重复元素的子集

>**问题描述**：给定一个可能包含重复元素的整数数组nums，返回该数组所有可能的子集（幂集）。**说明**：解集不能包含重复的子集。

>**示例**：输入: nums = [1,2,2]；输出:[ [ [1], [2], [1,2,2], [1,2],  [2,2],  [ ] ]

**方法一**：逐个选择输出

```
    public boolean isSelect(LinkedList<Integer>path,int[] nums,int end){
        int sum=0;
        for(int i=0;i<end;i++){
            if(nums[i]==nums[end])
                sum++;
        }
        if(sum==0)
            return true;
        for(int i=0;i<path.size();i++){
            if(path.get(i)==nums[end])
                sum--;
        }
        if(sum==0)
            return true;
        return false;
    }
    public void print_subsets(List<List<Integer>> ans,int[] nums,LinkedList<Integer> path,int cur){
        if(cur==0){
            ans.add(new ArrayList(path));//加入空集
            Arrays.sort(nums);
        }
        if(cur==nums.length)
            return;
        if(isSelect(path,nums,cur)==true){
            path.add(nums[cur]);//压栈
            ans.add(new ArrayList(path));
            print_subsets(ans,nums,path,cur+1);
            path.removeLast();//弹栈
        }
        //实际上，绝对不可以将弹栈操作放在if语句外面！原因如下：
        //1、弹栈操作可以会引起异常，如栈为空不可以再弹出任何元素
        //2、如果在上一步没有选择元素，接下来再弹出元素会出现结果错误
        print_subsets(ans,nums,path,cur+1);
    }
    public List<List<Integer>> subsetsWithDup(int[] nums){
        List<List<Integer>> ans=new LinkedList();
        LinkedList<Integer> path=new LinkedList();//用来存储子集
        print_subsets(ans,nums,path,0);
        return ans;
    }
```

**方法二**：位向量法，全部确定完之后再输出

>描述：设置一个数组，取值只能是0或者1，取1时即选择了这个元素，以此方法即可确定所有子集。

&emsp;&emsp;与求不含重复元素集合的子集不同的是：在程序当中要添加一个判断函数，用来判断之前相同的元素是否已经被选择过，如果已经被选择过则跳过该元素。

&emsp;&emsp;为了配合完成上述函数的功能，我们需要设置一个flag数组，用来标记相对应位置上的元素是否已经被选择。

```
    //判断之前的相同整数是否已经被选择过
    public boolean isSelect(int []flag,int []nums,int begin,int end){
        for(int i=begin;i<end;i++){
            if(nums[i]==nums[end]&&flag[i]==0)
                return false;
        }
        return true;
    }
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> ans=new LinkedList();
        int []flag=new int[nums.length];//标记数组
        print_subsets(ans,flag,nums,0);
        return ans;
    }
    //位向量法
    public void print_subsets(List<List<Integer>> ans,int []flag,int[] nums,int cur){
        if(cur==0)//初始化排序
            Arrays.sort(nums);
        List<Integer> tem=new LinkedList();//添加子集
        if(cur==nums.length){
            for(int i=0;i<cur;i++){
                if(flag[i]==1)
                    tem.add(nums[i]);
            }
            ans.add(tem);
            return;
        }
        if(isSelect(flag,nums,0,cur)==true){//被选择时要加一个判断
            flag[cur]=1;
            print_subsets(ans,flag,nums,cur+1);
            flag[cur]=0;
        }
        //flag[cur]=0;//可以放在这里，不影响程序的正确运行。不过最好是将该句放在上面的if语句当中
        print_subsets(ans,flag,nums,cur+1);
    }
```
