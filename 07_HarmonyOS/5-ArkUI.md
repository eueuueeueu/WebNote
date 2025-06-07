# ArkUI

## 基本语法

在初步了解ArkTS语言后，本指南将以具体的示例来说明ArkTS的基本组成。

如下图所示，点击“按钮”时，文本内容从“Hello World”变为“Hello ArkUI”。

**图1** 示例效果图

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250603203405.55907244212307855544742165455268:50001231000000:2800:B2A7632DB720ABAED3E37A0B9EA61BCF4603719AB89228C0FB181ED2D32BB6AA.gif)

本示例中，ArkTS的基本组成如下所示。

**图2** ArkTS的基本组成

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250603203405.12921250877999893822136050149437:50001231000000:2800:23F8F73A3384307B8CA92C660707802D7920B1C968F6AAA94458ED6D45907499.png)

说明

自定义变量不能与基础通用属性/事件名重复。

- 装饰器： 用于装饰类、结构、方法以及变量，并赋予其特殊的含义。如上述示例中@Entry、@Component和@State都是装饰器，[@Component](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-create-custom-components#component)表示自定义组件，[@Entry](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-create-custom-components#entry)表示该自定义组件为入口组件，[@State](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-state)表示组件中的状态变量，状态变量变化会触发UI刷新。
- [UI描述](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-declarative-ui-description)：以声明式的方式来描述UI的结构，例如build()方法中的代码块。
- [自定义组件](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-create-custom-components)：可复用的UI单元，可组合其他组件，如上述被@Component装饰的struct Hello。
- 系统组件：ArkUI框架中默认内置的基础和容器组件，可以直接调用，例如示例中的Column、Text、Divider、Button。
- [属性方法](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-component-general-attributes)：组件可以通过链式调用配置多项属性，如fontSize()、width()、height()、backgroundColor()等。
- [事件方法](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-component-general-events)：组件可以通过链式调用设置多个事件的响应逻辑，如跟随在Button后面的onClick()。

除此之外，ArkTS扩展了多种语法范式来使开发更加便捷：

- [@Builder](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder)/[@BuilderParam](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builderparam)：特殊的封装UI描述的方法，细粒度的封装和复用UI描述。
- [@Extend](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-extend)/[@Styles](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-style)：扩展系统组件和封装属性样式，更灵活地组合系统组件。
- [stateStyles](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-statestyles)：多态样式，可以依据组件的内部状态的不同，设置不同样式。



## 声明式UI



ArkTS以声明方式组合和扩展组件来描述应用程序的UI，同时还提供了基本的属性、事件和子组件配置方法，帮助开发者实现应用交互逻辑。

### 创建组件

根据组件构造方法的不同，创建组件包含有参数和无参数两种方式。

说明

创建组件不需要使用new关键字。

#### 无参数

如果组件接口定义中不包含必选构造参数，则组件后面的“()”不需要配置任何内容。例如：Divider组件不包含构造参数。

```ts
Column() {
  Text('item 1')
  Divider()
  Text('item 2')
}
```



#### 有参数

如果组件接口定义包含构造参数，则在组件后的“()”中配置相应参数。

- Image组件的必选参数src。

  ```ts
  Image('https://xyz/test.jpg')
  ```

- Text组件的非必选参数content。

  ```ts
  // string类型的参数
  Text('test')
  // $r形式引入应用资源，可应用于多语言场景
  Text($r('app.string.title_value'))
  // 无参数形式
  Text()
  ```

  

- 变量或表达式可以用于参数赋值，表达式结果类型必须符合参数要求。

  例如，设置变量或表达式来构造Image和Text组件的参数。

  ```ts
  Image(this.imagePath)
  Image('https://' + this.imageUrl)
  Text(`count: ${this.count}`)
  ```

  

### 配置属性

属性方法以“.”链式调用配置组件样式和其他属性，建议每个属性方法单独一行。

- 配置Text组件的字体大小。

  ```ts
  Text('test')
    .fontSize(12)
  ```

- 配置组件的多个属性。

  ```ts
  Image('test.jpg')
    .alt('error.jpg')    
    .width(100)    
    .height(100)
  ```

- 除了直接传递常量参数，还可以传递变量或表达式。

  ```ts
  Text('hello')
    .fontSize(this.size)
  Image('test.jpg')
    .width(this.count % 2 === 0 ? 100 : 200)    
    .height(this.offset + 100)
  ```

- 对于系统组件，ArkUI还为其属性预定义了一些枚举类型供开发者调用，枚举类型可以作为参数传递，但必须满足参数类型要求。

  例如，可以按以下方式配置Text组件的颜色和字体样式。

  ```ts
  Text('hello')
    .fontSize(20)
    .fontColor(Color.Red)
    .fontWeight(FontWeight.Bold)
  ```

  

### 配置事件

事件方法以“.”链式调用的方式配置系统组件支持的事件，建议每个事件方法单独写一行。

- 使用箭头函数配置组件的事件方法。

   ```ts
   Button('Click me')
     .onClick(() => {
       this.myText = 'ArkUI';
     })
   ```

- 使用箭头函数表达式配置组件的事件方法，要求使用“() => {...}”，以确保函数与组件绑定，同时符合ArkTS语法规范。

```ts
Button('add counter')
  .onClick(() => {
    this.counter += 2;
  })
```

- 使用组件的成员函数配置组件的事件方法，需要bind this。ArkTS语法不建议使用成员函数配合bind this来配置组件的事件方法。

```ts
myClickHandler(): void {
  this.counter += 2;
}
...
Button('add counter')
  .onClick(this.myClickHandler.bind(this))
```

- 使用声明的箭头函数时可以直接调用，不需要bind this。

   ```ts
   fn = () => {
     console.info(`counter: ${this.counter}`)
     this.counter++
   }
   ...
   Button('add counter')
     .onClick(this.fn)
   ```



### 配置子组件

如果组件支持子组件配置，则需在尾随闭包"{...}"中为组件添加子组件的UI描述。Column、Row、Stack、Grid、List等组件都是容器组件。

- 以下是简单的Column组件配置子组件的示例。

```ts
Column() {
  Text('Hello')
    .fontSize(100)
  Divider()
  Text(this.myText)
    .fontSize(100)
    .fontColor(Color.Red)
}
```

- 容器组件均支持子组件配置，可以实现相对复杂的多级嵌套。

```ts
Column() {
  Row() {
    Image('test1.jpg')
      .width(100)
      .height(100)
    Button('click +1')
      .onClick(() => {
        console.info('+1 clicked!');
      })
  }
}
```



## 自定义组件

### 创建自定义组件

在ArkUI中，UI显示的内容均为组件，由框架直接提供的称为系统组件，由开发者定义的称为自定义组件。进行UI界面开发时，不仅要组合使用系统组件，还需考虑代码的可复用性、业务逻辑与UI的分离，以及后续版本的演进等因素。因此，将UI和部分业务逻辑封装成自定义组件是不可或缺的能力。

自定义组件具有以下特点：

- 可组合：允许开发者组合使用系统组件、及其属性和方法。
- 可重用：自定义组件可以被其他组件重用，并作为不同的实例在不同的父组件或容器中使用。
- 数据驱动UI更新：通过状态变量的改变，来驱动UI的刷新。

#### 自定义组件的基本用法

以下示例展示了自定义组件的基本用法。

```ts
@Component
struct HelloComponent {
  @State message: string = 'Hello, World!';


  build() {
    // HelloComponent自定义组件组合系统组件Row和Text
    Row() {
      Text(this.message)
        .onClick(() => {
          // 状态变量message的改变驱动UI刷新，UI从'Hello, World!'刷新为'Hello, ArkUI!'
          this.message = 'Hello, ArkUI!';
        })
    }
  }
}
```



> 如果在其他文件中引用自定义组件，需要使用export关键字导出组件，并在使用的页面import该自定义组件。



可以在其他自定义组件的build()函数中多次创建HelloComponent，以实现自定义组件的重用。

```ts
@Entry
@Component
struct ParentComponent {
  build() {
    Column() {
      Text('ArkUI message')
      HelloComponent({ message: 'Hello World!' });
      Divider()
      HelloComponent({ message: '你好，世界!' });
    }
  }
}
```



#### 自定义组件的基本结构

##### struct

自定义组件基于struct实现，struct + 自定义组件名 + {...}的组合构成自定义组件，不能有继承关系。对于struct的实例化，可以省略new。



> 自定义组件名、类名、函数名不得与系统组件名重复。

##### @Component

@Component装饰器仅能装饰struct关键字声明的数据结构。struct被@Component装饰后具备组件化的能力，需要实现build方法描述UI，一个struct只能被一个@Component装饰。@Component可以接受一个可选的boolean类型参数。

> 从API version 9开始，该装饰器支持在ArkTS卡片中使用。
>
> 从API version 11开始，@Component可以接受一个可选的boolean类型参数。



```ts
@Component
struct MyComponent {
}
```



##### build()函数

build()函数用于定义自定义组件的声明式UI描述，自定义组件必须定义build()函数。

```ts
@Component
struct MyComponent {
  build() {
  }
}
```



##### @Entry

@Entry装饰的自定义组件将作为UI页面的入口。在单个UI页面中，最多可以使用@Entry装饰一个自定义组件。@Entry可以接受一个可选的[LocalStorage](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localstorage)的参数。



> 从API version 9开始，该装饰器支持在ArkTS卡片中使用。
>
> 从API version 10开始，@Entry可以接受一个可选的[LocalStorage](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localstorage)的参数或者一个可选的[EntryOptions](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-create-custom-components#entryoptions10)参数。
>
> 从API version 11开始，该装饰器支持在元服务中使用。

```ts
@Entry
@Component
struct MyComponent {
}
```

##### EntryOptions

命名路由跳转选项。

| 名称                | 类型                                                         | 必填 | 说明                                                         |
| :------------------ | :----------------------------------------------------------- | :--- | :----------------------------------------------------------- |
| routeName           | string                                                       | 否   | 表示作为命名路由页面的名字。                                 |
| storage             | [LocalStorage](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localstorage) | 否   | 页面级的UI状态存储。                                         |
| useSharedStorage12+ | boolean                                                      | 否   | 是否使用LocalStorage.getShared()接口返回的共享的[LocalStorage](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localstorage)实例对象。默认值false。true：使用共享的[LocalStorage](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localstorage)实例对象。false：不使用共享的[LocalStorage](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localstorage)实例对象。 |

>  当useSharedStorage设置为true，并且storage也被赋值时，useSharedStorage的值优先级更高。



```ts
@Entry({ routeName : 'myPage' })
@Component
struct MyComponent {
}
```



##### @Reusable

@Reusable装饰的自定义组件具备可复用能力。详细请参考：[@Reusable装饰器：组件复用](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-reusable#使用场景)。

>  从API version 10开始，该装饰器支持在ArkTS卡片中使用。



```ts
@Reusable
@Component
struct MyComponent {
}
```





#### 成员函数/变量

自定义组件除了必须要实现build()函数外，还可以实现其他成员函数，成员函数具有以下约束：

- 自定义组件的成员函数为私有的，且不建议声明为静态函数。

自定义组件可以包含成员变量，成员变量具有以下约束：

- 自定义组件的成员变量为私有的，且不建议声明成静态变量。
- 自定义组件的成员变量本地初始化有些是可选的，有些是必选的。具体是否需要本地初始化，是否需要从父组件通过参数传递初始化子组件的成员变量，请参考[状态管理](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-state-management-overview)。

#### 自定义组件的参数规定

以上示例中，可以在build方法里创建自定义组件，同时在创建自定义组件的过程中，根据装饰器的规则来初始化自定义组件的参数。

```ts
@Component
struct MyComponent {
  private countDownFrom: number = 0;
  private color: Color = Color.Blue;


  build() {
  }
}


@Entry
@Component
struct ParentComponent {
  private someColor: Color = Color.Pink;


  build() {
    Column() {
      // 创建MyComponent实例，并将创建MyComponent成员变量countDownFrom初始化为10，将成员变量color初始化为this.someColor
      MyComponent({ countDownFrom: 10, color: this.someColor })
    }
  }
}
```



以下示例代码将父组件中的函数传递给子组件，并在子组件中调用。

```ts
@Entry
@Component
struct Parent {
  @State cnt: number = 0
  submit: () => void = () => {
    this.cnt++;
  }


  build() {
    Column() {
      Text(`${this.cnt}`)
      Son({ submitArrow: this.submit })
    }
  }
}


@Component
struct Son {
  submitArrow?: () => void


  build() {
    Row() {
      Button('add')
        .width(80)
        .onClick(() => {
          if (this.submitArrow) {
            this.submitArrow()
          }
        })
    }
    .height(56)
  }
}
```



#### build()函数

所有在build()函数中声明的语句统称为UI描述，UI描述需要遵循以下规则：

- @Entry装饰的自定义组件，其build()函数下的根节点唯一且必要，且必须为容器组件，其中ForEach禁止作为根节点。

  @Component装饰的自定义组件，其build()函数下的根节点唯一且必要，可以为非容器组件，其中ForEach禁止作为根节点。

  ```ts
  @Entry
  @Component
  struct MyComponent {
    build() {
      // 根节点唯一且必要，必须为容器组件
      Row() {
        ChildComponent() 
      }
    }
  }
  
  
  @Component
  struct ChildComponent {
    build() {
      // 根节点唯一且必要，可为非容器组件
      Image('test.jpg')
    }
  }
  ```

- 不允许声明本地变量，反例如下。

   ```ts
   build() {
     // 反例：不允许声明本地变量
     let num: number = 1;
   }
   ```



- 不允许在UI描述里直接使用console.info，但允许在方法或者函数里使用，反例如下。

  ```ts
  build() {
    // 反例：不允许console.info
    console.info('print debug log');
  }
  ```



- 不允许创建本地的作用域，反例如下。

```ts
build() {
  // 反例：不允许本地作用域
  {
    // ...
  }
}
```



- 不允许调用没有用@Builder装饰的方法，允许系统组件的参数是TS方法的返回值。

```ts
@Component
struct ParentComponent {
  doSomeCalculations() {
  }


  calcTextValue(): string {
    return 'Hello World';
  }


  @Builder doSomeRender() {
    Text(`Hello World`)
  }


  build() {
    Column() {
      // 反例：不能调用没有用@Builder装饰的方法
      this.doSomeCalculations();
      // 正例：可以调用
      this.doSomeRender();
      // 正例：参数可以为调用TS方法的返回值
      Text(this.calcTextValue())
    }
  }
}
```



- 不允许使用switch语法，当需要使用条件判断时，请使用[if](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-rendering-control-ifelse)。示例如下。

```ts
build() {
  Column() {
    // 反例：不允许使用switch语法
    switch (expression) {
      case 1:
        Text('...')
        break;
      case 2:
        Image('...')
        break;
      default:
        Text('...')
        break;
    }
    // 正例：使用if
    if(expression == 1) {
      Text('...')
    } else if(expression == 2) {
      Image('...')
    } else {
      Text('...')
    }
  }
}
```

- 不允许使用表达式，请使用if组件，示例如下。

```ts
build() {
  Column() {
    // 反例：不允许使用表达式
    (this.aVar > 10) ? Text('...') : Image('...')


    // 正例：使用if判断
    if(this.aVar > 10) {
      Text('...')
    } else {
      Image('...')
    }
  }
}
```

- 不允许直接改变状态变量，反例如下。详细分析见[@State常见问题：不允许在build里改状态变量](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-state#不允许在build里改状态变量)。

```ts
@Component
struct MyComponent {
  @State textColor: Color = Color.Yellow;
  @State columnColor: Color = Color.Green;
  @State count: number = 1;
  build() {
    Column() {
      // 应避免直接在Text组件内改变count的值
      Text(`${this.count++}`)
        .width(50)
        .height(50)
        .fontColor(this.textColor)
        .onClick(() => {
          this.columnColor = Color.Red;
        })
      Button("change textColor").onClick(() =>{
        this.textColor = Color.Pink;
      })
    }
    .backgroundColor(this.columnColor)
  }
}
```



在ArkUI状态管理中，状态驱动UI更新。

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250603203405.52451404740520657507283405144958:50001231000000:2800:1D8C49F3B1EB73948BBDC03A3BC8E459AFF4648E8EACB07A67ED7B35617702A8.png)

所以，不能在自定义组件的build()或@Builder方法里直接改变状态变量，这可能会造成循环渲染的风险。Text('${this.count++}')在全量更新或最小化更新会产生不同的影响：

- 全量更新（API8及以前版本）： ArkUI可能会陷入一个无限的重渲染的循环里，因为Text组件的每一次渲染都会改变应用的状态，就会再引起下一轮渲染的开启。 当 this.columnColor 更改时，都会执行整个build构建函数，因此，Text(${this.count++})绑定的文本也会更改，每次重新渲染Text(${this.count++})，又会使this.count状态变量更新，导致新一轮的build执行，从而陷入无限循环。
- 最小化更新（API9-至今版本）：当this.columnColor更改时，只有Column组件会更新，Text组件不会更改。只有当this.textColor更改时，会去更新整个Text组件，其所有属性函数都会执行，所以会看到Text(${this.count++})自增。因为目前UI以组件为单位进行更新，如果组件上某一个属性发生改变，会更新整体的组件。所以整体的更新链路是：this.textColor = Color.Pink ->Text组件整体更新->this.count++ ->Text组件整体更新。值得注意的是，这种写法在初次渲染时会导致Text组件渲染两次，从而影响性能。

build函数中更改应用状态的行为可能会比上面的示例更加隐蔽，比如：

- 在@Builder，@Extend或@Styles方法内改变状态变量 。
- 在计算参数时调用函数中改变应用状态变量，例如 Text('${this.calcLabel()}')。
- 对当前数组做出修改，sort()改变了数组this.arr，随后的filter方法会返回一个新的数组。



```ts
// 反例
@State arr : Array<...> = [ ... ];
ForEach(this.arr.sort().filter(...), 
  item => { 
  // ...
})
// 正确的执行方式为：filter返回一个新数组，后面的sort方法才不会改变原数组this.arr
ForEach(this.arr.filter(...).sort(), 
  item => { 
  // ...
})
```



#### 自定义组件通用样式

自定义组件通过“.”链式调用设置通用样式。

```ts
@Component
struct ChildComponent {
  build() {
    Button(`Hello World`)
  }
}


@Entry
@Component
struct MyComponent {
  build() {
    Row() {
      ChildComponent()
        .width(200)
        .height(300)
        .backgroundColor(Color.Red)
    }
  }
}
```





> ArkUI给自定义组件设置样式时，相当于给ChildComponent套了一个不可见的容器组件，这些样式是设置在容器组件上，而非直接设置给ChildComponent的Button组件。渲染结果显示，背景颜色红色并没有直接设置到Button上，而是设置在Button所在的不可见容器组件上。



### 页面和自定义组件生命周期



在开始之前，我们先明确自定义组件和页面的关系：

- 自定义组件：@Component装饰的UI单元，可以组合多个系统组件实现UI的复用，可以调用组件的生命周期。
- 页面：即应用的UI页面。可以由一个或者多个自定义组件组成，@Entry装饰的自定义组件为页面的入口组件，即页面的根节点，一个页面有且仅能有一个@Entry。只有被@Entry装饰的组件才可以调用页面的生命周期。

页面生命周期，即被@Entry装饰的组件生命周期，提供以下生命周期接口：

- [onPageShow](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-custom-component-lifecycle#onpageshow)：页面每次显示时触发一次，包括路由过程、应用进入前台等场景。
- [onPageHide](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-custom-component-lifecycle#onpagehide)：页面每次隐藏时触发一次，包括路由过程、应用进入后台等场景。
- [onBackPress](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-custom-component-lifecycle#onbackpress)：当用户点击返回按钮时触发。

组件生命周期，即一般用@Component装饰的自定义组件的生命周期，提供以下生命周期接口：

- [aboutToAppear](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-custom-component-lifecycle#abouttoappear)：组件即将出现时回调该接口，具体时机为在创建自定义组件的新实例后，在执行其build()函数之前执行。
- [onDidBuild](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-custom-component-lifecycle#ondidbuild12)：组件build()函数执行完成之后回调该接口，开发者可以在这个阶段进行埋点数据上报等不影响实际UI的功能。不建议在onDidBuild函数中更改状态变量、使用animateTo等功能，这可能会导致不稳定的UI表现。
- [aboutToDisappear](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/ts-custom-component-lifecycle#abouttodisappear)：aboutToDisappear函数在自定义组件析构销毁之前执行。不允许在aboutToDisappear函数中改变状态变量，特别是@Link变量的修改可能会导致应用程序行为不稳定。

生命周期流程如下图所示，下图展示的是被@Entry装饰的组件（页面）生命周期。

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250603203406.26678896234287665827749338764848:50001231000000:2800:5201E4F935852C5222417774157EADF15D0E780AF281E0C72E93F2085E55A0B3.png)

根据上面的流程图，我们从自定义组件的初始创建、重新渲染和删除来详细说明。

#### 自定义组件的创建和渲染流程

1. 自定义组件的创建：自定义组件的实例由ArkUI框架创建。
2. 初始化自定义组件的成员变量：通过本地默认值或者构造方法传递参数来初始化自定义组件的成员变量，初始化顺序为成员变量的定义顺序。
3. 如果开发者定义了aboutToAppear，则执行该方法。
4. 在首次渲染的时候，执行build方法渲染系统组件，如果子组件为自定义组件，则创建自定义组件的实例。在首次渲染的过程中，框架会记录状态变量和组件的映射关系，当状态变量改变时，驱动其相关的组件刷新。
5. 如果开发者定义了onDidBuild，则执行该方法。

#### 自定义组件重新渲染

当事件句柄被触发（比如设置了点击事件，即触发点击事件）改变了状态变量时，或者LocalStorage / AppStorage中的属性更改，并导致绑定的状态变量更改其值时：

1. 框架观察到变化，启动重新渲染。
2. 根据框架持有的两个map（[自定义组件的创建和渲染流程](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-page-custom-components-lifecycle#自定义组件的创建和渲染流程)中第4步），框架知道状态变量管理的UI组件及其更新函数。执行这些更新函数，实现最小化更新。

#### 自定义组件的删除

如果if组件的分支改变或ForEach循环渲染中数组的个数改变，组件将被移除：

1. 在删除组件之前，将调用其aboutToDisappear生命周期函数，标记着该节点将要被销毁。ArkUI的节点删除机制是：后端节点直接从组件树上摘下，后端节点被销毁，对前端节点解引用，前端节点已经没有引用时，将被JS虚拟机垃圾回收。
2. 自定义组件和它的变量将被删除，如果组件有同步的变量（如[@Link](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-link)、[@Prop](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-prop)、[@StorageLink](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-appstorage#storagelink)），将从[同步源](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-state-management-overview#基本概念)上取消注册。

不建议在生命周期aboutToDisappear中使用async await。如果在此生命周期中使用异步操作（如 Promise 或回调方法），自定义组件将被保留在Promise的闭包中，直到回调方法执行完毕，这会阻止自定义组件的垃圾回收。

以下示例展示了生命周期的调用时机：

> 当前router接口已不推荐使用，此处为更直观体现页面和自定义组件生命周期之间的时序关系，才使用router进行页面路由跳转。Navigation相关的时序关系参考[页面生命周期](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-navigation-navigation#页面生命周期)。



```ts
// Index.ets
@Entry
@Component
struct MyComponent {
  @State showChild: boolean = true;
  @State btnColor: string = "#FF007DFF";


  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onPageShow() {
    console.info('Index onPageShow');
  }


  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onPageHide() {
    console.info('Index onPageHide');
  }


  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onBackPress() {
    console.info('Index onBackPress');
    this.btnColor = "#FFEE0606";
    // 返回true表示页面自己处理返回逻辑，不进行页面路由；返回false表示使用默认的路由返回逻辑，不设置返回值按照false处理
    return true;
  }


  // 组件生命周期
  aboutToAppear() {
    console.info('MyComponent aboutToAppear');
  }


  // 组件生命周期
  onDidBuild() {
    console.info('MyComponent onDidBuild');
  }


  // 组件生命周期
  aboutToDisappear() {
    console.info('MyComponent aboutToDisappear');
  }


  build() {
    Column() {
      // this.showChild为true，创建Child子组件，执行Child aboutToAppear
      if (this.showChild) {
        Child()
      }
      Button('delete Child')
        .margin(20)
        .backgroundColor(this.btnColor)
        .onClick(() => {
          // 更改this.showChild为false，删除Child子组件，执行Child aboutToDisappear
          this.showChild = false;
        })
      // push到Page页面，执行onPageHide
      Button('push to next page')
        .onClick(() => {
          this.getUIContext().getRouter().pushUrl({ url: 'pages/Page' });
        })
    }
  }
}


@Component
struct Child {
  @State title: string = 'Hello World';


  // 组件生命周期
  aboutToDisappear() {
    console.info('Child aboutToDisappear');
  }


  // 组件生命周期
  onDidBuild() {
    console.info('Child onDidBuild');
  }


  // 组件生命周期
  aboutToAppear() {
    console.info('Child aboutToAppear');
  }


  build() {
    Text(this.title)
      .fontSize(50)
      .margin(20)
      .onClick(() => {
        this.title = 'Hello ArkUI';
      })
  }
}
```



```ts
// Page.ets
@Entry
@Component
struct Page {
  @State textColor: Color = Color.Black;
  @State num: number = 0;


  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onPageShow() {
    console.info('Page onPageShow');
    this.num = 5;
  }


  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onPageHide() {
    console.info('Page onPageHide');
  }


  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onBackPress() { // 不设置返回值按照false处理
    console.info('Page onBackPress');
    this.textColor = Color.Grey;
    this.num = 0;
  }


  // 组件生命周期
  aboutToAppear() {
    console.info('Page aboutToAppear');
    this.textColor = Color.Blue;
  }


  // 组件生命周期
  onDidBuild() {
    console.info('Page onDidBuild');
  }


  // 组件生命周期
  aboutToDisappear() {
    console.info('Page aboutToDisappear');
  }


  build() {
    Column() {
      Text(`num 的值为：${this.num}`)
        .fontSize(30)
        .fontWeight(FontWeight.Bold)
        .fontColor(this.textColor)
        .margin(20)
        .onClick(() => {
          this.num += 5;
        })
    }
    .width('100%')
  }
}
```



以上示例中，Index页面包含两个自定义组件，一个是被@Entry装饰的MyComponent，也是页面的入口组件，即页面的根节点；一个是Child，是MyComponent的子组件。只有@Entry装饰的节点才可以使页面级别的生命周期方法生效，因此在MyComponent中声明当前Index页面的页面生命周期函数（onPageShow / onPageHide / onBackPress）。MyComponent及其子组件Child分别声明了各自的组件级别生命周期函数（aboutToAppear / onDidBuild / aboutToDisappear）。

- 应用冷启动的初始化流程为：MyComponent aboutToAppear --> MyComponent build --> MyComponent onDidBuild --> Child aboutToAppear --> Child build --> Child onDidBuild --> Index onPageShow。此时日志输出信息如下：

```ts
MyComponent aboutToAppear
MyComponent onDidBuild
Child aboutToAppear
Child onDidBuild
Index onPageShow
```



- 点击“delete Child”，设置this.showChild为false，删除Child组件，执行Child aboutToDisappear方法。
- 点击“push to next page”，调用this.getUIContext().getRouter().pushUrl({ url: 'pages/Page' })接口，跳转到另外一个页面，当前Index页面隐藏，执行页面生命周期Index onPageHide。此处调用的是pushUrl接口，Index页面被隐藏，并没有销毁，所以只调用onPageHide。跳转到新页面后，执行初始化新页面的生命周期的流程。
- 如果调用的是this.getUIContext().getRouter().replaceUrl({ url: 'pages/Page' })，则当前Index页面被销毁，上文已经提到，组件的销毁是从组件树上直接摘下子树，所以执行的生命周期流程将变为：Page aboutToAppear --> Page build --> Page onDidBuild --> Index onPageHide --> Page onPageShow --> MyComponent aboutToDisappear --> Child aboutToDisappear。此时日志输出信息如下：

```ts
Page aboutToAppear
Page onDidBuild
Index onPageHide
Page onPageShow
MyComponent aboutToDisappear
Child aboutToDisappear
```

- 如果当前页面在Index页，点击返回按钮，触发页面生命周期Index onBackPress，若onBackPress使用默认false返回值，则触发返回后会导致当前Index页面被隐藏，执行页面生命周期Index onPageHide。此时日志输出信息如下：

```ts
Index onBackPress
Index onPageHide
```

- 如果当前页面在Page页，点击返回按钮，触发页面生命周期Page onBackPress，若onBackPress使用默认false返回值，则触发返回后会导致当前Page页面被销毁，执行页面生命周期Page onPageHide和Page aboutToDisappear。相关日志输出信息如下：

```ts
Page onBackPress
Page onPageHide
Index onPageShow
Page aboutToDisappear
```



- 最小化应用或者应用进入后台，触发Index onPageHide。当前Index页面未被销毁，所以并不会执行组件的aboutToDisappear。应用回到前台，执行Index onPageShow。
- 退出应用时，触发以下生命周期：Index onPageHide --> MyComponent aboutToDisappear --> Child aboutToDisappear。。

#### 自定义组件监听页面生命周期

使用[无感监听页面路由](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-arkui-observer#observeronrouterpageupdate11)的能力，能够实现在自定义组件中监听页面的生命周期。

```ts
// Index.ets
import { uiObserver, UIObserver } from '@kit.ArkUI';


@Entry
@Component
struct Index {
  listener: (info: uiObserver.RouterPageInfo) => void = (info: uiObserver.RouterPageInfo) => {
    let routerInfo: uiObserver.RouterPageInfo | undefined = this.queryRouterPageInfo();
    if (info.pageId == routerInfo?.pageId) {
      if (info.state == uiObserver.RouterPageState.ON_PAGE_SHOW) {
        console.log(`Index onPageShow`);
      } else if (info.state == uiObserver.RouterPageState.ON_PAGE_HIDE) {
        console.log(`Index onPageHide`);
      }
    }
  }


  aboutToAppear(): void {
    let uiObserver: UIObserver = this.getUIContext().getUIObserver();
    uiObserver.on('routerPageUpdate', this.listener);
  }


  aboutToDisappear(): void {
    let uiObserver: UIObserver = this.getUIContext().getUIObserver();
    uiObserver.off('routerPageUpdate', this.listener);
  }


  build() {
    Column() {
      Text(`this page is ${this.queryRouterPageInfo()?.pageId}`)
        .fontSize(25)
      Button("push self")
        .onClick(() => {
          this.getUIContext().getRouter().pushUrl({ url: 'pages/Index' });
        })
      Column() {
        SubComponent()
      }
    }
  }
}


@Component
struct SubComponent {
  listener: (info: uiObserver.RouterPageInfo) => void = (info: uiObserver.RouterPageInfo) => {
    let routerInfo: uiObserver.RouterPageInfo | undefined = this.queryRouterPageInfo();
    if (info.pageId == routerInfo?.pageId) {
      if (info.state == uiObserver.RouterPageState.ON_PAGE_SHOW) {
        console.log(`SubComponent onPageShow`);
      } else if (info.state == uiObserver.RouterPageState.ON_PAGE_HIDE) {
        console.log(`SubComponent onPageHide`);
      }
    }
  }


  aboutToAppear(): void {
    let uiObserver: UIObserver = this.getUIContext().getUIObserver();
    uiObserver.on('routerPageUpdate', this.listener);
  }


  aboutToDisappear(): void {
    let uiObserver: UIObserver = this.getUIContext().getUIObserver();
    uiObserver.off('routerPageUpdate', this.listener);
  }


  build() {
    Column() {
      Text(`SubComponent`)
    }
  }
}
```



## 组件扩展



ArkUI通过@Builder装饰器为开发者提供代码精简解决方案，该装饰器不仅能通过模块化封装简化UI开发流程，还衍生出@BuilderParam装饰器、@LocalBuilder装饰器和wrapBuilder，形成完整的可复用的UI结构体系。



>  @Builder装饰器是@BuilderParam装饰器、@LocalBuilder装饰器和wrapBuilder的基石。



**@Builder装饰器**

[@Builder装饰器](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder)装饰器专为构建模块化、可复用的UI结构而设计，其内部禁止定义状态变量和调用组件生命周期方法，仅支持通过参数与调用方进行数据交互。



**@BuilderParam装饰器**

当多个场景共用同一个@Builder函数时，若需要为特定场景扩展功能（类似slot占位符机制），可通过[@BuilderParam装饰器](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builderparam)装饰器实现，该装饰器专门用于接收并封装@Builder函数。



**@LocalBuilder装饰器**

在使用@Builder实现组件间数据传递时，开发者需注意组件层级关系。组件树中的父子关系可能与状态管理的父子关系产生不一致。为此，框架提供了[@LocalBuilder装饰器](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-localbuilder)装饰器来解决这一特定问题。



**wrapBuilder**

当页面中存在多个具有不同UI结构的全局@Builder函数时，开发者会面临较高的维护成本。为此，框架提供了[wrapBuilder](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-wrapbuilder)机制来简化这一场景下的代码维护工作。



### @Builder



ArkUI提供轻量的UI元素复用机制@Builder，其内部UI结构固定，仅与使用方进行数据传递。开发者可将重复使用的UI元素抽象成函数，在build函数中调用。

@Builder装饰的函数也称为“自定义构建函数”。



@Builder装饰器和@Component装饰器的区别：

1. @Builder装饰器用于封装可复用的UI结构，通过提取重复的布局代码提高开发效率。该装饰器严格禁止在其内部定义状态变量或使用生命周期函数，必须通过参数传递的方式与调用方完成数据交互。
2. 在ArkUI框架中，@Component装饰器作为封装复杂UI组件的核心机制，允许开发者通过组合多个基础组件来构建可复用的复合界面。该装饰器不仅支持内部状态变量的定义，还能完整管理组件的生命周期。

#### 私有自定义构建函数

定义语法：

```ts
@Entry
@Component
struct BuilderDemo {
  @Builder
  showTextBuilder() {
    // @Builder装饰此函数，使其能以链式调用的方式配置并构建Text组件
    Text('Hello World')
      .fontSize(30)
      .fontWeight(FontWeight.Bold)
  }
  @Builder
  showTextValueBuilder(param: string) {
    Text(param)
      .fontSize(30)
      .fontWeight(FontWeight.Bold)
  }
  build() {
    Column() {
      // 无参数
      this.showTextBuilder()
      // 有参数
      this.showTextValueBuilder('Hello @Builder')
    }
  }
}
```



使用方法：

- 允许在自定义组件内定义一个或多个@Builder函数，该函数被认为是该组件的私有、特殊类型的成员函数。
- 私有自定义构建函数允许在自定义组件内、build函数和其他自定义构建函数中调用。
- 在自定义组件中，this指代当前所属组件，组件的状态变量可在自定义构建函数内访问。建议通过this访问组件的状态变量，而不是通过参数传递。

#### 全局自定义构建函数

定义语法：

```ts
@Builder
function showTextBuilder() {
  Text('Hello World')
    .fontSize(30)
    .fontWeight(FontWeight.Bold)
}
@Entry
@Component
struct BuilderDemo {
  build() {
    Column() {
      showTextBuilder()
    }
  }
}
```

- 如果不涉及组件状态变化，建议使用全局的自定义构建函数。
- 全局自定义构建函数允许在build函数和其他自定义构建函数中调用。

#### 参数传递规则

自定义构建函数的参数传递有[按值传递](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#按值传递参数)和[按引用传递](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#按引用传递参数)两种，均需遵守以下规则：

- 参数的类型必须与参数声明的类型一致，不允许undefined、null和返回undefined、null的表达式。
- 在@Builder装饰的函数内部，不允许改变参数值。
- @Builder内UI语法遵循[UI语法规则](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-create-custom-components#build函数)。
- 只有当传入一个参数且该参数直接传入对象字面量时，才会按引用传递，其他传递方式均为按值传递。

##### 按值传递参数

调用@Builder装饰的函数默认按值传递。当传递的参数为状态变量时，状态变量的改变不会引起@Builder函数内的UI刷新。所以当使用状态变量的时候，推荐使用[按引用传递](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#按引用传递参数)。

```ts
@Builder function overBuilder(paramA1: string) {
  Row() {
    Text(`UseStateVarByValue: ${paramA1} `)
  }
}
@Entry
@Component
struct Parent {
  @State label: string = 'Hello';
  build() {
    Column() {
      overBuilder(this.label)
    }
  }
}
```

##### 按引用传递参数

按引用传递参数时，传递的参数可为状态变量，且状态变量的改变会引起@Builder函数内的UI刷新。

```ts
class Tmp {
  paramA1: string = '';
}


@Builder function overBuilder(params: Tmp) {
  Row() {
    Text(`UseStateVarByReference: ${params.paramA1} `)
  }
}
@Entry
@Component
struct Parent {
  @State label: string = 'Hello';
  build() {
    Column() {
      // 在父组件中调用overBuilder组件时，
      // 把this.label通过引用传递的方式传给overBuilder组件。
      overBuilder({ paramA1: this.label })
      Button('Click me').onClick(() => {
        // 单击Click me后，UI文本从Hello更改为ArkUI。
        this.label = 'ArkUI';
      })
    }
  }
}
```



> 1. @Builder装饰的函数内部不允许修改参数值，否则框架会抛出运行时错误。但开发者可以在使用@Builder的自定义组件中改变其参数。请参考[在@Builder装饰的函数内部修改入参内容](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#在builder装饰的函数内部修改入参内容)。
> 2. @Builder按引用传递且仅传入一个参数时，才会触发动态渲染UI。请参考[按引用传递参数](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#按引用传递参数)。
> 3. 如果@Builder传入的参数是两个或两个以上，不会触发动态渲染UI。请参考[@Builder存在两个或者两个以上参数](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#builder存在两个或者两个以上参数)。
> 4. @Builder传入的参数中同时包含按值传递和按引用传递，不会触发动态渲染UI。请参考[@Builder存在两个或者两个以上参数](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#builder存在两个或者两个以上参数)。
> 5. @Builder的参数必须按照对象字面量的形式，把所需属性一一传入，才会触发动态渲染UI。请参考[@Builder存在两个或者两个以上参数](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/arkts-builder#builder存在两个或者两个以上参数)。



