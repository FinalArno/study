## MVVM（是一种架构）

- 1，MVVM 是 model - view - viewModel 的缩写
- 2，model 代表数据模型，也可以在 model 中定义数据修改以及操\*作的业务逻辑
- 3，view 代表 UI 组件，它负责将数据模型转换成 UI 展现出来
- 4，viewmodel 是用来监听数据模型的改变和控制视图的行为，处理用户交互等，简单的理解就是一个同步的 view 与 model 对象，用来控制 view 与 model，相当于 mvc 架构中的 controller
- 5，在 mvvm 架构中，model 跟 view 没有直接的关联，而是通过 viewmodel 进行交互，model 与 viewmodel 之间的交互是双向的，因此 view 的数据变化会同步到 model 中，而 model 的变化也会同步到 view 中
- 6，**viewmodel**通过双向数据绑定把 view 与 model 连接起来，而 view 与 model 之间的同步是自动的，无需认为干涉，开发者只需要关心业务逻辑，不需要操作 dom，不需要关注数据状态同步的问题，复制的数据状态维护全由 MVVM 来管理

## Vue 实现数据双向绑定原理：Object.defineProperty()

> 原理是**数据劫持**配合**发布者-订阅者模式**，通过 **Object.defineProperty()**来劫持各个属性的 getter 以及 setter，在数据变动的时候通知订阅者，触发相应的监听回调

- 当把一个普通的 js 对象传给 Vue 实例来作为它 data 选项时，Vue 将会遍历它的属性，用**Object.defineProperty()**来将它转换为 getter（用户访问数据） 以及 setter（用户赋值的时候通知订阅者）
- 用户看不到 getter 以及 setter，但是在内部它让 Vue 追踪依赖，在属性被访问以及修改的时候通知订阅者

> Vue 数据双向绑定将 MVVM 数据绑定作为入口，结合 Observer，Complier，Watcher 三者。通过 Observer 来观察 model 数据的变化，通过 Complie 来编译以及解析模版指令，最终利用 Watcher 来搭起 Observer 与 Complie 之间的桥梁，从而达到 **数据变化=>视图更新** **视图交互变化（如 input）=>数据 model 变更**的数据双向绑定模式

### js 简单实现双向绑定代码

```
<body>
    <div id="app">
        <input type="text" id="txt">
        <p id="show"></p>
    </div>
</body>
<script type="text/javascript">
    var obj = {};
    Object.defineProperty(obj, 'txt', {
        get: function () {
            return obj
        },
        set: function (newValue) {
            document.getElementById('txt').value = newValue;
            document.getElementById('show').innerHTML = newValue;
        }
    })
    document.addEventListener('keyup', function (ev) {
        obj.txt = ev.target.value;
    })
</script>
```

[mvc 与 mvvm 的区别]('https://www.jianshu.com/p/b0aab1ffad93')

## 一 ,概述

MVC,MVP,MVVM 是三种常见的前端架构模式(Architectural Pattern),它通过分离关注点来改进代码组织方式。不同于设计模式(Design Pattern),只是为了解决一类问题而总结出的抽象方法，一种架构模式往往能使用多种设计模式。

MVC 模式是 MVP,MVVM 模式的基础，这两种模式更像是 MVC 模式的优化改良版,他们三个的 MV 即 Model，view 相同，不同的是 MV 之间的纽带部分。本文主要介绍 MVC 与 MVVM 的应用与区别，因为 MVP 好像不是很常用。

## 二，MVC

MVC 允许在不改变视图的情况下改变视图对用户输入的响应方式，用户对 View 的操作交给了 Controller 处理，在 Controller 中响应 View 的事件调用 Model 的接口对数据进行操作，一旦 Model 发生变化便通知相关视图进行更新。

如果前端没有框架，只使用原生的 html+js，MVC 模式可以这样理解。将 html 看成 view;js 看成 controller，负责处理用户与应用的交互，响应对 view 的操作（对事件的监听），调用 Model 对数据进行操作，完成 model 与 view 的同步（根据 model 的改变，通过选择器对 view 进行操作）;将 js 的 ajax 当做 Model，也就是数据层，通过 ajax 从服务器获取数据。

按照上面这种方式分层，感觉多少有点强行 MVC，因为 Model 层被弱化了。我们可以看看其他主流 MVC 框架是怎么分层的，拿 BackBone 举例。

首先简单介绍一下 backbone，它是一个轻量级前端 MVC 框架，用于结构化管理页面中大量的 js（就是管理大量 js 文件的项目更适用），建立与服务器，视图间的无缝连接，为构建复杂应用提供基础架构。backbone 里面包含 Model，View，Collection，Router 等模块。这里的 Model 和 View 与 MVC 模式的 Model 和 View 有区别，我们先看 Model。

> MVVM 与 MVC 最大的区别就是：它实现了 View 和 Model 的自动同步，也就是当 Model 的属性改变时，我们不用再自己手动操作 Dom 元素，来改变 View 的显示，而是改变属性后该属性对应 View 层显示会自动改变。
