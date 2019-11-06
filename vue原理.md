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

> Vue 数据双向绑定将 MVVM 数据绑定作为入口，结合 Observer，Complie，Watcher 三者。通过 Observer 来观察 model 数据的变化，通过 Complie 来编译以及解析模版指令，最终利用 Watcher 来搭起 Observer 与 Complie 之间的桥梁，从而达到 **数据变化=>视图更新** **视图交互变化（如 input）=>数据 model 变更**的数据双向绑定模式

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
