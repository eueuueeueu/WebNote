## UIAbility

## 概述

[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)组件是一种包含UI的应用组件，主要用于和用户交互。例如，图库类应用可以在UIAbility组件中展示图片瀑布流。

UIAbility的设计理念：

1.  支持应用组件级的跨端迁移和多端协同。
2.  支持多设备和多窗口形态。

UIAbility划分原则与建议：

UIAbility组件是系统调度的基本单元，为应用提供绘制界面的窗口。一个应用可以包含一个或多个UIAbility组件。例如，在支付应用中，可以将入口功能和收付款功能分别配置为独立的UIAbility。

每一个UIAbility组件实例都会在最近任务列表中显示一个对应的任务。

对于开发者而言，可以根据具体场景选择单个还是多个UIAbility，划分建议如下：

- 如果开发者希望在任务视图中看到一个任务，建议使用“一个UIAbility+多个页面”的方式，可以避免不必要的资源加载。
- 如果开发者希望在任务视图中看到多个任务，或者需要同时开启多个窗口，建议使用多个UIAbility实现不同的功能。

  例如，即时通讯类应用中的消息列表与音视频通话采用不同的UIAbility进行开发，既可以方便地切换任务窗口，又可以实现应用的两个任务窗口在一个屏幕上分屏显示。

> 任务视图用于快速查看和管理当前设备上运行的所有任务或应用。



## UIAbility生命周期

当用户打开、切换和返回到对应应用时，应用中的[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例会在其生命周期的不同状态之间转换。UIAbility类提供了一系列回调，通过这些回调可以知道当前UIAbility实例的某个状态发生改变，会经过UIAbility实例的创建和销毁，或者UIAbility实例发生了前后台的状态切换。

UIAbility的生命周期包括Create、Foreground、Background、Destroy四个状态，如下图所示。

**图1** UIAbility生命周期状态

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250530171842.23092249354253592230156046759160:50001231000000:2800:CA7F70F315AE3D745AB9A6170C070E6C43EFFBB5A374C898855EBE02697030A3.png)

## 生命周期状态说明

### Create

Create状态为在应用加载过程中，[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例创建完成时触发，系统会调用[onCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityoncreate)回调。可以在该回调中进行页面初始化操作，例如变量定义资源加载等，用于后续的UI展示。

```ts
import { AbilityConstant, UIAbility, Want } from '@kit.AbilityKit';


export default class EntryAbility extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    // 页面初始化
  }
  // ...
}
```

> [Want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)是对象间信息传递的载体，可以用于应用组件间的信息传递。Want的详细介绍请参见[信息传递载体Want](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/want-overview)。



### WindowStageCreate和WindowStageDestroy

[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例创建完成之后，在进入Foreground之前，系统会创建一个WindowStage。WindowStage创建完成后会进入[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)回调，可以在该回调中设置UI加载、设置WindowStage的事件订阅。

**图2** WindowStageCreate和WindowStageDestroy状态

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250530171842.63910917730397383328779753006871:50001231000000:2800:F45DE4DA9DEFD00FB7A094139FEF8ABD2555864F666072F47B5189235359D93E.png)

在onWindowStageCreate()回调中通过[loadContent()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#loadcontent9)方法设置应用要加载的页面，并根据需要调用[on('windowStageEvent')](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#onwindowstageevent9)方法订阅[WindowStage的事件](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#windowstageeventtype9)（获焦/失焦、切到前台/切到后台、前台可交互/前台不可交互）。



> - 不同开发场景下[WindowStage事件](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#windowstageeventtype9)的时序可能存在差异。
> - 对于不同类型的产品，当应用主窗口从前台进入后台时，UIAbility生命周期的变化也会存在差异。详见[Stage模型下主窗口的生命周期](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/window-overview#stage模型下主窗口的生命周期)。



```ts
import { UIAbility } from '@kit.AbilityKit';
import { window } from '@kit.ArkUI';
import { hilog } from '@kit.PerformanceAnalysisKit';


const TAG: string = '[EntryAbility]';
const DOMAIN_NUMBER: number = 0xFF00;


export default class EntryAbility extends UIAbility {
  // ...
  onWindowStageCreate(windowStage: window.WindowStage): void {
    // 设置WindowStage的事件订阅（获焦/失焦、切到前台/切到后台、前台可交互/前台不可交互）
    try {
      windowStage.on('windowStageEvent', (data) => {
        let stageEventType: window.WindowStageEventType = data;
        switch (stageEventType) {
          case window.WindowStageEventType.SHOWN: // 切到前台
            hilog.info(DOMAIN_NUMBER, TAG, `windowStage foreground.`);
            break;
          case window.WindowStageEventType.ACTIVE: // 获焦状态
            hilog.info(DOMAIN_NUMBER, TAG, `windowStage active.`);
            break;
          case window.WindowStageEventType.INACTIVE: // 失焦状态
            hilog.info(DOMAIN_NUMBER, TAG, `windowStage inactive.`);
            break;
          case window.WindowStageEventType.HIDDEN: // 切到后台
            hilog.info(DOMAIN_NUMBER, TAG, `windowStage background.`);
            break;
          case window.WindowStageEventType.RESUMED: // 前台可交互状态
            hilog.info(DOMAIN_NUMBER, TAG, `windowStage resumed.`);
            break;
          case window.WindowStageEventType.PAUSED: // 前台不可交互状态
            hilog.info(DOMAIN_NUMBER, TAG, `windowStage paused.`);
            break;
          default:
            break;
        }
      });
    } catch (exception) {
      hilog.error(DOMAIN_NUMBER, TAG,
        `Failed to enable the listener for window stage event changes. Cause: ${JSON.stringify(exception)}`);
    }
    hilog.info(DOMAIN_NUMBER, TAG, `%{public}s`, `Ability onWindowStageCreate`);
    // 设置UI加载
    windowStage.loadContent('pages/Index', (err, data) => {
      // ...
    });
  }
}
```

> WindowStage的相关使用请参见[窗口开发指导](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/application-window-stage)。



对应于[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)回调。在[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例销毁之前，则会先进入[onWindowStageDestroy()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagedestroy)回调，可以在该回调中释放UI资源。

```ts
import { UIAbility } from '@kit.AbilityKit';
import { window } from '@kit.ArkUI';


export default class EntryAbility extends UIAbility {
  windowStage: window.WindowStage | undefined = undefined;


  // ...
  onWindowStageCreate(windowStage: window.WindowStage): void {
    this.windowStage = windowStage;
    // ...
  }


  onWindowStageDestroy() {
    // 释放UI资源
  }
}
```



### WindowStageWillDestroy

对应[onWindowStageWillDestroy()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagewilldestroy12)回调，在WindowStage销毁前执行，此时WindowStage可以使用。

```ts
import { UIAbility } from '@kit.AbilityKit';
import { window } from '@kit.ArkUI';
import { BusinessError } from '@kit.BasicServicesKit';
import { hilog } from '@kit.PerformanceAnalysisKit';


const TAG: string = '[EntryAbility]';
const DOMAIN_NUMBER: number = 0xFF00;


export default class EntryAbility extends UIAbility {
  windowStage: window.WindowStage | undefined = undefined;
  // ...
  onWindowStageCreate(windowStage: window.WindowStage): void {
    this.windowStage = windowStage;
    // ...
  }


  onWindowStageWillDestroy(windowStage: window.WindowStage) {
    // 释放通过windowStage对象获取的资源
    // 在onWindowStageWillDestroy()中注销WindowStage事件订阅（获焦/失焦、切到前台/切到后台、前台可交互/前台不可交互）
    try {
      if (this.windowStage) {
        this.windowStage.off('windowStageEvent');
      }
    } catch (err) {
      let code = (err as BusinessError).code;
      let message = (err as BusinessError).message;
      hilog.error(DOMAIN_NUMBER, TAG, `Failed to disable the listener for windowStageEvent. Code is ${code}, message is ${message}`);
    }
  }


  onWindowStageDestroy() {
    // 释放UI资源
  }
}
```



> WindowStage的相关使用请参见[窗口开发指导](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/application-window-stage)。



### Foreground和Background

Foreground和Background状态分别在[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例切换至前台和切换至后台时触发，对应于[onForeground()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonforeground)回调和[onBackground()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonbackground)回调。

onForeground()回调，在UIAbility的UI可见之前，如UIAbility切换至前台时触发。可以在onForeground()回调中申请系统需要的资源，或者重新申请在onBackground()中释放的资源。

onBackground()回调，在UIAbility的UI完全不可见之后，如UIAbility切换至后台时候触发。可以在onBackground()回调中释放UI不可见时无用的资源，或者在此回调中执行较为耗时的操作，例如状态保存等。

例如应用在使用过程中需要使用用户定位时，假设应用已获得用户的定位权限授权。在UI显示之前，可以在onForeground()回调中开启定位功能，从而获取到当前的位置信息。

当应用切换到后台状态，可以在onBackground()回调中停止定位功能，以节省系统的资源消耗。



```ts
import { UIAbility } from '@kit.AbilityKit';


export default class EntryAbility extends UIAbility {
  // ...


  onForeground(): void {
    // 申请系统需要的资源，或者重新申请在onBackground()中释放的资源
  }


  onBackground(): void {
    // 释放UI不可见时无用的资源，或者在此回调中执行较为耗时的操作
    // 例如状态保存等
  }
}
```



当应用的UIAbility实例已创建，且UIAbility配置为[singleton](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uiability-launch-type#singleton启动模式)启动模式时，再次调用[startAbility()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextstartability)方法启动该UIAbility实例时，只会进入该UIAbility的[onNewWant()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonnewwant)回调，不会进入其[onCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityoncreate)和[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)生命周期回调。应用可以在该回调中更新要加载的资源和数据等，用于后续的UI展示。



```ts
import { AbilityConstant, UIAbility, Want } from '@kit.AbilityKit';


export default class EntryAbility extends UIAbility {
  // ...


  onNewWant(want: Want, launchParam: AbilityConstant.LaunchParam) {
    // 更新资源、数据
  }
}
```



### Destroy

Destroy状态在[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例销毁时触发。可以在onDestroy()回调中进行系统资源的释放、数据的保存等操作。

例如，调用[terminateSelf()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextterminateself)方法停止当前UIAbility实例，执行onDestroy()回调，并完成UIAbility实例的销毁。

> 从API 13开始，如果用户使用最近任务列表一键清理来关闭该UIAbility实例，将不会执行onDestroy()回调，而是会直接终止进程。



```ts
import { UIAbility } from '@kit.AbilityKit';


export default class EntryAbility extends UIAbility {
  // ...


  onDestroy() {
    // 系统资源的释放、数据的保存等
  }
}
```



## UIAbility基本用法

### 指定UIAbility的启动页面

应用中的[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)在启动过程中，需要指定启动页面，否则应用启动后会因为没有默认加载页面而导致白屏。可以在UIAbility的[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)生命周期回调中，通过[WindowStage](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#windowstage9)对象的[loadContent()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#loadcontent9)方法设置启动页面。

```ts
import { UIAbility } from '@kit.AbilityKit';
import { window } from '@kit.ArkUI';


export default class EntryAbility extends UIAbility {
  onWindowStageCreate(windowStage: window.WindowStage): void {
    // Main window is created, set main page for this ability
    windowStage.loadContent('pages/Index', (err, data) => {
      // ...
    });
  }
  // ...
}
```



> 在DevEco Studio中创建的UIAbility中，该UIAbility实例默认会加载Index页面，根据需要将Index页面路径替换为需要的页面路径即可。



### 获取UIAbility的上下文信息

[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)类拥有自身的上下文信息，该信息为[UIAbilityContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext)类的实例，[UIAbilityContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext)类拥有abilityInfo、currentHapModuleInfo等属性。通过UIAbilityContext可以获取UIAbility的相关配置信息，如包代码路径、Bundle名称、Ability名称和应用程序需要的环境状态等属性信息，以及可以获取操作UIAbility实例的方法（如[startAbility()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextstartability)、[connectServiceExtensionAbility()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextconnectserviceextensionability)、[terminateSelf()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextterminateself)等）。

如果需要在页面中获得当前Ability的Context，可调用[getContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-getcontext#getcontext)接口获取当前页面关联的UIAbilityContext或[ExtensionContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-extensioncontext)。

- 在UIAbility中可以通过this.context获取UIAbility实例的上下文信息。

```ts
import { UIAbility, AbilityConstant, Want } from '@kit.AbilityKit';


export default class EntryAbility extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    // 获取UIAbility实例的上下文
    let context = this.context;
    // ...
  }
}
```

- 在页面中获取UIAbility实例的上下文信息

```tsx
import { common, Want } from '@kit.AbilityKit';


@Entry
@Component
struct Page_EventHub {
  private context = this.getUIContext().getHostContext() as common.UIAbilityContext;


  startAbilityTest(): void {
    let want: Want = {
      // Want参数信息
    };
    this.context.startAbility(want);
  }


  // 页面展示
  build() {
    // ...
  }
}
```



- 当业务完成后，开发者如果想要终止当前[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例，可以通过调用[terminateSelf()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextterminateself)方法实现。

```ts
import { common } from '@kit.AbilityKit';
import { BusinessError } from '@kit.BasicServicesKit';


@Entry
@Component
struct Page_UIAbilityComponentsBasicUsage {
  // 页面展示
  build() {
    Column() {
      //...
      Button('FuncAbilityB')
        .onClick(() => {
          let context = this.getUIContext().getHostContext() as common.UIAbilityContext;
          try {
            context.terminateSelf((err: BusinessError) => {
              if (err.code) {
                // 处理业务逻辑错误
                console.error(`terminateSelf failed, code is ${err.code}, message is ${err.message}.`);
                return;
              }
              // 执行正常业务
              console.info(`terminateSelf succeed.`);
            });
          } catch (err) {
            // 捕获同步的参数错误
            let code = (err as BusinessError).code;
            let message = (err as BusinessError).message;
            console.error(`terminateSelf failed, code is ${code}, message is ${message}.`);
          }
        })
    }
  }
}
```



### 获取UIAbility拉起方的信息

拉起方（UIAbilityA）通过startAbility启动目标方（UIAbilityB）时，UIAbilityB可以通过[parameters](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want#属性)参数获取UIAbilityA的Pid、BundleName和AbilityName等信息。

1. 通过点击UIAbilityA中的"拉起UIAbilityB"按钮，拉起UIAbilityB。

```ts
import { common, Want } from '@kit.AbilityKit';
import { BusinessError } from '@kit.BasicServicesKit';


@Entry
@Component
struct Index {
  @State message: string = 'Hello World';
  @State context: common.UIAbilityContext = this.getUIContext().getHostContext() as common.UIAbilityContext;


  build() {
    Scroll() {
      Column() {
        Text(this.message)
          .id('HelloWorld')
          .fontSize(50)
          .fontWeight(FontWeight.Bold)
          .alignRules({
            center: { anchor: '__container__', align: VerticalAlign.Center },
            middle: { anchor: '__container__', align: HorizontalAlign.Center }
          })
          .onClick(() => {
            this.message = 'Welcome';
          })
        Button('terminateSelf').onClick(() => {
          this.context.terminateSelf()
        })


        Button('拉起UIAbilityB').onClick((event: ClickEvent) => {
          let want: Want = {
            bundleName: this.context.abilityInfo.bundleName,
            abilityName: 'UIAbilityB',
          }


          this.context.startAbility(want, (err: BusinessError) => {
            if (err.code) {
              console.error(`Failed to startAbility. Code: ${err.code}, message: ${err.message}.`);
            }
          });
        })
      }
    }
  }
}
```

2. 在UIAbilityB的[onCreate](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityoncreate)生命周期中，获取并打印UIAbilityA的Pid、BundleName和AbilityName。

```ts
import { AbilityConstant, UIAbility, Want } from '@kit.AbilityKit';
import { window } from '@kit.ArkUI';


export default class UIAbilityB extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    // 调用方无需手动传递parameters参数，系统会自动向Want对象中传递调用方信息。
    console.log(`onCreate, callerPid: ${want.parameters?.['ohos.aafwk.param.callerPid']}.`);
    console.log(`onCreate, callerBundleName: ${want.parameters?.['ohos.aafwk.param.callerBundleName']}.`);
    console.log(`onCreate, callerAbilityName: ${want.parameters?.['ohos.aafwk.param.callerAbilityName']}.`);
  }


  onDestroy(): void {
    console.log(`UIAbilityB onDestroy.`);
  }


  onWindowStageCreate(windowStage: window.WindowStage): void {
    console.log(`Ability onWindowStageCreate.`);


    windowStage.loadContent('pages/Index', (err) => {
      if (err.code) {
        console.error(`Failed to load the content, error code: ${err.code}, error msg: ${err.message}.`);
        return;
      }
      console.log(`Succeeded in loading the content.`);
    });
  }
}
```

## 启动UIAbility

[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)是系统调度的最小单元。在设备内的功能模块之间跳转时，会涉及到启动特定的UIAbility，包括应用内的其他UIAbility、或者其他应用的UIAbility（例如启动三方支付UIAbility）。



### 启动应用内的UIAbility

当一个应用内包含多个[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)时，存在应用内启动UIAbility的场景。例如在支付应用中从入口UIAbility启动收付款UIAbility。

假设应用中有两个UIAbility：EntryAbility和FuncAbility（可以在同一个Module中，也可以在不同的Module中），需要从EntryAbility的页面中启动FuncAbility。

1. 在EntryAbility中，通过调用[startAbility()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextstartability)方法启动UIAbility，[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)为UIAbility实例启动的入口参数，其中bundleName为待启动应用的Bundle名称，abilityName为待启动的Ability名称，moduleName在待启动的UIAbility属于不同的Module时添加，parameters为自定义信息参数。示例中的context的获取方式请参见[获取UIAbility的上下文信息](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uiability-usage#获取uiability的上下文信息)。

```ts
import { common, Want } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { BusinessError } from '@kit.BasicServicesKit';


const TAG: string = '[Page_UIAbilityComponentsInteractive]';
const DOMAIN_NUMBER: number = 0xFF00;


@Entry
@Component
struct Page_UIAbilityComponentsInteractive {
  private context = this.getUIContext().getHostContext() as common.UIAbilityContext;


  build() {
    Column() {
      //...
      List({ initialIndex: 0 }) {
        ListItem() {
          Row() {
            //...
          }
          .onClick(() => {
            // context为Ability对象的成员，在非Ability对象内部调用需要
            // 将Context对象传递过去
            let wantInfo: Want = {
              deviceId: '', // deviceId为空表示本设备
              bundleName: 'com.samples.stagemodelabilitydevelop',
              moduleName: 'entry', // moduleName非必选
              abilityName: 'FuncAbilityA',
              parameters: {
                // 自定义信息
                info: '来自EntryAbility Page_UIAbilityComponentsInteractive页面'
              },
            };
            // context为调用方UIAbility的UIAbilityContext
            this.context.startAbility(wantInfo).then(() => {
              hilog.info(DOMAIN_NUMBER, TAG, 'startAbility success.');
            }).catch((error: BusinessError) => {
              hilog.error(DOMAIN_NUMBER, TAG, 'startAbility failed.');
            });
          })
        }
        //...
      }
      //...
    }
    //...
  }
}
```



2. 在FuncAbility的[onCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityoncreate)或者[onNewWant()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonnewwant)生命周期回调文件中接收EntryAbility传递过来的参数。

```ts
import { AbilityConstant, UIAbility, Want } from '@kit.AbilityKit';


export default class FuncAbilityA extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    // 接收调用方UIAbility传过来的参数
    let funcAbilityWant = want;
    let info = funcAbilityWant?.parameters?.info;
  }
  //...
}
```

> 在被拉起的FuncAbility中，可以通过获取传递过来的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数的parameters来获取拉起方[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)的PID、Bundle Name等信息。



3. 在FuncAbility业务完成之后，如需要停止当前[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例，在FuncAbility中通过调用[terminateSelf()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextterminateself)方法实现。

```ts
import { common } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';


const TAG: string = '[Page_FromStageModel]';
const DOMAIN_NUMBER: number = 0xFF00;


@Entry
@Component
struct Page_FromStageModel {
  build() {
    Column() {
      //...
      Button('FuncAbilityB')
        .onClick(() => {
          let context = this.getUIContext().getHostContext() as common.UIAbilityContext; // UIAbilityContext
          // context为需要停止的UIAbility实例的AbilityContext
          context.terminateSelf((err) => {
            if (err.code) {
              hilog.error(DOMAIN_NUMBER, TAG, `Failed to terminate self. Code is ${err.code}, message is ${err.message}`);
              return;
            }
          });
        })
    }
    //...
  }
}
```



> 调用terminateSelf()方法停止当前UIAbility实例时，默认会保留该实例的快照（Snapshot），即在最近任务列表中仍然能查看到该实例对应的任务。如不需要保留该实例的快照，可以在其对应UIAbility的[module.json5配置文件](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/module-configuration-file)中，将[abilities标签](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/module-configuration-file#abilities标签)的removeMissionAfterTerminate字段配置为true。



4. 如需要关闭应用所有的[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例，可以调用[ApplicationContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-applicationcontext)的[killAllProcesses()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-applicationcontext#applicationcontextkillallprocesses)方法实现关闭应用所有的进程。



### 启动应用内的UIAbility并获取返回结果

在一个EntryAbility启动另外一个FuncAbility时，希望在被启动的FuncAbility完成相关业务后，能将结果返回给调用方。例如在应用中将入口功能和账号登录功能分别设计为两个独立的[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)，在账号登录UIAbility中完成登录操作后，需要将登录的结果返回给入口UIAbility。

1. 在EntryAbility中，调用[startAbilityForResult()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextstartabilityforresult-2)接口启动FuncAbility，异步回调中的data用于接收FuncAbility停止自身后返回给EntryAbility的信息。示例中的context的获取方式请参见[获取UIAbility的上下文信息](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uiability-usage#获取uiability的上下文信息)。

```ts
import { common, Want } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { BusinessError } from '@kit.BasicServicesKit';


const TAG: string = '[Page_UIAbilityComponentsInteractive]';
const DOMAIN_NUMBER: number = 0xFF00;


@Entry
@Component
struct Page_UIAbilityComponentsInteractive {
  build() {
    Column() {
      //...
      List({ initialIndex: 0 }) {
        ListItem() {
          Row() {
            //...
          }
          .onClick(() => {
            let context = this.getUIContext().getHostContext() as common.UIAbilityContext; // UIAbilityContext
            const RESULT_CODE: number = 1001;
            let want: Want = {
              deviceId: '', // deviceId为空表示本设备
              bundleName: 'com.samples.stagemodelabilitydevelop',
              moduleName: 'entry', // moduleName非必选
              abilityName: 'FuncAbilityA',
              parameters: {
                // 自定义信息
                info: '来自EntryAbility UIAbilityComponentsInteractive页面'
              }
            };
            context.startAbilityForResult(want).then((data) => {
              if (data?.resultCode === RESULT_CODE) {
                // 解析被调用方UIAbility返回的信息
                let info = data.want?.parameters?.info;
                hilog.info(DOMAIN_NUMBER, TAG, JSON.stringify(info) ?? '');
                if (info !== null) {
                  this.getUIContext().getPromptAction().showToast({
                    message: JSON.stringify(info)
                  });
                }
              }
              hilog.info(DOMAIN_NUMBER, TAG, JSON.stringify(data.resultCode) ?? '');
            }).catch((err: BusinessError) => {
              hilog.error(DOMAIN_NUMBER, TAG, `Failed to start ability for result. Code is ${err.code}, message is ${err.message}`);
            });
          })
        }
        //...
      }
      //...
    }
    //...
  }
}
```



2. 在FuncAbility停止自身时，需要调用[terminateSelfWithResult()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextterminateselfwithresult)方法，入参[abilityResult](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-ability-abilityresult)为FuncAbility需要返回给EntryAbility的信息。

```ts
import { common } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';


const TAG: string = '[Page_FuncAbilityA]';
const DOMAIN_NUMBER: number = 0xFF00;


@Entry
@Component
struct Page_FuncAbilityA {
  build() {
    Column() {
      //...
      List({ initialIndex: 0 }) {
        ListItem() {
          Row() {
            //...
          }
          .onClick(() => {
            let context = this.getUIContext().getHostContext() as common.UIAbilityContext; // UIAbilityContext
            const RESULT_CODE: number = 1001;
            let abilityResult: common.AbilityResult = {
              resultCode: RESULT_CODE,
              want: {
                bundleName: 'com.samples.stagemodelabilitydevelop',
                moduleName: 'entry', // moduleName非必选
                abilityName: 'FuncAbilityB',
                parameters: {
                  info: '来自FuncAbility Index页面'
                },
              },
            };
            context.terminateSelfWithResult(abilityResult, (err) => {
              if (err.code) {
                hilog.error(DOMAIN_NUMBER, TAG, `Failed to terminate self with result. Code is ${err.code}, message is ${err.message}`);
                return;
              }
            });
          })
        }
        //...
      }
      //...
    }
    //...
  }
}
```

3. FuncAbility停止自身后，EntryAbility通过[startAbilityForResult()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextstartabilityforresult-2)方法回调接收被FuncAbility返回的信息，RESULT_CODE需要与前面的数值保持一致。

```ts
import { common, Want } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { BusinessError } from '@kit.BasicServicesKit';


const TAG: string = '[Page_UIAbilityComponentsInteractive]';
const DOMAIN_NUMBER: number = 0xFF00;


@Entry
@Component
struct Page_UIAbilityComponentsInteractive {
  build() {
    Column() {
      //...
      List({ initialIndex: 0 }) {
        ListItem() {
          Row() {
            //...
          }
          .onClick(() => {
            let context = this.getUIContext().getHostContext() as common.UIAbilityContext; // UIAbilityContext
            const RESULT_CODE: number = 1001;


            let want: Want = {
              deviceId: '', // deviceId为空表示本设备
              bundleName: 'com.samples.stagemodelabilitydevelop',
              moduleName: 'entry', // moduleName非必选
              abilityName: 'FuncAbilityA',
              parameters: {
                // 自定义信息
                info: '来自EntryAbility UIAbilityComponentsInteractive页面'
              }
            };
            context.startAbilityForResult(want).then((data) => {
              if (data?.resultCode === RESULT_CODE) {
                // 解析被调用方UIAbility返回的信息
                let info = data.want?.parameters?.info;
                hilog.info(DOMAIN_NUMBER, TAG, JSON.stringify(info) ?? '');
                if (info !== null) {
                  this.getUIContext().getPromptAction().showToast({
                    message: JSON.stringify(info)
                  });
                }
              }
              hilog.info(DOMAIN_NUMBER, TAG, JSON.stringify(data.resultCode) ?? '');
            }).catch((err: BusinessError) => {
              hilog.error(DOMAIN_NUMBER, TAG, `Failed to start ability for result. Code is ${err.code}, message is ${err.message}`);
            });
          })
        }
        //...
      }
      //...
    }
    //...
  }
}
```



### 启动UIAbility的指定页面



一个[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)可以对应多个页面，在不同的场景下启动该UIAbility时需要展示不同的页面，例如从一个UIAbility的页面中跳转到另外一个UIAbility时，希望启动目标UIAbility的指定页面。

UIAbility的启动分为两种情况：UIAbility冷启动和UIAbility热启动。

- UIAbility冷启动：指的是UIAbility实例处于完全关闭状态下被启动，这需要完整地加载和初始化UIAbility实例的代码、资源等。
- UIAbility热启动：指的是UIAbility实例已经启动并在前台运行过，由于某些原因切换到后台，再次启动该UIAbility实例，这种情况下可以快速恢复UIAbility实例的状态。

#### 调用方UIAbility指定启动页面

调用方[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)启动另外一个UIAbility时，通常需要跳转到指定的页面。例如FuncAbility包含两个页面（Index对应首页，Second对应功能A页面），此时需要在传入的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数中配置指定的页面路径信息，可以通过want中的parameters参数增加一个自定义参数传递页面跳转信息。示例中的context的获取方式请参见[获取UIAbility的上下文信息](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uiability-usage#获取uiability的上下文信息)。



```ts
import { common, Want } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { BusinessError } from '@kit.BasicServicesKit';


const TAG: string = '[Page_UIAbilityComponentsInteractive]';
const DOMAIN_NUMBER: number = 0xFF00;


@Entry
@Component
struct Page_UIAbilityComponentsInteractive {
  build() {
    Column() {
      //...
      List({ initialIndex: 0 }) {
        ListItem() {
          Row() {
            //...
          }
          .onClick(() => {
            let context = this.getUIContext().getHostContext() as common.UIAbilityContext; // UIAbilityContext
            let want: Want = {
              deviceId: '', // deviceId为空表示本设备
              bundleName: 'com.samples.stagemodelabilityinteraction',
              moduleName: 'entry', // moduleName非必选
              abilityName: 'FuncAbility',
              parameters: { // 自定义参数传递页面信息
                router: 'funcA'
              }
            };
            // context为调用方UIAbility的UIAbilityContext
            context.startAbility(want).then(() => {
              hilog.info(DOMAIN_NUMBER, TAG, 'Succeeded in starting ability.');
            }).catch((err: BusinessError) => {
              hilog.error(DOMAIN_NUMBER, TAG, `Failed to start ability. Code is ${err.code}, message is ${err.message}`);
            });
          })
        }
        //...
      }
      //...
    }
    //...
  }
}
```



#### 目标UIAbility冷启动

目标[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)冷启动时，在目标UIAbility的[onCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityoncreate)生命周期回调中，接收调用方传过来的参数。然后在目标UIAbility的[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)生命周期回调中，解析调用方传递过来的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数，获取到需要加载的页面信息url，传入[windowStage.loadContent()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#loadcontent9)方法。

```ts
import { AbilityConstant, Want, UIAbility } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { window, UIContext } from '@kit.ArkUI';


const DOMAIN_NUMBER: number = 0xFF00;
const TAG: string = '[EntryAbility]';


export default class EntryAbility extends UIAbility {
  funcAbilityWant: Want | undefined = undefined;
  uiContext: UIContext | undefined = undefined;


  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    // 接收调用方UIAbility传过来的参数
    this.funcAbilityWant = want;
  }


  onWindowStageCreate(windowStage: window.WindowStage): void {
    // Main window is created, set main page for this ability
    hilog.info(DOMAIN_NUMBER, TAG, '%{public}s', 'Ability onWindowStageCreate');
    // Main window is created, set main page for this ability
    let url = 'pages/Index';
    if (this.funcAbilityWant?.parameters?.router && this.funcAbilityWant.parameters.router === 'funcA') {
      url = 'pages/Page_ColdStartUp';
    }
    windowStage.loadContent(url, (err, data) => {
      // ...
    });
  }
}
```



#### 目标UIAbility热启动



在应用开发中，会遇到目标[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)实例之前已经启动过的场景，这时再次启动目标UIAbility时，不会重新走初始化逻辑，只会直接触发[onNewWant()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonnewwant)生命周期方法。为了实现跳转到指定页面，需要在onNewWant()中解析参数进行处理。

例如短信应用和联系人应用配合使用的场景。

1. 用户先打开短信应用，短信应用的UIAbility实例启动，显示短信应用的主页。
2. 用户将设备回到桌面界面，短信应用进入后台运行状态。
3. 用户打开联系人应用，找到联系人张三。
4. 用户点击联系人张三的短信按钮，会重新启动短信应用的UIAbility实例。
5. 由于短信应用的UIAbility实例已经启动过了，此时会触发该UIAbility的onNewWant()回调，而不会再走[onCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityoncreate)和[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)等初始化逻辑。

图1 目标UIAbility热启动

![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250530171844.15740898442646914310322893902923:50001231000000:2800:A1E9D45C48E57D02BC5F18E6688802DA760C80BFDF807F3F8C9830AA78CA20D6.png)





开发步骤如下所示。

1. 冷启动短信应用的UIAbility实例时，在[onWindowStageCreate()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonwindowstagecreate)生命周期回调中，通过调用[getUIContext()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-window#getuicontext10)接口获取UI上下文实例[UIContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-arkui-uicontext)对象。

```ts
import { hilog } from '@kit.PerformanceAnalysisKit';
import { Want, UIAbility } from '@kit.AbilityKit';
import { window, UIContext } from '@kit.ArkUI';


const DOMAIN_NUMBER: number = 0xFF00;
const TAG: string = '[EntryAbility]';


export default class EntryAbility extends UIAbility {
  funcAbilityWant: Want | undefined = undefined;
  uiContext: UIContext | undefined = undefined;


  // ...


  onWindowStageCreate(windowStage: window.WindowStage): void {
    // Main window is created, set main page for this ability
    hilog.info(DOMAIN_NUMBER, TAG, '%{public}s', 'Ability onWindowStageCreate');
    let url = 'pages/Index';
    if (this.funcAbilityWant?.parameters?.router && this.funcAbilityWant.parameters.router === 'funcA') {
      url = 'pages/Page_ColdStartUp';
    }


    windowStage.loadContent(url, (err, data) => {
      if (err.code) {
        return;
      }


      let windowClass: window.Window;
      windowStage.getMainWindow((err, data) => {
        if (err.code) {
          hilog.error(DOMAIN_NUMBER, TAG, `Failed to obtain the main window. Code is ${err.code}, message is ${err.message}`);
          return;
        }
        windowClass = data;
        this.uiContext = windowClass.getUIContext();
      });
      hilog.info(DOMAIN_NUMBER, TAG, 'Succeeded in loading the content. Data: %{public}s', JSON.stringify(data) ?? '');
    });
  }
}
```



2. 在短信应用UIAbility的[onNewWant()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability#uiabilityonnewwant)回调中解析调用方传递过来的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数，通过调用[UIContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-arkui-uicontext)中的[getRouter()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-arkui-uicontext#getrouter)方法获取[Router](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-arkui-uicontext#router)对象，并进行指定页面的跳转。此时再次启动该短信应用的UIAbility实例时，即可跳转到该短信应用的UIAbility实例的指定页面。

```ts
import { AbilityConstant, Want, UIAbility } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import type { Router, UIContext } from '@kit.ArkUI';
import type { BusinessError } from '@kit.BasicServicesKit';


const DOMAIN_NUMBER: number = 0xFF00;
const TAG: string = '[EntryAbility]';


export default class EntryAbility extends UIAbility {
  funcAbilityWant: Want | undefined = undefined;
  uiContext: UIContext | undefined = undefined;
  // ...
  onNewWant(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    if (want?.parameters?.router && want.parameters.router === 'funcA') {
      let funcAUrl = 'pages/Page_HotStartUp';
      if (this.uiContext) {
        let router: Router = this.uiContext.getRouter();
        router.pushUrl({
          url: funcAUrl
        }).catch((err: BusinessError) => {
          hilog.error(DOMAIN_NUMBER, TAG, `Failed to push url. Code is ${err.code}, message is ${err.message}`);
        });
      }
    }
  }
}
```



> 当被调用方[UIAbility组件启动模式](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uiability-launch-type)设置为multiton启动模式时，每次启动都会创建一个新的实例，那么onNewWant()回调就不会被用到。
