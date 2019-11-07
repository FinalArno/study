## Object.defineProperty()语法
>Object.defineProperty()的作用就是直接在一个对象上定义一个新属性，或者修改一个已经存在的属性

```
Object.defineProperty(obj, prop, desc)
```
1. obj 需要定义属性的当前对象
2. prop 当前需要定义的属性名
3. desc 属性描述符

### desc有如下字段
1. value:属性的值 默认 undefined
2. get 默认 undefined
3. set 默认 undefined
4. writable:当前属性是否可以修改 默认 false
5. configurable:当前属性是否可以配置，如果为false则不能删除，不能配置其属性 默认 false
6. enumerable 当前属性是否能在for in 中遍历出来，是否能用Object.keys()获取 默认 false
   
>结合writable: false 和 configurable: false 就可以创建一个真正的常量属性（不可修改，不可重新定义或者删除）

### Object.preventExtensions(...) 禁止扩张
保留当前属性并且禁止新增属性

### Object.seal() 密封
相当于使用了Object.preventExtensions(...)，并且为每个属性加上了configurable:false
密封之后不能新增属性，也不能重新配置和删除现有的属性，但是可以修改属性的值

### Object.freeze()冻结对象

相当于使用了Object.seal()，并且为每个属性加上了writable:false，这样不能新增属性，也不能重新配置和删除现有的属性，更不能修改属性的值了，
>但是如果这个对象中的值是其他的对象，那么就无法冻结其他对象，如果想要把其他对象也冻结，那么就需要遍历这个对象的所有引用对象，
给他们也加上Object.freeze()

## 属性赋值,通过obj.xxx = 'xxx'形式
1. 如果在原型链上存在一个名为P的只读属性（只读的数据属性或者没有setter的访问器属性），则拒绝
2. 如果在原型链上存在一个名为P的且拥有setter的访问器属性，则调用这个setter
3. 如果没有名为P的自身属性，则如果这个对象是可扩展的，就创建一个新属性，否则，如果这个对象是不可扩展的，则拒绝
4. 如果已经存在一个可写的名为P的自身属性，则调用Object.defineProperty(),该操作只会更改P属性的值，其他的特性（比如可枚举性）都不会改变

## Object.create() 用于继承
```
var person={speak:'person'}
var xiaoMing=Object.create(person);
xiaoMing.speak;//person
xiaoMing.speak='xiaoMing';
person.speak;//person
xiaoMing.speak;//xiaoMing
```