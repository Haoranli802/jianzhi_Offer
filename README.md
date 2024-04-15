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
