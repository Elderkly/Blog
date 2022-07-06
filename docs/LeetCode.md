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
