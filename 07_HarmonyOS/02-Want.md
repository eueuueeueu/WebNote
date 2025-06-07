

## Want的定义与用途

[Want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)是一种对象，用于在应用组件之间传递信息。

其中，一种常见的使用场景是作为[startAbility()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-inner-application-uiabilitycontext#uiabilitycontextstartability)方法的参数。例如，当UIAbilityA需要启动UIAbilityB并向UIAbilityB传递一些数据时，可以使用Want作为一个载体，将数据传递给UIAbilityB。

**图1** Want用法示意

![](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20250530171845.79901085716834244871144153144345:50001231000000:2800:C52B9E8F5D60ECDB0EF0B799BADB4B46BCE3F4F8889CC122E18EDA1E28BE6DE1.png)

## Want的类型

- **显式Want**：在启动目标应用组件时，调用方传入的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数中指定了abilityName和bundleName，称为显式Want。

  显式Want通常用于应用内组件启动，通过在Want对象内指定本应用Bundle名称信息（bundleName）和abilityName来启动应用内目标组件。当有明确处理请求的对象时，显式Want是一种简单有效的启动目标应用组件的方式。

  > 从API 12开始，已不再推荐三方应用使用指定Ability方式（即显式Want）拉起其他应用，推荐通过指定[应用链接](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/app-startup-overview#%E5%BA%94%E7%94%A8%E9%93%BE%E6%8E%A5)的方式来实现。

- **隐式Want**：在启动目标应用组件时，调用方传入的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数中未指定abilityName，称为隐式Want。

  当需要处理的对象不明确时，可以使用隐式Want，在当前应用中使用其他应用提供的某个能力，而不关心提供该能力的具体应用。隐式Want使用[skills标签](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/module-configuration-file#skills%E6%A0%87%E7%AD%BE)来定义需要使用的能力，并由系统匹配声明支持该请求的所有应用来处理请求。例如，需要打开一个链接的请求，系统将匹配所有声明支持该请求的应用，然后让用户选择使用哪个应用打开链接。

  > - 根据系统中待匹配应用组件的匹配情况不同，使用隐式Want启动应用组件时会出现以下三种情况。
>
  >   - 未匹配到满足条件的应用组件：启动失败。
>   - 匹配到一个满足条件的应用组件：直接启动该应用组件。
  >   - 匹配到多个满足条件的应用组件（[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-uiability)）：弹出选择框让用户选择。
  >
  > - 对于启动ServiceExtensionAbility的场景。
>
  >   - 调用方传入的[want](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-app-ability-want)参数中带有abilityName，则不允许通过隐式Want启动ServiceExtensionAbility。
>   - 调用方传入的want参数中带有bundleName，则允许使用startServiceExtensionAbility()方法隐式Want启动ServiceExtensionAbility，默认返回优先级最高的ServiceExtensionAbility，如果优先级相同，返回第一个。



## 常见系统应用Want



通常跳转系统应用的场景有两类：**跳转系统设置的各级页面**以及**跳转其他系统应用**。

- 跳转**系统设置的各级页面**Want参数需指定bundleName与abilityName，如需跳转设置相关子页面，除了指定bundleName与abilityName外，通常还需要填写对应uri与parameters，如下表格给出了常用各类设置子页面的uri与parameters配置信息：

| 页面           | bundleName               | abilityName                          | uri                       | parameters    |
| :------------- | :----------------------- | :----------------------------------- | :------------------------ | :------------ |
| WLAN页         | com.huawei.hmos.settings | com.huawei.hmos.settings.MainAbility | wifi_entry                | /             |
| 蓝牙页         | com.huawei.hmos.settings | com.huawei.hmos.settings.MainAbility | bluetooth_entry           | /             |
| 位置页         | com.huawei.hmos.settings | com.huawei.hmos.settings.MainAbility | location_manager_settings | /             |
| 指定应用信息页 | com.huawei.hmos.settings | com.huawei.hmos.settings.MainAbility | application_info_entry    | APPbundleName |
| 应用与元服务页 | com.huawei.hmos.settings | com.huawei.hmos.settings.MainAbility | application_info_entry    | /             |
| 声音和振动     | com.huawei.hmos.settings | com.huawei.hmos.settings.MainAbility | volume_settings           | /             |

> **"/"代表无需配置；"APPbundleName"为指定应用的bundleName，获取方式可参考官网[如何获取当前HAP的BundleName](https://developer.huawei.com/consumer/cn/doc/harmonyos-faqs/faqs-package-structure-26)。**



跳转系统设置页及子页面可参考如下代码进行配置：

```ts
// 跳转系统设置页及子页面配置参考
let want: Want = {
  bundleName: "com.huawei.hmos.settings",
  abilityName: "com.huawei.hmos.settings.MainAbility",
  uri: "xxxx",
  parameters: {
    pushParams: "xxxxx"
  }
}
context.startAbility(want)


// 跳转到设置的声音和振动；包含铃声、声音模式、免打扰等
let want: Want = {
  bundleName: 'com.huawei.hmos.settings',
  abilityName: 'com.huawei.hmos.settings.MainAbility',
  uri: "volume_settings",
  parameters: {
    // 传对应应用的包名
    pushParams: 'com.example.gotosettings'
  }
};
context.startAbility(want)
```



- 跳转**其他系统应用**，例如浏览器、应用市场、短信、联系人、文件管理器、电子邮箱等，Want参数配置情况各有不同，各类系统应用Want相关配置可参考如下表格：

| 应用       |           action            | bundleName                  | abilityName                   | uri                                                   | parameters                 |
| :--------- | :-------------------------: | :-------------------------- | :---------------------------- | :---------------------------------------------------- | :------------------------- |
| 浏览器     |  ohos.want.action.viewData  | com.huawei.hmos.browser     | MainAbility                   | 需自行构建，可参考下列代码                            | /                          |
| 文件管理器 |              /              | com.huawei.hmos.filemanager | MainAbility                   | /                                                     | /                          |
| 短信       |              /              | com.ohos.mms                | com.ohos.mms.MainAbility      | /                                                     | 需自行构建，可参考下列代码 |
| 联系人     |              /              | com.ohos.contacts           | com.ohos.contacts.MainAbility | /                                                     | 需自行构建，可参考下列代码 |
| 应用市场   |              /              | /                           | /                             | store://appgallery.huawei.com/app/detail?id=C${APPID} | /                          |
| 电子邮箱   | ohos.want.action.sendToData | com.huawei.hmos.email       | /                             | 需自行构建，可参考下列代码                            | /                          |

> **"/"代表无需配置；"APPID"为应用在AGC上对应的APPID。**



```ts
// 跳转浏览器并传递参数
let want: Want = {
  action: "ohos.want.action.viewData", // 表示要执行操作
  bundleName: 'com.huawei.hmos.browser', // 系统浏览器对应bundleName
  abilityName: 'MainAbility', // 系统浏览器对应abilityName
  uri: "https://www.huawei.com/", // 传递的参数
}


// 跳转应用市场指定应用
let want: Want = {
  uri: "store: // appgallery.huawei.com/app/detail?id=C+应用在应用商城的ID"
}


// 跳转短信并传递参数
export class Contact { // 构造联系人
  contactsName: string;
  telephone: number;


  constructor(contactsName: string, telephone: number) {
    this.contactsName = contactsName;
    this.telephone = telephone;
  }
}
let params: Array<Object> = [new Contact("张三", 12345678912)]


let want:Want = {
  bundleName: 'com.ohos.mms', // 短信对应的bundleName
  abilityName: 'com.ohos.mms.MainAbility', // 短信对应的abilityName
  parameters: {
    contactObjects: JSON.stringify(params), // 联系人信息
    content:"xxxxxxxxxx", // 短信内容
    pageFlag: 'conversation'
  }
}


// 跳转联系人并传递参数
let want: Want = {
  bundleName: 'com.ohos.contacts',
  abilityName: 'com.ohos.contacts.MainAbility',
  parameters: {
    'phoneNumber': 'xxxxxxx', // 需新增联系人电话
    'contactName': 'xxxx', // 需新增联系人姓名
    'pageFlag': 'page_flag_save_contact'
  }
}


// 跳转文件管理器
let want: Want = {
  bundleName: 'com.huawei.hmos.filemanager',
  abilityName: 'MainAbility',
}


// 跳转电子邮箱
let want: Want = {
  bundleName: 'com.huawei.hmos.email',
  action: 'ohos.want.action.sendToData',
  uri: 'mailto:feedback@example.com?subject=App Feedback&body=Please describe your feedback here...'
}
let context = getContext(this) as common.UIAbilityContext
context.startAbility(want)
```

