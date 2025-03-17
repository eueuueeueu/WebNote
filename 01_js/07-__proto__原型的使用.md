# *__proto*__原型的使用

> js中对象的一个特殊的属性 *__proto*__(备胎)
>
> 原型的作用:用来做属性和方法共享的(节约内存开销)

```js
  let o = {
    name: 'o',
    a: 1
  }
  let o1 = {
    name: 'o1',
    b: 2,
  }
  let o2 = {
    name: 'o2',
    c: 3,
    log: function () {
      console.log('this.b:', this.b);
    }
  }
  o.__proto__ = o1
  o1.__proto__ = o2
  console.log(o.a);//1
  console.log(o.b);//2
  console.log(o.c);//3
  console.log(o);
  o.log()  //this-->o -->o1-->b--->2
```

## 实例

```js
  (function (window) {
    // '公共'对象是创建在自执行函数的作用域中的
    let 公共 = {
      speak: function () {
        console.log('说话');
      },
      sing: function () {
        console.log('唱歌');
      },
      eat: function () {
        console.log('吃饭');
      },
    }
    // createPerson函数是嵌套在自执行函数中
    // createPerson函数中去搜索变量的顺序是[[createPerson]]-->[[IIFE scope]]
    function createPerson(name, gender) {
      let person = {
        name: name,
        gender: gender
      }
      person.__proto__ = 公共
      return person
    }
    window.createPerson = createPerson
  })(window)

  let 董浩 = createPerson('董浩', '男')
  console.log(董浩.name);//董浩
  console.log(董浩.gender);//男
  董浩.speak()//说话
  董浩.sing()//唱歌
  董浩.eat()//吃饭
```

