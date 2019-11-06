## MVVM（是一种架构）

- 1，MVVM 是 model - view - viewModel 的缩写
- 2，model 代表数据模型，也可以在 model 中定义数据修改以及操\*作的业务逻辑
- 3，view 代表 UI 组件，它负责将数据模型转换成 UI 展现出来
- 4，viewmodel 是用来监听数据模型的改变和控制视图的行为，处理用户交互等，简单的理解就是一个同步的 view 与 model 对象，用来控制 view 与 model，相当于 mvc 架构中的 controller
- 5，在 mvvm 架构中，model 跟 view 没有直接的关联，而是通过 viewmodel 进行交互，model 与 viewmodel 之间的交互是双向的，因此 view 的数据变化会同步到 model 中，而 model 的变化也会同步到 view 中
- 6，**viewmodel**通过双向数据绑定把 view 与 model 连接起来，而 view 与 model 之间的同步是自动的，无需认为干涉，开发者只需要关心业务逻辑，不需要操作 dom，不需要关注数据状态同步的问题，复制的数据状态维护全由 MVVM 来管理
