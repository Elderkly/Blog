# LeetCode

### [1837] K 进制表示下的各位数字总和

难度：简单  
思路：在将 10 进制的数转换为 k 进制的过程中，我们只需要用 a 维护各位数字之和即可。

```javascript
var sumBase = function (n, k) {
  let a = 0;
  while (n) {
    a += Math.trunc(n % k);
    n = n / k;
  }
  return a;
};
```

### [504] 七进制数

难度：简单

思路：使用数组存储个位数，遍历完成后逆转一次组合即可

```javascript
var convertToBase7 = function (num) {
  return num.toString(7);
};
```

```javascript
var convertToBase7 = function (num) {
  if (num === 0) return "0";
  const nv = num < 0;
  num = Math.abs(num);
  const arr = [];
  while (num > 0) {
    arr.push(num % 7);
    num = Math.floor(num / 7);
  }
  nv && arr.push("-");
  return arr.reverse().join("");
};
```

### [405] 数字转换为十六进制数 负数采用补码表示

难度：简单

```javascript
var toHex = function (num) {
  if (num === 0) return "0";
  const s = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, "a", "b", "c", "d", "e", "f"];
  const arr = [];
  if (num < 0) {
    num = Math.pow(2, 32) - Math.abs(num);
  }
  while (num > 0) {
    arr.push(s[num % 16]);
    num = Math.floor(num / 16);
  }
  return arr.reverse().join("");
};
```

### [451] 根据字符出现频率排序

难度：简单  
思路：先计算每个字符出现的频率，再根据频率生成字符

```javascript
var frequencySort = function (s) {
  const map = new Map();
  for (let i = 0; i < s.length; i++) {
    const x = s[i];
    const f = (map.get(x) || 0) + 1;
    map.set(x, f);
  }
  const list = [...map.keys()];
  list.sort((a, b) => map.get(b) - map.get(a));
  const sub = [];
  for (let j = 0; j < list.length; j++) {
    for (let z = 0; z < map.get(list[j]); z++) {
      sub.push(list[j]);
    }
  }
  return sub.join("");
};
```

### [41] 找出缺失的第一个正数

难度：困难  
思路：将所有正数都放入新数组中后，找出第一个缺失的 index 即是最小缺失正整数  
技巧：像这种找最小缺失的要善用数组动态赋值即`a[n] = 1`，则数组会产生 n-1 个空属性

```javascript
var firstMissingPositive = function (nums) {
  if (nums.length === 0 || (nums[0] < 0 && nums.length === 1)) return 1;
  const arr = [];
  nums.map((x) => {
    if (x > 0) arr[x] = 1;
  });
  if (!arr.length) return 1;

  for (let i = 1; i < arr.length; i++) {
    if (!arr[i]) return i;
  }

  return arr.length;
};
```

### [1863] 找出所有子集的异或总和再求和

难度：中等  
方法 1：先求出所有子集后再遍历求和

```javascript
var subsetXORSum = function (nums) {
  let a = [[]];
  for (let i = 0; i < nums.length; i++) {
    const temp = a.map((e) => {
      const o = [...e];
      o.push(nums[i]);
      return o;
    });
    a = a.concat(temp);
  }

  let sum = 0;
  for (let i = 0; i < a.length; i++) {
    if (a[i].length === 1) sum += a[i][0];
    else {
      let xsum = 0;
      a[i].map((e) => (xsum ^= e));
      sum += xsum;
    }
  }
  return sum;
};
```

方法 2： 回溯  
注意：递归算法先看一行所有的递归结果出来后再看另一行 注意递归一定是先往下不断嵌套递归后 再从下往上跳出循环

```javascript
let res;
const dfs = (val, idx, nums) => {
  if (idx === nums.length) {
    res += val;
    return;
  }
  dfs(val ^ nums[idx], idx + 1, nums);
  dfs(val, idx + 1, nums);
};

const subsetXORSum = (nums) => {
  res = 0;
  dfs(0, 0, nums);
  return res;
};
```

**https://leetcode.cn/problems/sum-of-all-subset-xor-totals/solution/sum-of-all-subset-xor-totals-by-leetcode-o5aa/**

### 按位异或赋值 (^=)

#### 获取数组所有子集

思路：定义一个二维数组，对目标数组进行遍历，每次都往二维数组的每一项里 push 一个 item，遍历完成合并二维数组，以此类推  
注意：push 会改变原数组 concat 不会改变原数组所以需要保存返回值

```javascript
const P = (nums) => {
  let Arr = [[]];
  for (let i = 0; i < nums.length; i++) {
    const temp = Arr.map((e) => {
      const o = [...e]; //  这里浅拷贝数组的原因是push操作会改变原数组
      o.push(nums[i]);
      return o;
    });
    Arr = Arr.concat(temp);
  }
  return Arr;
};
```

### 面试题 16.01 不额外使用空间交换数组元素

难度：中等  
技巧：看到不使用额外空间就要想到异或操作

```javascript
var swapNumbers = function (numbers) {
  numbers[1] ^= numbers[0];
  numbers[0] ^= numbers[1];
  numbers[1] ^= numbers[0];
  return numbers;
};
```

### 201. 数字范围按位与 给定区间找出区间内所有数字按位相与的结果

难度：中等  
思路：结果的规律为两个数的最大公共前缀后面补 0。  
 例如： 5 = 0101 6 = 0110 7 = 0111  
 5 & 6 & 7 = 0100 = 4  
 所以思路就是先将两个数不断右移一位找出最大公共前缀后，将结果再左移进行补零就是最后的结果

```javascript
var rangeBitwiseAnd = function (left, right) {
  let sum = 0;
  while (left < right) {
    left >>= 1;
    right >>= 1;
    sum++;
  }
  return left << sum;
};
```

### 537. 复数乘法

难度：中等  
思路：先分离出两个复数的实部和虚部后，采用公式`(a + bi)(c + di) = (ac − bd) + (ad + bc)i`计算

```javascript
const getNumber = (string) => {
  const s = string.split("+");
  return [s[0], s[1].replace("i", "")];
};
var complexNumberMultiply = function (num1, num2) {
  const [a, b] = getNumber(num1),
    [c, d] = getNumber(num2);
  return `${a * c - b * d}+${a * d + b * c}i`;
};
```

#### 复数

**https://www.shuxuele.com/numbers/complex-numbers.html**

### 1302. 层数最深叶子节点的和

难度：中等  
思路：dfs 算法求解 在判断最大层级的处理逻辑上 当结点层级比最大节点大时 直接调整 sum 即可

```javascript
let sum = 0;
let maxLevel = -1;
const deep = (level, root) => {
  if (!root.val) return;
  level++;
  if (maxLevel < level) {
    sum = root.val;
    maxLevel = level;
  } else if (maxLevel === level) {
    sum += root.val;
  }
  root.left && deep(level, root.left);
  root.right && deep(level, root.right);
};
var deepestLeavesSum = function (root) {
  deep(0, root);
  return sum;
};
```

### 1331. 数组序号转换

给你一个整数数组 arr ，请你将数组中的每个元素替换为它们排序后的序号。  
难度：简单  
思路：需要处理元素相同的情况，使用 Map 记录每个数字的位置后，再遍历一次输出即可。  
**技巧：看到重复元素要第一时间想到 Set 和 Map**

```javascript
var arrayRankTransform = function (arr) {
  const a = [...arr].sort((a, b) => a - b);
  const newArr = [];
  const MapArr = new Map();
  let idx = 0;
  a.forEach((e, index) => {
    !MapArr.has(e) && MapArr.set(e, ++idx);
  });
  arr.forEach((e, index) => newArr.push(MapArr.get(e)));
  return newArr;
};
```

### 1403. 非递增顺序的最小子序列

> 给你一个数组 nums，请你从中抽取一个子序列，满足该子序列的元素之和 严格 大于未包含在该子序列中的各元素之和。  
> 如果存在多个解决方案，只需返回 长度最小 的子序列。如果仍然有多个解决方案，则返回 元素之和最大 的子序列。  
> 与子数组不同的地方在于，「数组的子序列」不强调元素在原数组中的连续性，也就是说，它可以通过从数组中分离一些（也可能不分离）元素得到。  
> 注意，题目数据保证满足所有约束条件的解决方案是 唯一 的。同时，返回的答案应当按 非递增顺序 排列。

难度： 简单  
解题思路： 利用满足条件时`sum-total < total`进行解答

```javascript
/**
 * @param {number[]} nums
 * @return {number[]}
 */
const getSum = (nums) => (nums[0] ? nums.reduce((s, p) => (s += p)) : 0);
var minSubsequence = function (nums) {
  const n = [];
  nums = nums.sort((a, b) => b - a);
  const sum = getSum(nums);
  let total = 0;
  for (let i of nums) {
    n.push(i);
    total += i;
    if (sum - total < total) break;
  }
  return n;
};
```

### 899. 有序队列

> 给定一个字符串 s 和一个整数 k 。你可以从 s 的前 k 个字母中选择一个，并把它加到字符串的末尾。  
> 返回 在应用上述步骤的任意数量的移动后，字典上最小的字符串  。
> 难度：困难  
> 思路：当 k = 1 时，遍历字符串，每次截取第一个字符插入到末尾，找出最小的一个；当 k != 1 时，直接返回升序结果。  
> 技巧：js 中字符串可以直接用大于小于符号比较大小。

```javascript
var orderlyQueue = function (s, k) {
  if (k === 1) {
    let n = s;
    for (let i = 0; i < s.length - 1; i++) {
      s = s.substring(1) + s[0];
      n = n < s ? n : s;
    }
    return n;
  }
  return [...s].sort().join("");
};
```

**https://leetcode.cn/problems/orderly-queue/solution/you-xu-dui-lie-by-leetcode-solution-p6gv/**

### 623. 在二叉树中增加一行

> 给定一个二叉树的根  root  和两个整数 val 和  depth ，在给定的深度  depth  处添加一个值为 val 的节点行。  
> 注意，根节点  root  位于深度  1。  
> 难度： 中等  
> 思路： DFS 遍历， 利用 depth 来做条件判断

```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @param {number} val
 * @param {number} depth
 * @return {TreeNode}
 */
var addOneRow = function (root, val, depth) {
  if (!root) return null;
  if (depth === 1) {
    return new TreeNode(val, root);
  }
  if (depth === 2) {
    root.left = new TreeNode(val, root.left);
    root.right = new TreeNode(val, undefined, root.right);
  } else {
    root.left = addOneRow(root.left, val, depth - 1);
    root.right = addOneRow(root.right, val, depth - 1);
  }
  return root;
};
```

### ! 952. 按公因数计算最大组件大小

难度：困难
**https://leetcode.cn/problems/largest-component-size-by-common-factor/solution/an-gong-yin-shu-ji-suan-zui-da-zu-jian-d-amdx/**

### 636. 函数的独占时间

> 给一个程序运行甘特图，求出每个进程执行时间

难度： 中等  
思路： 使用栈记录每次操作，根据类型进行分别处理，当元素为 end 时，此时栈顶一定为该进程的 start 操作，此时运行时间将其相减+1 即可；当元素为 start 时，若栈不为空，表示有元素在执行，此时将栈顶元素暂停，记录元素已执行时间，修改栈顶元素的时间为当前的 time 以备后序操作。  
技巧： 注意此题与括号匹配类似。

```javascript
/**
 * @param {number} n
 * @param {string[]} logs
 * @return {number[]}
 */
var exclusiveTime = function (n, logs) {
  const stack = []; // {idx, 开始运行的时间}
  const res = new Array(n).fill(0); //  程序总运行时间
  for (let log of logs) {
    const splitArr = log.split(":");
    let [idx, type, time] = splitArr;
    idx = Number(idx);
    time = Number(time);
    if (type === "start") {
      if (stack.length) {
        //  栈顶有元素，中断原程序执行
        res[stack[stack.length - 1][0]] += time - stack[stack.length - 1][1]; //  记录栈顶元素运行时间
        stack[stack.length - 1][1] = time; //  修改栈顶元素开始时间为当前时间
      }
      stack.push([idx, time]); //  新元素入栈
    } else {
      const t = stack.pop();
      res[t[0]] += time - t[1] + 1; //  计算程序运行时间
      if (stack.length) {
        //  出栈后还有元素， 表示上一个程序被当前这个程序中断
        stack[stack.length - 1][1] = time + 1; //  更新上一个程序的开始时间
      }
    }
  }
  return res;
};
```
### 190. 颠倒二进制位 0100 => 0010
难度：简单
注意：js中形参接收二进制数后会自动转换为10进制数，此时需要遍历求原二进制数，无法使用toString转换    
```javascript
var reverseBits = function(n) {
    const arr = []
    while(n > 0) {
        arr.push(n % 2)
        n = parseInt(n/2) 
    }
    while(arr.length < 32) arr.push(0)
    return parseInt(arr.join(''), 2)
};

### 640. 求解方程

> 求解一个给定的方程，将 x 以字符串 "x=#value"  的形式返回。该方程仅包含 '+' ， '-' 操作，变量  x  和其对应系数。  
> 如果方程没有解，请返回  "No solution" 。如果方程有无限解，则返回 “Infinite solutions” 。  
> 题目保证，如果方程中只有一个解，则 'x' 的值是一个整数。  
> 难度：中等  
> 思路：遍历字符串，根据符号切换操作符，遇到等号则将计算的 x 的系数与数值进行翻转，达到移项的效果。最终结果为数值/系数。

```javascript
function solveEquation(s: string): string {
  let x = 0,
    num = 0,
    n = s.length;
  for (let i = 0, op = 1; i < n; ) {
    switch (s[i]) {
      case "+":
        op = 1;
        i++;
        break;
      case "-":
        op = -1;
        i++;
        break;
      case "=":
        x *= -1;
        num *= -1;
        op = 1;
        i++;
        break;
      default:
        let j = i;
        while (j < n && s[j] !== "+" && s[j] !== "-" && s[j] !== "=") j++;
        if (s[j - 1] === "x")
          x += (i < j - 1 ? Number(s.substring(i, j - 1)) : 1) * op;
        else num += Number(s.substring(i, j)) * op;
        i = j;
        break;
    }
  }
  if (x === 0) return num === 0 ? "Infinite solutions" : "No solution";
  return `x=${num / -x}`;
}
```

### 768. 最多能完成排序的块 II
> 这个问题和“最多能完成排序的块”相似，但给定数组中的元素可以重复，输入数组最大长度为2000，其中的元素最大为10**8。   
> arr是一个可能包含重复元素的整数数组，我们将这个数组分割成几个“块”，并将这些块分别进行排序。之后再连接起来，使得连接的结果和按升序排序后的原数组相同。   
> 我们最多能将数组分成多少块？   
难度： 困难    
**https://leetcode.cn/problems/max-chunks-to-make-sorted-ii/solution/zui-duo-neng-wan-cheng-pai-xu-de-kuai-ii-w5c6/**
```javascript
function maxChunksToSorted(arr: number[]): number {
    let res = 0
    const sortArr = [...arr].sort((a,b) => a - b)
    const map = new Map()
    for (let i = 0; i < arr.length; i++) {
        const x = arr[i], y = sortArr[i]
        map.set(x, (map.get(x) || 0 ) + 1)
        if (map.get(x) === 0) map.delete(x)
        map.set(y, (map.get(y) || 0) - 1)
        if (map.get(y) === 0) map.delete(y)
        if (map.size === 0) res++
    }
    return res
};
```

### * 5. 最长回文子串
> 给你一个字符串 s，找到 s 中最长的回文子串。
难度： 中等
思路：   
**https://leetcode.cn/problems/longest-palindromic-substring/solution/zui-chang-hui-wen-zi-chuan-by-leetcode-solution/**

```javascript
function longestPalindrome(s) {
    const length = s.length
    let  maxLen = 1, begin = 0
    if (length < 2) return s
    const charArr = Array.from(new Array(length), () => new Array(length).fill(false))
    for (let i = 0; i < length; i++) {
        charArr[i][i] = true
    }
    for(let Len = 2; Len <= length; Len ++) {
        for (let i = 0; i < length; i ++) {
            const j = Len + i - 1
            if (j >= length) break
            if (s[i] !== s[j]) charArr[i][j] = false
            else {
                if (j - i < 3) {
                    charArr[i][j] = true
                } else {
                    //  charArr[i][j]表示更大一圈的string charArr[i + 1][j - 1]表示往里缩小一位
                    charArr[i][j] = charArr[i + 1][j - 1]
                }
            }
            if (charArr[i][j] && j - i + 1 > maxLen) {
                maxLen = j - i + 1
                begin = i
            }
        }
    }
    return s.substring(begin, begin + maxLen)
};
```