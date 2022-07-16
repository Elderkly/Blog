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
var swapNumbers = function(numbers) {
    numbers[1] ^= numbers[0]
    numbers[0] ^= numbers[1]
    numbers[1] ^= numbers[0]
    return numbers
};
```

### 201. 数字范围按位与 给定区间找出区间内所有数字按位相与的结果
难度：中等   
思路：结果的规律为两个数的最大公共前缀后面补0。   
    例如： 5 = 0101  6 = 0110 7 = 0111   
    5 & 6 & 7 = 0100 = 4   
    所以思路就是先将两个数不断左移一位找出最大公共前缀后，将结果再右移进行补零就是最后的结果   
```javascript
var rangeBitwiseAnd = function(left, right) {
    let sum = 0
    while(left < right) {
        left >>= 1
        right >>= 1
        sum ++
    }
    return left << sum
};
```

