# 封装组件的三种方式

案例需求：封装一个提示框组件，点击按钮，显示提示框，点击确定或取消，隐藏提示框，执行对应的回调函数

<img src="D:\WebClass\note\vue\images\02\01.png">

### 常规组件

- 普通组件是 Vue.js 中最常见的组件类型，通常用于构建 UI 界面。可以在应用中多次复用

`App.vue父页面`

```vue
<template>
  <DialogBox
    :isShow="isShow"
    title="提示"
    content="代码是写给人看的,顺便给机器执行的"
    @confirm="clickConfirm"
    @cancel="clickCancel"
  />
  <button @click="isShow = true" class="p-[5px] bg-sky-400 rounded-lg">open DialogBox</button>
</template>
<script setup>
import { ref } from 'vue'
import DialogBox from './components/DialogBox/DialogBox.vue'
const isShow = ref(false)
function clickConfirm() {
  isShow.value = false
  // 执行点击确定后的回调
  console.log('你点击了确定')
}
function clickCancel() {
  isShow.value = false
  // 执行点击取消后的回调
  console.log('你点击了取消')
}
</script>
```

`src/components/DialogBox.vue组件`

```vue
<template>
  <div
    v-show="isShow"
    class="flex flex-col absolute z-[1000] top-[50%] left-[50%] -translate-x-[50%] -translate-y-[50%] px-[10px] py-[20px] shadow-lg bg-white rounded-md"
  >
    <h1 class="font-[600] text-[20px]">{{ props.title }}</h1>
    <p class="my-[10px]">{{ props.content }}</p>
    <div class="flex justify-end">
      <button @click="emits('confirm')" class="px-[15px] py-[2px] rounded-md bg-[#ffa500] mr-[5px]">
        确定
      </button>
      <button @click="emits('cancel')" class="px-[15px] py-[2px] rounded-md bg-[#ffa500]">
        取消
      </button>
    </div>
  </div>
  <!-- 通过Teleport将遮罩层传送到body中，不受父组件的影响 -->
  <Teleport to="body">
    <div
      v-show="isShow"
      class="fixed top-0 z-[999] left-0 w-screen h-screen bg-[rgba(1,1,1,.2)]"
    ></div>
  </Teleport>
</template>
<script setup>
import { defineProps, defineEmits } from 'vue'
const emits = defineEmits(['confirm', 'cancel'])
const props = defineProps({
  title: {
    type: String,
    default: '标题',
  },
  content: {
    type: String,
    default: '内容',
  },
  isShow: {
    type: Boolean,
    default: false,
  },
})
</script>
```

### 高阶组件

- 高阶组件是一种设计模式，用于增强或扩展普通组件的功能。它本身不是一个组件，而是一个函数，接收一个组件作为参数并返回一个新的组件。

`App.vue父页面`

```vue
<template>
  <DialogBox />
  <button @click="DialogBoxApi.open" class="p-[5px] bg-sky-400 rounded-lg">open DialogBox</button>
</template>
<script setup>
import { useDialogBox } from './components/DialogBox'
const [DialogBox, DialogBoxApi] = useDialogBox({
  title: '提示',
  content: '代码是给人看的,顺便给机器执行的',
  cancel() {
    console.log('你点击了取消')
  },
  confirm() {
    console.log('你点击了确定')
  },
})
</script>
```

src/components/DialogBox/DialogBox.vue组件

```vue
<template>
  <div
    v-show="isShow"
    class="flex flex-col absolute z-[1000] top-[50%] left-[50%] -translate-x-[50%] -translate-y-[50%] px-[10px] py-[20px] shadow-lg bg-white rounded-md"
  >
    <h1 class="font-[600] text-[20px]">{{ props.title }}</h1>
    <p class="my-[10px]">{{ props.content }}</p>
    <div class="flex justify-end">
      <button @click="onConfirm" class="px-[15px] py-[2px] rounded-md bg-[#ffa500] mr-[5px]">确定</button>
      <button @click="onCancel" class="px-[15px] py-[2px] rounded-md bg-[#ffa500]">取消</button>
    </div>
  </div>
  <!-- 通过Teleport将遮罩层传送到body中，不受父组件的影响 -->
  <Teleport to="body">
    <div
      v-show="isShow"
      class="fixed top-0 z-[999] left-0 w-screen h-screen bg-[rgba(1,1,1,.2)]"
    ></div>
  </Teleport>
</template>
<script setup>
import { defineProps, defineEmits } from 'vue'
const emits = defineEmits(['confirm', 'cancel', 'close'])
const hide = () => emits('close')
function onCancel() {
  hide()
  emits('cancel')
}
function onConfirm() {
  hide()
  emits('confirm')
}
const props = defineProps({
  title: {
    type: String,
    default: '标题',
  },
  content: {
    type: String,
    default: '内容',
  },
  isShow: {
    type: Boolean,
    default: false,
  },
})
</script>
```

src/components/DialogBox/index.js

```js
import { defineComponent, ref, h } from 'vue'
import Main from "./DialogBox.vue";
export const useDialogBox = (option) => {
  const isShow = ref(false)
  const DialogBoxApi = {
    open() { isShow.value = true },
    hide() { isShow.value = false },
  }
  const DialogBox = defineComponent({
    setup() {
      return () => h(Main, {
        isShow: isShow.value,
        title: option.title,
        content: option.content,
        onCancel: option.cancel,
        onConfirm: option.confirm,
        onClose() {
          DialogBoxApi.hide()
        }
      })
    }
  })
  return [DialogBox, DialogBoxApi]
}
```

### 命令组件

- 命令式组件是通过 JavaScript 代码直接操作 DOM 或组件实例的组件。它们通常用于需要动态创建、销毁或控制的场景，比如模态框、通知、工具提示等。









