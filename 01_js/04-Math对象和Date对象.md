# Math对象和Date对象

## 一、Date对象

> 创建日期对象	  new Date()

```js
  function formatTime() {
    let date = new Date()
    // 获取当前时间
    console.log(date);//Thu Dec 05 2024 16:20:49 GMT+0800 (香港标准时间)
    let year = date.getFullYear()
    let month = date.getMonth() + 1
    let day = date.getDate()
    let hour = date.getHours()
    let minute = date.getMinutes()
    let second = date.getSeconds()
    let week = date.getDay()
    return year + '年' + month + '月' + day + '日' + hour + ':' + minute + ':' + second + '星期' + week
  }
  console.log(formatTime());//2024年12月5日16:38:12星期4
```

> 时间戳	  new Date().getTime()      Date.now()
>
> 返回 1970 年 1 月 1 日至今的毫秒数。

```js
  console.log(Date.now());//1733388373974
  let date2 = new Date()
  console.log(date2.getTime());//1733388373974
```

> 例题：从现在开始后的897650分钟，是几年几月几日

```js
  let currentTime = Date.now()//获取现在的时间戳
  // 将897650分钟转换成毫秒数
  let ms = 897650 * 60 * 1000
  let res = currentTime + ms
  console.log(new Date(res));//Fri Aug 21 2026 01:44:09 GMT+0800 (香港标准时间)
  // 转换一下格式
  let formatDate = new Date(res)
  let year = formatDate.getFullYear()
  let month = formatDate.getMonth() + 1
  let day = formatDate.getDate()
  console.log(year + '年' + month + '月' + day + '日');//2026年8月21日
```

## 二、Math对象

### 1、属性

#### Math.E

> 算术常量 e，即自然对数的底数，约等于 2.718。

#### Math.LN2

> `2` 的自然对数，约等于 `0.693`。

#### Math.LN10

> `10` 的自然对数，约等于 `2.303`。

#### Math.LOG2E

> 以 `2` 为底的 `E` 的对数，约等于 `1.443`。

#### Math.LOG10E

> 以 `10` 为底的 `E` 的对数，约等于 `0.434`。

#### Math.PI

> 圆周率，一个圆的周长和直径之比，约等于 `3.14159`。

#### Math.SQRT1_2

> 二分之一的平方根，同时也是 `2` 的平方根的倒数，约等于 `0.707`。

#### Math.SQRT2

> `2` 的平方根，约等于 `1.414`。

### 2、常用方法

#### Math.random(x)

> 返回一个 0 到 1 之间的伪随机数。

#### Math.abs(x)

> 返回一个数的绝对值。

#### Math.ceil(x)

> 返回大于一个数的最小整数，即一个数向上取整后的值。

#### Math.floor(x)

> 返回小于一个数的最大整数，即一个数向下取整后的值。

#### Math.round(x)

> 返回四舍五入后的整数。

#### Math.max(a,b,c,d......)

> 返回零到多个数值中最大值。

#### Math.min(a,b,c,d......)

> 返回零到多个数值中最小值。

#### Math.pow(x,y)

> 返回一个数的 y 次幂。

#### Math.sqrt(x)

> 返回一个数的平方根。

### 3、用random()得到两个数之间的随机整数

> 得到一个两数之间的随机整数，包括两个数在内

```js
function getRandomIntInclusive(min, max) {
  const minCeiled = Math.ceil(min);
  const maxFloored = Math.floor(max);
  return Math.floor(Math.random() * (maxFloored - minCeiled + 1) + minCeiled); // 包含最小值和最大值
}
```

更多方法：参考https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math