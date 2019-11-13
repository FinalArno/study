# VUE API

## 全局配置

## 全局 API

### Vue.extend(options)

- 参数 options 为对象
- 用法：使用 Vue.extend()构造处一个对象，这个对象包含组件选项，里面有 templete ，data（必须是一个函数）

```js
<div id="mount-point"></div>;
// 创建构造器
var Profile = Vue.extend({
  template: "<p>{{firstName}} {{lastName}} aka {{alias}}</p>",
  data: function() {
    return {
      firstName: "Walter",
      lastName: "White",
      alias: "Heisenberg"
    };
  }
});
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount("#mount-point");
//最终生成
<div id="mount-point">
  <p>Walter White aka Heisenberg</p>
</div>;
```

### Vue.nextTick([callback,context]) 异步更新队列

> vue 更新 DOM 是异步的，只要侦听到数据变化，vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变化。如果同一个 watcher 被多次触发，只会被推入到队列中一次。在缓冲时 Vue 会去除重复数据避免不必要的 DOM 操作和计算，然后，在下一个事件循环 tick 中，Vue 刷新并执行（已去重）的工作，Vue 在内部异步队列尝试使用原生的 Promise.then,MutationObserver 和 setImmediate,如果浏览器不支持，则使用 setTimeout(fn,0)代替

例如，当设置 vue.someData='new value'，该数据绑定的组件不会立即重新渲染。当刷新队列时，组件会在下一个事件循环 tick 中更新。当我们需要在 DOM 更新后做点什么，那就用 **Vue.nextTick(callback)**，这样回调函数就会在 DOM 更新完以后调用。
例如：

```js
Vue.component("example", {
  template: "<span>{{ message }}</span>",
  data: function() {
    return {
      message: "未更新"
    };
  },
  methods: {
    updateMessage: function() {
      this.message = "已更新";
      console.log(this.$el.textContent); // => '未更新' 这时DOM还未更新
      this.$nextTick(function() {
        console.log(this.$el.textContent); // => '已更新' 这时DOM已经更新
      });
    }
  }
});
```

因为 nextTick 返回的是一个 Promise，所以我们可以结合 ES2017 的 async/await 函数来完成相同的事情

```js
methods:{
    updateMessage:async function() {
        this.message = "已更新";
        console.log(this.$el.textContent); // => '未更新' 这时DOM还未更新
        await this.$nextTick();
        console.log(this.$el.textContent); // => '已更新' 这时 DOM 已经更新
    }
}
```

### Vue.set(target,propertyName/index,value)

向响应式对象中添加一个属性，并确保这个**新属性**同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新属性，因为 Vue 无法探测普通的新增属性 (比如 this.myObject.newProperty = 'hi')

### Vue.delete(target,propertyName/index)

删除对象的属性，如果对象是响应式的，确保删除能触发视图更新。这个方法主要用于避开 Vue 不能检测到属性被删除的限制。

> 目标对象不能是一个 Vue 实例或者 Vue 实例的根数据对象

### Vue.directive(id,[definition])

参数 String id,{Function|Object} \[definition]

用法：注册获取全局指令

```js
// 注册
Vue.directive("my-directive", {
  bind: function() {},
  inserted: function() {},
  update: function() {},
  componentUpdated: function() {},
  unbind: function() {}
});

// 注册 (指令函数)
Vue.directive("my-directive", function() {
  // 这里将会被 `bind` 和 `update` 调用
});

// getter，返回已注册的指令
var myDirective = Vue.directive("my-directive");
```

### Vue.filter(id,[definition])

参数: String id，{Function} \[definition]
用法：注册或者获取全局过滤器

```js
// 注册
Vue.filter("my-filter", function(value) {
  // 返回处理后的值
});

// getter，返回已注册的过滤器
var myFilter = Vue.filter("my-filter");
```

### Vue.component(id,[definition])

参数:String id，{Function|Object} \[definition]
用法：注册或者获取全局组件，注册还会自动使用指定的 id 设置组件的名称

```js
// 注册组件，传入一个扩展过的构造器
Vue.component('my-component', Vue.extend({ /_ ... _/ }))

// 注册组件，传入一个选项对象 (自动调用 Vue.extend)
Vue.component('my-component', { /_ ... _/ })

// 获取注册的组件 (始终返回构造器)
var MyComponent = Vue.component('my-component')
```

### Vue.use(plugin)

参数：{Object|Function} pulgin
用法：安装 Vuejs 插件，如果插件是一个对象，必须提供 install 方法，如果插件是一个函数，他会被作为 install 方法，install 方法调用的时候，会将 Vue 作为参数传入。
通过全局方法 Vue.use() 使用插件。它需要在你调用 new Vue() 启动应用之前完成

### 开发插件

VUE 的插件应该暴露一个 install 方法，这个方法的第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象

```js
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
Vue.use(MyPlugin)
```

### Vue.mixin(mixin)

参数： {Object} mixin
用法：全局注册一个混入，影响注册之后所有创建的每个 Vue 实例，插件作者可以使用混入，向组件注入自定义的行为
创建一个混入对象：

```js
// 定义一个混入对象
var myMixin = {
  created: function() {
    this.hello();
  },
  methods: {
    hello: function() {
      console.log("hello from mixin!");
    }
  }
};
// 定义一个使用混入对象的组件
var Component = Vue.extend({
  mixins: [myMixin]
});

var component = new Component(); // => "hello from mixin!"

Vue.mixin(myMixin); //全局混入
```

### Vue.compile( template )

参数：{string} template
用法：在 render 函数中编译模板字符串。只在独立构建时有效

```js
var res = Vue.compile("<div><span>{{ msg }}</span></div>");

new Vue({
  data: {
    msg: "hello"
  },
  render: res.render,
  staticRenderFns: res.staticRenderFns
});
```

### Vue.observable( object )

参数：{Object} object
用法：让一个对象可响应。Vue 内部会用它来处理 data 函数返回的对象。

返回的对象可以直接用于渲染函数和计算属性内，并且会在发生改变时触发相应的更新。也可以作为最小化的跨组件状态存储器，用于简单的场景：

```js
const state = Vue.observable({ count: 0 });

const Demo = {
  render(h) {
    return h(
      "button",
      {
        on: {
          click: () => {
            state.count++;
          }
        }
      },
      `count is: ${state.count}`
    );
  }
};
```

## 选项/数据

### data

类型 Object|Function
