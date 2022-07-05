# LeetCode

### [1837] K 进制表示下的各位数字总和

难度：简单  
思路：在将 10 进制的数转换为 k 进制的过程中，我们只需要用 res 维护各位数字之和即可。

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

### [405] 数字转换为十六进制数

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
