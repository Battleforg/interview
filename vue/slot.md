# v-slot

## 插槽
通过插槽，可以合成组件，当组件渲染的时候，```<slot></slot>```中的内容会被替换为填入的模版代码（html、组件）。

如果组件的template中没有包含一个```<slot>```元素，则该组件开始标签和结束标签之间的任何内容都会被抛弃。

## 后备内容
可以将后备内容放在```<slot></slot>```之间，当在一个父组件使用组件并且不提供插槽内容时后备内容会被渲染。如果提供了插槽内容，提供的内容则会被渲染。

## 具名插槽
如果有多个插槽，需要为```<slot>```元素设置name属性。只能有一个插槽不带name属性，这个插槽会被视name属性为“default”。

在向具名插槽提供内容时，需要使用```<template>```元素并设置```v-slot:```，可以用固定字符串```v-slot:name```表示插槽的名称，例如```v-slot:header```；还可以用动态name，```v-slot:[dynamicSlotName]```。

```<template>``` 元素中的所有内容都将会被传入对应的插槽。任何没有被包裹在带有 ```v-slot``` 的 ```<template>``` 中的内容都会被视为默认插槽的内容。也可以在一个```<template>```中包裹默认插槽的内容，用```v-slot:default```。

### 具名插槽的缩写
```v-slot:```可以缩写成```#```, 例如：```v-slot:header```可以缩写成```#header```。特别注意默认插槽的缩写：```#default```。因为v-bind和v-on的缩写都支持动态参数，猜测v-slot的缩写也可以用动态参数，```#[dynamicName]```。

## 插槽作用域
首先，父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。因此，父组件无法在插槽内访问子组件实例的数据。

第一个情况，如果数据是父组件传给子组件的prop，且prop不会用于进一步计算或者副作用（像异步请求数据），直接在父组件填入插槽的模版中用变量引用原先的prop，再用v-bind:prop传入动态prop。

在不满足第一种情况时，使用插槽prop。
### 插槽prop
如果必须在插槽内访问子组件中才有的数据，需要使用插槽prop，也就是绑定在```<slot>```元素上的attribute，例如```v-bind:attribute="xxx"```。然后在父组件中，


```html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

作用域插槽的内部工作原理是将你的插槽内容包裹在一个拥有单个参数的函数里，在单文件组件下，可以解构传入的插槽prop：
```html
<current-user v-slot="{ user: person = { firstName: 'Guest' } }">
  {{ person.firstName }}
</current-user>
```

### 独占默认插槽的缩写写法
在组件只有默认插槽时，
```html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

可以缩写为
```html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

只要出现多个插槽，请始终为所有的插槽使用完整的基于 ```<template>``` 的语法.
```html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</current-user>
```

