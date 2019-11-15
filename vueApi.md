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
限制：如果是组件只能接收 Function

- data 是 Vue 实例的数据对象，Vue 将会递归将 data 的属性转换为 getter/setter。从而能够让 data 的属性能够响应数据变化
- 以\$或者\_开头的属性不会被代理，因为可能会和 Vue 的内置属性冲突
- 当一个组件被定义，data 必须声明为返回一个初始数据对象的函数，因为组件可能被用来创建多个实例。如果 data 仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！通过提供 data 函数，每次创建一个新实例后，我们能够调用 data 函数，从而返回初始数据的一个全新副本数据对象。

> 注意，如果你为 data 属性使用了箭头函数，则 this 不会指向这个组件的实例，不过你仍然可以将其实例作为函数的第一个参数来访问。

```js
data: vm => ({ a: vm.myProp });
```

### props

类型：Array\<String>|Object

示例：

```js
// 简单语法
Vue.component("props-demo-simple", {
  props: ["size", "myMessage"]
});

// 对象语法，提供验证
Vue.component("props-demo-advanced", {
  props: {
    // 检测类型
    height: Number,
    // 检测类型 + 其他验证
    age: {
      type: Number,
      default: 0,
      required: true,
      validator: function(value) {
        return value >= 0;
      }
    }
  }
});
```

### propData

类型 {\[key:Sting]: value:any}
只用户 new 创建的实例中，创建的时候传入 props，方便测试
示例:

```js
var Comp = Vue.extend({
  props: ["msg"],
  template: "<div>{{ msg }}</div>"
});

var vm = new Comp({
  propsData: {
    msg: "hello"
  }
});
```

### computed 计算属性

类型 :{\[key:String]:Function|{get:Function,set:Funtion}}

计算的结果会被缓冲起来，除非依赖的响应式属性发生变化才会重新计算
示例：

```js
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // 仅读取
    aDouble: function() {
      return this.a * 2;
    },
    // 读取和设置
    aPlus: {
      get: function() {
        return this.a + 1;
      },
      set: function(v) {
        this.a = v - 1;
      }
    }
  }
});
vm.aPlus; // => 2
vm.aPlus = 3;
vm.a; // => 2
vm.aDouble; // => 4
```

### methods

> 注意：不应该使用箭头函数来定义 method 里面的函数（例如：plus:()=>{}）,因为箭头函数绑定了父级作用域的上下文 Window，所以 this 将不会按照期望指向 Vue 实例。

### watch

类型：{\[key:String]:String|Function|Object|Array}

示例：

```js
var vm = new Vue({
  data: {},
  watch: {
    a: function(val, oldVal) {
      //...to do
    },
    b:function(),
    c:{
      handler(val, oldVal){
         //...to do
      },
      deep:true,// 该回调会在任何被侦听的对象的 property 改变时被调用，不论其被嵌套多深
    },
    d:{
      handler(val, oldVal){
         //...to do
      },
      inmmediate:true,// 该回调将会在侦听开始之后被立即调用
    },
    'e.f':function(val, oldVal) {//监听对象e的f属性变化
      //...to do
    },
  },
  methods:{
    watchb(val,oldVal){
        //...to do
    }
  }
});
```

## 选项/DOM

### el

### templete

### render

类型：(createElement: () => VNode) =>VNode
详细： createElement 参数
字符串模板的代替方案，允许你发挥 JavaScript 最大的编程能力。该渲染函数接收一个 createElement 方法作为第一个参数用来创建 VNode。

如果组件是一个函数组件，渲染函数还会接收一个额外的 context 参数，为没有实例的函数组件提供上下文信息。

```js
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // 一个 HTML 标签名、组件选项对象，或者
  // resolve 了上述任何一种的一个 async 函数。必填项。
  "div",

  // {Object}
  // 一个与模板中属性对应的数据对象。可选。
  {
    // (详情见下一节)
  },

  // {String | Array}
  // 子级虚拟节点 (VNodes)，由 `createElement()` 构建而成，
  // 也可以使用字符串来生成“文本虚拟节点”。可选。
  [
    "先写一些文字",
    createElement("h1", "一则头条"),
    createElement(MyComponent, {
      props: {
        someProp: "foobar"
      }
    })
  ]
);
```

### renderError

2.2.0 新增

类型：(createElement: () => VNode, error: Error) => VNode
详细：只在开发者环境下工作。当 render 函数遭遇错误时，提供另外一种渲染输出。其错误将会作为第二个参数传递到 renderError。这个功能配合 hot-reload 非常实用。
示例：

```js
new Vue({
  render(h) {
    throw new Error("oops");
  },
  renderError(h, err) {
    return h("pre", { style: { color: "red" } }, err.stack);
  }
}).$mount("#app");
```

> 参考：render&renderError.html

## 选项 / 生命周期钩子

> 所有的生命周期钩子自动绑定 this 上下文到实例中，因此你可以访问数据，对属性和方法进行运算。这意味着你不能使用箭头函数来定义一个生命周期方法 (例如 created: () => this.fetchTodos())。这是因为箭头函数绑定了父上下文，因此 this 与你期待的 Vue 实例不同，this.fetchTodos 的行为未定义。

### beforeCreated

类型：Function
描述：在实例初始化之后，数据观察（data observer）和 event/watcher 事件配置之前被调用

### created

类型：Function
描述：在实例初始化之后，数据观察（data observer）和 event/watcher 事件已经被配置好了，但是挂载阶段还未开始，\$el 属性不可见

### beforeMounted

类型：Function
描述：在挂载开始之前被调用，相关的 render 函数首次被调用，该钩子函数在服务端渲染（Nuxt）的期间不会被调用，服务端渲染完了会调用

### mounted

类型：Function
描述：el 被新创建的$el替换，并挂载到实例上去之后调用该钩子函数，如果root实例挂载了一个文档元素，当mounted被调用时，vm.$el 也在文档内

> 注意，mounted 不会承诺所有的子组件也都一起被挂载，如果你期望等到视图都渲染完了可以用 vm.\$nextTick 来代替 mounted

```js
mounted:function(){
  this.$nextTick(function(){
   //这里所有的视图都渲染完成
  });
}
```

同上，该钩子函数在服务端渲染期间也不会被调用，只有等服务端渲染完成后才会调用

### beforeUpdate

类型：Function
描述：数据更新时调用，发生在虚拟 DOM 更新之前，这个钩子函数适合在虚拟 DOM 更新之前访问现有的 DOM，比如手动移除已添加的事件监听

> 该钩子函数在服务端渲染期间不会被调用，因为只有初次渲染会在服务端进行

### updated

类型：Function
描述：由于数据改变导致虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子函数
当这个钩子函数被调用时，说明组件 DOM 已更新，所以可以执行跟 DOM 相关的操作，然而在大多数情况下，应该避免在此期间更改状态。如果要相应状态改变，则推荐使用 computed 计算属性或者 watch 取代。

> 同 mounted 一样，updated 不会承诺所有的子组件都重绘，如果期望等整个视图都重绘完毕，那么可以结合 this.\$nextTick 使用
> 该钩子函数在服务端渲染期间也不会被调用，只有等服务端渲染完成后才会调用

```js
updated:function(){
  this.$nextTick(function(){
    //这里所有的视图都渲染完成
  })
}
```

### activated

类型：Function
描述：keep-alive 组件激活时调用
该钩子函数在服务端渲染期间也不会被调用，只有等服务端渲染完成后才会调用

### 动态组件上使用 keep-alive

### keep-alive 用法 \<keep-alive> \<component /> \</keep-alive>

参数：

include - 字符串或正则表达式。只有名称匹配的组件会被缓存。
exclude - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。
max - 数字。最多可以缓存多少组件实例。

用法：\<keep-alive> 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 \<transition> 相似，\<keep-alive> 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。
当组件在 \<keep-alive> 内被切换，它的 **activated** 和 **deactivated** 这两个生命周期钩子函数将会被对应执行。

### deactivated

类型：Function
描述：keep-alive 组件停用时调用
该钩子函数在服务端渲染期间也不会被调用，只有等服务端渲染完成后才会调用

### beforeDestory

类型：Function
描述：实例被销毁之前调用，在这一步，实例仍热完全可以用
该钩子函数在服务端渲染期间也不会被调用，只有等服务端渲染完成后才会调用

### destory

类型：Function
描述：Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解除绑定，所有的事件监听会被移除，所有的子实例也会被销毁
该钩子函数在服务端渲染期间也不会被调用，只有等服务端渲染完成后才会调用

### errorCaptured

类型：(err:Error,vm:Component,info:String)=>?boolean
描述：当捕获一个子组件的错误调用时，此钩子函数会受到三个参数：错误对象，发生错误的组件实例，以及一个包含错误来源信息的字符串。此钩子函数可以返回 false 以阻止该错误继续向上传播

> 你可以在此钩子中修改组件的状态。因此在模板或渲染函数中设置其它内容的短路条件非常重要，它可以防止当一个错误被捕获时该组件进入一个无限的渲染循环。

错误传播规则：

- 默认情况下，如果全局的 config.errorHandler 被定义，所有的错误仍会发送它，因此这些错误仍然会向单一的分析服务的地方进行汇报。

- 如果一个组件的继承或父级从属链路中存在多个 errorCaptured 钩子，则它们将会被相同的错误逐个唤起。

- 如果此 errorCaptured 钩子自身抛出了一个错误，则这个新错误和原本被捕获的错误都会发送给全局的 config.errorHandler。

- 一个 errorCaptured 钩子能够返回 false 以阻止错误继续向上传播。本质上是说“这个错误已经被搞定了且应该被忽略”。它会阻止其它任何会被这个错误唤起的 errorCaptured 钩子和全局的 config.errorHandler。

## 选项 / 资源

### directives

类型：Object
描述：包含 Vue 实例可用的指令的哈希表

### filters

类型：Object
描述：包含 Vue 实例可用过滤器的哈希表。

### components

类型：Object
描述：包含 Vue 实例可用组件的 hash 表

## 选项/组合

### parent

类型：Vue instance
描述：指定以创建的实例之父实例，在两者之间建立父子关系，子实例可以用 this.$parent访问父实例，子实例被推入父实例的$children 数组中

> 节制地使用 $parent 和 $children - 它们的主要目的是作为访问组件的应急方法。更推荐用 props 和 events 实现父子组件通信
> 参考：Parent.html

### mixins

类型：Array\<Object>
描述：mixins 选项接受一个混入对象的数组。这些混入实例对象可以像正常的实例对象一样包含选项，他们将在 Vue.extend() 里最终选择使用相同的选项合并逻辑合并。举例：如果你的混入包含一个钩子而创建组件本身也有一个，两个函数将被调用。

Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子**之前**被调用。

```js
var mixin = {
  created: function() {
    console.log(1);
  }
};
var vm = new Vue({
  created: function() {
    console.log(2);
  },
  mixins: [mixin]
});
// => 1
// => 2
```

### extends

类型： Object|Function
描述：允许声明扩展另一个组件(可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。这主要是为了便于扩展单文件组件。

> vue 的 extends 和 mixins 类似，通过暴露一个 extends 对象到组件中使用。
> extends 会比 mixins 先执行。执行顺序：extends > mixins > 组件

示例：

```js
var CompA = { ... }

// 在没有调用 `Vue.extend` 时候继承 CompA
var CompB = {
  extends: CompA,
  ...
}


----------
const extendsTest = {
    methods: {
        hello3() {
            console.log("hello extends");
        }
    },
    beforeCreate(){
        console.log("extends的beforeCreated");

    },
    created() {
        this.hello3();
    },
}
new Vue({
   ...
   extends:extendsTest  // 使用extends
})
```

### provide/inject

类型：provide：Object | () => Object
inject：Array\<string> | { [key: string]: string | Symbol | Object }

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。如果你熟悉 React，这与 React 的上下文特性很相似。

provide 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。在该对象中你可以使用 ES2015 Symbols 作为 key，但是只在原生支持 Symbol 和 Reflect.ownKeys 的环境下可工作。
inject 选项应该是：

一个字符串数组，或
一个对象，对象的 key 是本地的绑定名，value 是：
在可用的注入内容中搜索用的 key (字符串或 Symbol)，或
一个对象，该对象的：
from 属性是在可用的注入内容中搜索用的 key (字符串或 Symbol)
default 属性是降级情况下使用的 value

> provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。
> 提示：provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

```js
// 父级组件提供 'foo'
var Provider = {
  provide: {
    foo: "bar"
  }
  // ...
};

// 子组件注入 'foo'
var Child = {
  inject: ["foo"],
  created() {
    console.log(this.foo); // => "bar"
  }
  // ...
};
```

参考：provide&inject.html

## 选项/其他

### name

类型：string
限制：只有作为组件选项时起作用
允许组件模板递归地调用自身。注意，组件在全局用 Vue.component() 注册时，全局 ID 自动作为组件的 name。

指定 name 选项的另一个好处是便于调试。有名字的组件有更友好的警告信息。另外，当在有 vue-devtools，未命名组件将显示成 \<AnonymousComponent>，这很没有语义。通过提供 name 选项，可以获得更有语义信息的组件树

### delimiters

类型： Array\<string>
描述：改变纯文本插入的分隔符 默认是["{{", "}}"]
示例

```js
new Vue({
  delimiters: ["${", "}"]
});
```

### functional

类型：boolean
描述：使组件无状态（没有 data）和无实例（没有 this 上下文），他们用一个简单的 render 函数返回虚拟节点使他们更容易渲染

示例：

```js
new Vue({
  functional: true //此时为true就不能用data以及this了
  props:[
    'aaa'
  ],
  render:function(creatElement,context){//为了弥补缺少的this，提供第二个参数context上下文
    //...
    console.log(context.props.aaa)
  }
});
```

### model

类型： {prop?:string,event?:string}
描述:允许一个自定义组件在使用 v-model 时定制 prop 和 event。默认情况下，一个组件上的 v-model 会把 value 用作 prop 且把 input 用作 event，但是一些输入类型比如单选框和复选框按钮可能想使用 value prop 来达到不同的目的。使用 model 选项可以回避这些情况产生的冲突。

示例：

```js
Vue.component("my-checkbox", {
  model: {
    prop: "checked",
    event: "change"
  },
  props: {
    // this allows using the `value` prop for a different purpose
    value: String,
    // use `checked` as the prop which take the place of `value`
    checked: {
      type: Number,
      default: 0
    }
  }
  // ...
});
```

### inheriAttrs

类型：boolean
