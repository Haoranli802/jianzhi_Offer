# jianzhi_Offer

LCR143树的子结构

```
首先判断当前树是不是跟目标树结构相等，判断函数为下，先假设left和right相等，用来对应目标树左或者右为null的情况，然后对比root val如果root val不相等，那么直接返回false。如果相等，如果目标树有左子树，那么继续递归比较左子树，右子树一样，然后返回左右都是true才是true。如果用当前树判断相等返回true，如果不相等，那么递归判断当时树的左右子树和目标树。最后都不相等返回false。
O(MN), O(N)
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if(A == null || B == null) return false;
        if(isSame(A, B)) return true;
        if(isSubStructure(A.left, B) || isSubStructure(A.right, B)) return true;
        return false;
    }
    private boolean isSame(TreeNode A, TreeNode B){
        boolean left = true;
        boolean right = true;
        if(A == null || A.val != B.val) return false;
        if(B.left != null) left = isSame(A.left, B.left);
        if(B.right != null) right = isSame(A.right, B.right);
        return left && right;
    }
}
```

***


JZ33二叉搜索树的后序遍历序列

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则返回 true ,否则返回 false 。假设输入的数组的任意两个数字都互不相同。

递归分治：
```
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        int n = sequence.length;
        if(n == 0) return false;
        return verify(sequence, 0, n - 1);
    }
    private boolean verify(int[] sequence, int left, int right){
        if(left >= right) return true;
        int root = sequence[right];
        int j = right - 1;
        while(j >= left && sequence[j] > root) j--;
        for(int i = left; i <= j; i++){
            if(sequence[i] > root) return false;
        }
        return verify(sequence, left, j) && verify(sequence, j + 1, right - 1);
    }
}
/**
具体思路就是用后序遍历的最后一个点为根，然后吧数组分为两半，根要比左半边大要比右半边小即可。然后递归遍历
左右半边。

O(N^2), O(N)
*/
```

单调栈：

```
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if(sequence.length == 0) return false;
        Stack<Integer> stack = new Stack<>();
        int root = Integer.MAX_VALUE;
        for(int i = sequence.length - 1; i >= 0; i--){
            if(sequence[i] > root) return false;
            while(!stack.isEmpty() && stack.peek() > sequence[i]){
                root = stack.pop();
            }
            stack.add(sequence[i]);
        }
        return true;
    }
}
/**
step 1：首先处理特殊情况，sequence为空的情况，返回false
step 2：维护一个单调栈s，不断迭代一个根节点root（初始化为INT_MAX）
step 3：通过循环反向遍历给定的列表，模拟逆序操作
step 4：循环内，如果当前遍历节点大于root，则返回false
step 5：如果当前节点小于栈顶节点且栈非空，则内循环更新root，并不断退栈，这一过程在找到当前节点的父节点
step 6：内循环结束后，将数字进入单调栈

O(N), O(N)
 */
```

***

JZ44数字序列中某一位的数字

```
import java.util.*;
public class Solution {
    public int findNthDigit (int n) {
        //记录n是几位数
        int digit = 1;
        //记录当前位数区间的起始数字：1,10,100...
        long start = 1; 
        //记录当前区间之前总共有多少位数字
        long sum = 9; 
        //将n定位在某个位数的区间中
        while(n > sum){
            n -= sum;
            start *= 10; 
            digit++; 
            //该区间的总共位数
            sum = 9 * start * digit;
        }
        //定位n在哪个数字上
        String num = "" + (start + (n - 1) / digit);
        //定位n在数字的哪一位上
        int index = (n - 1) % digit;
        return (int)(num.charAt(index)) - (int)('0');
    }
}
```

***

JZ71跳台阶扩展：一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶(n为正整数)总共有多少种跳法。

对于最后一级台阶，我们可以由倒数第二级台阶跳1步，也可以由倒数第三级太极跳两步所以f(n) = f(n - 1) + f(n - 2) + f(n - 3) + ... + f(1) + f(0)又因为f(n - 1) = f(n - 2) + f(n - 3) + ... + f(1) + f(0)

所有f(n) = 2 * f(n - 1)
```
public class Solution {
    public int jumpFloorII(int target) {
        int[] dp = new int[target + 1];
        //初始化前面两个
        dp[0] = 1; 
        dp[1] = 1;
        //依次乘2
        for(int i = 2; i <= target; i++) 
            dp[i] = 2 * dp[i - 1];
        return dp[target];
    }
}
//O(N), O(N)
```

***

JZ46把数字翻译成字符串

```
import java.util.*;
public class Solution {
    public int solve (String nums) {
        //排除0
        if(nums.equals("0"))  
            return 0;
        //排除只有一种可能的10 和 20
        if(nums == "10" || nums == "20")  
            return 1;
        //当0的前面不是1或2时，无法译码，0种
        for(int i = 1; i < nums.length(); i++){  
            if(nums.charAt(i) == '0')
                if(nums.charAt(i - 1) != '1' && nums.charAt(i - 1) != '2')
                    return 0;
        }
        int[] dp = new int[nums.length() + 1];
        //辅助数组初始化为1
        Arrays.fill(dp, 1);  
        for(int i = 2; i <= nums.length(); i++){
            //在11-19，21-26之间的情况
            if((nums.charAt(i - 2) == '1' && nums.charAt(i - 1) != '0') || (nums.charAt(i - 2) == '2' && nums.charAt(i - 1) > '0' && nums.charAt(i - 1) < '7'))
               dp[i] = dp[i - 1] + dp[i - 2];
            else
                dp[i] = dp[i - 1];
        }
        return dp[nums.length()];
    }
}
//O(N), O(N)
```

***

JZ51数组中的逆序对

```
public class Solution {
    public int mod = 1000000007;
    public int mergeSort(int left, int right, int [] data, int [] temp){
        //停止划分
        if(left >= right)    
            return 0;
        //取中间
        int mid = (left + right) / 2; 
        //左右划分合并
        int res = mergeSort(left, mid, data, temp) + mergeSort(mid + 1, right, data, temp); 
        //防止溢出
        res %= mod;  
        int i = left, j = mid + 1;
        for(int k = left; k <= right; k++)
            temp[k] = data[k];
        for(int k = left; k <= right; k++){
            if(i == mid + 1)
                data[k] = temp[j++];
            else if(j == right + 1 || temp[i] <= temp[j])
                data[k] = temp[i++];
            //左边比右边大，答案增加
            else{ 
                data[k] = temp[j++];
                // 统计逆序对
                res += mid - i + 1; 
            }
        }
        return res % mod;
    }
    public int InversePairs(int [] array) {
        int n = array.length;
        int[] res = new int[n];
        return mergeSort(0, n - 1, array, res);
    }
}
//O(NlogN), O(N)
```
