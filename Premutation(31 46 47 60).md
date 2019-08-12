
# 排列

---
[toc]

---

## 一、全排列

>**常见排列算法：一般递归算法、去重递归算法、[字典排序](https://blog.csdn.net/cpfeed/article/details/7376132)、回溯法、分支限界法**

**1.1 给定一个没有重复数字的序列，返回其所有可能的全排列**

1. **方法一：递归交换**
   ```
   public void fullyArranged(List<List<Integer>> ans,int []nums,int cur){
        if(cur==0)
            Arrays.sort(nums);
        if(cur==nums.length){
            List<Integer> temp=new ArrayList<>();
            for(int i=0;i<nums.length;i++){
                temp.add(nums[i]);
            }
            ans.add(temp);
        }
        else{
            for(int i=cur;i<nums.length;i++){
                int v=nums[cur];
                nums[cur]=nums[i];
                nums[i]=v;
                fullyArranged(ans,nums,cur+1);
                //递归函数结束时数组会恢复到原来最开始的状态
                v=nums[cur];
                nums[cur]=nums[i];
                nums[i]=v;
            }
        }
    }
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> ans=new ArrayList();
        fullyArranged(ans,nums,0);
        return ans;
    }
   ```

2. **方法二：回溯法(普通递归)**
   >&emsp;&emsp;一种通过探索所有可能的候选解来找出所有的解的算法。如果候选解被确认不是 一个解的话(或者至少不是最后一个解)，回溯算法会通过在上一步进行一些变化抛弃该解，即**回溯**并且再次尝试。

    ```
    public void backtrack(int n,ArrayList<Integer> nums,List<List<Integer>> output,int first) {
        if (first == n)
        output.add(new ArrayList<Integer>(nums));
        for (int i = first; i < n; i++) {
        Collections.swap(nums, first, i);
        backtrack(n, nums, output, first + 1);
        //递归函数结束时数组会恢复到原来最开始的状态
        Collections.swap(nums, first, i);
        }
    }

    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> output = new LinkedList();//初始化output
        ArrayList<Integer> nums_lst = new ArrayList<Integer>();//将数组转换位list
        for (int num : nums)
            nums_lst.add(num);
        int n = nums.length;
        backtrack(n, nums_lst, output, 0);
        return output;
    }
    ```

3. **方法三：回溯法(深度优先遍历、状态重置)**
    >按照顺序选取数字，保证上一层选过的数字不会在下一层出现，就能够得到不重不漏的所有排列。
    
    ![](https://pic.leetcode-cn.com/561e67d343f2ca828644095a67f39694628b730dfffa24b52af9cbc6e011f134-46-1.png)
    
    **注意**
    1. 在每一层，我们都有若干条分支供我们选择。由于是排列问题，之前使用过的数字，在下一层中不能再选取，那么从当前层走到下一层的时候，我们就要问一问自己，哪些数字已经使用过。在编码实现中，可以使用一个布尔型数组used，用于记录之前(当前路径之前的层)哪些数字使用过。
    2. 在程序执行到上面这棵树的叶子节点的时候，此时递归到底，方法要返回，对于这个最后一层选取的数，要做两件事：
         * 释放对它的占用
         * 将它从当前选取放进的排列中弹出
    3. 在每一层的方法执行完毕，即将要返回的时候都需要这么做。这两点可以概括为**状态重置**。

    **代码一**
    ```
    //curSize表示当前的路径path里面有多少个元素
    private void generatePermution(int[] nums, boolean[] visited, 
        int curSize, int len, Stack<Integer> path, List<List<Integer>> res) {
        
        if (curSize == len) {
            // 此时 path 已经保存了 nums 中的所有数字，已经成为了一个排列
            res.add(new ArrayList<>(path));
            return;
        }
        for (int i = 0; i < len; i++) {
            if (!visited[i]) {
                path.push(nums[i]);
                visited[i] = true;
                generatePermution(nums, visited, curSize + 1, len, path, res);
                // 刚开始接触回溯算法的时候常常会忽略状态重置
                // 回溯的时候，一定要记得状态重置
                path.pop();
                visited[i] = false;
            }
        }
    }

    public List<List<Integer>> permute(int[] nums) {
        int len = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        boolean[] used = new boolean[len];
        if (len == 0) {
            return res;
        }
        generatePermution(nums, used, 0, len, new Stack<>(), res);
        return res;
    }

    public static void main(String[] args) {
        int[] nums = new int[]{1, 2, 3, 4};
        Solution solution = new Solution();
        List<List<Integer>> permute = solution.permute(nums);
        for (int i = 0; i < permute.size(); i++) {
            System.out.println(permute.get(i));
        }
    }
    ```

    **代码二：深度优先遍历**
    ```
    //DFS全排列，生成1~n的排列，按照递增排序输出
    public static void DFSArranged(int []a,int []flag,int cur){
        //全排列数组、标记数组、当前位置
        if(cur==0){
            Arrays.sort(a);
        }
        if(cur==a.length){
            for(int i=0;i<a.length;i++)
                System.out.print(a[i]+" ");
            System.out.println();
        }
        else{
            for(int i=0;i<a.length;i++){
                if(flag[i]==0){
                    a[cur]=i+1;//根据位置来赋值，不必担心存在覆盖的情况
                    flag[i]=1;
                    DFSArranged(a, flag, cur+1);
                    flag[i]=0;
                }
            }
        }
    }
    ```

**1.2 给定一个可包含重复数字的序列，返回所有不重复的全排列**

1. **方法一：递归交换(筛选)**
   
   ```
   //判断是否重复
   public boolean isSwap(int []nums,int begin,int end){
        for(int i=begin;i<end;i++){
            if(nums[i]==nums[end])
                return false;
        }
        return true;
    }
    public void fullyArranged(List<List<Integer>> ans,int []nums,int cur){
        if(cur==0)
            Arrays.sort(nums);
        if(cur==nums.length){
            List<Integer> temp=new ArrayList<>();
            for(int i=0;i<nums.length;i++){
                temp.add(nums[i]);
            }
            ans.add(temp);
        }
        else{
            for(int i=cur;i<nums.length;i++){
                if(isSwap(nums,cur,i)==true){
                    int v=nums[cur];
                    nums[cur]=nums[i];
                    nums[i]=v;
                    fullyArranged(ans,nums,cur+1);
                    //递归函数结束时数组会恢复到原来最开始的状态
                    v=nums[cur];
                    nums[cur]=nums[i];
                    nums[i]=v;
                }
            }
        }
    }    
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> ans=new ArrayList();
        fullyArranged(ans,nums,0);
        return ans;
    }
   ```

2. **方法二：生成可重集的排列**

   ```
   public static void Arranged(int []P,int []A,int cur){
        if (cur == A.length) {
            for (int i = 0; i < A.length; i++)
                System.out.print(A[i]+" ");
            System.out.println();
        }
        else {
            for (int i = 0; i < A.length; i++) {
                int flag = 1;
                int k;
                for (k = 0; k < cur; k++)
                    if (P[i]==A[k])
                        flag = 0;
                if (flag == 1) {
                    A[cur] = P[i];
                    Arranged(P,A, cur + 1);
                }
            }
        }
    }
   ```

3. **方法三：回溯+剪枝(分支限界法)**
   >当数组中有了重复元素的时候，我们可以先把数组排序，这样在递归的过程中就可以很容易发现重复的元素。当发现重复元素时，让这个分支跳过，就达到"剪枝"的效果，重复的排列不会出现在结果集中。

   ![](https://pic.leetcode-cn.com/f181bf01d112e80d01524c81fef01844157ab9890a5d3d9c9a1749eae03d2503-47-1.png)
   ![](https://pic.leetcode-cn.com/99cc47e2bfa71081595ea74f5650e64ced99d41927838927a9c45bb26c16252a-47-2.png)
   ![](https://pic.leetcode-cn.com/19181be13361e11f426288072b410b5201d96c900102fd0471e134330e163015-47-3.png)
   ![](https://pic.leetcode-cn.com/f79af52cd64546b318d84dd1727b0bbcdb4477b83aefc33931fa838991603a31-47-4.png)

   >**思路**：在开始回溯法之前，对数组进行一次排序操作。在进入一个新的分支之前，看一看这个数是不是和之前的数一样，如果这个数和之前的数一样，并且之前的数还未使用时，那接下来如果走这个分支时，就会使用到那个和当前一样的数，就会发生重复，此时分支和之前的分支一模一样。
   ```
    private List<List<Integer>> res = new ArrayList<>();
    private boolean[] used;

    private void findPermuteUnique(int[] nums, int depth, Stack<Integer> stack) {
        if (depth == nums.length) {
            res.add(new ArrayList<>(stack));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (!used[i]) {
                // 修改 2：因为排序以后重复的数一定不会出现在开始，故 i > 0
                // 和之前的数相等，并且之前的数还未使用过，只有出现这种情况，才会出现相同分支
                // 这种情况跳过即可
                if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
                    continue;
                }
                used[i] = true;
                stack.add(nums[i]);
                findPermuteUnique(nums, depth + 1, stack);
                stack.pop();
                used[i] = false;
            }
        }
    }

    public List<List<Integer>> permuteUnique(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return res;
        }
        // 修改 1：首先排序，之后才有可能发现重复分支
        Arrays.sort(nums);
        used = new boolean[len];
        findPermuteUnique(nums, 0, new Stack<>());
        return res;
    }

    public static void main(String[] args) {
        int[] nums = {1, 1, 2};
        Solution solution = new Solution();
        List<List<Integer>> permuteUnique = solution.permuteUnique(nums);
        System.out.println(permuteUnique);
    }
   ```

## 二、下一个排列

**问题描述**：实现下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。如果不存在下一个更大的排列，则将数字重新排列成最小的排列(即升序排列)。必须原地修改，只允许使用额外常数空间。

>**思路**：遍历找到最后一对升序对的第二个数据的下标，两个数据分别记为A和B。在B的后面寻找比A大的最小的数据，记为C，交换A和C。最后将交换完的B后面的所有数据按照非降序重新排列。

```
 public void nextPermutation(int[] nums) {
        //必须原地修改，只允许使用额外常数空间
        int len=nums.length;
        int k=-1;
        for(int i=1;i<len;i++){
            if(nums[i]>nums[i-1]){
                k=i;
            }
        }
        if(k==-1){//数组本身按照非增序排列，已经是最大的，则将数字重新排列成最小的排列
            Arrays.sort(nums);
            return;
        }
        int last=k;
        for(int i=k+1;i<len;i++){
            if(nums[i]>nums[k-1]&&nums[i]<nums[last]){
                last=i;
            }
        }
        int v=nums[k-1];
        nums[k-1]=nums[last];
        nums[last]=v;

        //冒泡排序
        int flag=1;
        while(flag==1){
            flag=0;
            for(int j=k+1;j<len;j++){
                if(nums[j]<nums[j-1]){
                    v=nums[j];
                    nums[j]=nums[j-1];
                    nums[j-1]=v;
                    flag=1;
                }
            }
        }
        return;
    }
```

## 三、第k个排列

**问题描述**：给出集合[1,2,3,4,...,n]，其所有元素共有n!种排列。按大小顺序列出所有排列情况，并一一标记。给定n和k，返回第k个排列。

>**思路**：这道题是让求出n个数字的第k个排列组合，由于其特殊性，我们不用将所有的排列组合的情况都求出来，然后返回其第k个，我们可以只求出第k个排列组合即可。

首先我们要知道当n=3时，其排列组合共有3!=6种，当n=4时，其排列组合共有4!=24种。我们就以n=4, k=17的情况来分析，所有排列组合情况如下：

1234

1243

1324

1342

1423

1432

2134

2143

2314

2341

2413

2431

3124

3142

3214

3241

3412 <--- k=17

3421

4123

4132

4213

4231

4312

4321

&emsp;&emsp;我们可以发现：每一位上1,2,3,4分别都出现了6次，当第一位上的数字确定了，后面三位上每个数字都出现了2次；当第二位也确定了，后面的数字都只出现了1次；当第三位确定了，那么第四位上的数字也只能出现一次；那么下面我们来看k=17这种情况的每位数字如何确定，**由于k=17是转化为数组下标为16**：

1.最高位可取1,2,3,4中的一个，每个数字出现3!=6次，所以k=16的第一位数字的下标为16/6=2，即3被取出

2.第二位此时从1,2,4中取一个，k=16时，k'=16%(3!)=4，而剩下的每个数字出现2!=2次，所以第二数字的下标为4/2=2，即4被取出

3.第三位此时从1,2中去一个，k'=4时，k''=4%(2!)=0，而剩下的每个数字出现1!=1次，所以第三个数字的下标为0/1=0，即1被取出

4.第四位是从2中取一个，k''=0时，k'''=0%(1!)=0，而剩下的每个数字出现0!=1次，所以第四个数字的下标为0/1= 0，即2被取出

```
    public String getPermutation(int n,int k){
        String ans="";
        List<Integer> tem=new LinkedList();
        for(int i=1;i<=n;i++){
            tem.add(i);
        }
        int f[]=new int[n];
        f[0]=1;
        for(int i=1;i<n;i++){//求阶乘数
            f[i]=f[i-1]*i;
        }
        k--;//下标先减一
        for(int i=n;i>=1;i--){
            int j=k/f[i-1];
            k%=f[i-1];
            ans=ans+Integer.toString(tem.get(j));
            tem.remove(j);
        }
        return ans;
    }
```
