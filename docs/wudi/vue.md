# Vue面试题

## 基本原理

### 1.什么是MVVM,MVC？

**（1）MVC**

MVC 通过分离 Model、View 和 Controller 的方式来组织代码结构。其中 View 负责页面的显示逻辑，Model 负责存储页面的业务数据，以及对相应数据的操作。并且 View 和 Model 应用了观察者模式，当 Model 层发生改变的时候它会通知有关 View 层更新页面。Controller 层是 View 层和 Model 层的纽带，它主要负责用户与应用的响应操作，当用户与页面产生交互的时候，Controller 中的事件触发器就开始工作了，通过调用 Model 层，来完成对 Model 的修改，然后 Model 层再去通知 View 层更新。 ![image.png](https://s2.loli.net/2022/07/09/7HBMRZF1dLacCGz.webp)

（2）MVVM

MVVM 分为 Model、View、ViewModel：

- Model代表数据模型，数据和业务逻辑都在Model层中定义；
- View代表UI视图，负责数据的展示；
- ViewModel负责监听Model中数据的改变并且控制视图的更新，处理用户交互操作；

Model和View并无直接关联，而是通过ViewModel来进行联系的，Model和ViewModel之间有着双向数据绑定的联系。因此当Model中的数据改变时会触发View层的刷新，View中由于用户交互操作而改变的数据也会在Model中同步。

这种模式实现了 Model和View的数据自动同步，因此开发者只需要专注于数据的维护操作即可，而不需要自己操作DOM。 ![image.png](https://s2.loli.net/2022/07/09/wnbENSThUrFKjol.webp)

### 42. **MVVM**的优缺点?

优点:

- 分离视图（View）和模型（Model），降低代码耦合，提⾼视图或者逻辑的重⽤性: ⽐如视图（View）可以独⽴于Model变化和修改，⼀个ViewModel可以绑定不同的"View"上，当View变化的时候Model不可以不变，当Model变化的时候View也可以不变。你可以把⼀些视图逻辑放在⼀个ViewModel⾥⾯，让很多view重⽤这段视图逻辑
- 提⾼可测试性: ViewModel的存在可以帮助开发者更好地编写测试代码
- ⾃动更新dom: 利⽤双向绑定,数据更新后视图⾃动更新,让开发者从繁琐的⼿动dom中解放

缺点:

- Bug很难被调试: 因为使⽤双向绑定的模式，当你看到界⾯异常了，有可能是你View的代码有Bug，也可能是Model的代码有问题。数据绑定使得⼀个位置的Bug被快速传递到别的位置，要定位原始出问题的地⽅就变得不那么容易了。另外，数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的
- ⼀个⼤的模块中model也会很⼤，虽然使⽤⽅便了也很容易保证了数据的⼀致性，当时⻓期持有，不释放内存就造成了花费更多的内存
- 对于⼤型的图形应⽤程序，视图状态较多，ViewModel的构建和维护的成本都会⽐较⾼。



### 2.数据代理和劫持是怎么实现的？

当一个Vue实例创建时，Vue会遍历data中的属性，用 Object.defineProperty（vue3.0使用proxy ）将它们转为 getter/setter，并且在内部追踪相关依赖，在属性被访问和修改时通知变化。 每个组件实例都有相应的 watcher 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新。 ![0_tB3MJCzh_cB6i3mS-1.png](https://s2.loli.net/2022/07/09/Rc5GoW4MeblKy6i.webp)

### 3.双向绑定是怎么实现的？ 如何通过发布订阅模式实现数据的双向绑定？

Vue.js 是采用**数据劫持**结合**发布者-订阅者模式**的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。主要分为以下几个步骤：

1. 需要observe的数据对象进行递归遍历，包括子属性对象的属性，都加上setter和getter这样的话，给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化
2. compile解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图
3. Watcher订阅者是Observer和Compile之间通信的桥梁，主要做的事情是: ①在自身实例化时往属性订阅器(dep)里面添加自己 ②自身必须有一个update()方法 ③待属性变动dep.notice()通知时，能调用自身的update()方法，并触发Compile中绑定的回调，则功成身退。
4. MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。

![img](https://s2.loli.net/2022/07/09/eckUoLTVJDsCFOh.webp)

![img](https://s2.loli.net/2022/07/09/8lXf3netYqKsMAw.webp)



[双向绑定原理](https://juejin.cn/post/7065967379095748638#heading-9)

vue接收一个模板和data参数。

1，首先将data中的数据进行递归遍历，对每个属性执行Object.defineProperty，定义get和set函数。**并为每个属性添加一个dep数组**。当get执行时，会为调用的dom节点创建一个watcher存放在该数组中。当set执行时，重新赋值，并调用dep数组的notify方法，通知所有使用了该属性watcher，并更新对应dom的内容。

2，将模板加载到内存中，递归模板中的元素，检测到元素有v-开头的命令或者双大括号的指令，就会从data中取对应的值去修改模板内容，这个时候就将该dom元素添加到了该属性的dep数组中。这就实现了数据驱动视图。在处理v-model指令的时候，为该dom添加input事件（或change），输入时就去修改对应的属性的值，实现了页面驱动数据。

3，将模板与数据进行绑定后，将模板添加到真实dom树中

#### 收集依赖具体过程

-   Dep：`用于收集某个data属性依赖的dom节点集合，并提供更新方法`
-   Watcher：`每个dom节点的包裹对象`
    -   attr：该dom使用的data属性
    -   cb：修改该dom内容的回调函数，在对象创建的时候会接收

-   为data的每个属性添加一个dep数组，用来收集依赖的dom节点。
-   因为vue实例初始化的时候会解析模板，会触发data数据的getter，所以在此收集dom

-   在CompilerUtil类解析v-model，{{}}等命令时，会触发getter。
-   我们在触发之前创建Wather对象，该对象在初始化的时候调用getOldValue，首先为Dep添加一个静态属性target，值为该dom节点。
-   再调用CompilerUtil.getValue，获取该data的当前值，此时就以及触发了getter。然后我们在getter函数里面获取该静态变量Dep.target，并添加到对应的依赖数组dep中了，就完成了一次收集。
-   因为每次触发getter之前都对该静态变量赋值，所以不存在收集错依赖的情况。

#### 实现视图驱动数据

监听输入框的input、change事件。修改CompilerUtil的model方法

```js
model: function (node, value, vm) {
    new Watcher(vm, value, (newValue, oldValue)=>{
        node.value = newValue;
    });
    let val = this.getValue(vm, value);
    node.value = val;
	// 看这里
    node.addEventListener('input', (e)=>{
        let newValue = e.target.value;
        this.setValue(vm, value, newValue);
    })
},
```



#### 如何将watcher放在dep数组中？

在解析模板的时候，会根据v-指令获取对应data属性值，这个时候就会调用属性的get方法，我们先创建Watcher实例，并在其内部获取该属性值，作为旧值存放在watcher内部，我们在获取该值之前，在Watcher原型对象上添加属性Watcher.target = this;然后取值，将讲Watcher.target = null；这样get在被调用的时候就可以根据Watcher.target获取到watcher实例对象。

#### methods的原理

创建vue实例的时候，接收methods参数

在解析模板的时候遇到v-on的指令。会对**该dom元素添加对应事件的监听**，并使用call方法将vue绑定为该方法的this：`vm.$methods[value].call(vm, e);`

#### computed的原理

创建vue实例的时候，接收computed参数

初始化vue实例的时候，为computed的key进行Object.defineProperty处理，并添加get属性。

#### 更新时候发生了什么

属性set方法被触发 执行dep.notify()

通知所有使用了该属性watcher，执行watcher的update()方法  执行传过来的callback

并更新对应dom的内容



![img](https://s2.loli.net/2022/07/09/Rx1dtBSnWhVTcU9.webp)

[代码实现](https://juejin.cn/post/6844903903822086151)

### 4.v-model原理是什么? 语法糖实际是什么？

在自己封装组件的时在Vue中，我们可以使用`v-bind`实现单向的数据绑定，也就是通过**父组件向子组件传入数据** ，但是反过来，**子组件不可以修改父组件传递过来的数据** ，这也就是所谓的单向数据绑定。

而`v-model`就实现了双向数据绑定，实际上它就是通过Vue提供的事件机制。即在子组件通过`$emit()`触发一个事件，在父组件使用`v-on`来监听对应的事件并修改相应的数据时候，特别是输入框，下拉选择框等交互组件的时候，一般绑定值的时候，采用的是 `v-model`，使用 `v-model` 的主要好处是无需记特定的 `prop` 字段名，即可绑定到组件中的值，降低组件的使用成本。

毕竟，一个好的公共组件，首先是 `API` 的设计应该让人容易理解，并且使用方便。

其次，应该尽量将**重复的逻辑处理**放在子组件中，这样子才会让组件的封装更有意义。

当然，通过本文的学习，即使不是交互组件，**任何组件都可以**通过这种方式来实现 `v-model`。

`v-model` 实际上就是 `$emit('input')` 以及 `props:value` 的组合语法糖，只要组件中满足这两个条件，就可以在组件中使用 `v-model`。

**虽然在有些交互组件中有些许不同**，例如：

`checkbox` 和 `radio` 使用 `props:checked` 属性和 `$emit('change')` 事件。

`select` 使用 `props:value` 属性和 `$emit('change')` 事件。

但是，除了上面列举的这些，别的都是 `$emit('input')` 以及 `props:value` 。



**（1）作用在表单元素上** 动态绑定了 input 的 value 指向了 messgae 变量，并且在触发 input 事件的时候去动态把 message设置为目标值：

```javascript
<input v-model="sth" />
//  等同于
<input 
    v-bind:value="message" 
    v-on:input="message=$event.target.value"
>
//$event 指代当前触发的事件对象;
//$event.target 指代当前触发的事件对象的dom;
//$event.target.value 就是当前dom的value值;
//在@input方法中，value => sth;
//在:value中,sth => value;
```

**（2）作用在组件上** 在自定义组件中，v-model 默认会利用名为 value 的 prop和名为 input 的事件

**本质是一个父子组件通信的语法糖，通过prop和$.emit实现。** 因此父组件 v-model 语法糖本质上可以修改为：

```javascript
<child :value="message"  @input="function(e){message = e}"></child>
```

在组件的实现中，可以通过 v-model属性来配置子组件接收的prop名称，以及派发的事件名称。 例子：

```javascript
// 父组件
<aa-input v-model="aa"></aa-input>
// 等价于
<aa-input v-bind:value="aa" v-on:input="aa=$event.target.value"></aa-input>

// 子组件：
<input v-bind:value="aa" v-on:input="onmessage"></aa-input>

props:{value:aa,}
methods:{
    onmessage(e){
        $emit('input',e.target.value)
    }
}
```

默认情况下，一个组件上的v-model 会把 value 用作 prop且把 input 用作 event。但是一些输入类型比如单选框和复选框按钮可能想使用 value prop 来达到不同的目的。使用 model 选项可以回避这些情况产生的冲突。js 监听input 输入框输入数据改变，用oninput，数据改变以后就会立刻出发这个事件。通过input事件把数据$emit 出去，在父组件接受。父组件设置v-model的值为input `$emit`过来的值。

### 4.Object.defineProperty和Proxy特点对比分析？

#### Object.defineProperty()

作用：在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。

##### 1.基本使用

语法：`Object.defineProperty(obj, prop, descriptor)`

参数：

1.  要添加属性的对象
2.  要定义或修改的属性的名称或 [`Symbol`]
3.  要定义或修改的属性描述符

看一个简单的例子

```js
let person = {}
let personName = 'lihua'

//在person对象上添加属性namep,值为personName
Object.defineProperty(person, 'namep', {
    //但是默认是不可枚举的(for in打印打印不出来)，可：enumerable: true
    //默认不可以修改，可：wirtable：true
    //默认不可以删除，可：configurable：true
    get: function () {
        console.log('触发了get方法')
        return personName
    },
    set: function (val) {
        console.log('触发了set方法')
        personName = val
    }
})

//当读取person对象的namp属性时，触发get方法
console.log(person.namep)

//当修改personName时，重新访问person.namep发现修改成功
personName = 'liming'
console.log(person.namep)

// 对person.namep进行修改，触发set方法
person.namep = 'huahua'
console.log(person.namep)
```

通过这种方法，我们成功监听了person上的name属性的变化。

##### 2.监听对象上的多个属性

一个错误的版本

```js
Object.keys(person).forEach(function (key) {
    Object.defineProperty(person, key, {
        enumerable: true,
        configurable: true,
        // 默认会传入this
        get() {
            return person[key]
        },
        set(val) {
            console.log(`对person中的${key}属性进行了修改`)
            person[key] = val
            // 修改之后可以执行渲染操作
        }
    })
})
console.log(person.age)
```

栈溢出问题：我们在访问person身上的属性时，就会触发get方法，返回person[key]，但是访问person[key]也会触发get方法，导致递归调用，最终栈溢出

我们需要设置一个中转Obsever，来让get中return的值并不是直接访问obj[key]

```js
let person = {
    name: '',
    age: 0
}
// 实现一个响应式函数
function defineProperty(obj, key, val) {
    Object.defineProperty(obj, key, {
        get() {
            console.log(`访问了${key}属性`)
            return val
        },
        set(newVal) {
            console.log(`${key}属性被修改为${newVal}了`)
            val = newVal
        }
    })
}
// 实现一个遍历函数Observer
function Observer(obj) {
    Object.keys(obj).forEach((key) => {
        defineProperty(obj, key, obj[key])
    })
}
Observer(person)
console.log(person.age)
person.age = 18
console.log(person.age)
```

##### 3.深度监听一个对象

只要把对象传入其中，就可以实现对这个对象的属性监视，即使该对象的属性也是一个对象。
我们在defineProperty()函数中，添加一个递归的情况：

```js
function defineProperty(obj, key, val) {
    //如果某对象的属性也是一个对象，递归进入该对象，进行监听
    if(typeof val === 'object'){
    observer(val)
    }
    Object.defineProperty(obj, key, {
        get() {
            console.log(`访问了${key}属性`)
            return val
        },
        set(newVal) {
            console.log(`${key}属性被修改为${newVal}了`)
            val = newVal
        }
    })
}
```

##### 4.监听数组

对象的属性是一个数组  如何实现监听

```js
let arr = [1, 2, 3]
let obj = {}
//把arr作为obj的属性监听
Object.defineProperty(obj, 'arr', {
    get() {
        console.log('get arr')
        return arr
    },
    set(newVal) {
        console.log('set', newVal)
        arr = newVal
    }
})
console.log(obj.arr)//输出get arr [1,2,3]  正常
obj.arr = [1, 2, 3, 4] //输出set [1,2,3,4] 正常
obj.arr.push(3) //输出get arr 不正常，监听不到push
```

通过`push`方法给数组增加的元素，set方法是监听不到的

通过索引访问或者修改数组中已经存在的元素，是可以出发get和set的，但是对于通过push、unshift增加的元素，会增加一个索引，这种情况需要手动初始化，新增加的元素才能被监听到。另外， 通过 pop 或 shift 删除元素，会删除并更新索引，也会触发 setter 和 getter 方法

通过重写Array原型上的方法解决了这个问题

#### Proxy

当我们要给对象新增加一个属性时，也需要手动去监听这个新增属性

使用vue给 data 中的数组或对象新增属性时，需要使用 vm.$set 才能保证新增的属性也是响应式的

##### 1.基本使用

语法：`const p = new Proxy(target, handler)` 参数:

1.  target:要使用 `Proxy` 包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）
2.  handler:一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理 `p` 的行为。

通过Proxy，我们可以对`设置代理的对象`上的一些操作进行拦截，外界对这个对象的各种操作，都要先通过这层拦截。（和defineProperty差不多）

例子

```js
//定义一个需要代理的对象
let person = {
    age: 0,
    school: 'upc'
}
//定义handler对象
let hander = {
    get(obj, key) {
        // 如果对象里有这个属性，就返回属性值，如果没有，就返回默认值66
        return key in obj ? obj[key] : 66
    },
    set(obj, key, val) {
        obj[key] = val
        return true
    }
}
//把handler对象传入Proxy
let proxyObj = new Proxy(person, hander)

// 测试get能否拦截成功
console.log(proxyObj.age)//输出0
console.log(proxyObj.school)//输出西电
console.log(proxyObj.name)//输出默认值66

// 测试set能否拦截成功
proxyObj.age = 18
console.log(proxyObj.age)//输出18 修改成功
```

Proxy代理的是整个对象，而不是对象的某个特定属性，不需要我们通过遍历来逐个进行数据绑定。

>   值得注意的是:之前我们在使用Object.defineProperty()给对象添加一个属性之后，我们对对象属性的读写操作仍然在对象本身。
>   但是一旦使用Proxy，如果想要读写操作生效，我们就要对Proxy的实例对象`proxyObj`进行操作

##### 2.解决Object.defineProperty中遇到的问题

使用Object.defineProperty的时候，我们遇到的问题有：

1.一次只能对一个属性进行监听，需要遍历来对所有属性监听。这个我们在上面已经解决了。

2.在遇到一个对象的属性还是一个对象的情况下，需要递归监听。
3.对于对象的新增属性，需要手动监听
4.对于数组通过push、unshift方法增加的元素，也无法监听



### 5.v-if和v-show特点对比分析

- **手段**：v-if是动态的向DOM树内添加或者删除DOM元素；v-show是通过设置DOM元素的display样式属性控制显隐；
- **编译过程**：v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；v-show只是简单的基于css切换；
- **编译条件**：v-if是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译; v-show是在任何条件下，无论首次条件是否为真，都被编译，然后被缓存，而且DOM元素保留；
- **性能消耗**：v-if有更高的切换消耗；v-show有更高的初始渲染消耗；
- **使用场景**：v-if适合运营条件不大可能改变；v-show适合频繁切换。

### 11. v-if、v-show、v-html 的原理

- v-if会调用addIfCondition方法，生成vnode的时候会忽略对应节点，render的时候就不会渲染；
- v-show会生成vnode，render的时候也会渲染成真实节点，只是在render过程中会在节点的属性中修改show属性值，也就是常说的display；
- v-html会先移除节点下的所有节点，调用html方法，通过addProp添加innerHTML属性，归根结底还是设置innerHTML为v-html的值。

### 6.computed和method、computed和watch  对比分析

#### Computed 和 Methods 的区别

可以将同一函数定义为一个 method 或者一个计算属性。对于最终的结果，两种方式是相同的

**不同点：**

- computed: 计算属性是基于它们的依赖进行缓存的，只有在它的相关依赖发生改变时才会重新求值；
- method 调用总会执行该函数。

#### Computed 和 Watch 的区别

[源码学习](https://juejin.cn/post/6974293549135167495)

watch用法

```js
<body>
    <div id="app">
        姓： <input type="text" v-model=firstName> 名：
        <input type="text" v-model=lastName> 姓名：
        <span>{{fullname}}</span>
    </div>
</body>
<script type="text/javascript">
    var app = new Vue({
        el: "#app",
        data: {
            firstName: 'z',
            lastName: 's',
            fullname: 'zs'
        },
        watch: {
            firstName(newval) {
​
                this.fullname = newval + this.lastName
            },
            lastName(newval) {
                this.fullname = this.firstName + newval
            }
​
        }
    })
​
</script>
```

computed用法

```js
<body>
    <div id="app">
        姓： <input type="text" v-model=firstName> 名：
        <input type="text" v-model=lastName> 姓名：
        <span>{{fullname}}</span>
    </div>
</body>
<script type="text/javascript">
    var app = new Vue({
        el: "#app",
        data: {
            firstName: 'z',
            lastName: 's'
        },
        computed: {
            fullname() {
                return this.firstName + this.lastName
            }
        }
    })
​
</script>
```



**对于Computed：**

- 它支持缓存，只有依赖的数据发生了变化，才会重新计算
- 不支持异步，当Computed中有异步操作时，无法监听数据的变化
- computed的值会默认走缓存，计算属性是基于它们的响应式依赖进行缓存的，也就是基于data声明过，或者父组件传递过来的props中的数据进行计算的。
- 如果一个属性是由其他属性计算而来的，这个属性依赖其他的属性，一般会使用computed
- 如果computed属性的属性值是函数，那么默认使用get方法，函数的返回值就是属性的属性值；在computed中，属性有一个get方法和一个set方法，当数据发生变化时，会调用set方法。

**对于Watch：**

- 它不支持缓存，数据变化时，它就会触发相应的操作
- 支持异步监听
- 监听的函数接收两个参数，第一个参数是最新的值，第二个是变化之前的值
- 当一个属性发生变化时，就需要执行相应的操作
- 监听数据必须是data中声明的或者父组件传递过来的props中的数据，当发生变化时，会触发其他操作，函数有两个的参数：
  - immediate：组件加载立即触发回调函数
  - deep：深度监听，发现数据内部的变化，在复杂数据类型中使用，例如数组中的对象发生变化。需要注意的是，deep无法监听到数组和对象内部的变化。

当想要执行异步或者昂贵的操作以响应不断的变化时，就需要使用watch。

**总结：**

- computed 计算属性 : 依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed 的值。
- watch 侦听器 : 更多的是**观察**的作用，**无缓存性**，类似于某些数据的监听回调，每当监听的数据变化时都会执行回调进行后续操作。

**运用场景：**

- 当需要进行数值计算,并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时都要重新计算。
- 当需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许执行异步操作 ( 访问一个 API )，限制执行该操作的频率，并在得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

#### 源码实现

##### Watcher的种类

`name`变量被三处地方所依赖，分别是`html里，computed里，watch里`。只要`name`一改变，html里就会重新渲染，computed里就会重新计算，watch里就会重新执行。那么是谁去通知这三个地方`name`修改了呢？那就是`Watcher`了

```js
<div>{{name}}</div>

data() {
        return {
            name: '林三心'
        }
    },
    computed: {
        info () {
            return this.name
        }
    },
    watch: {
        name(newVal) {
            console.log(newVal)
        }
    }
```

-   `渲染Watcher`：变量修改时，负责通知HTML里的重新渲染
-   `computed Watcher`：变量修改时，负责通知computed里依赖此变量的computed属性变量的修改
-   `user Watcher`：变量修改时，负责通知watch属性里所对应的变量函数的执行

##### computed的本质 —— computed watch

我们知道new Vue()的时候会调用_init方法，该方法会初始化生命周期，初始化事件，初始化render，初始化data，computed，methods，wacther等等。[Vue.js源码角度：剖析模版和数据渲染成最终的DOM的过程](https://juejin.cn/post/6844903664998416392)。今天主要来看以下初始化watch(initWatch)的实现，我加上了注释方便理解，定义在src/core/instance/state.js中：

```JS
// 用于传入Watcher实例的一个对象，即computed watcher
const computedWatcherOptions = { computed: true }

function initComputed (vm: Component, computed: Object) {
  // $flow-disable-line
  // 声明一个watchers且同时挂载到vm实例上
  const watchers = vm._computedWatchers = Object.create(null)
  // 在SSR模式下computed属性只能触发getter方法
  const isSSR = isServerRendering()

  // 遍历传入的computed方法
  for (const key in computed) {
    // 取出computed对象中的每个方法并赋值给userDef
    const userDef = computed[key]
    const getter = typeof userDef === 'function' ? userDef : userDef.get
    if (process.env.NODE_ENV !== 'production' && getter == null) {
      warn(
        `Getter is missing for computed property "${key}".`,
        vm
      )
    }

    // 如果不是SSR服务端渲染，则创建一个watcher实例
    if (!isSSR) {
      // create internal watcher for the computed property.
      watchers[key] = new Watcher(
        vm,
        getter || noop,
        noop,
        computedWatcherOptions
      )
    }

    // component-defined computed properties are already defined on the
    // component prototype. We only need to define computed properties defined
    // at instantiation here.
    if (!(key in vm)) {
      // 如果computed中的key没有设置到vm中，通过defineComputed函数挂载上去 
      defineComputed(vm, key, userDef)
    } else if (process.env.NODE_ENV !== 'production') {
      // 如果data和props有和computed中的key重名的，会产生warning
      if (key in vm.$data) {
        warn(`The computed property "${key}" is already defined in data.`, vm)
      } else if (vm.$options.props && key in vm.$options.props) {
        warn(`The computed property "${key}" is already defined as a prop.`, vm)
      }
    }
  }
}
```

通过源码我们可以发现它先声明了一个名为watchers的空对象，同时在vm上也挂载了这个空对象。之后**遍历计算属性，并把每个属性的方法赋给userDef**，如果userDef是function的话就赋给getter，接着判断是否是服务端渲染，如果不是的话就创建一个Watcher实例。

这里**新建的Watcher实例中我们传入了第四个参数computedWatcherOptions**。

const computedWatcherOptions = { computed: true }，这个对象是实现computed watcher的关键。这时，Watcher中的逻辑就有变化了：

```JS
    // 源码定义在src/core/observer/watcher.js中
    // options
    if (options) {
      this.deep = !!options.deep
      this.user = !!options.user
      this.computed = !!options.computed
      this.sync = !!options.sync
      this.before = options.before
    } else {
      this.deep = this.user = this.computed = this.sync = false
    }
    // 其他的代码......
    this.dirty = this.computed // for computed watchers
```

这里传入的**options**就是上边定义的computedWatcherOptions，当走initData方法的时候，options并不存在，但当走到**initComputed**的时候，computedWatcherOptions中的computed为true，注意上边的一行代码**this.dirty = this.computed**，将this.computed赋值给this.dirty。接着看下边的代码：

```JS
  evaluate () {
    if (this.dirty) {
      this.value = this.get()
      this.dirty = false
    }
    return this.value
  }
```

只有this.dirty为true的时候才能通过 this.get() 求值，然后把 this.dirty 设置为 false。在求值过程中，会执行 value = this.getter.call(vm, vm)，**这实际上就是执行了计算属性定义的 getter 函数**，否则直接返回value。

当对**计算属性依赖的数据做修改**的时候，会触发 setter 过程，通知所有订阅它变化的 watcher 更新，**执行 watcher.update() 方法**：

```JS
  /**
   * Subscriber interface.
   * Will be called when a dependency changes.
   */
  update () {
    /* istanbul ignore else */
    if (this.computed) {
      // A computed property watcher has two modes: lazy and activated.
      // It initializes as lazy by default, and only becomes activated when
      // it is depended on by at least one subscriber, which is typically
      // another computed property or a component's render function.
      if (this.dep.subs.length === 0) {
        // In lazy mode, we don't want to perform computations until necessary,
        // so we simply mark the watcher as dirty. The actual computation is
        // performed just-in-time in this.evaluate() when the computed property
        // is accessed.
        this.dirty = true
      } else {
        // In activated mode, we want to proactively perform the computation
        // but only notify our subscribers when the value has indeed changed.
        this.getAndInvoke(() => {
          this.dep.notify()
        })
      }
    } else if (this.sync) {
      this.run()
    } else {
      queueWatcher(this)
    }
  }
```

那么对于计算属性这样的 computed watcher，它实际上是有 2 种模式，lazy 和 active。如果 this.dep.subs.length === 0 成立，则说明没有人去订阅这个 computed watcher 的变化，就把 this.dirty = true，只有当下次再访问这个计算属性的时候才会重新求值。否则会执行getAndInvoke方法：

```JS
  getAndInvoke (cb: Function) {
    const value = this.get()
    if (
      value !== this.value ||
      // Deep watchers and watchers on Object/Arrays should fire even
      // when the value is the same, because the value may
      // have mutated.
      isObject(value) ||
      this.deep
    ) {
      // set new value
      const oldValue = this.value
      this.value = value
      this.dirty = false
      if (this.user) {
        try {
          cb.call(this.vm, value, oldValue)
        } catch (e) {
          handleError(e, this.vm, `callback for watcher "${this.expression}"`)
        }
      } else {
        cb.call(this.vm, value, oldValue)
      }
    }
  }
```

getAndInvoke 函数会重新计算，然后对比新旧值，在三种情况下(1.新旧值不相等的情况2.value是对象或数组的时候3.设置deep属性的时候)会执行回调函数，那么这里这个回调函数是 this.dep.notify()，在我们这个场景下就是触发了渲染 watcher 重新渲染。这就能解释官网中所说的**计算属性是基于它们的依赖进行缓存的**。

##### Computed源码总结

基于Watcher类，有一个lazy属性，可以进行缓存作用，如果lazy是true证明是计算属性，直接返回数据，不用继续求值，这就是缓存值的原理

![image-20220621085011422](https://s2.loli.net/2022/07/09/rQvj72xenwctDIC.png)

1.遍历计算属性，并把每个属性的方法赋给userDef

2.新建的Watcher实例中我们传入了第四个参数computedWatcherOptions

3.**initComputed**的时候，computedWatcherOptions中的computed为true

4.evaluate () 中 只有this.dirty为true的时候才能通过 this.get() 求值，然后把 this.dirty 设置为 false

5.求值过程中，会执行 value = this.getter.call(vm, vm)，**这实际上就是执行了计算属性定义的 getter 函数**，如果dirty为false直接返回value

6.**计算属性依赖的数据做修改**的时候，会触发 setter 过程，通知所有订阅它变化的 watcher 更新，**执行 watcher.update() 方法**

7.computed watcher是有 2 种模式，lazy 和 active。如果 this.dep.subs.length === 0 成立，则说明没有人去订阅这个 computed watcher 的变化，就把 this.dirty = true只有当下次再访问这个计算属性的时候才会重新求值

8.getAndInvoke 函数会重新计算，然后对比新旧值，在三种情况下(1.新旧值不相等的情况2.value是对象或数组的时候3.设置deep属性的时候)会执行回调函数

##### watch底层是如何工作的？

上边提到了在new Vue()的时候调用了_init方法完成了初始化。在这当中有调用了initWatch方法，定义在src/core/instance/state.js中：

```JS
function initWatch (vm: Component, watch: Object) {
  for (const key in watch) {
    const handler = watch[key]
    if (Array.isArray(handler)) {
      for (let i = 0; i < handler.length; i++) {
        createWatcher(vm, key, handler[i])
      }
    } else {
      createWatcher(vm, key, handler)
    }
  }
}
```

遍历watch对象，并将每个watch[key]赋值给handler，如果是数组则遍历createWatcher方法，否则直接调用createWatcher方法。接下来看一下createWatcher方法的定义：

```JS
function createWatcher (
  vm: Component,
  expOrFn: string | Function,
  handler: any,
  options?: Object
) {
  if (isPlainObject(handler)) {
    options = handler
    handler = handler.handler
  }
  if (typeof handler === 'string') {
    handler = vm[handler]
  }
  return vm.$watch(expOrFn, handler, options)
}
```

通过代码可以发现，createWatcher方法vm.?watch(keyOrFn, handler, options) 函数，调用了Vue.prototype.$watch方法，定义在src/core/instance/state.js中：

```JS
  Vue.prototype.$watch = function (
    expOrFn: string | Function,
    cb: any,
    options?: Object
  ): Function {
    const vm: Component = this
    if (isPlainObject(cb)) {
      return createWatcher(vm, expOrFn, cb, options)
    }
    options = options || {}
    options.user = true
    const watcher = new Watcher(vm, expOrFn, cb, options)
    if (options.immediate) {
      cb.call(vm, watcher.value)
    }
    return function unwatchFn () {
      watcher.teardown()
    }
  }
}
```

通过代码我们可以发现， watch 最终会调用Vue.prototype.watch 方法，

这个方法首先判断 cb 如果是一个对象，则调用 createWatcher 方法，这是因为*w**a**t**c**h*方法，这个方法首先判断*cb*如果是一个对象，则调用***createWatcher***方法，

接着执行 const watcher = new Watcher(vm, expOrFn, cb, options) 实例化了一个 watcher，这里需要注意一点这是一个 user watcher，因为 options.user = true。

通过实例化 watcher 的方式，一旦我们 watch 的数据发送变化，它最终会执行 watcher 的 run 方法，执行回调函数 cb，并且如果我们设置了 immediate 为 true，则直接会执行回调函数 cb。即设置immediate属性为true的时候，第一次watch绑定的时候就可以执行。

最后返回了一个 unwatchFn 方法，它会调用 teardown 方法去移除这个 watcher。

所以watcher是如何工作的？本质上也是基于 Watcher 实现的，它是一个 user watcher。前面提到了计算属性computed本质上是一个computed watcher

##### Watch源码总结

双向数据绑定有一个Watcher类，只是普通的watch实例化，有没有deep参数只需要加上判断，即可。

还有可以监听函数，将当前函数赋值给getter,监听的函数里面涉及到的状态都会被监听到，发生了变化就会触发watch。

还要新增一个取消观察函数的函数

watch中deep:true实现：当用户指定了 watch 中的deep属性为 true 时，如果当前监控的值是数组或者对象。 在watch类里面有get方法对deep，和复杂对象处理方法，**会对对象中的每一项进行求值**，此时会将当前 watcher 存入到对应属性的依赖中(将当前依赖放到 Dep.target上)，这样数组中对象发生变化时也会通知数据更新

_traverse()方法里面

不是数组也不是对象返回，冰冻对象返回，Vnode实例返回

对数组和对象进行递归判断

### 17. 对keep-alive的理解，它是如何实现的，具体缓存的是什么？

如果需要在组件切换的时候，保存一些组件的状态防止多次渲染，就可以使用 keep-alive 组件包裹需要保存的组件。

**（1）keep-alive**

keep-alive有以下三个属性：

- include 字符串或正则表达式，只有名称匹配的组件会被匹配；
- exclude 字符串或正则表达式，任何名称匹配的组件都不会被缓存；
- max 数字，最多可以缓存多少组件实例。

注意：keep-alive 包裹动态组件时，会缓存不活动的组件实例。

**主要流程**

1. 判断组件 name ，不在 include 或者在 exclude 中，直接返回 vnode，说明该组件不被缓存。
2. 获取组件实例 key ，如果有获取实例的 key，否则重新生成。
3. key生成规则，cid +"∶∶"+ tag ，仅靠cid是不够的，因为相同的构造函数可以注册为不同的本地组件。
4. 如果缓存对象内存在，则直接从缓存对象中获取组件实例给 vnode ，不存在则添加到缓存对象中。 5.最大缓存数量，当缓存组件数量超过 max 值时，清除 keys 数组内第一个组件。

**（2）keep-alive 的实现**

```javascript
const patternTypes: Array<Function> = [String, RegExp, Array] // 接收：字符串，正则，数组

export default {
  name: 'keep-alive',
  abstract: true, // 抽象组件，是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

  props: {
    include: patternTypes, // 匹配的组件，缓存
    exclude: patternTypes, // 不去匹配的组件，不缓存
    max: [String, Number], // 缓存组件的最大实例数量, 由于缓存的是组件实例（vnode），数量过多的时候，会占用过多的内存，可以用max指定上限
  },

  created() {
    // 用于初始化缓存虚拟DOM数组和vnode的key
    this.cache = Object.create(null)
    this.keys = []
  },

  destroyed() {
    // 销毁缓存cache的组件实例
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },

  mounted() {
    // prune 削减精简[v.]
    // 去监控include和exclude的改变，根据最新的include和exclude的内容，来实时削减缓存的组件的内容
    this.$watch('include', (val) => {
      pruneCache(this, (name) => matches(val, name))
    })
    this.$watch('exclude', (val) => {
      pruneCache(this, (name) => !matches(val, name))
    })
  },
}
复制代码
```

**render函数：**

1. 会在 keep-alive 组件内部去写自己的内容，所以可以去获取默认 slot 的内容，然后根据这个去获取组件
2. keep-alive 只对第一个组件有效，所以获取第一个子组件。
3. 和 keep-alive 搭配使用的一般有：动态组件 和router-view

```javascript
render () {
  //
  function getFirstComponentChild (children: ?Array<VNode>): ?VNode {
    if (Array.isArray(children)) {
  for (let i = 0; i < children.length; i++) {
    const c = children[i]
    if (isDef(c) && (isDef(c.componentOptions) || isAsyncPlaceholder(c))) {
      return c
    }
  }
  }
  }
  const slot = this.$slots.default // 获取默认插槽
  const vnode: VNode = getFirstComponentChild(slot)// 获取第一个子组件
  const componentOptions: ?VNodeComponentOptions = vnode && vnode.componentOptions // 组件参数
  if (componentOptions) { // 是否有组件参数
    // check pattern
    const name: ?string = getComponentName(componentOptions) // 获取组件名
    const { include, exclude } = this
    if (
      // not included
      (include && (!name || !matches(include, name))) ||
      // excluded
      (exclude && name && matches(exclude, name))
    ) {
      // 如果不匹配当前组件的名字和include以及exclude
      // 那么直接返回组件的实例
      return vnode
    }

    const { cache, keys } = this

    // 获取这个组件的key
    const key: ?string = vnode.key == null
      // same constructor may get registered as different local components
      // so cid alone is not enough (#3269)
      ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
      : vnode.key

    if (cache[key]) {
      // LRU缓存策略执行
      vnode.componentInstance = cache[key].componentInstance // 组件初次渲染的时候componentInstance为undefined

      // make current key freshest
      remove(keys, key)
      keys.push(key)
      // 根据LRU缓存策略执行，将key从原来的位置移除，然后将这个key值放到最后面
    } else {
      // 在缓存列表里面没有的话，则加入，同时判断当前加入之后，是否超过了max所设定的范围，如果是，则去除
      // 使用时间间隔最长的一个
      cache[key] = vnode
      keys.push(key)
      // prune oldest entry
      if (this.max && keys.length > parseInt(this.max)) {
        pruneCacheEntry(cache, keys[0], keys, this._vnode)
      }
    }
    // 将组件的keepAlive属性设置为true
    vnode.data.keepAlive = true // 作用：判断是否要执行组件的created、mounted生命周期函数
  }
  return vnode || (slot && slot[0])
}
复制代码
```

keep-alive 具体是通过 cache 数组缓存所有组件的 vnode 实例。当 cache 内原有组件被使用时会将该组件 key 从 keys 数组中删除，然后 push 到 keys数组最后，以便清除最不常用组件。

**实现步骤：**

1. 获取 keep-alive 下第一个子组件的实例对象，通过他去获取这个组件的组件名
2. 通过当前组件名去匹配原来 include 和 exclude，判断当前组件是否需要缓存，不需要缓存，直接返回当前组件的实例vNode
3. 需要缓存，判断他当前是否在缓存数组里面：

- 存在，则将他原来位置上的 key 给移除，同时将这个组件的 key 放到数组最后面（LRU）
- 不存在，将组件 key 放入数组，然后判断当前 key数组是否超过 max 所设置的范围，超过，那么削减未使用时间最长的一个组件的 key

1. 最后将这个组件的 keepAlive 设置为 true

**（3）keep-alive 本身的创建过程和 patch 过程**

缓存渲染的时候，会根据 vnode.componentInstance（首次渲染 vnode.componentInstance 为 undefined） 和 keepAlive 属性判断不会执行组件的 created、mounted 等钩子函数，而是对缓存的组件执行 patch 过程∶ 直接把缓存的 DOM 对象直接插入到目标元素中，完成了数据更新的情况下的渲染过程。

**首次渲染**

- 组件的首次渲染∶判断组件的 abstract 属性，才往父组件里面挂载 DOM

```javascript
// core/instance/lifecycle
function initLifecycle (vm: Component) {
  const options = vm.$options

  // locate first non-abstract parent
  let parent = options.parent
  if (parent && !options.abstract) { // 判断组件的abstract属性，才往父组件里面挂载DOM
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    parent.$children.push(vm)
  }

  vm.$parent = parent
  vm.$root = parent ? parent.$root : vm

  vm.$children = []
  vm.$refs = {}

  vm._watcher = null
  vm._inactive = null
  vm._directInactive = false
  vm._isMounted = false
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
}
复制代码
```

- 判断当前 keepAlive 和 componentInstance 是否存在来判断是否要执行组件 prepatch 还是执行创建 componentlnstance

```javascript
// core/vdom/create-component
init (vnode: VNodeWithData, hydrating: boolean): ?boolean {
    if (
      vnode.componentInstance &&
      !vnode.componentInstance._isDestroyed &&
      vnode.data.keepAlive
    ) { // componentInstance在初次是undefined!!!
      // kept-alive components, treat as a patch
      const mountedNode: any = vnode // work around flow
      componentVNodeHooks.prepatch(mountedNode, mountedNode) // prepatch函数执行的是组件更新的过程
    } else {
      const child = vnode.componentInstance = createComponentInstanceForVnode(
        vnode,
        activeInstance
      )
      child.$mount(hydrating ? vnode.elm : undefined, hydrating)
    }
  },
复制代码
```

prepatch 操作就不会在执行组件的 mounted 和 created 生命周期函数，而是直接将 DOM 插入

**（4）LRU （least recently used）缓存策略**

LRU 缓存策略∶ 从内存中找出最久未使用的数据并置换新的数据。 LRU（Least rencently used）算法根据数据的历史访问记录来进行淘汰数据，其核心思想是 **"如果数据最近被访问过，那么将来被访问的几率也更高"**。 最常见的实现是使用一个链表保存缓存数据，详细算法实现如下∶

- 新数据插入到链表头部
- 每当缓存命中（即缓存数据被访问），则将数据移到链表头部
- 链表满的时候，将链表尾部的数据丢弃。




### 7.nextTick有什么应用场景？原理是什么？

![12c574e8b85e729b0d9905959cc281ab.png](https://s2.loli.net/2022/07/09/RWciDJKTjVkaoGt.jpg)

**打印的结果是begin, 而不是我们设置的end。**这个结果足以说明Vue中DOM的更新并非同步

Vue异步执行DOM更新。只要观察到数据变化，Vue将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个watcher被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和DOM操作上非常重要。然后，在下一个的事件循环“tick”中，Vue刷新队列并执行实际 (已去重的) 工作

Vue 的 nextTick 其本质是对 JavaScript 执行原理 EventLoop 的一种应用。

nextTick 的核心是利用了如 Promise 、MutationObserver、setImmediate、setTimeout的原生 JavaScript 方法来模拟对应的微/宏任务的实现，本质是为了利用 JavaScript 的这些异步回调任务队列来实现 Vue 框架中自己的异步回调队列。

nextTick 不仅是 Vue 内部的异步队列的调用方法，同时也允许开发者在实际项目中使用这个方法来满足实际应用中对 DOM 更新数据时机的后续逻辑处理

nextTick 是典型的将底层 JavaScript 执行原理应用到具体案例中的示例，引入异步更新队列机制的原因∶

- 如果是同步更新，则多次对一个或多个属性赋值，会频繁触发 UI/DOM 的渲染，可以减少一些无用渲染
- 同时由于 VirtualDOM 的引入，每一次状态发生变化后，状态变化的信号会发送给组件，组件内部使用 VirtualDOM 进行计算得出需要更新的具体的 DOM 节点，然后对 DOM 进行更新操作，每次更新状态后的渲染过程需要更多的计算，而这种无用功也将浪费更多的性能，所以异步渲染变得更加至关重要

Vue采用了数据驱动视图的思想，但是在一些情况下，仍然需要操作DOM。有时候，可能遇到这样的情况，DOM1的数据发生了变化，而DOM2需要从DOM1中获取数据，那这时就会发现DOM2的视图并没有更新，这时就需要用到了`nextTick`了。

由于Vue的DOM操作是异步的，所以，在上面的情况中，就要将DOM2获取数据的操作写在`$nextTick`中。

```javascript
this.$nextTick(() => {    // 获取数据的操作...})
```

所以，在以下情况下，会用到nextTick：

- 在数据变化后执行的某个操作，而这个操作需要使用随数据变化而变化的DOM结构的时候，这个操作就需要方法在`nextTick()`的回调函数中。
- 在vue生命周期中，如果在created()钩子进行DOM操作，也一定要放在`nextTick()`的回调函数中。

因为在created()钩子函数中，页面的DOM还未渲染，这时候也没办法操作DOM，所以，此时如果想要操作DOM，必须将操作的代码放在`nextTick()`的回调函数中。

 vue 采用的**异步更新策略**，当监听到数据发生变化的时候不会立即去更新DOM，
而是开启一个任务队列，并缓存在同一事件循环中发生的所有数据变更;

nextTick 接收一个回调函数作为参数，并将这个回调函数延迟到DOM更新后才执行；
**使用场景**：想要操作 *基于最新数据的生成DOM* 时，就将这个操作放在 nextTick 的回调中

nextTick 提供了四种异步方法 Promise.then、MutationObserver、setImmediate、setTimeOut(fn,0)

## 三、nextTick 实现原理

将传入的回调函数包装成异步任务，异步任务又分微任务和宏任务，为了尽快执行所以优先选择微任务； `nextTick` 提供了四种异步方法 `Promise.then`、`MutationObserver`、`setImmediate`、`setTimeOut(fn,0)`

### 3.1 Vue.nextTick 内部逻辑

在执行 `initGlobalAPI(Vue)` 初始化 Vue 全局 API 中，这么定义 `Vue.nextTick` ：

```javascript
function initGlobalAPI(Vue) {
    //...
    Vue.nextTick = nextTick;
}
复制代码
```

可以看出是直接把 nextTick 函数赋值给 Vue.nextTick，就可以了，非常简单。

### 3.2 vm.$nextTick 内部逻辑

```javascript
Vue.prototype.$nextTick = function (fn) {
    return nextTick(fn, this)
};
复制代码
```

可以看出是`vm.$nextTick`内部也是调用 `nextTick` 函数。

### 3.3 源码解读

`nextTick` 的源码位于 `src/core/util/next-tick.js` `nextTick` 源码主要分为两块：

```javascript
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

//  上面三行与核心代码关系不大，了解即可
//  noop 表示一个无操作空函数，用作函数默认值，防止传入 undefined 导致报错
//  handleError 错误处理函数
//  isIE, isIOS, isNative 环境判断函数，
//  isNative 判断是否原生支持，如果通过第三方实现支持也会返回 false

export let isUsingMicroTask = false     // nextTick 最终是否以微任务执行

const callbacks = []     // 存放调用 nextTick 时传入的回调函数
let pending = false     // 标识当前是否有 nextTick 在执行，同一时间只能有一个执行


// 声明 nextTick 函数，接收一个回调函数和一个执行上下文作为参数
export function nextTick(cb?: Function, ctx?: Object) {
    let _resolve
    // 将传入的回调函数存放到数组中，后面会遍历执行其中的回调
    callbacks.push(() => {
        if (cb) {   // 对传入的回调进行 try catch 错误捕获
            try {
                cb.call(ctx)
            } catch (e) {
                handleError(e, ctx, 'nextTick')
            }
        } else if (_resolve) {
            _resolve(ctx)
        }
    })
    
    // 如果当前没有在 pending 的回调，就执行 timeFunc 函数选择当前环境优先支持的异步方法
    if (!pending) {
        pending = true
        timerFunc()
    }
    
    // 如果没有传入回调，并且当前环境支持 promise，就返回一个 promise
    if (!cb && typeof Promise !== 'undefined') {
        return new Promise(resolve => {
            _resolve = resolve
        })
    }
}

复制代码
```

可以看到在 `nextTick` 函数中把通过参数 cb 传入的函数，做一下包装然后 push 到 `callbacks` 数组中。

然后用变量 pending 来保证执行一个事件循环中只执行一次 timerFunc()。

最后执行 `if (!cb && typeof Promise !== 'undefined')`，判断参数 `cb`不存在且浏览器支持 Promise，则返回一个 Promise 类实例化对象。例如 `nextTick().then(() => {})`，当 `_resolve` 函数执行，就会执行 then 的逻辑中。

来看一下 `timerFunc` 函数的定义，先只看用 Promise 创建一个异步执行的 `timerFunc` 函数 。

```javascript
// 判断当前环境优先支持的异步方法，优先选择微任务
// 优先级：Promise---> MutationObserver---> setImmediate---> setTimeout
// setTimeOut 最小延迟也要4ms，而 setImmediate 会在主线程执行完后立刻执行
// setImmediate 在 IE10 和 node 中支持

// 多次调用 nextTick 时 ,timerFunc 只会执行一次

let timerFunc   
// 判断当前环境是否支持 promise
if (typeof Promise !== 'undefined' && isNative(Promise)) {  // 支持 promise
    const p = Promise.resolve()
    timerFunc = () => {
    // 用 promise.then 把 flushCallbacks 函数包裹成一个异步微任务
        p.then(flushCallbacks)
        if (isIOS) setTimeout(noop)
    }
    // 标记当前 nextTick 使用的微任务
    isUsingMicroTask = true
    
    
    // 如果不支持 promise，就判断是否支持 MutationObserver
    // 不是IE环境，并且原生支持 MutationObserver，那也是一个微任务
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
    isNative(MutationObserver) ||
    MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
    let counter = 1
    // new 一个 MutationObserver 类
    const observer = new MutationObserver(flushCallbacks) 
    // 创建一个文本节点
    const textNode = document.createTextNode(String(counter))   
    // 监听这个文本节点，当数据发生变化就执行 flushCallbacks 
    observer.observe(textNode, { characterData: true })
    timerFunc = () => {
        counter = (counter + 1) % 2
        textNode.data = String(counter)  // 数据更新
    }
    isUsingMicroTask = true    // 标记当前 nextTick 使用的微任务
    
    
    // 判断当前环境是否原生支持 setImmediate
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
    timerFunc = () => { setImmediate(flushCallbacks)  }
} else {

    // 以上三种都不支持就选择 setTimeout
    timerFunc = () => { setTimeout(flushCallbacks, 0) }
}

复制代码
```

其中 `isNative` 方法是如何定义，代码如下。

```javascript
function isNative(Ctor) {
    return typeof Ctor === 'function' && /native code/.test(Ctor.toString())
}
复制代码
```

在其中发现 `timerFunc` 函数就是用各种异步执行的方法调用 `flushCallbacks` 函数。

来看一下 `flushCallbacks` 函数

```javascript
// 如果多次调用 nextTick，会依次执行上面的方法，将 nextTick 的回调放在 callbacks 数组中
// 最后通过 flushCallbacks 函数遍历 callbacks 数组的拷贝并执行其中的回调
function flushCallbacks() {
    pending = false
    const copies = callbacks.slice(0)    // 拷贝一份
    callbacks.length = 0    // 清空 callbacks
    for (let i = 0; i < copies.length; i++) {    // 遍历执行传入的回调
        copies[i]()
    }
}

// 为什么要拷贝一份 callbacks

// callbacks.slice(0) 将 callbacks 拷贝出来一份，
// 是因为考虑到 nextTick 回调中可能还会调用 nextTick 的情况,
// 如果 nextTick 回调中又调用了一次 nextTick，则又会向 callbacks 中添加回调，
// nextTick 回调中的 nextTick 应该放在下一轮执行，
// 如果不将 callbacks 复制一份就可能一直循环
复制代码
```

执行 `pending = false` 使下个事件循环中能 `nextTick` 函数中调用 `timerFunc` 函数。 执行 `var copies = callbacks.slice(0);callbacks.length = 0`; 把要异步执行的函数集合 `callbacks`克隆到常量 `copies`，然后把 `callbacks` 清空。 然后遍历 `copies` 执行每一项函数。回到 `nextTick` 中是把通过参数 `cb` 传入的函数包装后 push 到 `callbacks` 集合中。来看一下怎么包装的。

```javascript
function() {
    if (cb) {
        try {
            cb.call(ctx);
        } catch (e) {
            handleError(e, ctx, 'nextTick');
        }
    } else if (_resolve) {
        _resolve(ctx);
    }
}
复制代码
```

逻辑很简单。若参数 cb 有值。在 try 语句中执行 `cb.call(ctx)` ，参数 ctx 是传入函数的参数。 如果执行失败执行 `handleError(e, ctx, 'nextTick')`。

若参数 cb 没有值。执行 `_resolve(ctx)`，因为在 `nextTick` 函数中如何参数 cb 没有值，会返回一个 Promise 类实例化对象，那么执行 `_resolve(ctx)`，就会执行 then 的逻辑中。

到这里 `nextTick` 函数的主线逻辑就很清楚了。定义一个变量 `callbacks`，把通过参数 cb 传入的函数用一个函数包装一下，在这个中会执行传入的函数，及处理执行失败和参数 cb 不存在的场景，然后 添加到 callbacks。

调用 `timerFunc` 函数，在其中遍历 `callbacks` 执行每个函数，因为 `timerFunc` 是一个异步执行的函数，且定义一个变量 `pending` 来保证一个事件循环中只调用一次 `timerFunc` 函数。这样就实现了 `nextTick` 函数异步执行传入的函数的作用了。

那么其中的关键还是怎么定义 `timerFunc` 函数。因为在各浏览器下对创建异步执行函数的方法各不相同，要做兼容处理，下面来介绍一下各种方法。

### 3.4 为什么优先使用微任务：

按照上面事件循环的执行顺序，执行下一次宏任务之前会执行一次 UI 渲染，等待时长比微任务要多很多。所以在能使用微任务的时候优先使用微任务，不能使用微任务的时候才使用宏任务，优雅降级。*

### 8.data为什么是一个函数而不是一个对象？

JavaScript中的对象是引用类型的数据，当多个实例引用同一个对象时，只要一个实例对这个对象进行操作，其他实例中的数据也会发生变化。

而在Vue中，更多的是想要复用组件，那就需要每个组件都有自己的数据，这样组件之间才不会相互干扰。

所以组件的数据不能写成对象的形式，而是要写成函数的形式。数据以函数返回值的形式定义，这样当每次复用组件的时候，就会返回一个新的data，也就是说每个组件都有自己的私有数据空间，它们各自维护自己的数据，不会干扰其他组件的正常运行。




## SPA的原理

js会感知到url的变化，通过这一点可以用js监听url中hash值的变化,通过onhashchange事件,由于哈希值的变换并不会引发页面的刷新和跳转,当监听到hash变化,就可以动态的切换组件,就可以实现无刷新切换页面技术

### 21. Vue 单页应用与多页应用的区别

**概念：**

- SPA单页面应用（SinglePage Web Application），指只有一个主页面的应用，一开始只需要加载一次js、css等相关资源。所有内容都包含在主页面，对每一个功能模块组件化。单页应用跳转，就是切换相关组件，仅仅刷新局部资源。
- MPA多页面应用 （MultiPage Application），指有多个独立页面的应用，每个页面必须重复加载js、css等相关资源。多页应用跳转，需要整页资源刷新。

**区别：** ![775316ebb4c727f7c8771cc2c06e06dd.jpg](https://s2.loli.net/2022/07/20/N2AdwIrjF1xD5Z4.webp)



### 9.单页面Web应用的优缺点

单页面应用程序将所有的活动局限于一个Web页面中，在该Web页面初始化时加载相应的HTML、JavaScript 和 CSS。一旦页面加载完成，单页面应用不会因为用户的操作而进行页面的重新加载或跳转。取而代之的是利用 JavaScript 动态的变换HTML的内容，从而实现UI与用户的交互。由于避免了页面的重新加载，单页面应用可以提供较为流畅的用户体验。

###### 1.单页面应用的优点

- 良好的交互体验

单页应用的内容的改变不需要重新加载整个页面，获取数据也是通过Ajax异步获取，没有页面之间的切换，就不会出现“白屏现象”,也不会出现假死并有“闪烁”现象，页面显示流畅

- 良好的前后端工作分离模式

后端不再负责模板渲染、输出页面工作，后端API通用化，即同一套后端程序代码，不用修改就可以用于Web界面、手机、平板等多种客户端

- 减轻服务器压力

单页应用相对服务器压力小，服务器只用出数据就可以，不用管展示逻辑和页面合成，吞吐能力会提高几倍

###### 2.缺点

- 首屏加载慢

解决方案： 1，vue-router懒加载

Vue-router懒加载就是按需加载组件，只有当路由被访问时才会加载对应的组件，而不是在加载首页的时候就加载，项目越大，对首屏加载的速度提升得越明显

2，使用CDN加速

在做项目时，我们会用到很多库，采用cdn加载可以加快加载速度。

3，异步加载组件

4，服务端渲染

服务端渲染还能对seo优化起到作用，有利于搜索引擎抓取更多有用的信息（如果页面纯前端渲染，搜索引擎抓取到的就只是空页面）

- 不利于SEO

seo 本质是一个服务器向另一个服务器发起请求，解析请求内容。但一般来说搜索引擎是不会去执行请求到的js的。也就是说，搜索引擎的基础爬虫的原理就是抓取url，然后获取html源代码并解析。 如果一个单页应用，html在服务器端还没有渲染部分数据数据，在浏览器才渲染出数据，即搜索引擎请求到的html是模型页面而不是最终数据的渲染页面。 这样就很不利于内容被搜索引擎搜索到

解决方案：1，服务端渲染

服务器合成完整的 html 文件再输出到浏览器

2，页面预渲染

3，路由采用h5 history模式

- 不适合开发大型项目

大型项目中可能会涉及大量的DOM操作、复杂的动画效果，也就不适合使用Vue、react框架进行开发

### 7. slot是什么？有什么作用？原理是什么？

slot又名插槽，是Vue的内容分发机制，组件内部的模板引擎使用slot元素作为承载分发内容的出口。插槽slot是子组件的一个模板标签元素，而这一个标签元素是否显示，以及怎么显示是由父组件决定的。slot又分三类，默认插槽，具名插槽和作用域插槽。

- 默认插槽：又名匿名查抄，当slot没有指定name属性值的时候一个默认显示插槽，一个组件内只有有一个匿名插槽。
- 具名插槽：带有具体名字的插槽，也就是带有name属性的slot，一个组件可以出现多个具名插槽。
- 作用域插槽：默认插槽、具名插槽的一个变体，可以是匿名插槽，也可以是具名插槽，该插槽的不同点是在子组件渲染作用域插槽时，可以将子组件内部的数据传递给父组件，让父组件根据子组件的传递过来的数据决定如何渲染该插槽。

实现原理：当子组件vm实例化时，获取到父组件传入的slot标签的内容，存放在`vm.$slot`中，默认插槽为`vm.$slot.default`，具名插槽为`vm.$slot.xxx`，xxx 为插槽名，当组件执行渲染函数时候，遇到slot标签，使用`$slot`中的内容进行替换，此时可以为插槽传递数据，若存在数据，则可称该插槽为作用域插槽。



### 10.slot的应用场景

我们知道在Vue中 Child 组件的标签 的中间是不可以包着什么的 。

![img](https://s2.loli.net/2022/07/09/FXrBn6GwjZMVIed.webp)

可是往往在很多时候我们在使用组件的时候总想在组件间外面自定义一些标签，vue新增了一种插槽机制，叫做作用域插槽。

插槽，其实就相当于占位符。它在组件中给你的HTML模板占了一个位置，让你来传入一些东西。插槽又分为 匿名插槽、具名插槽、作用域插槽。

在 2.6.0 中，我们为具名插槽和作用域插槽引入了一个新的统一的语法 (即 `v-slot` 指令)。它取代了 `slot` 和 `slot-scope`

子组件里面写<slot>标签

#### 匿名插槽

匿名插槽，我们也可以叫它单个插槽或者默认插槽。和具名插槽相对，它是不需要设置 name 属性的，它隐藏的name属性为default。

father.vue

![image-20220621091100389](https://s2.loli.net/2022/07/09/a4Uzx8rltnvwOWf.png)

child.vue

![image-20220621091115897](https://s2.loli.net/2022/06/21/6AZtGqxIcYzTF7S.png)

匿名插槽，name的属性对应的是 default 也可以不写就是默认的意思啦；

在使用的时候还有一个问题要注意的 如果是有2个以上的匿名插槽是会child标签里面的内容全部都替换到每个slot；

#### 具名插槽 （vue2.6.0+被废弃的slot='name'）

顾名思义就是slot 是带有name的 定义， 或者使用简单缩写的定义 #header 使用：要用一个 template标签包裹

父组件 `v-slot:myName`

子组件 `<slot name="myName">`

father.vue

![image-20220621091140171](https://s2.loli.net/2022/07/09/amf5Cg1oSW9KxAv.png)

child.vue

<img src="https://s2.loli.net/2022/06/21/XKOCJqS7DVU1GhM.png" alt="image-20220621091151645"  />

多个具名插槽的使用 多个具名插槽，插槽的位置不是使用插槽的位置而定的，是在定义的时候的位置来替换的(子组件里面确定)

father.vue

![image-20220621091208254](https://s2.loli.net/2022/06/21/CSzm39cRM2WVQ4q.png)

child.vue

![image-20220621091217351](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20220621091217351.png)

#### 作用域插槽

就是用来传递数据的插槽

当你想在一个插槽中使用数据时，要注意一个问题作用域的问题，Vue 官方文档中说了父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的;

为了让 子组件中的数据 在父级的插槽 内容中可用我们可以将 数据 作为 元素的一个特性绑定上去： v-bind:text="text"

注意：

匿名的作用域插槽和具名的作用域插槽 区别在v-slot:defult="接受的名称"(defult(匿名的可以不写，具名的相反要写的是对应的name))

v-solt可以解构接收 解构接收的字段要和传的字段一样才可以 

子组件`<slot :one="user2" >`对应父组件里面 `v-slot="{one}"`   父组件可以通过one来取值

子组件`<slot name="footer" v-bind:users='user1'>`对应父组件里面  `v-slot:footer="slotProps"`   父组件可以通过slotProps.users来取值user1

![image-20220621091229483](https://s2.loli.net/2022/07/09/vh2jdKrETP4ZpSx.png)

效果图

![image-20220621091246267](https://s2.loli.net/2022/07/09/uCH1nfP2yw6Uh5N.png)

### 11.如何保存当前页面状态，(keep-alive)原理是什么？

既然是要保持页面的状态（其实也就是组件的状态），那么会出现以下两种情况：

- 前组件会被卸载
- 前组件不会被卸载

那么可以按照这两种情况分别得到以下方法：

**组件会被卸载：**

**（1）将状态存储在LocalStorage / SessionStorage**

只需要在组件即将被销毁的生命周期 `componentWillUnmount` （react）中在 LocalStorage / SessionStorage 中把当前组件的 state 通过 JSON.stringify() 储存下来就可以了。在这里面需要注意的是组件更新状态的时机。

比如从 B 组件跳转到 A 组件的时候，A 组件需要更新自身的状态。但是如果从别的组件跳转到 B 组件的时候，实际上是希望 B 组件重新渲染的，也就是不要从 Storage 中读取信息。所以需要在 Storage 中的状态加入一个 flag 属性，用来控制 A 组件是否读取 Storage 中的状态。

**优点：**

- 兼容性好，不需要额外库或工具。
- 简单快捷，基本可以满足大部分需求。

**缺点：**

- 状态通过 JSON 方法储存（相当于深拷贝），如果状态中有特殊情况（比如 Date 对象、Regexp 对象等）的时候会得到字符串而不是原来的值。（具体参考用 JSON 深拷贝的缺点）
- 如果 B 组件后退或者下一页跳转并不是前组件，那么 flag 判断会失效，导致从其他页面进入 A 组件页面时 A 组件会重新读取 Storage，会造成很奇怪的现象

**（2）路由传值**

通过 react-router 的 Link 组件的 prop —— to 可以实现路由间传递参数的效果。

在这里需要用到 state 参数，在 B 组件中通过 history.location.state 就可以拿到 state 值，保存它。返回 A 组件时再次携带 state 达到路由状态保持的效果。

**优点：**

- 简单快捷，不会污染 LocalStorage / SessionStorage。
- 可以传递 Date、RegExp 等特殊对象（不用担心 JSON.stringify / parse 的不足）

**缺点：**

- 如果 A 组件可以跳转至多个组件，那么在每一个跳转组件内都要写相同的逻辑。

**组件不会被卸载：**

**（1）单页面渲染**

要切换的组件作为子组件全屏渲染，父组件中正常储存页面状态。

**优点：**

- 代码量少
- 不需要考虑状态传递过程中的错误

**缺点：**

- 增加 A 组件维护成本
- 需要传入额外的 prop 到 B 组件
- 无法利用路由定位页面

除此之外，在Vue中，还可以是用keep-alive来缓存页面，当组件在keep-alive内被切换时组件的**activated、deactivated**这两个生命周期钩子函数会被执行 被包裹在keep-alive中的组件的状态将会被保留：

```javascript
<keep-alive>
	<router-view v-if="$route.meta.keepAlive"></router-view>
</kepp-alive>
```

**router.js**

```javascript
{
  path: '/',
  name: 'xxx',
  component: ()=>import('../src/views/xxx.vue'),
  meta:{
    keepAlive: true // 需要被缓存
  }
},
```

如果需要在组件切换的时候，保存一些组件的状态防止多次渲染，就可以使用 keep-alive 组件包裹需要保存的组件。

**（1）keep-alive**

keep-alive有以下三个属性：

- include 字符串或正则表达式，只有名称匹配的组件会被匹配；
- exclude 字符串或正则表达式，任何名称匹配的组件都不会被缓存；
- max 数字，最多可以缓存多少组件实例。

注意：keep-alive 包裹动态组件时，会缓存不活动的组件实例。

**主要流程**

1. 判断组件 name ，不在 include 或者在 exclude 中，直接返回 vnode，说明该组件不被缓存。
2. 获取组件实例 key ，如果有获取实例的 key，否则重新生成。
3. key生成规则，cid +"∶∶"+ tag ，仅靠cid是不够的，因为相同的构造函数可以注册为不同的本地组件。
4. 如果缓存对象内存在，则直接从缓存对象中获取组件实例给 vnode ，不存在则添加到缓存对象中。 5.最大缓存数量，当缓存组件数量超过 max 值时，清除 keys 数组内第一个组件。

**（2）keep-alive 的实现**

```javascript
const patternTypes: Array<Function> = [String, RegExp, Array] // 接收：字符串，正则，数组

export default {
  name: 'keep-alive',
  abstract: true, // 抽象组件，是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

  props: {
    include: patternTypes, // 匹配的组件，缓存
    exclude: patternTypes, // 不去匹配的组件，不缓存
    max: [String, Number], // 缓存组件的最大实例数量, 由于缓存的是组件实例（vnode），数量过多的时候，会占用过多的内存，可以用max指定上限
  },

  created() {
    // 用于初始化缓存虚拟DOM数组和vnode的key
    this.cache = Object.create(null)
    this.keys = []
  },

  destroyed() {
    // 销毁缓存cache的组件实例
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },

  mounted() {
    // prune 削减精简[v.]
    // 去监控include和exclude的改变，根据最新的include和exclude的内容，来实时削减缓存的组件的内容
    this.$watch('include', (val) => {
      pruneCache(this, (name) => matches(val, name))
    })
    this.$watch('exclude', (val) => {
      pruneCache(this, (name) => !matches(val, name))
    })
  },
}
```

**render函数：**

1. 会在 keep-alive 组件内部去写自己的内容，所以可以去获取默认 slot 的内容，然后根据这个去获取组件
2. keep-alive 只对第一个组件有效，所以获取第一个子组件。
3. 和 keep-alive 搭配使用的一般有：动态组件 和router-view

```javascript
render () {
  //
  function getFirstComponentChild (children: ?Array<VNode>): ?VNode {
    if (Array.isArray(children)) {
  for (let i = 0; i < children.length; i++) {
    const c = children[i]
    if (isDef(c) && (isDef(c.componentOptions) || isAsyncPlaceholder(c))) {
      return c
    }
  }
  }
  }
  const slot = this.$slots.default // 获取默认插槽
  const vnode: VNode = getFirstComponentChild(slot)// 获取第一个子组件
  const componentOptions: ?VNodeComponentOptions = vnode && vnode.componentOptions // 组件参数
  if (componentOptions) { // 是否有组件参数
    // check pattern
    const name: ?string = getComponentName(componentOptions) // 获取组件名
    const { include, exclude } = this
    if (
      // not included
      (include && (!name || !matches(include, name))) ||
      // excluded
      (exclude && name && matches(exclude, name))
    ) {
      // 如果不匹配当前组件的名字和include以及exclude
      // 那么直接返回组件的实例
      return vnode
    }

    const { cache, keys } = this

    // 获取这个组件的key
    const key: ?string = vnode.key == null
      // same constructor may get registered as different local components
      // so cid alone is not enough (#3269)
      ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
      : vnode.key

    if (cache[key]) {
      // LRU缓存策略执行
      vnode.componentInstance = cache[key].componentInstance // 组件初次渲染的时候componentInstance为undefined

      // make current key freshest
      remove(keys, key)
      keys.push(key)
      // 根据LRU缓存策略执行，将key从原来的位置移除，然后将这个key值放到最后面
    } else {
      // 在缓存列表里面没有的话，则加入，同时判断当前加入之后，是否超过了max所设定的范围，如果是，则去除
      // 使用时间间隔最长的一个
      cache[key] = vnode
      keys.push(key)
      // prune oldest entry
      if (this.max && keys.length > parseInt(this.max)) {
        pruneCacheEntry(cache, keys[0], keys, this._vnode)
      }
    }
    // 将组件的keepAlive属性设置为true
    vnode.data.keepAlive = true // 作用：判断是否要执行组件的created、mounted生命周期函数
  }
  return vnode || (slot && slot[0])
}
```

keep-alive 具体是通过 cache 数组缓存所有组件的 vnode 实例。当 cache 内原有组件被使用时会将该组件 key 从 keys 数组中删除，然后 push 到 keys数组最后，以便清除最不常用组件。

**实现步骤：**

1. 获取 keep-alive 下第一个子组件的实例对象，通过他去获取这个组件的组件名
2. 通过当前组件名去匹配原来 include 和 exclude，判断当前组件是否需要缓存，不需要缓存，直接返回当前组件的实例vNode
3. 需要缓存，判断他当前是否在缓存数组里面：

- 存在，则将他原来位置上的 key 给移除，同时将这个组件的 key 放到数组最后面（LRU）
- 不存在，将组件 key 放入数组，然后判断当前 key数组是否超过 max 所设置的范围，超过，那么削减未使用时间最长的一个组件的 key

1. 最后将这个组件的 keepAlive 设置为 true

**（3）keep-alive 本身的创建过程和 patch 过程**

缓存渲染的时候，会根据 vnode.componentInstance（首次渲染 vnode.componentInstance 为 undefined） 和 keepAlive 属性判断不会执行组件的 created、mounted 等钩子函数，而是对缓存的组件执行 patch 过程∶ 直接把缓存的 DOM 对象直接插入到目标元素中，完成了数据更新的情况下的渲染过程。

**首次渲染**

- 组件的首次渲染∶判断组件的 abstract 属性，才往父组件里面挂载 DOM

```javascript
// core/instance/lifecycle
function initLifecycle (vm: Component) {
  const options = vm.$options

  // locate first non-abstract parent
  let parent = options.parent
  if (parent && !options.abstract) { // 判断组件的abstract属性，才往父组件里面挂载DOM
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    parent.$children.push(vm)
  }

  vm.$parent = parent
  vm.$root = parent ? parent.$root : vm

  vm.$children = []
  vm.$refs = {}

  vm._watcher = null
  vm._inactive = null
  vm._directInactive = false
  vm._isMounted = false
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
}
```

- 判断当前 keepAlive 和 componentInstance 是否存在来判断是否要执行组件 prepatch 还是执行创建 componentlnstance

```javascript
// core/vdom/create-component
init (vnode: VNodeWithData, hydrating: boolean): ?boolean {
    if (
      vnode.componentInstance &&
      !vnode.componentInstance._isDestroyed &&
      vnode.data.keepAlive
    ) { // componentInstance在初次是undefined!!!
      // kept-alive components, treat as a patch
      const mountedNode: any = vnode // work around flow
      componentVNodeHooks.prepatch(mountedNode, mountedNode) // prepatch函数执行的是组件更新的过程
    } else {
      const child = vnode.componentInstance = createComponentInstanceForVnode(
        vnode,
        activeInstance
      )
      child.$mount(hydrating ? vnode.elm : undefined, hydrating)
    }
  },
```

prepatch 操作就不会在执行组件的 mounted 和 created 生命周期函数，而是直接将 DOM 插入

**（4）LRU （least recently used）缓存策略**

LRU 缓存策略∶ 从内存中找出最久未使用的数据并置换新的数据。 LRU（Least rencently used）算法根据数据的历史访问记录来进行淘汰数据，其核心思想是 **"如果数据最近被访问过，那么将来被访问的几率也更高"**。 最常见的实现是使用一个链表保存缓存数据，详细算法实现如下∶

- 新数据插入到链表头部
- 每当缓存命中（即缓存数据被访问），则将数据移到链表头部
- 链表满的时候，将链表尾部的数据丢弃。

### 12.vue如何监听对象或数组的属性变化 数组检测缺陷问题

在 Vue 的数据绑定中会对一个对象属性的变化进行监听，并且通过依赖收集做出相应的视图更新

一个对象所有类型的属性变化都能被监听到吗？

之前用 Object.defineProperty通过对象的 getter/setter简单的实现了对象属性变化的监听，并且去通过依赖关系去做相应的依赖处理。

但是，这是存在问题的，尤其是当对象中某个属性的值是数组的时候。

**正如 Vue 文档所说：**

由于 JavaScript 的限制，Vue 无法检测到以下数组变动：

![image.png](https://s2.loli.net/2022/07/09/Sdow8xkYeqcmIQ3.webp)

对于响应式数组，当浏览器支持_proto_属性时，使用push等方法时先从其原型arrayMethods上寻找push方法，也就是重写后的方法，处理之后数组变化会通知到其订阅者，更新页面，当在arrayMethods上查询不到时会向上在Array.prototype上查询；

当浏览器不支持_proto_属性时，使用push等方法时会从数组自身上查询，如果查询不到会向上再Array.proptotype上查询。

对于非响应式数组，当使用push等方法时会直接从Array.prototype上查询。

值得一提的是源码中通过判断浏览器是否支持_proto_来分别使用protoAugment和copyAugment方法将重写后的数组方法应用到数组中，这是因为对于IE10及以下浏览器是不支持_proto_属性的

判断当前环境是否可以使用对象的_proto_属性,该属性在IE11及更高浏览器中使用 export const hasProp = '*proto*' in {}

**结论：**

在将数组处理成响应式数据后，如果使用数组原始方法改变数组时，数组值会发生变化，但是并不会触发数组的setter来通知所有依赖该数组的地方进行更新，为此，vue通过重写数组的某些方法来监听数组变化，重写后的方法中会手动触发通知该数组的所有依赖进行更新。

**Vue重新的属性**

push pop shift unshift splice sort reverse

看来Vue能对数组进行监听的原因是，把数组的方法重写了。总结起来就是这几步：

**01**先获取原生 Array 的原型方法，因为拦截后还是需要原生的方法帮我们实现数组的变化。

**02**对 Array 的原型方法使用 Object.defineProperty 做一些拦截操作。

**03**把需要被拦截的 Array 类型的数据原型指向改造后原型

```js
// 触发更新视图
function updateView() {
    console.log('视图更新')
}

// 重新定义数组原型
const oldArrayProperty = Array.prototype
// 创建新对象，原型指向 oldArrayProperty ，再扩展新的方法不会影响原型
const arrProto = Object.create(oldArrayProperty);
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
    arrProto[methodName] = function () {
        updateView() // 触发视图更新
        oldArrayProperty[methodName].call(this, ...arguments)
        // Array.prototype.push.call(this, ...arguments)
    }
})

// 重新定义属性，监听起来
function defineReactive(target, key, value) {
    // 深度监听
    observer(value)

    // 核心 API
    Object.defineProperty(target, key, {
        get() {
            return value
        },
        set(newValue) {
            if (newValue !== value) {
                // 深度监听
                observer(newValue)

                // 设置新值
                // 注意，value 一直在闭包中，此处设置完之后，再 get 时也是会获取最新的值
                value = newValue

                // 触发更新视图
                updateView()
            }
        }
    })
}

// 监听对象属性
function observer(target) {
    if (typeof target !== 'object' || target === null) {
        // 不是对象或数组
        return target
    }

    // 污染全局的 Array 原型
    // Array.prototype.push = function () {
    //     updateView()
    //     ...
    // }

    if (Array.isArray(target)) {
        target.__proto__ = arrProto
    }

    // 重新定义各个属性（for in 也可以遍历数组）
    for (let key in target) {
        defineReactive(target, key, target[key])
    }
}

// 准备数据
const data = {
    name: 'zhangsan',
    age: 20,
    info: {
        address: '北京' // 需要深度监听
    },
    nums: [10, 20, 30]
}

// 监听数据
observer(data)

// 测试
// data.name = 'lisi'
// data.age = 21
// // console.log('age', data.age)
// data.x = '100' // 新增属性，监听不到 —— 所以有 Vue.set
// delete data.name // 删除属性，监听不到 —— 所有已 Vue.delete
// data.info.address = '上海' // 深度监听
data.nums.push(4) // 监听数组
```

### 13.事件修饰符有哪些

- `.stop`：等同于 JavaScript 中的 `event.stopPropagation()` ，防止事件冒泡；
- `.prevent` ：等同于 JavaScript 中的 `event.preventDefault()` ，防止执行预设的行为（如果事件可取消，则取消该事件，而不停止事件的进一步传播）；
- `.capture` ：与事件冒泡的方向相反，事件捕获由外到内；
- `.self` ：只会触发自己范围内的事件，不包含子元素；
- `.once` ：只会触发一次。

### 14.template渲染过程 ，模板编译原理

vue的模版编译过程主要如下：**template -> ast -> render函数**

vue 在模版编译版本的码中会执行 compileToFunctions 将template转化为render函数：

```javascript
// 将模板编译为render函数const { render, staticRenderFns } = compileToFunctions(template,options//省略}, this)
```

CompileToFunctions中的主要逻辑如下∶ **（1）调用parse方法将template转化为ast（抽象语法树）**

```javascript
constast = parse(template.trim(), options)
```

- **parse的目标**：把tamplate转换为AST树，它是一种用 JavaScript对象的形式来描述整个模板。
- **解析过程**：利用正则表达式顺序解析模板，当解析到开始标签、闭合标签、文本的时候都会分别执行对应的 回调函数，来达到构造AST树的目的。

AST元素节点总共三种类型：type为1表示普通元素、2为表达式、3为纯文本

**（2）对静态节点做优化**

```javascript
optimize(ast,options)
```

这个过程主要分析出哪些是静态节点，给其打一个标记，为后续更新渲染可以直接跳过静态节点做优化

深度遍历AST，查看每个子树的节点元素是否为静态节点或者静态节点根。如果为静态节点，他们生成的DOM永远不会改变，这对运行时模板更新起到了极大的优化作用。

**（3）生成代码**

```javascript
const code = generate(ast, options)
```

generate将ast抽象语法树编译成 render字符串并将静态部分放到 staticRenderFns 中，最后通过 `new Function(`` render``)` 生成render函数。

### 15.mixin和extends的应用

**（1）mixin 和 extends** mixin 和 extends均是用于合并、拓展组件的，两者均通过 mergeOptions 方法实现合并。

- mixins 接收一个混入对象的数组，其中混入对象可以像正常的实例对象一样包含实例选项，这些选项会被合并到最终的选项中。Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子之前被调用。
- extends 主要是为了便于扩展单文件组件，接收一个对象或构造函数。

**（2）mergeOptions 的执行过程**

- 规范化选项（normalizeProps、normalizelnject、normalizeDirectives)
- 对未合并的选项，进行判断

```javascript
if(!child._base) {
    if(child.extends) {        
        parent = mergeOptions(parent, child.extends, vm)    }    
    if(child.mixins) {      
     for(let i = 0, l = child.mixins.length; i < l; i++){            
         parent = mergeOptions(parent, child.mixins[i], vm)       
        }    
    }
}
```

- 合并处理。根据一个通用 Vue 实例所包含的选项进行分类逐一判断合并，如 props、data、 methods、watch、computed、生命周期等，将合并结果存储在新定义的 options 对象里。
- 返回合并结果 options。

### 16.v-cloak的应用

在开发过程中，会遇到刷新或者切换路由页面闪烁的情况，等数据加载成功再重新展示，需要用到v-cloak防止闪烁。

v-cloak 指令设置样式，样式会在 Vue 实例编译结束时，从 HTML 元素上被移除。

这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。

解决方法

> 1.在vue容器的div里面加上 v-cloak：

```vue
<div id="app" v-cloak>
```

> 2.css样式中加：

```css
<style>
    [v-cloak] {
        display: none !important;
    }
</style>
```

### 17.Vue的渲染过程

[vue渲染过程](https://segmentfault.com/a/1190000018495383)

Vue 推荐在绝大多数情况下使用 template 来创建你的 HTML。但是模板毕竟是模板，不是真实的dom节点。从模板到真实dom节点还需要经过一些步骤

1. 把模板编译为render函数
2. 实例进行挂载, 根据根节点render函数的调用，递归的生成虚拟dom
3. 对比虚拟dom，渲染到真实dom
4. 组件内部data发生变化，组件和子组件引用data作为props重新调用render函数，生成虚拟dom, 返回到步骤3

## 第一步: 模板到render

在我们使用Vue的组件化进行开发应用的时候, 如果仔细的查看我们要引入的组件, 例子如下

```javascript
// App.vue 
<template>
    <div>
        hello word
    </div>
</template>

<script>

export default {
}

</script>

<style>

</style>
```

在我们的主入口main.js

```javascript
import Vue from 'vue'
import App from './App'

console.log(App)

new Vue({
  render: h => h(App)
}).$mount('#app')
```

![clipboard.png](https://s2.loli.net/2022/07/22/SBFnwXedpxs8fGK.png)

我们能够看到在我们引入的App这个模块，里面是一个对象，对象里面存在一个方法叫做render。在说render函数之前，我们可以想一想，每一次加载一个组件，然后对模板进行解析，解析完后，生成Dom，挂载到页面上。这样会导致效率很低效。而使用Vue-cli进行组件化开发，在我们引入组件的后，其实会有一个解析器(`vue-loader`)对此模板进行了解析，生成了render函数。当然，如果没有通过解析器解析为render函数，也没有关系，在组件第一次挂载的时候，Vue会自己进行解析。源码请参考: [https://github.com/vuejs/vue/...](https://link.segmentfault.com/?enc=zJM7gE6r0HURaRJ7vOqgyw%3D%3D.KXezcBZLFlnOAPSXvcwPmB1Ys7yOY3c0gLeqcim9sDI8Q5HuwybujVipn4dAWwlfVyzUEOGAkdZNCJzVkTKH40q19ibBqn16Ruv7kkU9BSZpYLVT6ihX0sJ5Fx0maygi)
这样，能保证组件每次调用的都是render函数，使用render函数生成VNode。

## 第二步：虚拟节点VNode

我们把Vue的实例挂载到`#app`, 会调用实例里面的render方法，生成虚拟DOM。来看看什么是虚拟节点，把例子修改一下。

```arcade
new Vue({
  render: h => {
    let root = h(App)
    console.log('root:', root)
    return root
  }
}).$mount('#app')
```

![clipboard.png](https://s2.loli.net/2022/07/22/xlLApSXoY98sKeC.png)

上面生成的VNode就是虚拟节点，虚拟节点里面有一个属性**`elm`**, 这个属性指向真实的DOM节点。因为VNode指向了真实的DOM节点，那么虚拟节点经过对比后，生成的DOM节点就可以直接进行替换。
**这样有什么好处呢？**
一个组件对象，如果内部的`data`发生变化，触发了render函数，重新生成了VNode节点。那么就可以直接找到所对应的节点，然后直接替换。那么这个过程只会在本组件内发生，不会影响其他的组件。于是组件与组件是隔离的。
例子如下:

```javascript
// main.js
const root = new Vue({
  data: {
    state: true
  },
  mounted() {
    setTimeout(() => {
      console.log(this)
      this.state = false
    }, 1000)
  },
  render: function(h) {
    const { state } = this // state 变化重新触发render
    let root = h(App)
    console.log('root:', root)
    return root
  }
}).$mount('#app')
// App.vue
<script>
export default {
  render: (h) => {
    let app = h('h1', ['hello world'])
    console.log('app:', app)
    return app
  }
}
</script>
```

![clipboard.png](https://s2.loli.net/2022/07/22/64bNVi1WaIzljEC.png)
我们可以看到，当`main.js`中重新触发render函数的时候，render方法里面有引用App.vue这个子组件。但是并没有触发App.vue组件的的render函数。

**`在一个组件内，什么情况会触发render?`**。

## 如何才能触发组件的render

数据劫持是Vue的一大特色，原理官方已经讲的很多了[深入响应式原理](https://link.segmentfault.com/?enc=TVGAgYV6EWmwbqTYDsvvwA%3D%3D.bxYehXOny7hcoA3swKzfZN8bVrSGDMpfdEqTCJg%2F9buXLGcl3RJi1YFfSlACMhZO)。在我们给组件的data的属性进行的赋值的时候(set)，此属性如果在组件内部初次渲染过程被引用(`data的属性被访问，也就是数据劫持的get`), 包括生命周期方法或者render方法。于是会触发组件的update(beforeUpdate -> render -> updated)。

> 注: 为了防止data被多次set从而触发多次update, Vue把update存放到异步队列中。这样就能保证多次data的set只会触发一次update。

**`当props会触发组件的重新渲染是怎么发生的呢？`**

把父组件的data通过props传递给子组件的时候，子组件在初次渲染的时候生命周期或者render方法，有调用data相关的props的属性, 这样子组件也被添加到父组件的data的相关属性依赖中，这样父组件的data在set的时候，就相当于触发自身和子组件的update。
例子如下:

```javascript
// main.vue
import Vue from 'vue'
import App from './App'

const root = new Vue({
  data: {
    state: false
  },
  mounted() {
    setTimeout(() => {
      this.state = true
    }, 1000)
  },
  render: function(h) {
    const { state } = this // state 变化重新触发render
    let root = h(App, { props: { status: state } })
    console.log('root:', root)
    return root
  }
}).$mount('#app')

window.root = root
// App.vue
<script>
export default {
  props: {
    status: Boolean
  },
  render: function (h){
    const { status } = this
    let app = h('h1', ['hello world'])
    console.log('app:', app)
    return app
  }
}
</script>
```

截图如下:

![clipboard.png](https://s2.loli.net/2022/07/22/tCd48DWF9oA7gLZ.png)
在`main.js`中 **state** 状态发生了变化，由`false` => `true`, 触发了**自身**与**子组件**的render方法。

### 18.修改后页面保存渲染的原理

### 19.Vue data 中某一个属性的值发生改变后，视图会立即同步执行重新渲染吗？

不会立即同步执行重新渲染。Vue 实现响应式并不是数据发生变化之后 DOM 立即变化，而是按一定的策略进行 DOM 的更新。Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化， Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。

如果同一个watcher被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环tick中，Vue 刷新队列并执行实际（已去重的）工作。

### 25. 描述下Vue自定义指令

在 Vue2.0 中，代码复用和抽象的主要形式是组件。然而，有的情况下，你仍然需要对普通 DOM 元素进行底层操作，这时候就会用到自定义指令。 一般需要对DOM元素进行底层操作时使用，尽量只用来操作 DOM展示，不修改内部的值。当使用自定义指令直接修改 value 值时绑定v-model的值也不会同步更新；如必须修改可以在自定义指令中使用keydown事件，在vue组件中使用 change事件，回调中修改vue数据;

**（1）自定义指令基本内容**

- 全局定义：`Vue.directive("focus",{})`

- 局部定义：`directives:{focus:{}}`

- 钩子函数：指令定义对象提供钩子函数

  o bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。

  o inSerted：被绑定元素插入父节点时调用（仅保证父节点存在，但不一定已被插入文档中）。

  o update：所在组件的VNode更新时调用，但是可能发生在其子VNode更新之前调用。指令的值可能发生了改变，也可能没有。但是可以通过比较更新前后的值来忽略不必要的模板更新。

  o ComponentUpdate：指令所在组件的 VNode及其子VNode全部更新后调用。

  o unbind：只调用一次，指令与元素解绑时调用。

- 钩子函数参数 o el：绑定元素

  o bing： 指令核心对象，描述指令全部信息属性

  o name

  o value

  o oldValue

  o expression

  o arg

  o modifers

  o vnode  虚拟节点

  o oldVnode：上一个虚拟节点（更新钩子函数中才有用）

**（2）使用场景**

- 普通DOM元素进行底层操作的时候，可以使用自定义指令
- 自定义指令是用来操作DOM的。尽管Vue推崇数据驱动视图的理念，但并非所有情况都适合数据驱动。自定义指令就是一种有效的补充和扩展，不仅可用于定义任何的DOM操作，并且是可复用的。

**（3）使用案例**

初级应用：

- 鼠标聚焦
- 下拉菜单
- 相对时间转换
- 滚动动画

高级应用：

- 自定义指令实现图片懒加载
- 自定义指令集成第三方插件



### 20.Vue中 scoped css原理

在开发环境我们的组件会先经过 vue-loader 的处理，然后结合运行时的框架代码渲染到页面上

Scope CSS 的本质是基于 HTML 和 CSS 选择器的属性，通过分别给 HTML 标签和 CSS 选择器添加 `data-v-xxxx` 属性的方式实现

针对 Scope CSS 而言，vue-loader 会做这 3 件事：

-   解析组件，提取出 `template`、`script`、`style` 对应的代码块
-   构造并导出 `export` 组件实例，在组件实例的选项上绑定 ScopId
-   对 `style` 的 CSS 代码进行编译转化，应用 ScopId生成选择器的属性

vue-loader 的底层使用了 Vue 官方提供的包（package） [@vue/component-compiler-utils](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvuejs%2Fcomponent-compiler-utils)，其提供了解析组件（.vue 文件）、编译模版 `template`、编译 `style`等 3 种能力

`template` 会被编译成 `render` 函数，然后会根据 `render` 函数创建对应的 VNode，最后再由 VNode 渲染成真实的 DOM 在页面上：

### 38. template和jsx的有什么分别？

对于 runtime 来说，只需要保证组件存在 render 函数即可，而有了预编译之后，只需要保证构建过程中生成 render 函数就可以。在 webpack 中，使用`vue-loader`编译.vue文件，内部依赖的`vue-template-compiler`模块，在 webpack 构建过程中，将template预编译成 render 函数。与 react 类似，在添加了jsx的语法糖解析器`babel-plugin-transform-vue-jsx`之后，就可以直接手写render函数。

所以，template和jsx的都是render的一种表现形式，不同的是：JSX相对于template而言，具有更高的灵活性，在复杂的组件中，更具有优势，而 template 虽然显得有些呆滞。但是 template 在代码结构上更符合视图与逻辑分离的习惯，更简单、更直观、更好维护。



### 22.render函数触发过程

[第一次挂载和每次数据更新都会触发render函数](https://www.zhihu.com/question/406811368)

在vue内部的$mount方法里（$mount为Vue处理mount相关的方法），调用了mountComponent方法

在mountComponent内，可以发现两点：

**1.定义了updateComponent函数**，updateComponent调用了vm._render()。vm._render()内会调用this.$options.render。

2.**将updateComponent函数传给实例化的Watcher。**

传给了watcher之后，只要有任何数据等变化，那么watcher就会调用updateComponent函数，之后render就会被调用。

### 23.new Vue发生了什么

[原理](https://juejin.cn/post/6997776616294187015)

![5f17cedf4e974df89fb807ff961ae00b](https://s2.loli.net/2022/06/20/75EQRtOALMcUkC2.png)

#### 初始化及挂载

![img](https://img.kancloud.cn/c4/dd/c4dd695d1c4423aeb8ea55e67fff486d_828x336.gif)

在 `new Vue()` 之后。 Vue 会调用 `_init` 函数进行初始化，也就是这里的 `init` 过程，它会初始化生命周期、事件、 props、 methods、 data、 computed 与 watch 等。其中最重要的是通过 `Object.defineProperty` 设置 `setter` 与 `getter` 函数，用来实现「**响应式**」以及「**依赖收集**」，后面会详细讲到，这里只要有一个印象即可。

初始化之后调用 `$mount` 会挂载组件，如果是运行时编译，即不存在 render function 但是存在 template 的情况，需要进行「**编译**」步骤。

```js
new Vue({
    el:'#app',
    store,
    router,
    render: h => h(App)
})
```

new Vue()是创建Vue实例，而Vue是一个类，当执行 new Vue()的时候，它的内部主要是执行了一个`_init`私有函数

```jsx
// 从源码可以看到vue类中非常干净，只是执行了一个_init私有函数, 并且只能通过new关键字初始化
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}
```

 看下 `_init`私有函数内部，这个函数主要是做了一堆初始化工作，比如对options进行合并，初始化生命周期，初始化事件中心，初始化渲染，初始化data,props,computed,watcher等，最后调用 vm.$mount做挂载

```tsx
export function initMixin (Vue: Class<Component>) {
  Vue.prototype._init = function (options?: Object) {
    const vm: Component = this
    // a uid
    vm._uid = uid++

    let startTag, endTag
    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      startTag = `vue-perf-start:${vm._uid}`
      endTag = `vue-perf-end:${vm._uid}`
      mark(startTag)
    }

    // a flag to avoid this being observed
    vm._isVue = true
    // merge options
    if (options && options._isComponent) {
      // optimize internal component instantiation
      // since dynamic options merging is pretty slow, and none of the
      // internal component options needs special treatment.
      initInternalComponent(vm, options)
    } else {
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      initProxy(vm)
    } else {
      vm._renderProxy = vm
    }
    // expose real self
    vm._self = vm
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm) // resolve injections before data/props
    initState(vm)
    initProvide(vm) // resolve provide after data/props
    callHook(vm, 'created')

    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      vm._name = formatComponentName(vm, false)
      mark(endTag)
      measure(`vue ${vm._name} init`, startTag, endTag)
    }

    if (vm.$options.el) {
      vm.$mount(vm.$options.el)
    }
  }
}
```

#### 编译

compile编译可以分成 `parse`、`optimize` 与 `generate` 三个阶段，最终需要得到 render function。

![img](https://img.kancloud.cn/7e/80/7e80fcd4e490557058f4e19af76e5795_824x496.gif)

##### parse

`parse` 会用正则等方式解析 template 模板中的指令、class、style等数据，形成AST。

##### optimize

`optimize` 的主要作用是标记 static 静态节点，这是 Vue 在编译过程中的一处优化，后面当 `update` 更新界面时，会有一个 `patch` 的过程， diff 算法会直接跳过静态节点，从而减少了比较的过程，优化了 `patch` 的性能。

##### generate

`generate` 是将 AST 转化成 render function 字符串的过程，得到结果是 render 的字符串以及 staticRenderFns 字符串。

在经历过 `parse`、`optimize` 与 `generate` 这三个阶段以后，组件中就会存在渲染 VNode 所需的 render function 了。

#### 响应式

接下来也就是 Vue.js 响应式核心部分。

![img](https://img.kancloud.cn/3f/32/3f32fe9400f1f726e6d10eadd342b277_1460x728.gif)

这里的 `getter` 跟 `setter` 已经在之前介绍过了，在 `init` 的时候通过 `Object.defineProperty` 进行了绑定，它使得当被设置的对象被读取的时候会执行 `getter` 函数，而在当被赋值的时候会执行 `setter` 函数。

当 render function 被渲染的时候，因为会读取所需对象的值，所以会触发 `getter` 函数进行「**依赖收集**」，「**依赖收集**」的目的是将观察者 Watcher 对象存放到当前闭包中的订阅者 Dep 的 subs 中。形成如下所示的这样一个关系。

![img](https://img.kancloud.cn/43/c8/43c84091dc595cdf3eb9db090b7aec13_520x245.gif)

在修改对象的值的时候，会触发对应的 `setter`， `setter` 通知之前「**依赖收集**」得到的 Dep 中的每一个 Watcher，告诉它们自己的值改变了，需要重新渲染视图。这时候这些 Watcher 就会开始调用 `update` 来更新视图，当然这中间还有一个 `patch` 的过程以及使用队列来异步更新的策略，这个我们后面再讲。

#### Virtual DOM

我们知道，render function 会被转化成 VNode 节点。Virtual DOM 其实就是一棵以 JavaScript 对象（ VNode 节点）作为基础的树，用对象属性来描述节点，实际上它只是一层对真实 DOM 的抽象。最终可以通过一系列操作使这棵树映射到真实环境上。由于 Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。

比如说下面这样一个例子：

```
{
    tag: 'div',                 /*说明这是一个div标签*/
    children: [                 /*存放该标签的子节点*/
        {
            tag: 'a',           /*说明这是一个a标签*/
            text: 'click me'    /*标签的内容*/
        }
    ]
}
```

渲染后可以得到

```
<div>
    <a>click me</a>
</div>
```

这只是一个简单的例子，实际上的节点有更多的属性来标志节点，比如 isStatic （代表是否为静态节点）、 isComment （代表是否为注释节点）等。

#### 更新视图

![img](https://img.kancloud.cn/d1/dc/d1dc77e6aec6a13caa86bc32afd24509_731x339.gif)

前面我们说到，在修改一个对象值的时候，会通过 `setter -> Watcher -> update` 的流程来修改对应的视图，那么最终是如何更新视图的呢？

当数据变化后，执行 render function 就可以得到一个新的 VNode 节点，我们如果想要得到新的视图，最简单粗暴的方法就是直接解析这个新的 VNode 节点，然后用 `innerHTML` 直接全部渲染到真实 DOM 中。但是其实我们只对其中的一小块内容进行了修改，这样做似乎有些「**浪费**」。

那么我们为什么不能只修改那些「改变了的地方」呢？这个时候就要介绍我们的「**`patch`**」了。我们会将新的 VNode 与旧的 VNode 一起传入 `patch` 进行比较，经过 diff 算法得出它们的「**差异**」。最后我们只需要将这些「**差异**」的对应 DOM 进行修改即可。

#### 再看全局

<img src="https://img.kancloud.cn/01/db/01db136b4380b1804c072899e92daa3d_1752x1216.gif" alt="img" style="zoom:50%;" />

### 24.框架对比分析

#### 说说vue和react的异同

相同点:

-   核心库都只关注ui层面的问题解决，路由/状态管理都由其他库处理。

-   都使用了虚拟dom来提高重渲染效率。

-   都采用了组件化思想，将应用中不同功能抽象成一个组件，提高了代码复用性。

-   都能进行跨平台，react使用react native，vue使用weex

-   都有自己的构建工具:

    vue: vue-cli

    react: create-react-app

不同点:

-   最大的不同是组件的编写方式

    vue推荐使用类似于常规html模板的方式来编写组件, 基于vue强大的指令系统来进行数据的绑定和添加事件监听。在vue中，一个组件就是一个.vue文件。

    而react中采用jsx语法，每一个jsx表达式都是一个react元素. 在react中，一个组件本质就是一个函数或者一个类。

-   虚拟dom渲染效率方面

    由于vue对数据进行了劫持，因此每一个响应式数据都能进行依赖跟踪。当组件重新渲染时，不必重新渲染它的整个子组件树，而是只渲染应该重渲染的子组件。

    在react中，一旦组件状态变化导致重渲染后，其整个子组件树都会默认重新渲染。可以通过pureComponent或者shouldComponentUpdate来进行优化。

-   响应式方面

    vue由于使用defineProperty或者proxy, 能对数据进行劫持。因此只要修改了响应式数据本身就能导致组件的重渲染。

    而在react中，并没有对数据本身进行劫持，需要手动调用setState才能触发组件的重渲染。并且react强调使用不可变数据，即每次更改状态时，新状态的引用必须和旧状态不同。如果说没有使用不可变数据并且又在组件内使用了pureComponent或者shouldComponentUpdate进行优化，可能会导致状态变化组件没有重新渲染。

-   高阶组件

    react中存在HOC(高阶组件)的概念，因为react中的每一个组件本质都是一个函数或者类，都是编写在js代码中。因此可以轻松的实现高阶组件来对组件进行扩展。而vue采用模板编译的方式编写组件，无法使用HOC, 通常通过mixin来扩展组件.

-   指令系统

    vue有一套强大的指令系统并且支持自定义指令来封装一些功能。

    react则更偏向底层，使用javascript原生代码进行封装功能。

#### 说说vue3的composition api 和 react hook的区别？

react:

 由于react没有实现真正的数据双向绑定即没有对数据进行劫持，react是依靠hook的调用顺序来知道重渲染时，本次的state对应于哪一个useState hook。因此在react中使用hook有如下要求:

-   不能在循环/判断/嵌套函数内使用hook 
-   总是确保hook出现在函数组件的最顶部 
-   对于一些hook如useEffect, useMemo, useCallBack, 必须手动注册依赖项。 

而在vue中, 基于vue的响应式系统，composiiton api在调用时可以不用考虑顺序并且能使用在判断/循环/内部函数中。并且由于vue的响应式数据会自动收集依赖，因此使用一些composiiton api如computed以及watchEffect时无需手动注册依赖。

后面基本是一些小的问题比如:

-   vue中的scoped style是如何实现作用域样式以及为什么vue不使用css module来实现作用域？ 

-   为什么vue要将传递给子组件的属性分为props和attrs?(这个不会,把props和attrs?  (这个不会, 把props和attrs?(这个不会,把props和attrs的区别说了一下) 



## 生命周期

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1493c640d7e4cf2bd7785cea7c86789~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)

### 1.生命周期有哪些，vue2和vue3有什么区别

#### Vue2生命周期

Vue 实例有⼀个完整的⽣命周期，也就是从开始创建、初始化数据、编译模版、挂载Dom -> 渲染、更新 -> 渲染、卸载 等⼀系列过程，称这是Vue的⽣命周期。

1. **beforeCreate（创建前）**：数据观测和初始化事件还未开始，此时 data 的响应式追踪、event/watcher 都还没有被设置，也就是说不能访问到data、computed、watch、methods上的方法和数据。
2. **created（创建后）** ：实例创建完成，实例上配置的 options 包括 data、computed、watch、methods 等都配置完成，但是此时渲染得节点还未挂载到 DOM，所以不能访问到 `$el` 属性。
3. **beforeMount（挂载前）**：在挂载开始之前被调用，相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。此时还没有挂载html到页面上。
4. **mounted（挂载后）**：在el被新创建的 vm.$el 替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的html内容替换el属性指向的DOM对象。完成模板中的html渲染到html 页面中。mounted以后可以进行dom操作
5. **beforeUpdate（更新前）**：响应式数据更新时调用，此时虽然响应式数据更新了，但是对应的真实 DOM 还没有被渲染。
6. **updated（更新后）** ：在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。此时 DOM 已经根据响应式数据的变化更新了。调用时，组件 DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。
7. **beforeDestroy（销毁前）**：实例销毁之前调用。这一步，实例仍然完全可用，`this` 仍能获取到实例。
8. **destroyed（销毁后）**：实例销毁后调用，调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务端渲染期间不被调用。

另外还有 `keep-alive` 独有的生命周期，分别为 `activated` 和 `deactivated` 。用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `activated` 钩子函数。

首先给出结论：created 和 mounted 中发起 ajax 请求是一样的，没有区别。
为啥没有区别：created 和 mounted 是在同一个 tick 中执行的，而ajax 请求的时间一定会超过一个 tick。所以即便ajax的请求耗时是 0ms， 那么也是在 nextTick 中更新数据到 DOM 中。所以说在不依赖 DOM 节点的情况下一点区别都没有。



#### Vue3生命周期

组件中setup的执行顺序

创建组件，先初始化props，再依次执行setup、beforeCreate、created

setup() 内部(组合式api)调用的生命周期钩子里是没有`beforeCreate` 和 `Create`函数的。
官方解释： 因为 `setup` 是围绕 `beforeCreate` 和 `created` 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 `setup` 函数中编写

 beforeMount => onBeforeMount

 mounted => onMounted

 beforeUpdate => onBeforeUpdate

 updated => onUpdated

 beforeUnmount => onBeforeUnmount

 unmount => onUnmounted

在comsition API 中不存在beforeCreate和created这两个生命周期函数 因为setup函数的执行时间在beforeCreate和created之前，有需要执行的内容可以直接在setup函数中执行，没必要再写在beforeCreate和created中

vue3中新增了onRenderTracked生命周期函数 指的是每次渲染之后收集页面响应式的依赖的时候会自动执行的函数

当页面渲染的时候，vue都会重新收集响应式的依赖，响应式的依赖一旦重新渲染需要重新收集的时候onRenderTracked便会自动执行一次 页面首次渲染便会执行 页面再次重新渲染也会执行 与onRenderTracked对应的函数是onRenderTriggered 指每次重新渲染被触发的时候，首次页面加载不会触发，当数据改变，页面重新渲染的时候触发，onRenderTracked也会再次触发。

### 2.created和mounted有什么区别

- created:在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
- mounted:在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。

### 3.异步请求放在那个生命周期函数

我们可以在钩子函数 created、beforeMount、mounted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。

推荐在 created 钩子函数中调用异步请求，因为在 created 钩子函数中调用异步请求有以下优点：

- 能更快获取到服务端数据，减少页面加载时间，用户体验更好；
- SSR不支持 beforeMount 、mounted 钩子函数，放在 created 中有助于一致性。

### 4.keep-alive生命周期

keep-alive是 Vue 提供的一个内置组件，用来对组件进行缓存——在组件切换过程中将状态保留在内存中，防止重复渲染DOM。

如果为一个组件包裹了 keep-alive，那么它会多出两个生命周期：deactivated、activated。同时，beforeDestroy 和 destroyed 就不会再被触发了，因为组件不会被真正销毁。

当组件被换掉时，会被缓存到内存中、触发 deactivated 生命周期；当组件被切回来时，再去缓存里找这个组件、触发 activated钩子函数。

### 5.Vue 子组件和父组件执行顺序

**加载渲染过程：**

1. 父组件 beforeCreate
2. 父组件 created
3. 父组件 beforeMount
4. 子组件 beforeCreate
5. 子组件 created
6. 子组件 beforeMount
7. 子组件 mounted
8. 父组件 mounted

**更新过程：**

1. 父组件 beforeUpdate
2. 子组件 beforeUpdate
3. 子组件 updated
4. 父组件 updated

**销毁过程：**

1. 父组件 beforeDestroy
2. 子组件 beforeDestroy
3. 子组件 destroyed
4. 父组件 destoryed

### 6.生命周期源码分析

[Vue 的完整生命周期源码流程详解](https://juejin.cn/post/7017712966485147678)

[Vue 的生命周期之间到底做了什么事清？](https://juejin.cn/post/6844904114879463437)

#### 初始化流程

```js
export function initMixin (Vue: Class<Component>) {
  // 在原型上添加 _init 方法
  Vue.prototype._init = function (options?: Object) {
    // 保存当前实例
    const vm: Component = this
    // 合并配置
    if (options && options._isComponent) {
      // 把子组件依赖父组件的 props、listeners 挂载到 options 上，并指定组件的$options
      initInternalComponent(vm, options)
    } else {
      // 把我们传进来的 options 和当前构造函数和父级的 options 进行合并，并挂载到原型上
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    vm._self = vm
    initLifecycle(vm) // 初始化实例的属性、数据：$parent, $children, $refs, $root, _watcher...等
    initEvents(vm) // 初始化事件：$on, $off, $emit, $once
    initRender(vm) // 初始化渲染： render, mixin
    callHook(vm, 'beforeCreate') // 调用生命周期钩子函数
    initInjections(vm) // 初始化 inject
    initState(vm) // 初始化组件数据：props, data, methods, watch, computed
    initProvide(vm) // 初始化 provide
    callHook(vm, 'created') // 调用生命周期钩子函数

    if (vm.$options.el) {
      // 如果传了 el 就会调用 $mount 进入模板编译和挂载阶段
      // 如果没有传就需要手动执行 $mount 才会进入下一阶段
      vm.$mount(vm.$options.el)
    }
  }
}
```

#### new Vue

从 `new Vue(options)` 开始作为入口，`Vue` 只是一个简单的构造函数，内部是这样的：

```js
function Vue (options) {
  this._init(options)
}
```

进入了 `_init` 函数之后，先初始化了一些属性。

1.  `initLifecycle`：初始化一些属性如`$parent`，`$children`。根实例没有 `$parent`，`$children` 开始是空数组，直到它的 `子组件` 实例进入到 `initLifecycle` 时，才会往父组件的 `$children` 里把自身放进去。所以 `$children` 里的一定是组件的实例。
2.  `initEvents`：初始化事件相关的属性，如 `_events` 等。
3.  `initRender`：初始化渲染相关如 `$createElement`，并且定义了 `$attrs` 和 `$listeners` 为`浅层`响应式属性。具体可以查看`细节`章节。并且还定义了`$slots`、`$scopedSlots`，其中 `$slots` 是立刻赋值的，但是 `$scopedSlots` 初始化的时候是一个 `emptyObject`，直到组件的 `vm._render` 过程中才会通过 `normalizeScopedSlots` 去把真正的 `$scopedSlots` 整合后挂到 `vm` 上。

然后开始第一个生命周期：

```js
callHook(vm, 'beforeCreate')
```

#### beforeCreate被调用完成

`beforeCreate` 之后

1.  初始化 `inject`
2.  初始化  `state`
    -   初始化 `props`
    -   初始化 `methods`
    -   初始化 `data`
    -   初始化 `computed`
    -   初始化 `watch`
3.  初始化 `provide`

所以在 `data` 中可以使用 `props` 上的值，反过来则不行

然后进入 `created` 阶段：

```js
callHook(vm, 'created')
```

#### created被调用完成

调用 `$mount` 方法，开始挂载组件到 `dom` 上。

如果使用了 `runtime-with-compile` 版本，则会把你传入的 `template` 选项，或者 `html` 文本，通过一系列的编译生成 `render` 函数。

-   编译这个 `template`，生成 `ast` 抽象语法树。
-   优化这个 `ast`，标记静态节点。（渲染过程中不会变的那些节点，优化性能）。
-   根据 `ast`，生成 `render` 函数。

对应具体的代码就是：

```js
const ast = parse(template.trim(), options)
if (options.optimize !== false) {
  optimize(ast, options)
}
const code = generate(ast, options)
```

如果是脚手架搭建的项目的话，这一步 `vue-cli` 已经帮你做好了，所以就直接进入 `mountComponent` 函数。

那么，确保有了 `render` 函数后，我们就可以往`渲染`的步骤继续进行了

#### beforeMount被调用完成

把 `渲染组件的函数` 定义好，具体代码是：

```js
updateComponent = () => {
  vm._update(vm._render(), hydrating)
}
```

拆解来看，`vm._render` 其实就是调用我们上一步拿到的 `render` 函数生成一个 `vnode`，而 `vm._update` 方法则会对这个 `vnode` 进行 `patch` 操作，帮我们把 `vnode` 通过 `createElm`函数创建新节点并且渲染到 `dom节点` 中

接下来就是执行这段代码了，是由 `响应式原理` 的一个核心类 `Watcher` 负责执行这个函数，为什么要它来代理执行呢？因为我们需要在这段过程中去 `观察` 这个函数读取了哪些响应式数据，将来这些**响应式数据更新**的时候，我们需要重新执行 `updateComponent` 函数

如果是更新后调用 `updateComponent` 函数的话，`updateComponent` 内部的 `patch` 就不再是初始化时候的创建节点，而是对新旧 `vnode` 进行 `diff`，最小化的更新到 `dom节点` 上去

这一切交给 `Watcher` 完成：

```js
new Watcher(vm, updateComponent, noop, {
  before () {
    if (vm._isMounted) {
      callHook(vm, 'beforeUpdate')
    }
  }
}, true /* isRenderWatcher */)
```

注意这里在`before` 属性上定义了`beforeUpdate` 函数，也就是说在 `Watcher` 被响应式属性的更新触发之后，重新渲染新视图之前，会先调用 `beforeUpdate` 生命周期。

注意，在 `render` 的过程中，如果遇到了 `子组件`，则会调用 `createComponent` 函数。

`createComponent` 函数内部，会为子组件生成一个属于自己的`构造函数`，可以理解为子组件自己的 `Vue` 函数：

```js
Ctor = baseCtor.extend(Ctor)
```

在普通的场景下，其实这就是 `Vue.extend` 生成的构造函数，它继承自 `Vue` 函数，拥有它的很多全局属性。

这里插播一个知识点，除了组件有自己的`生命周期`外，其实 `vnode` 也是有自己的 `生命周期的`，只不过我们平常开发的时候是接触不到的。

那么`子组件的 vnode` 会有自己的 `init` 周期，这个周期内部会做这样的事情：

```js
// 创建子组件
const child = createComponentInstanceForVnode(vnode)
// 挂载到 dom 上
child.$mount(vnode.elm)
```

而 `createComponentInstanceForVnode` 内部又做了什么事呢？它会去调用 `子组件` 的构造函数。

```js
new vnode.componentOptions.Ctor(options)
```

构造函数的内部是这样的：

```js
const Sub = function VueComponent (options) {
  this._init(options)
}
```

这个 `_init` 其实就是我们文章开头的那个函数，也就是说，如果遇到 `子组件`，那么就会优先开始`子组件`的构建过程，也就是说，从 `beforeCreated` 重新开始。这是一个递归的构建过程。

也就是说，如果我们有 `父 -> 子 -> 孙` 这三个组件，那么它们的初始化生命周期顺序是这样的：

```
父 beforeCreate 
父 create 
父 beforeMount 
子 beforeCreate 
子 create 
子 beforeMount 
孙 beforeCreate 
孙 create 
孙 beforeMount 
孙 mounted 
子 mounted 
父 mounted 
```

然后，`mounted` 生命周期被触发

#### mounted被调用完成

到此为止，组件的挂载就完成了，初始化的生命周期结束

#### 更新流程

当一个响应式属性被更新后，触发了 `Watcher` 的回调函数，也就是 `vm._update(vm._render())`，在更新之前，会先调用刚才在 `before` 属性上定义的函数，也就是

```js
callHook(vm, 'beforeUpdate')
```

注意，由于 Vue 的异步更新机制，`beforeUpdate` 的调用已经是在 `nextTick` 中了。 具体代码如下：

```js
nextTick(flushSchedulerQueue)

function flushSchedulerQueue {
  for (index = 0; index < queue.length; index++) {
    watcher = queue[index]
    if (watcher.before) {
     // callHook(vm, 'beforeUpdate')
      watcher.before()
    }
 }
}
```

#### beforeUpdate被调用完成

然后经历了一系列的 `patch`、`diff` 流程后，组件重新渲染完毕，调用 `updated` 钩子。

注意，这里是对 `watcher` 倒序 `updated` 调用的。

也就是说，假如同一个属性通过 `props` 分别流向 `父 -> 子 -> 孙` 这个路径，那么收集到依赖的先后也是这个顺序，但是触发 `updated` 钩子确是 `孙 -> 子 -> 父` 这个顺序去触发的。

```js
function callUpdatedHooks (queue) {
  let i = queue.length
  while (i--) {
    const watcher = queue[i]
    const vm = watcher.vm
    if (vm._watcher === watcher && vm._isMounted) {
      callHook(vm, 'updated')
    }
  }
}
```

#### updated被调用完成

至此，渲染更新流程完毕

#### 销毁流程

在刚刚所说的更新后的 `patch` 过程中，如果发现有组件在下一轮渲染中消失了，比如 `v-for` 对应的数组中少了一个数据。那么就会调用 `removeVnodes` 进入组件的销毁流程。

`removeVnodes` 会调用 `vnode` 的 `destroy` 生命周期，而 `destroy` 内部则会调用我们相对比较熟悉的 `vm.$destroy()`。（keep-alive 包裹的子组件除外）

这时，就会调用 `callHook(vm, 'beforeDestroy')`

#### beforeDestroy被调用完成

之后就会经历一系列的`清理`逻辑，清除父子关系、`watcher` 关闭等逻辑。但是注意，`$destroy` 并不会把组件从视图上移除，如果想要手动销毁一个组件，则需要我们自己去完成这个逻辑。

然后，调用最后的 `callHook(vm, 'destroyed')`

#### destroyed被调用完成

## 组件间通信

### 1.vue2的组件间通信方式

组件通信的方式如下：

#### （1） props  /  $emit

父组件通过`props`向子组件传递数据，子组件通过`$emit`和父组件通信

##### 1. 父组件向子组件传值

- `props`只能是父组件向子组件进行传值，`props`使得父子组件之间形成了一个单向下行绑定。子组件的数据会随着父组件不断更新。
- `props` 可以显示定义一个或一个以上的数据，对于接收的数据，可以是各种数据类型，同样也可以传递一个函数。
- `props`属性名规则：若在`props`中使用驼峰形式，模板中需要使用短横线的形式

```javascript
// 父组件
<template>
    <div id="father">
        <son :msg="msgData" :fn="myFunction"></son>
    </div>
</template>

<script>
import son from "./son.vue";
export default {
    name: father,
    data() {
        msgData: "父组件数据";
    },
    methods: {
        myFunction() {
            console.log("vue");
        }
    },
    components: {
        son
    }
};
</script>

// 子组件
<template>
    <div id="son">
        <p>{{msg}}</p>
        <button @click="fn">按钮</button>
    </div>
</template>
<script>
export default {
    name: "son",
    props: ["msg", "fn"]
};
</script>
```

##### 2. 子组件向父组件传值

- `$emit`绑定一个自定义事件，当这个事件被执行的时就会将参数传递给父组件，而父组件通过`v-on`监听并接收参数。

```javascript
// 父组件
<template>
  <div class="section">
    <com-article :articles="articleList" @onEmitIndex="onEmitIndex"></com-article>
    <p>{{currentIndex}}</p>
  </div>
</template>

<script>
import comArticle from './test/article.vue'
export default {
  name: 'comArticle',
  components: { comArticle },
  data() {
    return {
      currentIndex: -1,
      articleList: ['红楼梦', '西游记', '三国演义']
    }
  },
  methods: {
    onEmitIndex(idx) {
      this.currentIndex = idx
    }
  }
}
</script>


//子组件
<template>
  <div>
    <div v-for="(item, index) in articles" :key="index" @click="emitIndex(index)">{{item}}</div>
  </div>
</template>

<script>
export default {
  props: ['articles'],
  methods: {
    emitIndex(index) {
      this.$emit('onEmitIndex', index) // 触发父组件的方法，并传递参数index
    }
  }
}
</script>
```

#### （2）eventBus事件总线（`$emit / $on`）

`eventBus`事件总线适用于**父子组件**、**非父子组件**等之间的通信，使用步骤如下： **（1）创建事件中心管理组件之间的通信**

```javascript
// event-bus.js

import Vue from 'vue'
export const EventBus = new Vue()

```

**（2）发送事件** 假设有两个兄弟组件`firstCom`和`secondCom`：

```javascript
<template>
  <div>
    <first-com></first-com>
    <second-com></second-com>
  </div>
</template>

<script>
import firstCom from './firstCom.vue'
import secondCom from './secondCom.vue'
export default {
  components: { firstCom, secondCom }
}
</script>
```

在`firstCom`组件中发送事件：

```javascript
<template>
  <div>
    <button @click="add">加法</button>    
  </div>
</template>

<script>
import {EventBus} from './event-bus.js' // 引入事件中心

export default {
  data(){
    return{
      num:0
    }
  },
  methods:{
    add(){
      EventBus.$emit('addition', {
        num:this.num++
      })
    }
  }
}
</script>
```

**（3）接收事件** 在`secondCom`组件中发送事件：

```javascript
<template>
  <div>求和: {{count}}</div>
</template>

<script>
import { EventBus } from './event-bus.js'
export default {
  data() {
    return {
      count: 0
    }
  },
  mounted() {
    EventBus.$on('addition', param => {
      this.count = this.count + param.num;
    })
  }
}
</script>
```

在上述代码中，这就相当于将`num`值存贮在了事件总线中，在其他组件中可以直接访问。事件总线就相当于一个桥梁，不用组件通过它来通信。

虽然看起来比较简单，但是这种方法也有不变之处，如果项目过大，使用这种方式进行通信，后期维护起来会很困难。

#### （3）依赖注入（provide / inject）

这种方式就是Vue中的**依赖注入**，该方法用于**父子组件之间的通信**。当然这里所说的父子不一定是真正的父子，也可以是祖孙组件，在层数很深的情况下，可以使用这种方法来进行传值。就不用一层一层的传递了。

`provide / inject`是Vue提供的两个钩子，和`data`、`methods`是同级的。并且`provide`的书写形式和`data`一样。

- `provide` 钩子用来发送数据或方法
- `inject`钩子用来接收数据或方法

在父组件中：

```javascript
provide() { 
    return {     
        num: this.num  
    };
}
```

在子组件中：

```javascript
inject: ['num']
```

还可以这样写，这样写就可以访问父组件中的所有属性：

```javascript
provide() {
 return {
    app: this
  };
}
data() {
 return {
    num: 1
  };
}

inject: ['app']
console.log(this.app.num)
```

**注意：** 依赖注入所提供的属性是**非响应式**的。

#### （3）ref / $refs

这种方式也是实现**父子组件**之间的通信。

`ref`： 这个属性用在子组件上，它的引用就指向了子组件的实例。可以通过实例来访问组件的数据和方法。

在子组件中：

```javascript
export default {
  data () {
    return {
      name: 'JavaScript'
    }
  },
  methods: {
    sayHello () {
      console.log('hello')
    }
  }
}
```

在父组件中：

```javascript
<template>
  <child ref="child"></component-a>
</template>
<script>
  import child from './child.vue'
  export default {
    components: { child },
    mounted () {
      console.log(this.$refs.child.name);  // JavaScript
      this.$refs.child.sayHello();  // hello
    }
  }
</script>
```

#### （4）`$parent / $children`

- 使用`$parent`可以让组件访问父组件的实例（访问的是上一级父组件的属性和方法）
- 使用`$children`可以让组件访问子组件的实例，但是，`$children`并不能保证顺序，并且访问的数据也不是响应式的。

在子组件中：

```javascript
<template>
  <div>
    <span>{{message}}</span>
    <p>获取父组件的值为:  {{parentVal}}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Vue'
    }
  },
  computed:{
    parentVal(){
      return this.$parent.msg;
    }
  }
}
</script>
```

在父组件中：

```javascript
// 父组件中
<template>
  <div class="hello_world">
    <div>{{msg}}</div>
    <child></child>
    <button @click="change">点击改变子组件值</button>
  </div>
</template>

<script>
import child from './child.vue'
export default {
  components: { child },
  data() {
    return {
      msg: 'Welcome'
    }
  },
  methods: {
    change() {
      // 获取到子组件
      this.$children[0].message = 'JavaScript'
    }
  }
}
</script>
```

在上面的代码中，子组件获取到了父组件的`parentVal`值，父组件改变了子组件中`message`的值。 **需要注意：**

- 通过`$parent`访问到的是上一级父组件的实例，可以使用`$root`来访问根组件的实例
- 在组件中使用`$children`拿到的是所有的子组件的实例，它是一个数组，并且是无序的
- 在根组件`#app`上拿`$parent`得到的是`new Vue()`的实例，在这实例上再拿`$parent`得到的是`undefined`，而在最底层的子组件拿`$children`是个空数组
- `$children` 的值是**数组**，而`$parent`是个**对象**

#### （5）`$attrs / $listeners`

考虑一种场景，如果A是B组件的父组件，B是C组件的父组件。如果想要组件A给组件C传递数据，这种隔代的数据，该使用哪种方式呢？

如果是用`props/$emit`来一级一级的传递，确实可以完成，但是比较复杂；如果使用事件总线，在多人开发或者项目较大的时候，维护起来很麻烦；如果使用Vuex，的确也可以，但是如果仅仅是传递数据，那可能就有点浪费了。

针对上述情况，Vue引入了`$attrs / $listeners`，实现组件之间的跨代通信。

先来看一下`inheritAttrs`，它的默认值true，继承所有的父组件属性除`props`之外的所有属性；`inheritAttrs：false` 只继承class属性 。

- `$attrs`：继承所有的父组件属性（除了prop传递的属性、class 和 style ），一般用在子组件的子元素上
- `$listeners`：该属性是一个对象，里面包含了作用在这个组件上的所有监听器，可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素。（相当于子组件继承父组件的事件）

A组件（`APP.vue`）：

```javascript
<template>
    <div id="app">
        //此处监听了两个事件，可以在B组件或者C组件中直接触发 
        <child1 :p-child1="child1" :p-child2="child2" @test1="onTest1" @test2="onTest2"></child1>
    </div>
</template>
<script>
import Child1 from './Child1.vue';
export default {
    components: { Child1 },
    methods: {
        onTest1() {
            console.log('test1 running');
        },
        onTest2() {
            console.log('test2 running');
        }
    }
};
</script>
```

B组件（`Child1.vue`）：

```javascript
<template>
    <div class="child-1">
        <p>props: {{pChild1}}</p>
        <p>$attrs: {{$attrs}}</p>
        <child2 v-bind="$attrs" v-on="$listeners"></child2>
    </div>
</template>
<script>
import Child2 from './Child2.vue';
export default {
    props: ['pChild1'],
    components: { Child2 },
    inheritAttrs: false,
    mounted() {
        this.$emit('test1'); // 触发APP.vue中的test1方法
    }
};
</script>
```

C 组件 (`Child2.vue`)：

```javascript
<template>
    <div class="child-2">
        <p>props: {{pChild2}}</p>
        <p>$attrs: {{$attrs}}</p>
    </div>
</template>
<script>
export default {
    props: ['pChild2'],
    inheritAttrs: false,
    mounted() {
        this.$emit('test2');// 触发APP.vue中的test2方法
    }
};
</script>
```

在上述代码中：

- C组件中能直接触发test的原因在于 B组件调用C组件时 使用 v-on 绑定了`$listeners` 属性
- 在B组件中通过v-bind 绑定`$attrs`属性，C组件可以直接获取到A组件中传递下来的props（除了B组件中props声明的）

#### （6）总结

**（1）父子组件间通信**

- 子组件通过 props 属性来接受父组件的数据，然后父组件在子组件上注册监听事件，子组件通过 emit 触发事件来向父组件发送数据。
- 通过 ref 属性给子组件设置一个名字。父组件通过 `$refs` 组件名来获得子组件，子组件通过 `$parent` 获得父组件，这样也可以实现通信。
- 使用 provide/inject，在父组件中通过 provide提供变量，在子组件中通过 inject 来将变量注入到组件中。不论子组件有多深，只要调用了 inject 那么就可以注入 provide中的数据。

**（2）兄弟组件间通信**

- 使用 eventBus 的方法，它的本质是通过创建一个空的 Vue 实例来作为消息传递的对象，通信的组件引入这个实例，通信的组件通过在这个实例上监听和触发事件，来实现消息的传递。
- 通过 `$parent/$refs` 来获取到兄弟组件，也可以进行通信。

**（3）任意组件之间**

- 使用 eventBus ，其实就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件。

如果业务逻辑复杂，很多组件之间需要同时处理一些公共的数据，这个时候采用上面这一些方法可能不利于项目的维护。这个时候可以使用 vuex ，vuex 的思想就是将这一些公共的数据抽离出来，将它作为一个全局的变量来管理，然后其他组件就可以对这个公共数据进行读写操作，这样达到了解耦的目的。

### 2.vue3的组件间通信方式

七种组件通信方式：

- props
- emit
- v-model
- refs
- provide/inject
- eventBus
- vuex/pinia(状态管理工具)

#### Props方式

`Props`方式是Vue中最常见的一种**父传子**的一种方式，使用也比较简单。

根据上面的demo，我们将数据以及对数据的操作定义在父组件，子组件仅做列表的一个渲染；

父组件代码如下：

```html
<template>
  <!-- 子组件 -->
  <child-components :list="list"></child-components>
  <!-- 父组件 -->
  <div class="child-wrap input-group">
    <input
      v-model="value"
      type="text"
      class="form-control"
      placeholder="请输入"
    />
    <div class="input-group-append">
      <button @click="handleAdd" class="btn btn-primary" type="button">
        添加
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref } from 'vue'
import ChildComponents from './child.vue'
const list = ref(['JavaScript', 'HTML', 'CSS'])
const value = ref('')
// add 触发后的事件处理函数
const handleAdd = () => {
  list.value.push(value.value)
  value.value = ''
}
</script>

```

子组件只需要对父组件传递的值进行渲染即可，代码如下：

```html
<template>
  <ul class="parent list-group">
    <li class="list-group-item" v-for="i in props.list" :key="i">{{ i }}</li>
  </ul>
</template>
<script setup>
import { defineProps } from 'vue'
const props = defineProps({
  list: {
    type: Array,
    default: () => [],
  },
})
</script>
```

#### emit方式

`emit`方式也是Vue中最常见的组件通信方式，该方式用于**子传父**；

根据上面的demo，我们将列表定义在父组件，子组件只需要传递添加的值即可。

子组件代码如下：

```html
<template>
  <div class="child-wrap input-group">
    <input
      v-model="value"
      type="text"
      class="form-control"
      placeholder="请输入"
    />
    <div class="input-group-append">
      <button @click="handleSubmit" class="btn btn-primary" type="button">
        添加
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref, defineEmits } from 'vue'
const value = ref('')
const emits = defineEmits(['add'])
const handleSubmit = () => {
  emits('add', value.value)
  value.value = ''
}
</script>
```

在子组件中点击【添加】按钮后，`emit`一个自定义事件，并将添加的值作为参数传递。

父组件代码如下：

```html
<template>
  <!-- 父组件 -->
  <ul class="parent list-group">
    <li class="list-group-item" v-for="i in list" :key="i">{{ i }}</li>
  </ul>
  <!-- 子组件 -->
  <child-components @add="handleAdd"></child-components>
</template>
<script setup>
import { ref } from 'vue'
import ChildComponents from './child.vue'
const list = ref(['JavaScript', 'HTML', 'CSS'])
// add 触发后的事件处理函数
const handleAdd = value => {
  list.value.push(value)
}
</script>

```

在父组件中只需要监听子组件自定义的事件，然后执行对应的添加操作。

#### v-model方式

`v-model`是Vue中一个比较出色的语法糖，就比如下面这段代码

```html
<ChildComponent v-model:title="pageTitle" />

```

就是下面这段代码的简写形势

```html
<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

`v-model`确实简便了不少，现在我们就来看一下上面那个demo，如何用v-model实现。

子组件

```html
<template>
  <div class="child-wrap input-group">
    <input
      v-model="value"
      type="text"
      class="form-control"
      placeholder="请输入"
    />
    <div class="input-group-append">
      <button @click="handleAdd" class="btn btn-primary" type="button">
        添加
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref, defineEmits, defineProps } from 'vue'
const value = ref('')
const props = defineProps({
  list: {
    type: Array,
    default: () => [],
  },
})
const emits = defineEmits(['update:list'])
// 添加操作
const handleAdd = () => {
  const arr = props.list
  arr.push(value.value)
  emits('update:list', arr)
  value.value = ''
}
</script>

```

在子组件中我们首先定义`props`和`emits`，然后添加完成之后`emit`指定事件。

> 注：`update:*`是Vue中的固定写法，`*`表示`props`中的某个属性名。

父组件中使用就比较简单，代码如下：

```html
<template>
  <!-- 父组件 -->
  <ul class="parent list-group">
    <li class="list-group-item" v-for="i in list" :key="i">{{ i }}</li>
  </ul>
  <!-- 子组件 -->
  <child-components v-model:list="list"></child-components>
</template>
<script setup>
import { ref } from 'vue'
import ChildComponents from './child.vue'
const list = ref(['JavaScript', 'HTML', 'CSS'])
</script>
```

#### refs方式

在使用选项式API时，我们可以通过`this.$refs.name`的方式获取指定元素或者组件，但是组合式API中就无法使用哪种方式获取。如果我们想要通过`ref`的方式获取组件或者元素，需要定义一个同名的Ref对象，在组件挂载后就可以访问了。

示例代码如下：

```html
<template>
  <ul class="parent list-group">
    <li class="list-group-item" v-for="i in childRefs?.list" :key="i">
      {{ i }}
    </li>
  </ul>
  <!-- 子组件 ref的值与<script>中的保持一致 -->
  <child-components ref="childRefs"></child-components>
  <!-- 父组件 -->
</template>
<script setup>
import { ref } from 'vue'
import ChildComponents from './child.vue'
const childRefs = ref(null)
</script>
```

子组件代码如下：

```html
<template>
  <div class="child-wrap input-group">
    <input
      v-model="value"
      type="text"
      class="form-control"
      placeholder="请输入"
    />
    <div class="input-group-append">
      <button @click="handleAdd" class="btn btn-primary" type="button">
        添加
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref, defineExpose } from 'vue'
const list = ref(['JavaScript', 'HTML', 'CSS'])
const value = ref('')
// add 触发后的事件处理函数
const handleAdd = () => {
  list.value.push(value.value)
  value.value = ''
}
defineExpose({ list })
</script>
```

`setup`组件默认是关闭的，也即通过模板`ref`获取到的组件的公开实例，**不会暴露任何在****`<script setup>`***\*中声明的绑定\**。如果需要**公开需要通过****`defineExpose`**** API暴露**。

#### provide/inject方式

`provide`和`inject`是Vue中提供的一对API，该API可以实现父组件向子组件传递数据，无论层级有多深，都可以通过这对API实现。示例代码如下所示：

父组件

```html
<template>
  <!-- 子组件 -->
  <child-components></child-components>
  <!-- 父组件 -->
  <div class="child-wrap input-group">
    <input
      v-model="value"
      type="text"
      class="form-control"
      placeholder="请输入"
    />
    <div class="input-group-append">
      <button @click="handleAdd" class="btn btn-primary" type="button">
        添加
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref, provide } from 'vue'
import ChildComponents from './child.vue'
const list = ref(['JavaScript', 'HTML', 'CSS'])
const value = ref('')
// 向子组件提供数据
provide('list', list.value)
// add 触发后的事件处理函数
const handleAdd = () => {
  list.value.push(value.value)
  value.value = ''
}
</script>
```

子组件

```html
<template>
  <ul class="parent list-group">
    <li class="list-group-item" v-for="i in list" :key="i">{{ i }}</li>
  </ul>
</template>
<script setup>
import { inject } from 'vue'
// 接受父组件提供的数据
const list = inject('list')
</script>
```

值得注意的是**使用`provide`进行数据传递时，尽量`readonly`进行数据的包装，避免子组件修改父级传递过去的数据**。

#### 事件总线

Vue3中移除了事件总线，但是可以借助于第三方工具来完成，Vue官方推荐[mitt](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fmitt)或[tiny-emitter](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Ftiny-emitter)；

在大多数情况下不推荐使用全局事件总线的方式来实现组件通信，虽然比较简单粗暴，但是长久来说维护事件总线是一个大难题，所以这里就不展开讲解了，具体可以阅读具体工具的文档

## 路由

### 1. Vue-Router 的懒加载如何实现

非懒加载：

```javascript
import List from '@/components/list.vue'
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
复制代码
```

（1）方案一(常用)：使用箭头函数+import动态加载

```javascript
const List = () => import('@/components/list.vue')
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
复制代码
```

（2）方案二：使用箭头函数+require动态加载

```javascript
const router = new Router({
  routes: [
   {
     path: '/list',
     component: resolve => require(['@/components/list'], resolve)
   }
  ]
})
复制代码
```

（3）方案三：使用webpack的require.ensure技术，也可以实现按需加载。 这种情况下，多个路由指定相同的chunkName，会合并打包成一个js文件。

```javascript
// r就是resolve
const List = r => require.ensure([], () => r(require('@/components/list')), 'list');
// 路由也是正常的写法  这种是官方推荐的写的 按模块划分懒加载 
const router = new Router({
  routes: [
  {
    path: '/list',
    component: List,
    name: 'list'
  }
 ]
}))
```

### 1.hash和history模式区别

Vue-Router有两种模式：**hash模式**和**history模式**。默认的路由模式是hash模式。	

#### 1. hash模式

**简介：** hash模式是开发中默认的模式，它的URL带着一个#，例如：[www.abc.com/#/vue](https://link.juejin.cn/?target=http%3A%2F%2Fwww.abc.com%2F%23%2Fvue)，它的hash值就是`#/vue`。

**特点**：hash值会出现在URL里面，但是不会出现在HTTP请求中，对后端完全没有影响。所以改变hash值，不会重新加载页面。这种模式的浏览器支持度很好，低版本的IE浏览器也支持这种模式。hash路由被称为是前端路由，已经成为SPA（单页面应用）的标配。

**原理：** hash模式的主要原理就是**onhashchange()事件**：

```javascript
window.onhashchange = function(event){
	console.log(event.oldURL, event.newURL);
	let hash = location.hash.slice(1);
}
```

使用onhashchange()事件的好处就是，在页面的hash值发生变化时，无需向后端发起请求，window就可以监听事件的改变，并按规则加载相应的代码。除此之外，hash值变化对应的URL都会被浏览器记录下来，这样浏览器就能实现页面的前进和后退。虽然是没有请求后端服务器，但是页面的hash值和对应的URL关联起来了。

#### 2. history模式

**简介：** history模式的URL中没有#，它使用的是传统的路由分发模式，即用户在输入一个URL时，服务器会接收这个请求，并解析这个URL，然后做出相应的逻辑处理。 **特点：** 当使用history模式时，URL就像这样：[abc.com/user/id](https://link.juejin.cn/?target=http%3A%2F%2Fabc.com%2Fuser%2Fid)。相比hash模式更加好看。但是，history模式需要后台配置支持。如果后台没有正确配置，访问时会返回404。 **API：** history api可以分为两大部分，切换历史状态和修改历史状态：

- **修改历史状态**：包括了 HTML5 History Interface 中新增的 `pushState()` 和 `replaceState()` 方法，这两个方法应用于浏览器的历史记录栈，提供了对历史记录进行修改的功能。只是当他们进行修改时，虽然修改了url，但浏览器不会立即向后端发送请求。如果要做到改变url但又不刷新页面的效果，就需要前端用上这两个API。
- **切换历史状态：** 包括`forward()`、`back()`、`go()`三个方法，对应浏览器的前进，后退，跳转操作。

history.state    用于存储2个方法的data数据，不同浏览器的读写权限不一样

响应pushState或者replaceState的调用

 每当活动的历史记录项发生变化时， `popstate` 事件都会被传递给window对象。如果当前活动的历史记录项是被 `pushState` 创建的，或者是由 `replaceState` 改变的，那么 `popstate` 事件的状态属性 `state` 会包含一个当前历史记录状态对象的拷贝



虽然history模式丢弃了丑陋的#。但是，它也有自己的缺点，就是在刷新页面的时候，如果没有相应的路由或资源，就会刷出404来。

如果想要切换到history模式，就要进行以下配置（后端也要进行配置）：

```javascript
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

#### 3. 两种模式对比

调用 history.pushState() 相比于直接修改 hash，存在以下优势:

- pushState() 设置的新 URL 可以是与当前 URL 同源的任意 URL；而 hash 只可修改 # 后面的部分，因此只能设置与当前 URL 同文档的 URL；
- pushState() 设置的新 URL 可以与当前 URL 一模一样，这样也会把记录添加到栈中；而 hash 设置的新值必须与原来不一样才会触发动作将记录添加到栈中；
- pushState() 通过 stateObject 参数可以添加任意类型的数据到记录中；而 hash 只可添加短字符串；
- pushState() 可额外设置 title 属性供后续使用。
- hash模式下，仅hash符号之前的url会被包含在请求中，后端如果没有做到对路由的全覆盖，也不会返回404错误；history模式下，前端的url必须和实际向后端发起请求的url一致，如果没有对用的路由处理，将返回404错误。

hash模式和history模式都有各自的优势和缺陷，还是要根据实际情况选择性的使用。

### 2.前端路由和后端路由的区别

1.什么是路由

路由是根据不同的url展示不同的内容或页面。

2.什么是前端路由

特点：不向后台发送请求，不刷新页面，前后端分离

前端路由即响应页面内容的任务是由前端来做的，根据不同的url更新页面的内容，随着SPA（单页面应用）的普遍使用，前后端开发分离，项目中基本都使用前端路由，通过路由实现页面的变化。例如，通过vue开发的SPA中，切换路由，并不刷新页面，而是根据路由在虚拟DOM中加载所需要的数据，实现页面内容的改变。

3.什么是后端路由

特点：向服务器发送请求，会刷新页面，前后端不能分离

在浏览器的地址栏中切换不同的url时，每次都向后台服务器发出请求，服务器根据不同的响应不同的数据，浏览器接收到数据后再进行渲染，所以后端路由会刷新页面，如果网速慢的话，就会看到一个空白页面等待服务端返回数据，后台路由最大的问题就是不能前后端分离。

4.什么时候使用前端路由

在单页面应用中，大部分页面结构不变，只改变部分内容时使用

5.前端路由的优缺点

优点：

1.用户体验好，页面初始化后，只需要根据路由变换页面内容，不需要再向服务器发送请求，内容变换速度快。

2.可以在浏览器中输入指定想要访问的url

3.实现了前后端分离，方便开发。

缺点：

1.使用浏览器的前进、后退键的时候会重新发送请求，没有合理的利用缓存

2.单页面无法记住之前滚动的位置，无法在前进、后退的时候记住滚动的位置

### 3.如何获取页面的hash变化

**（1）监听$route的变化**

```javascript
// 监听,当路由发生变化的时候执行
watch: {
  $route: {
    handler: function(val, oldVal){
      console.log(val);
    },
    // 深度观察监听
    deep: true
  }
},
```

**（2）window.location.hash读取#值** window.location.hash 的值可读可写，读取来判断状态是否改变，写入时可以在不重载网页的前提下，添加一条历史访问记录。

### 4.route和router的区别

- $route 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数
- $router 是“路由实例”对象包括了路由的跳转方法，钩子函数等

### 5.params和query的区别

#### 1.跳转方式不同

query可以使用name或者path方式跳转

```js
//query传参，使用name跳转
this.$router.push({
    name:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})

//query传参，使用path跳转
this.$router.push({
    path:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})

//query传参接收
this.queryName = this.$route.query.queryName;
this.queryId = this.$route.query.queryId;
```

params只能使用name方式进行跳转引入

```js
//params传参 使用name
this.$router.push({
  name:'second',
  params: {
    id:'20180822',
     name: 'query'
  }
})

//params接收参数
this.id = this.$route.params.id ;
this.name = this.$route.params.name ;
```

#### 2.浏览器url显示的不同

```js
//使用params时
//路由 
{ 
    path: '/second/:id/:name', 
    name: 'second', 
    component: () => import('@/view/second') 
}
//传参
this.$router.push({
    name: 'second',
    params: {
        id: '1245314',
        name: 'wendy'
    }
})
//浏览器url地址 页面刷新时参数不会丢失
localhost:8080/second/1245314/wendy
//如果路由后面没有 /:id/:name效果如下图，注意：地址栏没有参数,页面一刷新参数就会丢失
localhost:8080/second
//接收参数
this.$route.params.id
this.$route.params.name
复制代码
//使用query时
//传参
this.$router.push({
    name:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})
//浏览器url地址
localhost:8080?queryId='20180822'&queryName='query'
//页面刷新时参数不会丢失，因为参数写在地址栏上了
```

#### params:

1.params是路由的一部分，因此使用params传参，路由上必须写对应的参数；
2.进行路由跳转的时候要传值，否则会跳转页面失败；
3.params只能使用name来传参；
4.params相当于post请求，参数对用来说是不可见的

#### Query:

1.query传参可以使用path,也可以使用name;
2.query相当于get请求，参数拼接在路由的后面；
3.query是拼接在路由后面的，因此有没有没关系；

### 6.动态路由怎么定义

什么是动态路由 

**（1）param方式**

- 配置路由格式：`/router/:id`
- 传递的方式：在path后面跟上对应的值
- 传递后形成的路径：`/router/123`

1）路由定义

```javascript
//在APP.vue中
<router-link :to="'/user/'+userId" replace>用户</router-link>    

//在index.js
{
   path: '/user/:userid',
   component: User,
},
```

2）路由跳转

```javascript
// 方法1：
<router-link :to="{ name: 'users', params: { uname: wade }}">按钮</router-link

// 方法2：
this.$router.push({name:'users',params:{uname:wade}})

// 方法3：
this.$router.push('/user/' + wade)
```

3）参数获取 通过 `$route.params.userid` 获取传递的值

**（2）query方式**

- 配置路由格式：`/router`，也就是普通配置
- 传递的方式：对象中使用query的key作为传递方式
- 传递后形成的路径：`/route?id=123`

1）路由定义

```javascript
//方式1：直接在router-link 标签上以对象的形式
<router-link :to="{path:'/profile',query:{name:'why',age:28,height:188}}">档案</router-link>

// 方式2：写成按钮以点击事件形式
<button @click='profileClick'>我的</button>    

profileClick(){
  this.$router.push({
    path: "/profile",
    query: {
        name: "kobi",
        age: "28",
        height: 198
    }
  });
}
```

2）跳转方法

```javascript
// 方法1：
<router-link :to="{ name: 'users', query: { uname: james }}">按钮</router-link>

// 方法2：
this.$router.push({ name: 'users', query:{ uname:james }})

// 方法3：
<router-link :to="{ path: '/user', query: { uname:james }}">按钮</router-link>

// 方法4：
this.$router.push({ path: '/user', query:{ uname:james }})

// 方法5：
this.$router.push('/user?uname=' + jsmes)
```

3）获取参数

```javascript
通过$route.query 获取传递的值
```

### 7.VueRouter和Location.href跳转方式的区别

- 使用 `location.href= /url `来跳转，简单方便，但是刷新了页面；
- 使用 `history.pushState( /url )` ，无刷新页面，静态跳转；
- 引进 router ，然后使用 `router.push( /url )` 来跳转，使用了 `diff` 算法，实现了按需加载，减少了 dom 的消耗。其实使用 router 跳转和使用 `history.pushState()` 没什么差别的，因为vue-router就是用了 `history.pushState()` ，尤其是在history模式下。

### 8.Vue-Router 的懒加载如何实现

非懒加载：

```javascript
import List from '@/components/list.vue'
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
```

（1）方案一(常用)：使用箭头函数+import动态加载

```javascript
const List = () => import('@/components/list.vue')
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
```

（2）方案二：使用箭头函数+require动态加载

```javascript
const router = new Router({
  routes: [
   {
     path: '/list',
     component: resolve => require(['@/components/list'], resolve)
   }
  ]
})
```

（3）方案三：使用webpack的require.ensure技术，也可以实现按需加载。 这种情况下，多个路由指定相同的chunkName，会合并打包成一个js文件。

```javascript
// r就是resolve
const List = r => require.ensure([], () => r(require('@/components/list')), 'list');
// 路由也是正常的写法  这种是官方推荐的写的 按模块划分懒加载 
const router = new Router({
  routes: [
  {
    path: '/list',
    component: List,
    name: 'list'
  }
 ]
}))
```

### 9.路由导航守卫

- 全局前置/钩子：beforeEach、beforeResolve、afterEach
- 路由独享的守卫：beforeEnter
- 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

### 6. Vue-router 路由钩子在生命周期的体现

一、Vue-Router导航守卫

有的时候，需要通过路由来进行一些操作，比如最常见的登录权限验证，当用户满足条件时，才让其进入导航，否则就取消跳转，并跳到登录页面让其登录。 为此有很多种方法可以植入路由的导航过程：全局的，单个路由独享的，或者组件级的

1. 全局路由钩子

vue-router全局有三个路由钩子;

- router.beforeEach 全局前置守卫 进入路由之前
- router.beforeResolve 全局解析守卫（2.5.0+）在 beforeRouteEnter 调用之后调用
- router.afterEach 全局后置钩子 进入路由之后

具体使用∶

- beforeEach（判断是否登录了，没登录就跳转到登录页）

```javascript
router.beforeEach((to, from, next) => {  
    let ifInfo = Vue.prototype.$common.getSession('userData');  // 判断是否登录的存储信息
    if (!ifInfo) { 
        // sessionStorage里没有储存user信息    
        if (to.path == '/') { 
            //如果是登录页面路径，就直接next()      
            next();    
        } else { 
            //不然就跳转到登录      
            Message.warning("请重新登录！");     
            window.location.href = Vue.prototype.$loginUrl;    
        }  
    } else {    
        return next();  
    }
})
复制代码
```

- afterEach （跳转之后滚动条回到顶部）

```javascript
router.afterEach((to, from) => {  
    // 跳转之后滚动条回到顶部  
    window.scrollTo(0,0);
});
复制代码
```

1. 单个路由独享钩子

**beforeEnter** 如果不想全局配置守卫的话，可以为某些路由单独配置守卫，有三个参数∶ to、from、next

```javascript
export default [    
    {        
        path: '/',        
        name: 'login',        
        component: login,        
        beforeEnter: (to, from, next) => {          
            console.log('即将进入登录页面')          
            next()        
        }    
    }
]
复制代码
```

1. 组件内钩子

beforeRouteUpdate、beforeRouteEnter、beforeRouteLeave

这三个钩子都有三个参数∶to、from、next

- beforeRouteEnter∶ 进入组件前触发
- beforeRouteUpdate∶ 当前地址改变并且改组件被复用时触发，举例来说，带有动态参数的路径foo/∶id，在 /foo/1 和 /foo/2 之间跳转的时候，由于会渲染同样的foa组件，这个钩子在这种情况下就会被调用
- beforeRouteLeave∶ 离开组件被调用

注意点，beforeRouteEnter组件内还访问不到this，因为该守卫执行前组件实例还没有被创建，需要传一个回调给 next来访问，例如：

```javascript
beforeRouteEnter(to, from, next) {      
    next(target => {        
        if (from.path == '/classProcess') {          
            target.isFromProcess = true        
        }      
    })    
}
```

二、Vue路由钩子在生命周期函数的体现

1. 完整的路由导航解析流程（不包括其他生命周期）

- 触发进入其他路由。
- 调用要离开路由的组件守卫beforeRouteLeave
- 调用局前置守卫∶ beforeEach
- 在重用的组件里调用 beforeRouteUpdate
- 调用路由独享守卫 beforeEnter。
- 解析异步路由组件。
- 在将要进入的路由组件中调用 beforeRouteEnter
- 调用全局解析守卫 beforeResolve
- 导航被确认。
- 调用全局后置钩子的 afterEach 钩子。
- 触发DOM更新（mounted）。
- 执行beforeRouteEnter 守卫中传给 next 的回调函数

1. 触发钩子的完整顺序

路由导航、keep-alive、和组件生命周期钩子结合起来的，触发顺序，假设是从a组件离开，第一次进入b组件∶

- beforeRouteLeave：路由组件的组件离开路由前钩子，可取消路由离开。
- beforeEach：路由全局前置守卫，可用于登录验证、全局路由loading等。
- beforeEnter：路由独享守卫
- beforeRouteEnter：路由组件的组件进入路由前钩子。
- beforeResolve：路由全局解析守卫
- afterEach：路由全局后置钩子
- beforeCreate：组件生命周期，不能访问tAis。
- created;组件生命周期，可以访问tAis，不能访问dom。
- beforeMount：组件生命周期
- deactivated：离开缓存组件a，或者触发a的beforeDestroy和destroyed组件销毁钩子。
- mounted：访问/操作dom。
- activated：进入缓存组件，进入a的嵌套子组件（如果有的话）。
- 执行beforeRouteEnter回调函数next。

1. 导航行为被触发到导航完成的整个过程

- 导航行为被触发，此时导航未被确认。
- 在失活的组件里调用离开守卫 beforeRouteLeave。
- 调用全局的 beforeEach守卫。
- 在重用的组件里调用 beforeRouteUpdate 守卫(2.2+)。
- 在路由配置里调用 beforeEnteY。
- 解析异步路由组件（如果有）。
- 在被激活的组件里调用 beforeRouteEnter。
- 调用全局的 beforeResolve 守卫（2.5+），标示解析阶段完成。
- 导航被确认。
- 调用全局的 afterEach 钩子。
- 非重用组件，开始组件实例的生命周期：beforeCreate&created、beforeMount&mounted
- 触发 DOM 更新。
- 用创建好的实例调用 beforeRouteEnter守卫中传给 next 的回调函数。
- 导航完成



### 10.两种路由的工作原理

`Hash`模式的工作原理。

- `URL`中`#`后面的内容作为路径地址，当地址改变的时候不会向服务器发送请求，但是会触发`hashchange`事件。
- 监听`hashchange`事件，在该事件中记录当前的路由地址，然后根据路由地址找到对应组件。
- 根据当前路由地址找到对应组件重新渲染。

`History`模式

- 通过`history.pushState()`方法改变地址栏，并且将当前地址记录到浏览器的历史记录中。当前浏览器不会向服务器发送请求
- 监听`popstate`事件，可以发现浏览器历史操作的变化，记录改变后的地址，单击前进或者是后退按钮的时候触发该事件
- 根据当前路由地址找到对应组件重新渲染

### 11.vue router基本原理

`Vue Router`的核心代码

```js
//注册插件
Vue.use(VueRouter)
//创建路由对象
const router=new VueRouter({
    routes:[
        {name:'home',path:'/',component:homeComponent}
    ]
})
// 创建Vue实例，注册router对象
new Vue({
    router,
    render:h=>h(App)
}).$mount('#apps')
```

use`方法需要的参数可以是一个函数或者是对象，如果传递的是函数，`use`内部会直接调用该函数，

如果传递的是一个对象，那么在`use`内部会调用该对象的`install`方法。

![类图](https://s2.loli.net/2022/04/30/ILdSwKy4FH1oVYv.png)

上半部分是`VueRouter`的属性，而下半部分是`VueRouter`的方法

options`作用是记录构造函数中传入的对象, 我们在创建`Vue Router`的实例的时候，传递了一个对象，而该对象中定义了路由规则。而`options`就是记录传入的这个对象的。

`routeMap`:是一个对象，记录路由地址与组件的对应关系，也就是一个键值对的形式，后期会options中路由规则解析到`routeMap`中。

`data`是一个对象，该对象中有一个属性`current`,该属性用来记录当前的路由地址，`data`是一个响应式的对象，因为当前路由地址发生变化后，对应的组件要发生更新（*也就说当地址变化后，要加载对应组件*）

`install`是一个静态方法，用来实现`Vue`的插件机制。

`Constructor`是一个构造方法，该该构造方法中会初始化`options` ,`data`,`routeMap`这几个属性。

init方法主要是用来调用下面的三个方法，也就把不同的代码分隔到不同的方法中去实现。

`initEvent`方法，用来注册`popstate`事件，

`createRouteMap`方法，该方法会把构造函数中传入进来的路由规则，转换成键值对的形式存储到`routeMap`中。 键就是路由的地址，值就是对应的组件

`initComponents`方法，主要作用是用来创建`router-link`和`router-view`这两个组件的。

#### install方法实现

```
let _Vue = null;
export default class VueRouter {
  //调用install方法的时候，会传递Vue的构造函数
  static install(Vue) {
    //首先判断插件是否已经被安装，如果已经被安装，就不需要重复安装。   
    //1、判断当前插件是否已经被安装:  
    if (VueRouter.install.installed) {
      //条件成立，表明插件已经被安装，什么都不要做。
      return;
    }
    VueRouter.install.installed = true;
    //2、把Vue构造函数记录到全局变量中。
    _Vue = Vue;

    //3、把创建Vue实例时候传入的router对象注入到Vue实例上。
    _Vue.mixin({
      beforeCreate() {
        //在创建Vue实例的时候
        // 也就是new Vue()的时候，才会有$options这个属性，
        //组件中是没有$options这个属性的。
        if (this.$options.router) {
          _Vue.prototype.$router = this.$options.router;
        }
      },
    });
  }
}
```

#### 构造函数

`Constructor`是一个构造方法，该该构造方法中会初始化`options` ,`data`,`routeMap`这几个属性

```js
 constructor(options) {
  
    this.options = options;
 
    this.routeMap = {};
  
    this.data = _Vue.observable({
      current: "/",
    }); 
  }
```

#### createRouteMap方法实现

`createRouteMap`方法，该方法会把构造函数中传入进来的`options`参数中的路由规则，转换成键值对的形式存储到`routeMap`中。 键就是路由的地址，值就是对应的组件

```js
  createRouteMap() {
   
    this.options.routes.forEach((route) => {
      this.routeMap[route.path] = route.component;
    });
  }
```

#### initComponents方法实现

initComponents`方法，主要作用是用来创建`router-link`和`router-view`这两个组件的。

下面先在这个方法中创建`router-link`这个组件。

`<router-link to="/users"> 用户管理</router-link>`

`router-link`这个组件最终会被渲染成`a`标签，同时`to`作为一个属性，其值会作为`a`标签中的`href`属性的值。同时还要获取`<router-link>`这个组件中的文本，作为最终超链接的文本

```js
 initComponents(Vue) {
    Vue.component("router-link", {
      props: {
        to: String,
      },
      template: '<a :href="to"><slot></slot></a>',
    });
  }
```

我们通过`Vue.component`来创建`router-link`这个组件，同时通过`props`接收`to`属性传递过来的值，并且对应的类型为字符串。

最终渲染的模板是一个`a`标签，**`href`属性绑定了`to`属性的值**，同时使用`<slot>`插槽作为占位符，用具体的文字内容填充该占位符。

在VueRoute对象创建成功后，并且将`VueRouter`对象注册到`Vue`的实例上的时候，调用这两个方法。

也就是在`beforeCreate`这个钩子函数中

当然为了调用这两个方便，在这里我们又定义了`init`方法，来做了一次封装处理。

```js
  init() {
    this.createRouteMap();
    this.initComponents(_Vue);
  }
```

对`init`方法的调用如下：

```js
   beforeCreate() {
        //在创建Vue实例的时候
        // 也就是new Vue()的时候，才会有$options这个属性，
        //组件中是没有$options这个属性的。
        if (this.$options.router) {
          _Vue.prototype.$router = this.$options.router;
            //调用init
          this.$options.router.init();
        }
      },
```

this.$options.router.init();

this`表示的就是Vue实例，`$options`表示的就是在创建`Vue`的实例的时候传递的选项，如下所示：

```js
  const vm = new Vue({
        el: "#app",
        router,
      });
```

传递过来的选项中是有router

router就是VueRouter类的实例

`init`方法就是`VueRouter`这个类的实例方法

可以通过`this.$options.router.init()`的方式来调用

#### render函数

完整版中的编译器的作用就是将`template`模板转成`render`函数，所以在运行时版本中我们可以自己编写`render`函数

```js
 //该方法需要一个参数为Vue的构造函数。
  //当然也可以使用全局的_Vue.
  initComponents(Vue) {
    Vue.component("router-link", {
      props: {
        to: String,
      },
      // template: '<a :href="to"><slot></slot></a>',
      render(h) {
     
        return h(
          "a",
          {
            attrs: {
              href: this.to,
            },
          },
          [this.$slots.default]
        );
      },
    });
  }
```

#### 创建`router-view`组件

router-view`组件就是一个占位符。当根据路由规则找到组件后，会渲染到`router-view`的位置。

在`initComponents`方法中创建`router-view`组件

```js
 //该方法需要一个参数为Vue的构造函数。
  //当然也可以使用全局的_Vue.
  initComponents(Vue) {
    Vue.component("router-link", {
      props: {
        to: String,
      },
      // template: '<a :href="to"><slot></slot></a>',
      render(h) {
      
        return h(
          "a",
          {
            attrs: {
              href: this.to,
            },
          },
          [this.$slots.default]
        );
      },
    });
    const self = this;//修改this的指向
    Vue.component("router-view", {
      render(h) {
        //根据当前的路径从routeMap中查找对应的组件.
        const component = self.routeMap[self.data.current];
        //将组件转换成虚拟dom
        return h(component);
      },
    });
  }
```

当我们单击链接的时候，发现了浏览器进行了刷新操作。表明向服务器发送了请求，而我们单页面应用中是不希望向服务器发送请求。

```js
 //该方法需要一个参数为Vue的构造函数。
  //当然也可以使用全局的_Vue.
  initComponents(Vue) {
    Vue.component("router-link", {
      props: {
        to: String,
      },
      // template: '<a :href="to"><slot></slot></a>',
      render(h) {
  
        return h(
          "a",
          {
            attrs: {
              href: this.to,
            },
            on: {
              click: this.clickHandler,
            },
          },
          [this.$slots.default]
        );
      },
      methods: {
        clickHandler(e) {
        
          history.pushState({}, "", this.to);
        
          this.$router.data.current = this.to;

          //阻止向服务器发送器。
          e.preventDefault();
        },
      },
    });
    const self = this;
    Vue.component("router-view", {
      render(h) {
        //根据当前的路径从routeMap中查找对应的组件.
        const component = self.routeMap[self.data.current];
        //将组件转换成虚拟dom
        return h(component);
      },
    });
  }
```

#### initEvent方法实现

当点击浏览器中的后退与前进按钮的时候，地址栏中的地址发生了变化，但是对应的组件没有发生变化。

这时候要解决这个问题， 就需要用到`popstate`事件

`popstate`事件，可以发现浏览器历史操作的变化，记录改变后的地址，单击前进或者是后退按钮的时候触发该事件

```js
initEvent() {
    window.addEventListener("popstate", () => {
   
      this.data.current = window.location.pathname;
    });
  }
```

```js
init() {
    this.createRouteMap();
    this.initComponents(_Vue);
    this.initEvent();
  }
```



## Vuex

### 1.什么情况下应该使用 Vuex？

虽然 Vuex 可以帮助我们管理共享状态，但也附带了更多的概念和框架。这需要对短期和长期效益进行权衡。

如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。一个简单的 global event bus 就足够您所需了。但是，如果您需要构建是一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。引用 Redux 的作者 Dan Abramov 的话说就是：

### 1.Vuex基本原理，有哪些属性，为什么用Vuex

使用 vue/react 等框架，需要关注点基本就是数据，因为框架解决了数据和页面更新的实现。页面和数据的关系是y=f(x)

那么我们需要关注数据(model) 和 视图(组件) 之间的关系.

一个组件使用一个 model，一对一的关系
2. 一个组件使用多个 model，一对多的关系
3. 多个组件使用一个 model，多对一的关系
4. 多个组件使用多个 model，多对多的关系
所以组件和数据之间的对应关系，随着项目的复杂，变得混乱。所以需要统一管理数据，把数据的存取集中到一个地方，所有的组件都从这个地方取数据，更新数据也集中到同一个地方。

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。

- Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
- 改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样可以方便地跟踪每一个状态的变化。

![b025e120ca3d0bd2ded3d038d58cacf4.jpg](https://s2.loli.net/2022/07/09/l6GkJdUAwoPHMji.webp) 

Vuex为Vue Components建立起了一个完整的生态圈，包括开发中的API调用一环。 

**（1)核心流程中的主要功能：**

- Vue Components 是 vue 组件，组件会触发（dispatch）一些事件或动作，也就是图中的 Actions;
- 在组件中发出的动作，肯定是想获取或者改变数据的，但是在 vuex 中，数据是集中管理的，不能直接去更改数据，所以会把这个动作提交（Commit）到 Mutations 中;
- 然后 Mutations 就去改变（Mutate）State 中的数据;
- 当 State 中的数据被改变之后，就会重新渲染（Render）到 Vue Components 中去，组件展示更新后的数据，完成一个流程。
- 有五种属性，分别是 State、 Getter、Mutation 、Action、 Module
  - state => 基本数据(数据源存放地)
  - getters => 从基本数据派生出来的数据
  - mutations => 提交更改数据的方法，同步
  - actions => 像一个装饰器，包裹mutations，使之可以异步。
  - modules => 模块化Vuex

**（2）各模块在核心流程中的主要功能：**

- `Vue Components`∶ Vue组件。HTML页面上，负责接收用户操作等交互行为，执行dispatch方法触发对应action进行回应。
- `dispatch`∶操作行为触发方法，是唯一能执行action的方法。
- `actions`∶ 操作行为处理模块。负责处理Vue Components接收到的所有交互行为。包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。
- `commit`∶状态改变提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。
- `mutations`∶状态改变操作方法。是Vuex修改state的唯一推荐方法，其他修改方式在严格模式下将会报错。该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。
- `state`∶ 页面状态管理容器对象。集中存储Vuecomponents中data对象的零散数据，全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，利用Vue的细粒度数据响应机制来进行高效的状态更新。
- `getters`∶ state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。

### 2.Vuex中的actions和mutations的区别

mutation中的操作是一系列的同步函数，用于修改state中的变量的的状态。当使用vuex时需要通过commit来提交需要操作的内容。mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```javascript
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      state.count++      // 变更状态
    }
  }
})
```

当触发一个类型为 increment 的 mutation 时，需要调用此函数：

```javascript
store.commit('increment')
```

而Action类似于mutation，不同点在于：

- Action 可以包含任意异步操作。
- Action 提交的是 mutation，而不是直接变更状态。

```javascript
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。 所以，两者的不同点如下：

- Mutation专注于修改State，理论上是修改State的唯一途径；Action业务代码、异步请求。
- Mutation：必须同步执行；Action：可以异步，但不能直接操作State。
- 在视图更新时，先触发actions，actions再触发mutation
- mutation的参数是state，它包含store中的数据；store的参数是context，它是 state 的父级，包含 state、getters

### 3.Vuex和localstorage的区别

**（1）最重要的区别**

- vuex存储在内存中
- localstorage 则以文件的方式存储在本地，只能存储字符串类型的数据，存储对象需要 JSON的stringify和parse方法进行处理。 读取内存比读取硬盘速度要快

**（2）应用场景**

- Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。vuex用于组件之间的传值。
- localstorage是本地存储，是将数据存储到浏览器的方法，一般是在跨页面传递数据时使用 。
- Vuex能做到数据的响应式，localstorage不能

**（3）永久性**

刷新页面时vuex存储的值会丢失，localstorage不会。

**注意：** 对于不变的数据确实可以用localstorage可以代替vuex，但是当两个组件共用一个数据源（对象或数组）时，如果其中一个组件改变了该数据源，希望另一个组件响应该变化时，localstorage无法做到，原因就是区别1。

### 4.Vuex的mutations为什么不能做异步操作

- Vuex中所有的状态更新的唯一途径都是mutation，异步操作通过 Action 来提交 mutation实现，这样可以方便地跟踪每一个状态的变化，从而能够实现一些工具帮助更好地了解我们的应用。
- 每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。

### 4. Redux 和 Vuex 有什么区别，它们的共同思想

**（1）Redux 和 Vuex区别**

- Vuex改进了Redux中的Action和Reducer函数，以mutations变化函数取代Reducer，无需switch，只需在对应的mutation函数里改变state值即可
- Vuex由于Vue自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的State即可
- Vuex数据流的顺序是∶View调用store.commit提交对应的请求到Store中对应的mutation函数->store改变（vue检测到数据变化自动渲染）

通俗点理解就是，vuex 弱化 dispatch，通过commit进行 store状态的一次更变;取消了action概念，不必传入特定的 action形式进行指定变更;弱化reducer，基于commit参数直接对数据进行转变，使得框架更加简易;

**（2）共同思想**

- 单—的数据源
- 变化可以预测

本质上：redux与vuex都是对mvvm思想的服务，将数据从视图中抽离的一种方案; 形式上：vuex借鉴了redux，将store作为全局的数据中心，进行mode管理;

### 5. 为什么要用 Vuex 或者 Redux

由于传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致代码无法维护。

所以需要把组件的共享状态抽取出来，以一个全局单例模式管理。在这种模式下，组件树构成了一个巨大的"视图"，不管在树的哪个位置，任何组件都能获取状态或者触发行为。

另外，通过定义和隔离状态管理中的各种概念并强制遵守一定的规则，代码将会变得更结构化且易维护。

### 7. Vuex和单纯的全局对象有什么区别？

- Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
- 不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样可以方便地跟踪每一个状态的变化，从而能够实现一些工具帮助更好地了解我们的应用。

### 9. Vuex的严格模式是什么,有什么作用，如何开启？

在严格模式下，无论何时发生了状态变更且不是由mutation函数引起的，将会抛出错误。这能保证所有的状态变更都能被调试工具跟踪到。

在Vuex.Store 构造器选项中开启,如下

```php
const store = new Vuex.Store({
    strict:true,
})
```

### 5.基本注意

在组件里面  用方法执行dispatch('函数名'，value)

actions 对象中函数接受参数（context,value)   执行context.commit('函数名',value)

mutations  对象 中函数接受参数（state,value)

getters  用于将state中的数据进行加工

mapState  传入对象包含计算属性名和state对应的变量名  产生了一个对象  可以在计算属性对象里面进行展开 mapstate生成计算属性 从state中读取数据  对象可以简写成数组[ '  ' ]       namespaced true 才能让mapstate识别模块名简写

mapGetters  传入对象包含计算属性名和state对应的变量名

mapActions 生成对应方法  方法中会调用dispatch去联系actions  注意模块名

mapMutations 生成对应方法  方法中会调用commit去联系mutations 注意模块名

### 6.vuex持久化

vuex的 store 中的数据是保存在运行内存中的，当页面刷新时，页面会重新加载 vue 实例，vuex 里面的数据就会被重新赋值，这样就会出现页面刷新vuex中的数据丢失的问题。 如何解决浏览器刷新数据丢失问题呢？

#### 方法一：

全局监听，页面刷新的时候将 store 里 state 的值存到 sessionStorage 中，然后从sessionStorage 中获取，再赋值给 store ，并移除 sessionStorage 中的数据。在 app.vue 中添加以下代码：

```js
 created() {
    window.addEventListener('beforeunload',()=>{
       sessionStorage.setItem('list', JSON.stringify(this.$store.state))
    })
    
    try{
      sessionStorage.getItem('list') && this.$store.replaceState(Object.assign({},this.$store.state,JSON.parse(sessionStorage.getItem('list'))))
    }catch(err) {
      console.log(err);
    }
  
    sessionStorage.removeItem("list");
  }
复制代码
 注意!!! storage 只能存储字符串的数据，对于 JS 中常用的数组或对象不能直接存储。但我们可以通过JSON 对象提供的 parse 和 stringify 方法将其他数据类型转化成字符串，再存储到storage中就可以了。
```

#### 方法二：

安装 vuex-persistedstate 插件

```
1. npm install vuex-persistedstate -S //安装插件
2. 在 store/index.js 文件中添加以下代码：
import persistedState from 'vuex-persistedstate'
const store = new Vuex.Store({
 state:{},
 getters:{},
 ...
 plugins: [persistedState()] //添加插件
})
复制代码
注意!!! vuex-persistedstate 默认使用 localStorage 来存储数据，若要实现无痕浏览该如何实现呢？
```

这时候就需要使用 sessionStorage 进行存储，修改 plugins 中的代码

```js
plugins: [
    persistedState({ storage: window.sessionStorage })
]
```

### 7.Vuex为什么是响应式的

#### `vuex`响应式原理

一旦理解了`vue`的模板如何响应数据变化，那么`vuex`就好理解了

`vuex`本质上是将`state`值绑定到了一个`vue`对象上，请看超简略源码：

```js
class Store {
    constructor(options){
        this.state = new Vue({
            data:options.state
        })
    }
}
```

于是当我们在`test.vue`中写出这种代码：

```js
<template>
	<div>{{ $store.state.xx }}</div>
</template>
```

`test.vue`实例`mount`的时候执行`updateComponent`，就会为`updateComponent`函数绑定一个依赖：`Store.state.xx`这个属性的`Dep`对象（暂时命名为`xxDep`,便于后续说明）

那么一旦通过`commit`或其他手段更新了属性`Store.state.xx`，`xxDep`就会通知`updateComponent`所绑定的`Watcher`去执行`update`

```
Watcher.prototype.update = function(){
	if (this.lazy) {
    	...
    } else {
    	// 将此watcher加入队列，在nextick中执行
        // 最终会执行到Watcher.getter，本例中也就是updateComponent
		queueWatcher(this);
	}
}
```

从而最终又执行到了`updateComponent`去更新dom树，而在执行`updateComponent`过程中解析dom树时会重新获取`{{ $store.state.xx }}`，从而正确的更新了dom，实现了`store.state`到`vue`对象的绑定

#### store.getters

上面讲了`store.state`如何绑定到`vue`对象，那么`store.getters`呢？

```
var wrappedGetters = store._wrappedGetters;
var computed = {};
forEachValue(wrappedGetters, function (fn, key) {
  computed[key] = partial(fn, store);
  Object.defineProperty(store.getters, key, {
    get: function () { return store._vm[key]; },
    enumerable: true // for local getters
  });
});

store._vm = new Vue({
  data: {
    $$state: state
  },
  computed: computed
});
复制代码
```

可以看到对于每个getters的值，最终放在两个地方：`store.getters`, `store`内部的`vue`对象上的`computed`属性，`computed`属性的双向绑定机制跟`data`属性类似，这里不多讲

而通过`store.getters.key`获取的值根据以上代码，得到的是`store._vm[key]`,而这个就是`computed[key]`,因为`computed`属性都会绑定到`vm`对象上。所以`store.getters[key]===computed[key]`，是完完全全的同一个值

#### 装载到`vue`

`vue2`中使用`vuex`需要执行`vue.use(vuex)`。最终会执行到`vuex`的`install`方法

```js
// 初始化全局Vue对象时挂载store，并在跟元素上生成
new Vue({
    store,
    ...
})

function install() {
    Vue.mixin({
        beforeCreate() {
            if (this.$options.store) {
                this.$store = this.$options.store // 这里对应根组件
                return
            }
            this.$store = this.$parent.$store // 其他组件逐级向上取
        } 
    })
}
复制代码
```

通过生命周期给每个组件单独挂载`$store`，而不是直接`Vue.prototype.$store =`，这样可以防止声明多个`vuex`实例后覆盖

```
vue3`中挂载`vuex`要执行`app.use(store)`。最终会执行到`Store.prototype.install
function install (app, injectKey) {
    // globalProperties属性上挂载的属性可以在app下所有组件实例中访问到
    app.config.globalProperties.$store = this;
}
```

### 8.Vuex源码分析

[Vuex源码分析](https://juejin.cn/post/6895980141466386440)

#### Vuex的使用

```js
// store/index
import Vue from 'vue'
import Vuex from 'vuex'
import cart from './modules/cart'
import products from './modules/products'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    rootState: 'rootState'
  },
  mutations: {
    rootMutation (state, payload) {
      state.value = payload
    }
  },
  actions: {
    rootAction ({ commit }, payload) {
      commit('updateValue', payload)
    }
  },
  getters: {
    rootGetter: state => state.rootState
  },
  modules: {
    cart,
    products
  },
})

```

```js
// app.js
import Vue from 'vue'
import store from './store'

new Vue({
  el: '#app',
  store,
  render: h => h(App)
})

```

使用vuex有如下3个步骤；

  **1. 显式地通过 Vue.use() 来安装 Vuex；**

  **2. 通过 Vuex.Store 构造与实际业务相关的 store;**

  **3. 在 Vue 的实例化时，添加 store 属性；**

### 2、Vuex 的注册

   Vuex 使用时，必须显示的调用  Vue.use() ，所有本质来讲 Vuex 其实就是 vue 的一个插件（plugin），插件注册时会调用插件的 install 方法；Vuex 的 install 方法如下：

```JavaScript
let Vue

export function install (_Vue) {
  // 如果 Vuex已经注册，则return
  if (Vue && _Vue === Vue) {
    // dev环境下warning忽视
    if (__DEV__) {
      console.error(
        '[vuex] already installed. Vue.use(Vuex) should be called only once.'
      )
    }
    return
  }
  Vue = _Vue
  applyMixin(Vue)
}
复制代码
```

   定义了变量 Vue， _Vue 是插件注册是真正传入进来的 Vue 对象。首先判断 Vue 是否已经存在，避免 Vuex 重复注册，最后调用 applyMixin 方法：

```JavaScript
//applyMixin.js
export default function (Vue) {
  const version = Number(Vue.version.split('.')[0])

  // 对 Vue 2x 及以上版本的处理
  if (version >= 2) {
    Vue.mixin({ beforeCreate: vuexInit })
  } else {
    // aop,改写Vue实例的_init方法
    const _init = Vue.prototype._init
    Vue.prototype._init = function (options = {}) {
      options.init = options.init
        ? [vuexInit].concat(options.init)
        : vuexInit
      _init.call(this, options)
    }
  }
  
  function vuexInit () {
    const options = this.$options

    if (options.store) {
      this.$store = typeof options.store === 'function'
        ? options.store()
        : options.store
    } else if (options.parent && options.parent.$store) {
      this.$store = options.parent.$store
    }
  }
  
复制代码
```

   首先通过 version 判断当前 Vue 的版本，如果是 2.x 及以上的版本，通过 mixin 全局混入 hooks - beforeCreate ； 如果是 1.x 及以下的版本，则通过改写 _init 方法。两种方式的处理，目的都是在组件初始化前，调用 vuexInit 方法。此时的 this 对应的就是 Vue 的实例 （beforeCreate 方法执行时）， options 即 Vue 实例化时传入的参数，如果存在 store 属性的话，则将 this.$store 设置为 store 实例。

   其中 store 就是调用 new Vuex.Store(options) 构造函数（Class）返回的对象



### 3、Vuex.Store 的实例化

   store 是通过 new Vuex.Store(options) 实例化得到，Store 的代码如下，通过 Class 关键字实现了 Store 的类，并提供一系列的方法。

```JavaScript
export class Store {
    
   constructor (options = {}) {}
   
   get state (){}
   
   set state (v){}
    
   commit(){}
   
   dispatch(){}
   
   //......其他方法
}

复制代码
```

   其中 store 正是构造函数执行后返回的结果，store 中有我们常用的 commit、dispatch 方法，对于 state 属性，提供了 getter/setter 方法。这些方法的具体内容，稍后一一分析。

   首先来看下构造函数中的关键代码：

```
    this._committing = false
    this._actions = Object.create(null)  // 存储action，按照namespace的划分
    this._actionSubscribers = []  // 存放 订阅 store 的 action
    this._mutations = Object.create(null)  // 存储mutation，按照namespace的划分
    this._wrappedGetters = Object.create(null)   // 存储getter，按照namespace的划分
    this._modules = new ModuleCollection(options)
    this._modulesNamespaceMap = Object.create(null)
    this._subscribers = []  // 存放 订阅 store 的 mutation
    this._watcherVM = new Vue()
    this._makeLocalGettersCache = Object.create(null)

    // bind commit and dispatch to self
    const store = this
    const { dispatch, commit } = this

    // dispatch执行时，将内部的this指向当前的store
    this.dispatch = function boundDispatch (type, payload) {
      return dispatch.call(store, type, payload)
    }
    // commit执行时，将内部的this指向当前的store
    this.commit = function boundCommit (type, payload, options) {
      return commit.call(store, type, payload, options)
    }

    // strict mode
    this.strict = strict

    // 最顶层state： rootState
    const state = this._modules.root.state

    // init root module.
    // this also recursively registers all sub-modules
    // and collects all module getters inside this._wrappedGetters
    // 初始化module ，全局的模块（root）
    installModule(this, state, [], this._modules.root)
    // initialize the store vm, which is responsible for the reactivity
    // (also registers _wrappedGetters as computed properties)

    resetStoreVM(this, state)

复制代码
```

   上边的代码主要分三部分：1、初始化一些变量； 2、执行 installModule 方法； 3、执行 resetStoreVM 方法；

   其中初始化中，重点需要关注的是 this._modules = new ModuleCollection(options)

#### 3.2、初始化： installModule

   installModule 方法中主要完成 module 的一系列初始化，主要针对 module 的 state、getter、mutation、action 注册；又因为 store 中存在 modules，因此 module 中的 state、action 等也需要注册，这部分是通过递归调用 installModule 方法实现的，其中在 module 中，需要传入 context 属性，用于访问当前 module 的 state、 getter，对这部分的处理在 makeLocalContext 方法中实现；

   为了更好的理解 module 设置 namespace 之后处理，我们先分析 action 、mutation 等模块的注册，然后在分析 context 的实现。

##### 3.2.1、this._modules 中 state的处理；

   对于state 的处理核心代码如下：

```JavaScript
 const isRoot = !path.length

 if (!isRoot && !hot) {
    // 获取当前module的父module的state的值 rootState
    const parentState = getNestedState(rootState, path.slice(0, -1))
    const moduleName = path[path.length - 1]
    store._withCommit(() => {
      // state的响应式处理： 构造的state通过Vue.set方法设置之后，就会变成响应式。
      Vue.set(parentState, moduleName, module.state)
    })
  }
  
  // 其他代码
  
  module.forEachChild((child, key) => {
    installModule(store, rootState, path.concat(key), child, hot)
  })

复制代码
```

   第一次调用 installModule 方法是，path 为空，则 isRoot 为 true，跳过 if 语句，递归调用 installModule 时，传入的 path 为当前 module 的 moduleName 链路集合，module 参数为当前的 module。此时执行 if 语句，同 module 注册（register ）一样，此处 getNestedState 也是获取 moduleName 链路上的最末端的 state，同理 path.slice(0, -1) 剔除了当前的 moduleName，因此返回的是父module 的state （parentState）。通过调用 Vue.set(parentState, moduleName, module.state) ，在 parentState 中添加 moduleName 的属性，value 值为当前 module 的 state。通过 Vue.set 是为了实现数据的响应式，保证子 module 中的 state 发生变化，parentModule 中的 state 响应变化。

   由于第一次调用的传入的 module 是 store._modules.root ，因此之后的所有的递归操作都是基于这个入口操作的，也即这部分对应 state 的处理，最终都反应到了this._modules 上。

![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8d64028006444264b91473ae72db4550~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

##### 3.2.2、mutation的注册：registerMutation

   在 installModule 方法遍历当前 module 下的 mutation 方法，并依次调用 registerMutation 方法来注册。同时通过递归来完成各个子模块的注册。核心代码如下：

```JavaScript
 //当前模块的moduleName的链路集合
 const namespace = store._modules.getNamespace(path)

 // 遍历注册module中的mutation - 根据module的 [nameSpace + key] 作为key来区分mutation
  module.forEachMutation((mutation, key) => {
    const namespacedType = namespace + key
    registerMutation(store, namespacedType, mutation, local)
  })
  
 //.....
 module.forEachChild((child, key) => {
    installModule(store, rootState, path.concat(key), child, hot)
  })
 
复制代码
```

   在 registerMutation 方法的代码如下：

```JavaScript
// 注册Mutation - mutation 按照（namespace + handelName） 维护在 store._mutation对象上
function registerMutation (store, type, handler, local) {
  const entry = store._mutations[type] || (store._mutations[type] = [])
  entry.push(function wrappedMutationHandler (payload) {
    // 注入了当前module下的state
    handler.call(store, local.state, payload)
  })
}

复制代码
```

   首先来看下 type 参数是什么？ 其中 namespace 为 moduleName 链接的集合形成的一个字符串 （例如：'cart/'）。 module.forEachMutation 方法遍历的 mutation 下的方法，因此 key 即为 mutation 下对应的方法名。因此 type 为 当前 module 的链路下 ( moduleName集合 + 方法名 )，例如 'cart/incrementItemQuantity'。 则所有的 mutation 都根据 type 值存储在 store._mutation 对象中。并在 handler 执行时，注入了当前 module 下的  state，这里的 local 就是当前 module 下的 context ；最终的数据结构如下：

![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ad4932f48b2c495881fa0b5e572ab25f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

##### 3.2.3、action的注册：registerAction

   action 的注册同 mutation 基本一致，最终的结果存储在 store._action 对象中，也是一组键值对，key 值仍旧是 (moduleName集合 + 方法名)，value 为对应的 action 下定义的方法体。

```JavaScript
// 注册Action - action 按照 type（namespace + handelName -- 形如： a/b/ ） 维护在 store._action对象上，
// 并在执行时 注入了 context 和 root的属性
function registerAction (store, type, handler, local) {
  const entry = store._actions[type] || (store._actions[type] = [])
  entry.push(function wrappedActionHandler (payload) {
    // 注入了当前module的context（local）的属性，以及root （store）的属性
    let res = handler.call(store, {
      dispatch: local.dispatch,  // 赋值后， dispatch执行的时候，环境已经发布变化， 因此 dispatch 需要通过 call 绑定 store
      commit: local.commit,
      getters: local.getters,
      state: local.state,
      rootGetters: store.getters,
      rootState: store.state
    }, payload)
    if (!isPromise(res)) {
      res = Promise.resolve(res)
    }
      return res
  })
}
复制代码
```

   action 的 handler 中注入了更多的参数，包括 context （local相关的）和 root （store） 的属性。并通过 promise.all 进行包装，这也意味着，dispatch 触发的action，可支持 promise 的处理。

##### 3.2.4、getter的注册：registerGetter

   getter 的注册同 mutation、action 也基本一致，不同的是 gettert 不允许重复注册。所有的 getter 都存储在 store._wrappedGetters 中， getter 的handler中注册了 state，getter，rootState，rootGetter 4个参数；

##### 3.2.5、小结

   state、mutation、action、getter 注册完成之后，最终的结果如下：

![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97d4b996553345d7bf7c746dc665af15~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

   **至此，就可以回答问题2：构建了怎样的单一状态树，如何实现模块化管理？**

  **通过递归操作实现 state 、mutation、action、getter 的注册，最终将这些数据都存储到 store 实例中，根据各自的属性由 store 统一管理。**

#### 3.3、context 构建：makeLocalContext

   针对具体的 module，Vuex 在设计中给每个 module 提供一个 context 的属性。context 包含：dispatch 、commit、getter 、state 四个属性。

- dispatch 和 commit：context 中提供的这两个方法本质还是调用 store 中对应的方法。以 dispatch 为例，核心代码如下：

```JavaScript
    dispatch: noNamespace ? store.dispatch : (_type, _payload, _options) => {
      const args = unifyObjectStyle(_type, _payload, _options)
      const { payload, options } = args
      let { type } = args

      // options 不存在 或者  options.root 不为ture，则标识从子模块中获取 action。否则从root中操作
      if (!options || !options.root) {
        type = namespace + type
        if (__DEV__ && !store._actions[type]) {
          console.error(`[vuex] unknown local action type: ${args.type}, global type: ${type}`)
          return
        }
      }

      return store.dispatch(type, payload)
    }
复制代码
```

   有上文分析可以，context.dispatch 方法被注入到 action 的第一个参数中，在 module 的 action 中如果需要调用当前 module 下的 action 时，通常这样写：`dispatch( 'actionName', payload )` ；但是在 action 注册时，所有的 module 下的 action 都存储到 store._action 对象中， 对应的 key 为各个 module 的 (moduleName集合 + 方法名) 拼接而成。如果开启了 namespace 的话，在 _action 对象中实际上找不到 ‘actionName’这个方法 （因为 _action 中这个方法名称是 moduleName的集合和方法名组合的 ）

   到这里我们就明白了，context 中的 dispatch、commit 本质都是调用 store 的 dispatch、commit 方法，只不过在开启 namespace 之后，对 type 做了处理，拼接上了 namespace，以保证在 _action 对象中能找到对应的方法。

- getter、state 属性：核心代码如下。通过 Object.defineProperties 为 getter 、state 添加了 get 方法， getter 通过 namespace （本质的是 moduleName 的集合） 寻找对应 module 下的 getter ，state 则是直接通过 path （本质也是 moduleName 的集合）逐层寻找对应 module 下的 state。

```JavaScript
Object.defineProperties(local, {
    getters: {
      get: noNamespace
        ? () => store.getters
        : () => makeLocalGetters(store, namespace)
    },
    state: {
      get: () => getNestedState(store.state, path)
    }
复制代码
```

   state 的引用很好理解，这里主要来分析下 getter ， 为了更好的阐述这段代码，我们举例说明由，由上述分析可知，在 module 的 action 中注入了当前 module 的 getter， 假设存在一个名称为 number 的 getter ，我们在 action 中有如下应用：

```
 const num = getter.num
复制代码
```

   这里涉及到了2个引用，第一个是 getter ，第二个是 num 。 getter 的 get 方法指向的是 makeLocalGetters 的调用，代码如下：

```JavaScript
function makeLocalGetters (store, namespace) {
  if (!store._makeLocalGettersCache[namespace]) {
    const gettersProxy = {}
    const splitPos = namespace.length
    Object.keys(store.getters).forEach(type => {
      // skip if the target getter is not match this namespace
      // 忽略 namespace 不匹配的方法 ，namespace：a/b/， getter: a/b/getterName
      if (type.slice(0, splitPos) !== namespace) return

      // extract local getter type
      // 获取getterName
      const localType = type.slice(splitPos)

      // Add a port to the getters proxy.
      // Define as getter property because
      // we do not want to evaluate the getters in this time.
      Object.defineProperty(gettersProxy, localType, {
        get: () => store.getters[type],
        enumerable: true
      })
    })
    store._makeLocalGettersCache[namespace] = gettersProxy
  }

  return store._makeLocalGettersCache[namespace]
}
复制代码
```

   首先判断是否存在缓存，如果存在当前 getter 的缓存，则直接使用。如果不存在缓存时，则首先从 store.getter 中过滤出与当前 namespace 相关的 getter ，然后定义了一个 空对象，并通过 Object.defineProperty 为这个空对象设置 属性 - getter 的方法名，并为该属性设置 get 方法。

   回到刚才的实例：经过两次 get 引用后，当前 getter.name 就从当前 store.getter 属性找到了 moduleName集合 + getterName 对应的 getter 方法了。通过 Object.defineProperty 方法接触，能够保证数据获取的拿到实时的数据。

   至此可以解释 **问题3. context 与 store 实例有什么区别？**

   **store 包含更多的属性，而 context 只有四个属性 commit、dispatch、state、getter ，其中 commit、dispatch 本质上还是 store.commit、 store.dispatch ；而 state 、getter 代表的是当前 module 下的 state 和 getter，但前提是 module 开启了namespaced 属性。如果没有开启 namespace， 实际上 mutation、action 、getter 等注册时的 key 值就不是 moduleName 的链路集合 +  方法名， 而是方法名本身。**

### 4、数据响应式处理： resetStoreVM

   之前的分析中，我们多次涉及到 store.state， store.getter ，这两个属性也多次注入到诸如 mutation、action 当中，如果这些数据不能做到响应式的话，那么功能就完全瘫痪了，那么 Vuex 是如何实现的。 这个功能主要是通过 resetStoreVM 方法实现，我们都知道 Vue 中的是响应式的处理数据的，Vuex 正式利用 Vue 这一特点来实现数据的响应式。核心代码如下：

```JavaScript
function resetStoreVM (store, state, hot) {
  const oldVm = store._vm

  store.getters = {}
  store._makeLocalGettersCache = Object.create(null)
  const wrappedGetters = store._wrappedGetters
  const computed = {}
  
  forEachValue(wrappedGetters, (fn, key) => {
    computed[key] = partial(fn, store)
    // 将wrappedGetters的属性定义到store.getter中 ,其中get方法从 vue实例的compute中获取
    // 保证了外部获取getter时，其实是获取vue实例的属性，通过Vue的特性实现getter的响应式数据
    Object.defineProperty(store.getters, key, {
      get: () => store._vm[key], // computed
      enumerable: true // for local getters
    })
  })

  // store.vm被定义为Vue的实例，其中computed的属性定义为 partial(fn, store) ===》  fn(store)
  store._vm = new Vue({
    data: {
      $$state: state
    },
    computed
  })
  
}
复制代码
```

   通过上文分析可知 getter 根据对应的 namespace 的规则存储在 store._wrappedGetters 对象中，forEachValue 方法通过遍历 store._wrappedGetters 对象，复制该对象到 computed 对象中，同时设置 store.getters 的 get 方法 ，则有如下调用关系：

```JavaScript
this.$store.getter.xxx  ------>   store.vm[xxx]  （1）

复制代码
```

   继续分析，通过调用 Vue 的构造函数返回对象 store._vm ， 则可知 store._vm 中的数据是响应式的。又因为 store._vm 的 computed 属性被设置为局部变量 computed 对象（ 其实就是getter的集合 ），则（1）式 实际可以理解为：获取 store 的 getter 属性，就是获取 store._vm 中对应的 computed 的属性，因为 store._vm 是响应式的对象，则 getter 中的数据也是响应式的；

    又因为 store 实例中提供 state 属性的 get 方法。代码如下：

```JavaScript
  get state () {
    return this._vm._data.$$state
  }
复制代码
```

   则 this.$store.state 实际返回的是 store._vm._data.state，由resetStoreVM方法可知store.vm是Vue的实例，因此store.vm.data.state，由 resetStoreVM 方法可知 store._vm 是 Vue 的实例，因此 store._vm._data.state，由resetStoreVM方法可知store.vm是Vue的实例，因此store.vm.data.state 是响应式数据，则 store.state 也实现了响应式。

   至此就可以回答 **问题 4. 如何实现数据响应式？**

   **通过构造 Vue 实例，将 store.state 属性设置为 data 数据，将 store.getter 集合设置为 computed 属性，并将 computed 方法 （具体的 getter ）指向 store 中对应的 getter 方法。**

### 5、store的方法调用

#### 5.1、 commit方法

   commit 方法执行很简单，有之前的 mutation 部分可知，所有的 mutation 都被存放在 store._mutations 对象。如果能找到对应的 mutation 执行就好了，同时还需要完成 store 相关的订阅的执行。

```JavaScript
   const {
      type,
      payload,
      options
    } = unifyObjectStyle(_type, _payload, _options)
    
  const entry = this._mutations[type]
  
  // 执行mutation方法
  entry.forEach(function commitIterator (handler) {
    handler(payload)
  })
  
  this._subscribers.slice().forEach(sub => sub(mutation, this.state))
  
复制代码
```

   其中 unifyObjectStyle 是为了让 mutation、action 的两种调用方式，最终在执行是呈现统一的数据结构。

```
// 方式1
store.commit('increment', {
  amount: 10
})


//方式2 
store.commit({
  type: 'increment',
  amount: 10
})

复制代码
```

#### 5.2、 dispatch方法

   dispatch 方法被存放在 store._action对象。同 mutation 只需要找到对应的 action 执行就行，同时完成 action 相关的订阅 _actionSubscribers （before 订阅 和  after 订阅）。 同时通过 Promise.all 保证所有 action 执行完成之后，才执行 after actionSubscribers。核心代码如下：

```JavaScript
this._actionSubscribers
     .slice() 
     .filter(sub => sub.before)
     .forEach(sub => sub.before(action, this.state))
     
 const result = entry.length > 1
      ? Promise.all(entry.map(handler => handler(payload)))
      : entry[0](payload)
      
 return new Promise((resolve, reject) => {
      result.then(res => {
        try {
         // 执行 after actionSubscribers
        resolve(res)
      }, error => {
        // 执行 actionSubscribers error
        reject(error)
      })
    })     
复制代码
```

### 6、辅助方法

#### 6.1 mapXXX 方法

   Vuex 提供 mapState 、mapGetter 、mapMutation、 mapAction 四个辅助方法， 实现的原理基本上是一致， 以 mapAction 为例，通常有对象、数组两种引用方式 ：

```JavaScript
    // (1) 数组形式 - 无namespace 
    ...mapActions([
      'increment', 
      'incrementBy'
    ]),
    
    // (2) 对象形式 - 无namespace
    ...mapActions({
      add: 'increment'
    })
    
    
    // (3) 数组形式 - 有namespace
    ...mapActions('moduleName/moduleName',[
      'increment',
      'incrementBy'
    ]),
    
    //(4) 对象形式 - 无namespace
    ....mapActions('moduleName/moduleName',{
      add: 'increment' 
    })

复制代码
```

   有以上实例，可知 mapActions 的调用方式，共有 4 种，来看下源码：

```JavaScript
export const mapActions = normalizeNamespace((namespace, actions) => {
  const res = {}
  if (__DEV__ && !isValidMap(actions)) {
    console.error('[vuex] mapActions: mapper parameter must be either an Array or an Object')
  }
  normalizeMap(actions).forEach(({ key, val }) => {
    res[key] = function mappedAction (...args) {
      // get dispatch function from store
      let dispatch = this.$store.dispatch
      // 如果存在namespace,则将dispatch设置为module中的dispatch，否则为root的dispatch
      if (namespace) {
        const module = getModuleByNamespace(this.$store, 'mapActions', namespace)
        if (!module) {
          return
        }
        dispatch = module.context.dispatch
      }
      // 真正调用时，通过dispatch去触发action操作。
      return typeof val === 'function'
        ? val.apply(this, [dispatch].concat(args))
        : dispatch.apply(this.$store, [val].concat(args))
    }
  })
复制代码
```

   其中，normalizeNamespace 是为了统一 namespace 参数，如果没有 namespace 参数，则将参数为空，实例 中的 （1）为例，通过 normalizeNamespace 处理后，返回结果为：

```JavaScript
  // (1) 数组形式 - 无namespace 
    ...mapActions(''，[
      'increment',
      'incrementBy' 
    ]),
复制代码
```

   通过 normalizeNamespace 抹平 namespace 参数的影响之后，就剩下数组和对象两种引用方式了，如你所料, normalizeMap 是为了解决两种调用方式传入的数据格式不一致的问题，以（3），（4）为例，返回结果统一为 [{key:'',val:''}]：

```JavaScript
// (3) 数组形式 - 有namespace
    ...mapActions('moduleName/moduleName',[
      {key:'increment' : val:'increment' }, 
      {key:'incrementBy' : val:'incrementBy' }, 
    ]),
    
    //(4) 对象形式 - 无namespace
    ....mapActions('moduleName/moduleName',[
        { key:add: val:'increment'}
    ])
复制代码
```

   mapXXX 最终都会返回数组-对象（Array-Object），对象的 key ，即为你引用的 key （数组引用时，就是数组元素），value 为 function 。 以 dispatch 为例，最终被混合到 methods 中，真正执行调用该方法的时候，实际执行的就是：

```JavaScript
 // 真正调用时，通过dispatch去触发action操作。
      return typeof val === 'function'
        ? val.apply(this, [dispatch].concat(args))
        : dispatch.apply(this.$store, [val].concat(args))  // 常用
    }
复制代码
```

  通过调用 dispatch 执行对应的 action，其中 type 中是 val，对应到实例（4）中就是 increment 方法，args 就是执行方法传入的参数，最终以 payload 传入action。

  其中，如果存在 namespace 时，则 dispatch 为当前 context提供的方法。所以实例 （4）中最终执行的是下边的语句

```JavaScript
  context.dispatch('increment',payload)
复制代码
```

   由之前的分析可知  context.dispatch 会添加当前的 namespace 对应的 moduleName 的链接集合， 所以上边的语句最终执行的是：

```JavaScript
  store.dispatch('moduleName/moduleName/increment',payload)
复制代码
```

#### 6.1 createNamespacedHelpers

   createNamespacedHelpers 的目的是为了通过命名空间创建辅助函数。源码如下，非常简单，其实就是通过给 mapXXX 函数传递 namespace 参数。

```JavaScript
export const createNamespacedHelpers = (namespace) => ({
  mapState: mapState.bind(null, namespace),
  mapGetters: mapGetters.bind(null, namespace),
  mapMutations: mapMutations.bind(null, namespace),
  mapActions: mapActions.bind(null, namespace)
})
复制代码
```

   至此就可以回答 **问题 5. 辅助类的函数式如何实现？**

   **首先，统一 mapXXX 方法的调用方式 ： 有无 namespace 和对象或者数组的调用方式，然后通过对应的 namespace 获取 store 对象中对应属性元素 （state -  store.state , action  -  store._action ），并返回 key - value 的对象 （key 为引入时的对象属性或者数组元素，value 为对应的 store 中对应的方法或者属性），在使用中将这些属性注入到 Vue 的实例中。**

### 7、总结

   Vuex 本质上是一个 Vue 的插件，通过 Vue.use(Vuex) 引用后，在 Vue 的实例中注入（mixin）了 store(this.store)属性，这使得在Vue实例中都能通过this.store ( this.store ) 属性，这使得在 Vue 实例中都能通过 this.store(this.store)属性，这使得在Vue实例中都能通过this.store 访问到 Vuex 的实例 （store）。

   Vuex.Store 是一个类 （ class ），在实例化时，会将各个 module （ root 本身也可以理解为module ： rootModule ） 下的 state 、getter、mutation、action 按照一定的 namespace 组合 （ 如果没有设置namespace，则各个namespace的组合可以理解为空 ）存放在 store 下个对应属性中，同时通过巧妙的使用 Vue 的数据响应式的特点，使得 Vuex 的 state、getter 也能实现数据响应式。提供了dispatch 方法用于调用 action，提供 commit 方法用于调用 mutation。

   为方便在 Vue 中使用 Vuex，提供了一系列的辅助函数，这些辅助函数的本质，就是在 store 中找到对应属性的对象（例如，mapState 对应的就是 store.state ，mapMutation 对应的就是 store._mutation ）,在需要使用的时候，或者执行对应的方法（mapGetter、mapMutation、mapAction），或者返回对应的属性（mapState）。

## Vue3

### 1.Vue3有哪些新特性？

**（1）监测机制的改变**

- 3.0 将带来基于代理 Proxy的 observer 实现，提供全语言覆盖的反应性跟踪。
- 消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制：

**（2）只能监测属性，不能监测对象**

- 检测属性的添加和删除；
- 检测数组索引和长度的变更；
- 支持 Map、Set、WeakMap 和 WeakSet。

**（3）模板**

- 作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
- 同时，对于 render 函数的方面，vue3.0 也会进行一系列更改来方便习惯直接使用 api 来生成 vdom 。

**（4）对象式的组件声明方式**

- vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。
- 3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易

**（5）其它方面的更改**

- 支持自定义渲染器，从而使得 weex 可以通过自定义渲染器的方式来扩展，而不是直接 fork 源码来改的方式。
- 支持 Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对一些特殊的场景做了处理。
- 基于 tree shaking 优化，提供了更多的内置功能。

### 2.Proxy的优点

Vue 在实例初始化时遍历 data 中的所有属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。这样当追踪数据发生变化时，setter 会被自动调用。

Object.defineProperty 是 ES5 中一个无法 shim 的特性，这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因。

但是这样做有以下问题：

1. 添加或删除对象的属性时，Vue 检测不到。因为添加或删除的对象没有在初始化进行响应式处理，只能通过`$set` 来调用`Object.defineProperty()`处理。
2. 无法监控到数组下标和长度的变化。



Vue3 使用 Proxy 来监控数据的变化。Proxy 是 ES6 中提供的功能，其作用为：用于定义基本操作的自定义行为（如属性查找，赋值，枚举，函数调用等）。相对于`Object.defineProperty()`，其有以下特点：

1. Proxy 直接代理整个对象而非对象属性，这样只需做一层代理就可以监听同级结构下的所有属性变化，包括新增属性和删除属性。
2. Proxy 可以监听数组的变化。

在 Vue2 中， 0bject.defineProperty 会改变原始数据，而 Proxy 是创建对象的虚拟表示，并提供 set 、get 和 deleteProperty 等处理器，这些处理器可在访问或修改原始对象上的属性时进行拦截，有以下特点∶

- 不需用使用 `Vue.$set` 或 `Vue.$delete` 触发响应式。
- 全方位的数组变化检测，消除了Vue2 无效的边界情况。
- 支持 Map，Set，WeakMap 和 WeakSet。

Proxy 实现的响应式原理与 Vue2的实现原理相同，实现方式大同小异∶

- get 收集依赖
- Set、delete 等触发依赖
- 对于集合类型，就是对集合对象的方法做一层包装：原方法执行后执行依赖相关的收集或触发逻辑。

-   vue3使用 [proxy](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FProxy) 监听对象的变化

>   -   针对对象：针对整个对象，而不是对象的某个属性，所以也就不需要对 keys 进行遍历。
>   -   支持数组：Proxy 不需要对数组的方法进行重载，省去了众多 hack，减少代码量等于减少了维护成本，而且标准的就是最好的。
>   -   Proxy 的第二个参数可以有 13 种拦截方法，这比起 Object.defineProperty() 要更加丰富
>   -   Proxy 作为新标准受到浏览器厂商的重点关注和性能优化，相比之下 Object.defineProperty() 是一个已有的老方法。

每当我们改变代理对象(vue2对象)的时候，比如我们新增一个`age`属性，即使`change`函数里面没有使用到`age`, 我们也会触发`change`函数。 所以我们要正确收集依赖，怎样正确收集依赖呢

-   不同的对象单独存储
-   同一个对象不同属性也要单独存储

-   存储对象我们可以使用 [WeakMap](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FWeakMap)

>   **`WeakMap`** 对象是一组键/值对的集合，其中的键是弱引用(原对象销毁的时候可以被垃圾回收)的。其键必须是`对象`，而值可以是任意的。

-   存储对象不同属性可以使用 [Map](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FMap)

>   **`Map`** 对象保存键值对，并且能够记住键的原始插入顺序。任何值(对象或者[原始值](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FGlossary%2FPrimitive)) 都可以作为一个键或一个值。

![image-20220621090916477](https://s2.loli.net/2022/07/09/g2t5rcvMYABqNXC.png)

```js
const targetMap = new WeakMap()
const getDepend = (target, key) => {
  // 根据target对象获取Map
  let desMap = targetMap.get(target)
  if (!desMap) {
    desMap = new Map()
    targetMap.set(target, desMap)
  }
  // 根据key获取 depend类
  let depend = desMap.get(key)
  if (!depend) {
    depend = new Depend()
    desMap.set(key, depend)
  }
  return depend
}


```



```js
const reactive = (obj) => {
  return new Proxy(obj, {
    get: (target, key) => {
      // 收集依赖
      const depend = getDepend(target, key)
      depend.addDepend()
      return Reflect.get(target, key)
    },
    set: (target, key, value) => {
      const depend = getDepend(target, key)
      Reflect.set(target, key, value)
      // 当值发生改变时 触发
      depend.notify()
    }
  })
}
```

### 3.如何理解composition API

在 Vue2 中，代码是 Options API 风格的，也就是通过填充 (option) data、methods、computed 等属性来完成一个 Vue 组件。这种风格使得 Vue 相对于 React极为容易上手，同时也造成了几个问题：

1. 由于 Options API 不够灵活的开发方式，使得Vue开发缺乏优雅的方法来在组件间共用代码。
2. Vue 组件过于依赖`this`上下文，Vue 背后的一些小技巧使得 Vue 组件的开发看起来与 JavaScript 的开发原则相悖，比如在`methods` 中的`this`竟然指向组件实例来不指向`methods`所在的对象。这也使得 TypeScript 在Vue2 中很不好用。

Options Api可以理解为就是组件的各个选项，data、methods、computed、watch等等就像是组件的一个个选项，在对应的选项里做对应的事情。

不在data中定义的数据，是无法做到响应式的，那是因为Object.definePropety只会对data选项中的数据进行递归拦截

因为所有的数据都是挂载在this下面，typescript的类型推导也很麻烦，代码的复用、公共组件的导入导出也都很困难

```js
export default {
    data () {
        return {
            // 定义响应式数据的选项
        }
    },
    methods: {
        // 定义相关方法的选项
    },
    computed: {
        // 计算属性的选项
    },
    watch: {
        // 监听数据的选项
    }
    ...
}
```



于是在 Vue3 中，舍弃了 Options API，转而投向 Composition API。Composition API本质上是将 Options API 背后的机制暴露给用户直接使用，这样用户就拥有了更多的灵活性，也使得 Vue3 更适合于 TypeScript 结合。

Composition Api，我们也从名字来看，Composition表示组合，在Compostion Api的写法中，没有选项的概念了，设计指向的是组合，各种功能模块的组合。Composition Api支持将相同的功能模块代码写在一起，甚至可以将某个功能单独的封装成函数，随意导入引用；也可以**将任意的数据定义成响应式，再也不用局限于data中，我们只需要将每个实现的功能组合起来就可以了**。

如下，是一个使用了 Vue Composition API 的 Vue3 组件：

```javascript
<template>
  <button @click="increment">
    Count: {{ count }}
  </button>
</template>
 
<script>
// Composition API 将组件属性暴露为函数，因此第一步是导入所需的函数
import { ref, computed, onMounted } from 'vue'
 
export default {
  setup() {
// 使用 ref 函数声明了称为 count 的响应属性，对应于Vue2中的data函数
    const count = ref(0)
 
// Vue2中需要在methods option中声明的函数，现在直接声明
    function increment() {
      count.value++
    }
 // 对应于Vue2中的mounted声明周期
    onMounted(() => console.log('component mounted!'))
 
    return {
      count,
      increment
    }
  }
}
</script>
```

显而易见，Vue Composition API 使得 Vue3 的开发风格更接近于原生 JavaScript，带给开发者更多地灵活性

例子

```js
<template>
    <div @click="add">{{count}}</div>
</template>
<script setup>
    import { ref } from "vue";
    let count = ref(0);
    function add () {
        count.value++;
    };
</script>
```

功能单独封装成一个函数，供其他地方引用

定义一个新的count.js文件。

```js
import { ref } from "vue";
export default function Count () {
    let count = ref(0);
    function add () {
        count.value++;
    };
    return {count, add};
}
```

在我们的源代码里，只需要引入一下。

```js
<template>
    <div @click="add">{{count}}</div>
</template>
<script setup>
    import Count from "./count.js";
    const { count, add } = Count();
</script>
```

添加一个计算属性，每当count的值改变的时候，就计算出count * 2的值，大家应该马上就想到了computed，而在Compostion Api中，computed需要通过import导入使用。

```vue
<template>
    <div @click="add">{{count}}</div>
    <div>{{doubleCount}}</div>
</template>
<script setup>
    import { computed } from "vue";
    import Count from "./count.js";
    const { count, add } = Count();
    let doubleCount = computed(() => count.value * 2);
</script>

```

现在给count加点颜色，如果count是偶数，想让文字显示红色，如果是奇数，让文字显示绿色，这次我们使用watch来实现，在Composition Api中对应的是watchEffect。

```vue
<style scope>
    .count{
        color: v-bind(color)
    }
</style>
<template>
    <div @click="add" class="count">{{count}}</div>
    <div>{{doubleCount}}</div>
</template>
<script setup>
    import { computed, ref, watchEffect } from "vue";
    ...
    let color = ref('red');
    const watchEffectStop = watchEffect(() => {
        if (count.value % 2) {
            color.value = 'green';
        } else {
            color.value = 'red';
        }
    })
</script>
```

我们已经添加了一个watchEffect来监听count值的变化，相对于Vue2中的watch方法，watchEffect的使用还是有一些差别的。

1.  watchEffect是立即执行的，不需要添加immediate属性。

1.  watchEffect不需要指定对某个具体的数据监听，watchEffect会根据内容自动去感知，所以我们也可以在一个watchEffect中添加多个数据的监听处理（如果watchEffect中没有任何响应式数据，会不会执行呢？大家可以试一下）。

1.  watchEffect不能获取数据改变之前的值。

同时，watchEffect会返回一个对象watchEffectStop，通过执行watchEffectStop，我们可以控制监听在什么时候结束

### 5. Composition API与React Hook很像，区别是什么

从React Hook的实现角度看，React Hook是根据useState调用的顺序来确定下一次重渲染时的state是来源于哪个useState，所以出现了以下限制

- 不能在循环、条件、嵌套函数中调用Hook
- 必须确保总是在你的React函数的顶层调用Hook
- useEffect、useMemo等函数必须手动确定依赖关系

而Composition API是基于Vue的响应式系统实现的，与React Hook的相比

- 声明在setup函数内，一次组件实例化只调用一次setup，而React Hook每次重渲染都需要调用Hook，使得React的GC比Vue更有压力，性能也相对于Vue来说也较慢
- Compositon API的调用不需要顾虑调用顺序，也可以在循环、条件、嵌套函数中使用
- 响应式系统自动实现了依赖收集，进而组件的部分的性能优化由Vue内部自己完成，而React Hook需要手动传入依赖，而且必须必须保证依赖的顺序，让useEffect、useMemo等函数正确的捕获依赖变量，否则会由于依赖不正确使得组件性能下降。

虽然Compositon API看起来比React Hook好用，但是其设计思想也是借鉴React Hook的。



### 4.reactive和ref

![image-20220316213837203](https://s2.loli.net/2022/07/09/sKM9SQYpnGNUuOk.png)

`toRefs`会将我们一个`响应式`的对象转变为一个`普通`对象，然后将这个`普通对象`里的每一个属性变为一个响应式的数据



### 5.setup

`setup` 选项是一个接收 `props` 和 `context` 的函数

```js
export default{
    name: 'test',
    setup(props,context){

     return {}   // 这里返回的任何内容都可以用于组件的其余部分
    }
    // 组件的“其余部分”
}

```

接收一个`props`和`context`函数并且将`setup`内的内容通过`return`暴露给组件的其余部分

-   由于在执行 setup函数的时候，还没有执行 Created 生命周期方法，所以在 setup 函数中，无法使用 data 和 methods 的变量和方法
-   由于我们不能在 setup函数中使用 data 和 methods，所以 Vue 为了避免我们错误的使用，直接将 setup函数中的this 修改成了 undefined

![image-20220316213946552](https://s2.loli.net/2022/07/09/hPEXqI385x19o4n.png)



#### setup script优势

##### 1.自动注册子组件

vue3语法

在引入Child组件后，需要在components中注册对应的组件才可使用

```js
<template>
  <div>
    <h2>我是父组件!</h2>
    <Child />
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';
import Child from './Child.vue'

export default defineComponent({
  components: {
      Child
  },
  setup() {

    return {
      
    }
  }
});
</script>
```

setup script写法

直接省略了子组件注册的过程

```js
<template>
  <div>
    <h2>我是父组件!-setup script</h2>
    <Child />
  </div>
</template>

<script setup>
import Child from './Child.vue'

</script>
```

##### 2.属性和方法无需返回

composition API写起来有点繁琐的原因在于需要手动返回模板需要使用的属性和方法。

而在setup script中可以省略这一步

vue3语法

```js
<template>
  <div>
    <h2 @click="ageInc">{{ name }} is {{ age }}</h2>
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  setup() {
    const name = ref('CoCoyY1')
    const age = ref(18)

    const ageInc = () => {
      age.value++
    }

    return {
      name,
      age,

      ageInc
    }
  }
})
</script>
```

setup script语法

```js
<template>
  <div>
    <h2 @click="ageInc">{{ name }} is {{ age }}</h2>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const name = ref('CoCoyY1')
const age = ref(18)

const ageInc = () => {
  age.value++
}

</script>
```

##### 3.支持props、emit和context

vue3语法

```js
//Father.vue
<template>
  <div >
    <h2 >我是父组件！</h2>
    <Child msg="hello" @child-click="childCtx" />
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';
import Child from './Child.vue';

export default defineComponent({
  components: {
    Child
  },
  setup(props, context) {
    const childCtx = (ctx) => {
      console.log(ctx);
    }

    return {
      childCtx
    }
  }
})
</script>


//Child.vue
<template>
  <span @click="handleClick">我是子组件! -- msg: {{ props.msg }}</span>
</template>

<script>
import { defineComponent, ref } from 'vue'

export default defineComponent({
  emits: [
    'child-click'
  ],
  props: {
    msg: String
  },
  setup(props, context) {
    const handleClick = () => {
      context.emit('child-click', context)
    }

    return {
      props,
      handleClick
    }
  },
})
</script>
```

setup script写法

```js
//Father.vue
<template>
  <div >
    <h2 >我是父组件！</h2>
    <Child msg="hello" @child-click="childCtx" />
  </div>
</template>

<script setup>
import Child from './Child.vue';

const childCtx = (ctx) => {
  console.log(ctx);
}
</script>


//Child.vue
<template>
  <span @click="handleClick">我是子组件! -- msg: {{ props.msg }}</span>
</template>

<script setup>
import { useContext, defineProps, defineEmit } from 'vue'

const emit = defineEmit(['child-click'])
const ctx = useContext()
const props = defineProps({
  msg: String
})

const handleClick = () => {
  emit('child-click', ctx)
}
</script>

```

setup script语法糖提供了三个新的API来供我们使用：`defineProps`、`defineEmit`和`useContext`。

其中`defineProps`用来接收父组件传来的值props。`defineEmit`用来声明触发的事件表。`useContext`用来获取组件上下文context



### 6.Vue3 中 watch 与 watchEffect 有什么区别？

同一个功能的两种不同形态，底层的实现是一样的。

-   `watch`- 显式指定依赖源，依赖源更新时执行回调函数
-   `watchEffect` - 自动收集依赖源，依赖源更新时重新执行自身

Watch

这里的依赖源函数只会执行一次，回调函数会在每次依赖源改变的时候触发，但是并不对回调函数进行依赖收集。也就是说，依赖源和回调函数之间并不一定要有直接关系

```js
watch(
  () => { /* 依赖源收集函数 */ },
  () => { /* 依赖源改变时的回调函数 */ }
)
```

WatchEffect

`watchEffect` 相当于将 `watch` 的依赖源和回调函数合并，当任何你有用到的响应式依赖更新时，该回调函数便会重新执行。不同于 `watch`，`watchEffect` 的回调函数会被立即执行（即 `{ immediate: true }`）

简单理解watchEffect会在第一次运行时创建副作用函数并执行一次，如果存在响应式变量，取值会触发get函数，这个时候收集依赖存储起来，当其他地方给响应式变量重新赋值的时候，set函数中会触发方法派发更新，执行收集到的副作用函数，如果不存在响应式变量，就不会被收集触发

```js
watchEffect(
  () => { /* 依赖源同时是回调函数 */ }
)
```

## Virtual DOM

所谓的virtual dom，也就是虚拟节点。它通过JS的Object对象模拟DOM中的节点，然后再通过特定的render方法将其渲染成真实的DOM节点 dom diff 则是通过JS层面的计算，返回一个patch对象，即补丁对象，在通过特定的操作解析patch对象，完成页面的重新渲染![img](https://s2.loli.net/2022/07/09/8Bikdpt2xeu41EM.webp)



### 1.如何理解虚拟DOM

从本质上来说，Virtual Dom是一个JavaScript对象，通过对象的方式来表示DOM结构。将页面的状态抽象为JS对象的形式，配合不同的渲染工具，使跨平台渲染成为可能。通过事务处理机制，将多次DOM修改的结果一次性的更新到页面上，从而有效的减少页面渲染的次数，减少修改DOM的重绘重排次数，提高渲染性能。

虚拟DOM是对DOM的抽象，这个对象是更加轻量级的对 DOM的描述。它设计的最初目的，就是更好的跨平台，比如Node.js就没有DOM，如果想实现SSR，那么一个方式就是借助虚拟DOM，因为虚拟DOM本身是js对象。 在代码渲染到页面之前，vue会把代码转换成一个对象（虚拟 DOM）。以对象的形式来描述真实DOM结构，最终渲染到页面。在每次数据发生变化前，虚拟DOM都会缓存一份，变化之时，现在的虚拟DOM会与缓存的虚拟DOM进行比较。在vue内部封装了diff算法，通过这个算法来进行比较，渲染时修改改变的变化，原先没有发生改变的通过原先的数据进行渲染。

另外现代前端框架的一个基本要求就是无须手动操作DOM，一方面是因为手动操作DOM无法保证程序性能，多人协作的项目中如果review不严格，可能会有开发者写出性能较低的代码，另一方面更重要的是省略手动DOM操作可以大大提高开发效率。

>   真实DOM 结构

```html
<div class="container">
  <p>哈哈</p>
  <ul class="list">
    <li>1</li>
    <li>2</li>
  </ul>
</div>
```


```js


{ 
  // 选择器
  "sel": "div",
  // 数据
  "data": {
    "class": { "container": true }
  },
  // DOM
  "elm": undefined,
  // 和 Vue :key 一样是一种优化
  "key": undefined,
  // 子节点
  "children": [
    {
      "elm": undefined,
      "key": undefined,
      "sel": "p",
      "data": { "text": "哈哈" }
    },
    {
      "elm": undefined,
      "key": undefined,
      "sel": "ul",
      "data": {
        "class": { "list": true }
      },
      "children": [
        {
          "elm": undefined,
          "key": undefined,
          "sel": "li",
          "data": {
            "text": "1"
          },
          "children": undefined
        },
        {
          "elm": undefined,
          "key": undefined,
          "sel": "li",
          "data": {
            "text": "1"
          },
          "children": undefined
        }
      ]
    }
  ]
}


```



### 2.Diff算法

[精简版](https://juejin.cn/post/6986463124358430756)

#### 基本比较

在新老虚拟DOM对比时：

- 首先，对比节点本身，判断是否为同一节点，如果不为相同节点，则删除该节点重新创建节点进行替换
- 如果为相同节点，进行patchVnode，判断如何对该节点的子节点进行处理，先判断一方有子节点一方没有子节点的情况(如果新的children没有子节点，将旧的子节点移除)
- 比较如果都有子节点，则进行updateChildren，判断如何对这些新老节点的子节点进行操作（diff核心）。
- 匹配时，找到相同的子节点，递归比较子节点

在diff中，只对同层的子节点进行比较，放弃跨级的节点比较，使得时间复杂从O(n3)降低值O(n)，也就是说，只有当新旧children都为多个子节点时才需要用核心的Diff算法进行同层级比较。

![img](https://s2.loli.net/2022/07/09/L2ypftSn8dj14Kh.webp)

> Diff算法真的很美，整个流程如下图所示：

![diffvue2](https://s2.loli.net/2022/07/09/N8ir4wRuO2PpjvQ.png)

**一、 首先比较一下新旧节点是不是同一个节点（可通过比较sel（选择器）和key（唯一标识）值是不是相同），不是同一个节点则进行暴力删除（注：先以旧节点为基准插入新节点，然后再删除旧节点）。**

**二、 若是同一个节点则需要进一步比较**

1. 完全相同，不做处理
2. 新节点内容为文本，直接替换完事
3. 新节点有子节点，这个时候就要仔细考虑一下了:若老节点没有子元素，则直接清空老节点，将新节点的子元素插入即可；若老节点有子元素则就需要按照上述的更新策略老搞定了

我们把旧节点的一个元素称为旧前节点 旧节点的最后一个元素称为旧后节点
新节点的一个元素称为新前节点 新节点的最后一个元素称为新后节点
精细化比较主要分为五种情况

1. 旧前节点 === 新前节点
2. 旧后节点 === 新后节点
3. 新后节点 === 旧前节点
4. 新前节点 === 旧后节点
5. 以上四种情况都不满足，遍历旧节点所有子元素，寻找是否有新节点的元素

以上五种情况顺序执行。满足其中一种情况，后续的就不在比较，就会去下一个节点进行比较

## Diff 算法核心原理——源码

上面说了Diff 算法，在 Vue 里面就是 patch，铺垫了这么多，下面进入源码里看一下这个神乎其神的 patch 干了啥？

### patch

源码地址：`src/core/vdom/patch.js -700行`

其实 patch 就是一个函数，我们先介绍一下源码里的核心流程，再来看一下 patch 的源码，源码里每一行也有注释

它可以接收四个参数，主要还是前两个

- **oldVnode**：老的虚拟 DOM 节点
- **vnode**：新的虚拟 DOM 节点
- **hydrating**：是不是要和真实 DOM 混合，服务端渲染的话会用到，这里不过多说明
- **removeOnly**：transition-group 会用到，这里不过多说明

主要流程是这样的：

- vnode 不存在，oldVnode 存在，就删掉 oldVnode
- vnode 存在，oldVnode 不存在，就创建 vnode
- 两个都存在的话，通过 sameVnode 函数(后面有详解)对比是不是同一节点
  - 如果是同一节点的话，通过 patchVnode 进行后续对比节点文本变化或子节点变化
  - 如果不是同一节点，就把 vnode 挂载到 oldVnode 的父元素下
    - 如果组件的根节点被替换，就遍历更新父节点，然后删掉旧的节点
    - 如果是服务端渲染就用 hydrating 把 oldVnode 和真实 DOM 混合

### patchVnode

源码地址：`src/core/vdom/patch.js -501行`

**这个是在新的 vnode 和 oldVnode 是同一节点的情况下，才会执行的函数，主要是对比节点文本变化或子节点变化**

- 如果 vnode 不是文本节点也不是注释的情况下
  - 如果 vnode 和 oldVnode 都有子节点，而且子节点不一样的话，就调用 updateChildren 更新子节点
  - 如果只有 vnode 有子节点，就调用 addVnodes 创建子节点
  - 如果只有 oldVnode 有子节点，就调用 removeVnodes 删除该子节点
  - 如果 vnode 文本为 undefined，就删掉 vnode.elm 文本

#### vue2双端比较

所谓`双端比较`就是**新列表**和**旧列表**两个列表的头与尾互相对比，，在对比的过程中指针会逐渐向内靠拢，直到某一个列表的节点全部遍历过，对比停止。

我们先用四个指针指向两个列表的头尾

```js
function vue2Diff(prevChildren, nextChildren, parent) {
  let oldStartIndex = 0,
    oldEndIndex = prevChildren.length - 1
    newStartIndex = 0,
    newEndIndex = nextChildren.length - 1;
  let oldStartNode = prevChildren[oldStartIndex],
    oldEndNode = prevChildren[oldEndIndex],
    newStartNode = nextChildren[nextStartIndex],
    newEndNode = nextChildren[nextEndIndex];
}
```

我们根据四个指针找到四个节点，然后进行对比，那么如何对比呢？我们按照以下四个步骤进行对比

1.  使用**旧列表**的头一个节点`oldStartNode`与**新列表**的头一个节点`newStartNode`对比
2.  使用**旧列表**的最后一个节点`oldEndNode`与**新列表**的最后一个节点`newEndNode`对比
3.  使用**旧列表**的头一个节点`oldStartNode`与**新列表**的最后一个节点`newEndNode`对比
4.  使用**旧列表**的最后一个节点`oldEndNode`与**新列表**的头一个节点`newStartNode`对比

使用以上四步进行对比，去寻找`key`相同的可复用的节点，当在某一步中找到了则停止后面的寻找。

##### 非理想情况

有一种特殊情况，当四次对比都**没找到**复用节点时，我们只能拿**新列表**的第一个节点去**旧列表**中找与其`key`相同的节点

找节点的时候其实会有两种情况：一种在**旧列表**中找到了，另一种情况是没找到

当我们在旧列表中找到对应的`VNode`，我们只需要将找到的节点的`DOM`元素，移动到开头就可以了。这里的逻辑其实和`第四步`的逻辑是一样的，只不过`第四步`是移动的尾节点，这里是移动找到的节点。`DOM`移动后，由我们将**旧列表**中的节点改为`undefined`，这是**至关重要**的一步，因为我们已经做了节点的移动了所以我们不需要进行再次的对比了。最后我们将头指针`newStartIndex`向后移一位

如果在**旧列表**中没有找到复用节点，直接创建一个新的节点放到最前面就可以了，然后后**移头指针`newStartIndex`**。

最后当**旧列表**遍历到`undefind`时就跳过当前节点。

#### vue3 最长递增子序列

### vue3的优化

事件缓存：将事件缓存，可以理解为变成静态的了

添加静态标记：Vue2 是全量 Diff，Vue3 是静态标记 + 非全量 Diff

静态提升：创建静态节点时保存，后续直接复用

使用最长递增子序列优化了对比流程：Vue2 里在 updateChildren() 函数里对比变更，在 Vue3 里这一块的逻辑主要在 patchKeyedChildren() 函数里，具体看下面



在 Vue2 里 `updateChildren` 会进行

- 头和头比
- 尾和尾比
- 头和尾比
- 尾和头比
- 都没有命中的对比

在 Vue3 里 `patchKeyedChildren` 为

- 头和头比
- 尾和尾比
- 基于最长递增子序列进行移动/添加/删除

看个例子，比如

- 老的 children：`[ a, b, c, d, e, f, g ]`
- 新的 children：`[ a, b, f, c, d, e, h, g ]`

1. 先进行头和头比，发现不同就结束循环，得到 `[ a, b ]`
2. 再进行尾和尾比，发现不同就结束循环，得到 `[ g ]`
3. 再保存没有比较过的节点 `[ f, c, d, e, h ]`，并通过 newIndexToOldIndexMap 拿到在数组里对应的下标，生成数组 `[ 5, 2, 3, 4, -1 ]`，`-1` 是老数组里没有的就说明是新增
4. 然后再拿取出数组里的最长递增子序列，也就是 `[ 2, 3, 4 ]` 对应的节点 `[ c, d, e ]`
5. 然后只需要把其他剩余的节点，基于 `[ c, d, e ]` 的位置进行移动/新增/删除就可以了



其实就简单的看一眼我们就能发现，这两段文字是有一部分是相同的，**这些文字是不需要修改也不需要移动的**，真正需要进行修改中间的几个字母，所以`diff`就变成以下部分

```
text1: 'llo'
text2: 'y'
```

接下来换成`vnode`，我们以下图为例。

![img](https://s2.loli.net/2022/07/09/bq1JOZ6k2AxmtKG.webp)

图中的被绿色框起来的节点，他们是不需要移动的，只需要进行打补丁`patch`就可以了。我们把该逻辑写成代码。

```js
function vue3Diff(prevChildren, nextChildren, parent) {
  let j = 0,
    prevEnd = prevChildren.length - 1,
    nextEnd = nextChildren.length - 1,
    prevNode = prevChildren[j],
    nextNode = nextChildren[j];
  while (prevNode.key === nextNode.key) {
    patch(prevNode, nextNode, parent)
    j++
    prevNode = prevChildren[j]
    nextNode = nextChildren[j]
  }
  
  prevNode = prevChildren[prevEnd]
  nextNode = prevChildren[nextEnd]
  
  while (prevNode.key === nextNode.key) {
    patch(prevNode, nextNode, parent)
    prevEnd--
    nextEnd--
    prevNode = prevChildren[prevEnd]
    nextNode = prevChildren[nextEnd]
  }
}

```

这时候，我们就需要考虑边界情况了，这里有两种情况。一种是`j > prevEnd`；另一种是`j > nextEnd`。

![img](https://s2.loli.net/2022/07/09/rCXlFyTzNUj8JDm.webp)

我们以这张图为例，此时`j > prevEnd`且`j <= nextEnd`，我们只需要把**新列表**中`j`到`nextEnd`之间剩下的节点**插入**进去就可以了。相反， 如果`j > nextEnd`时，我们把**旧列表**中`j`到`prevEnd`之间的节点**删除**就可以了。

```js
function vue3Diff(prevChildren, nextChildren, parent) {
  // ...
  if (j > prevEnd && j <= nextEnd) {
    let nextpos = nextEnd + 1,
      refNode = nextpos >= nextChildren.length
                ? null
                : nextChildren[nextpos].el;
    while(j <= nextEnd) mount(nextChildren[j++], parent, refNode)
    
  } else if (j > nextEnd && j <= prevEnd) {
    while(j <= prevEnd) parent.removeChild(prevChildren[j++].el)
  }
}

```

我们再继续思考，在我们`while`循环时，指针是从两端向内逐渐靠拢的，所以我们应该在循环中就应该去判断边界情况，我们使用`label`语法，当我们触发边界情况时，退出全部的循环，直接进入判断。代码如下：

```js
function vue3Diff(prevChildren, nextChildren, parent) {
  let j = 0,
    prevEnd = prevChildren.length - 1,
    nextEnd = nextChildren.length - 1,
    prevNode = prevChildren[j],
    nextNode = nextChildren[j];
  // label语法
  outer: {
    while (prevNode.key === nextNode.key) {
      patch(prevNode, nextNode, parent)
      j++
      // 循环中如果触发边界情况，直接break，执行outer之后的判断
      if (j > prevEnd || j > nextEnd) break outer
      prevNode = prevChildren[j]
      nextNode = nextChildren[j]
    }

    prevNode = prevChildren[prevEnd]
    nextNode = prevChildren[nextEnd]

    while (prevNode.key === nextNode.key) {
      patch(prevNode, nextNode, parent)
      prevEnd--
      nextEnd--
      // 循环中如果触发边界情况，直接break，执行outer之后的判断
      if (j > prevEnd || j > nextEnd) break outer
      prevNode = prevChildren[prevEnd]
      nextNode = prevChildren[nextEnd]
    }
  }
  
  // 边界情况的判断
  if (j > prevEnd && j <= nextEnd) {
    let nextpos = nextEnd + 1,
      refNode = nextpos >= nextChildren.length
                ? null
                : nextChildren[nextpos].el;
    while(j <= nextEnd) mount(nextChildren[j++], parent, refNode)
    
  } else if (j > nextEnd && j <= prevEnd) {
    while(j <= prevEnd) parent.removeChild(prevChildren[j++].el)
  }
}

```

#### 判断是否需要移动

其实几个算法看下来，套路已经很明显了，就是找到移动的节点，然后给他移动到正确的位置。把该加的新节点添加好，把该删的旧节点删了，整个算法就结束了。这个算法也不例外，我们接下来看一下它是如何做的。

当`前/后置`的预处理结束后，我们进入真正的`diff`环节。首先，我们先根据**新列表**剩余的节点数量，创建一个`source`数组，并将数组填满`-1`。

![img](https://s2.loli.net/2022/07/09/9giAxkHRQEBv2h5.webp)

我们先写这块逻辑。

```js
function vue3Diff(prevChildren, nextChildren, parent) {
  //...
  outer: {
  // ...
  }
  
  // 边界情况的判断
  if (j > prevEnd && j <= nextEnd) {
    // ...
  } else if (j > nextEnd && j <= prevEnd) {
    // ...
  } else {
    let prevStart = j,
      nextStart = j,
      nextLeft = nextEnd - nextStart + 1,     // 新列表中剩余的节点长度
      source = new Array(nextLeft).fill(-1);  // 创建数组，填满-1
     
  }
}
```

那么这个`source`数组，是要做什么的呢？他就是来做新旧节点的对应关系的，我们将**新节点**在**旧列表**的位置存储在该数组中，我们在根据`source`计算出它的`最长递增子序列`用于移动DOM节点。为此，我们先建立一个对象存储当前**新列表**中的`节点`与`index`的关系，再去**旧列表**中去找位置。

在找节点时要注意，**如果旧节点在新列表中没有的话，直接删除就好**。除此之外，我们还需要一个数量表示记录我们已经`patch`过的节点，如果数量已经与**新列表**剩余的节点数量一样，那么剩下的`旧节点`我们就直接删除了就可以了

```js
function vue3Diff(prevChildren, nextChildren, parent) {
  //...
  outer: {
  // ...
  }
  
  // 边界情况的判断
  if (j > prevEnd && j <= nextEnd) {
    // ...
  } else if (j > nextEnd && j <= prevEnd) {
    // ...
  } else {
    let prevStart = j,
      nextStart = j,
      nextLeft = nextEnd - nextStart + 1,     // 新列表中剩余的节点长度
      source = new Array(nextLeft).fill(-1),  // 创建数组，填满-1
      nextIndexMap = {},                      // 新列表节点与index的映射
      patched = 0;                            // 已更新过的节点的数量
      
    // 保存映射关系  
    for (let i = nextStart; i <= nextEnd; i++) {
      let key = nextChildren[i].key
      nextIndexMap[key] = i
    } 
    
    // 去旧列表找位置
    for (let i = prevStart; i <= prevEnd; i++) {
      let prevNode = prevChildren[i],
      	prevKey = prevNode.key,
        nextIndex = nextIndexMap[prevKey];
      // 新列表中没有该节点 或者 已经更新了全部的新节点，直接删除旧节点
      if (nextIndex === undefind || patched >= nextLeft) {
        parent.removeChild(prevNode.el)
        continue
      }
      // 找到对应的节点
      let nextNode = nextChildren[nextIndex];
      patch(prevNode, nextNode, parent);
      // 给source赋值
      source[nextIndex - nextStart] = i
      patched++
    }
  }
}
```

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/01f5d145e4a84e3f922b4d39f80bcb6a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

找到位置后，我们观察这个重新赋值后的`source`，我们可以看出，如果是全新的节点的话，其在`source`数组中对应的值就是初始的`-1`，通过这一步我们可以区分出来哪个为全新的节点，哪个是可复用的。

其次，我们要判断是否需要移动。那么如何判断移动呢？很简单，和`React`一样我们用递增法，如果我们找到的`index`是一直递增的，说明不需要移动任何节点。我们通过设置一个变量来保存是否需要移动的状态。

```js
function vue3Diff(prevChildren, nextChildren, parent) {
  //...
  outer: {
  // ...
  }
  
  // 边界情况的判断
  if (j > prevEnd && j <= nextEnd) {
    // ...
  } else if (j > nextEnd && j <= prevEnd) {
    // ...
  } else {
    let prevStart = j,
      nextStart = j,
      nextLeft = nextEnd - nextStart + 1,     // 新列表中剩余的节点长度
      source = new Array(nextLeft).fill(-1),  // 创建数组，填满-1
      nextIndexMap = {},                      // 新列表节点与index的映射
      patched = 0,
      move = false,                           // 是否移动
      lastIndex = 0;                          // 记录上一次的位置
      
    // 保存映射关系  
    for (let i = nextStart; i <= nextEnd; i++) {
      let key = nextChildren[i].key
      nextIndexMap[key] = i
    } 
    
    // 去旧列表找位置
    for (let i = prevStart; i <= prevEnd; i++) {
      let prevNode = prevChildren[i],
      	prevKey = prevNode.key,
        nextIndex = nextIndexMap[prevKey];
      // 新列表中没有该节点 或者 已经更新了全部的新节点，直接删除旧节点
      if (nextIndex === undefind || patched >= nextLeft) {
        parent.removeChild(prevNode.el)
        continue
      }
      // 找到对应的节点
      let nextNode = nextChildren[nextIndex];
      patch(prevNode, nextNode, parent);
      // 给source赋值
      source[nextIndex - nextStart] = i
      patched++
      
      // 递增方法，判断是否需要移动
      if (nextIndex < lastIndex) {
      	move = false
      } else {
      	lastIndex = nextIndex
      }
    }
    
    if (move) {
    
    // 需要移动
    } else {
	
    //不需要移动
    }
  }
}

```

然而在`vue3.0`中，我们需要的是最长递增子序列在原本数组中的索引。所以我们还需要在创建一个数组用于保存每个值的最长子序列所对应在数组中的`index`

在 vue2 中是通过对旧节点列表建立一个 `{ key, oldVnode }`的映射表，然后遍历新节点列表的剩余节点，根据`newVnode.key`在旧映射表中寻找可复用的节点，然后打补丁并且移动到正确的位置。

而在 vue3 中是**建立一个存储新节点数组中的剩余节点在旧节点数组上的索引的映射关系数组**，建立完成这个数组后也即找到了**可复用的节点**，然后通过这个**数组计算得到最长递增子序列**，这个序列中的节点保持不动，然后将**新节点数组中的剩余节点移动到正确的位置**。

### 3. DOM如何移动

判断完是否需要移动后，我们就需要考虑如何移动了。一旦需要进行DOM移动，我们首先要做的就是找到`source`的**最长递增子序列**。

### 3.虚拟DOM怎么解析

虚拟DOM的解析过程：

- 首先对将要插入到文档中的 DOM 树结构进行分析，使用 js 对象将其表示出来，比如一个元素对象，包含 TagName、props 和 Children 这些属性。然后将这个 js 对象树给保存下来，最后再将 DOM 片段插入到文档中。
- 当页面的状态发生改变，需要对页面的 DOM 的结构进行调整的时候，首先根据变更的状态，重新构建起一棵对象树，然后将这棵新的对象树和旧的对象树进行比较，记录下两棵树的的差异。
- 最后将记录的有差异的地方应用到真正的 DOM 树中去，这样视图就更新了。

### 4.虚拟DOM性能真的好吗

- MVVM框架解决视图和状态同步问题

- 模板引擎可以简化视图操作,没办法跟踪状态

- 虚拟DOM跟踪状态变化

- 参考github上

  virtual-dom

  的动机描述

  - 虚拟DOM可以维护程序的状态,跟踪上一次的状态
  - 通过比较前后两次状态差异更新真实DOM

- 跨平台使用

  - 浏览器平台渲染DOM
  - 服务端渲染SSR(Nuxt.js/Next.js),前端是vue向,后者是react向
  - 原生应用(Weex/React Native)
  - 小程序(mpvue/uni-app)等

- 真实DOM的属性很多，创建DOM节点开销很大

- 虚拟DOM只是普通JavaScript对象，描述属性并不需要很多，创建开销很小

- **复杂视图情况下提升渲染性能**(操作dom性能消耗大,减少操作dom的范围可以提升性能)

- **复杂视图情况下提升渲染性能**,因为`虚拟DOM+Diff算法`可以精准找到DOM树变更的地方,减少DOM的操作(重排重绘)

**（1）保证性能下限，在不进行手动优化的情况下，提供过得去的性能** 看一下页面渲染的流程：**解析HTML -> 生成DOM -> 生成 CSSOM -> Layout -> Paint -> Compiler** 下面对比一下修改DOM时真实DOM操作和Virtual DOM的过程，来看一下它们重排重绘的性能消耗∶

- 真实DOM∶ 生成HTML字符串＋重建所有的DOM元素
- 虚拟DOM∶ 生成vNode+ DOMDiff＋必要的dom更新

Virtual DOM的更新DOM的准备工作耗费更多的时间，也就是JS层面，相比于更多的DOM操作它的消费是极其便宜的。尤雨溪在社区论坛中说道∶ 框架给你的保证是，你不需要手动优化的情况下，依然可以给你提供过得去的性能。 **（2）跨平台** Virtual DOM本质上是JavaScript的对象，它可以很方便的跨平台操作，比如服务端渲染、uniapp等。

- 首次渲染大量DOM时，由于多了一层虚拟DOM的计算，会比innerHTML插入慢。
- 正如它能保证性能下限，在真实DOM操作的时候进行针对性的优化时，还是更快的。

### 5.Vue key的作用是什么，为什么不建议index做key

vue 中 key 值的作用可以分为两种情况来考虑：

- 第一种情况是 v-if 中使用 key。由于 Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。因此当使用 v-if 来实现元素切换的时候，如果切换前后含有相同类型的元素，那么这个元素就会被复用。如果是相同的 input 元素，那么切换前后用户的输入不会被清除掉，这样是不符合需求的。因此可以通过使用 key 来唯一的标识一个元素，这个情况下，使用 key 的元素不会被复用。这个时候 key 的作用是用来标识一个独立的元素。
- 第二种情况是 v-for 中使用 key。用 v-for 更新已渲染过的元素列表时，它默认使用“就地复用”的策略。如果数据项的顺序发生了改变，Vue 不会移动 DOM 元素来匹配数据项的顺序，而是简单复用此处的每个元素。因此通过为每个列表项提供一个 key 值，来以便 Vue 跟踪元素的身份，从而高效的实现复用。这个时候 key 的作用是为了高效的更新渲染虚拟 DOM。

key 是为 Vue 中 vnode 的唯一标记，通过这个 key，diff 操作可以更准确、更快速

- 更准确：因为带 key 就不是就地复用了，在 sameNode 函数a.key === b.key对比中可以避免就地复用的情况。所以会更加准确。
- 更快速：利用 key 的唯一性生成 map 对象来获取对应节点，比遍历方式更快

使用index 作为 key和没写基本上没区别，因为不管数组的顺序怎么颠倒，index 都是 0, 1, 2...这样排列，导致 Vue 会复用错误的旧子节点，做很多额外的工作。



# vue框架分析与源码

## Vue2 完整的生命周期

大致可分成四个阶段

- 初始化阶段：为 Vue 实例初始化一些事件、属性和响应式数据等
- 模板编译阶段：把我们写的 `<template></template>` 模板编译成渲染函数 `render`
- 挂载阶段：把模板渲染到真实的 DOM 节点上，以及数据变更时执行更新操作
- 销毁阶段：把组件实例从父组件中删除，并取消依赖监听和事件监听

![image.png](https://s2.loli.net/2022/07/14/MAXPybl3861jqng.webp)

### 初始化阶段

### new Vue()

这个阶段做的第一件事，就是用 new 创建一个 Vue 实例对象

```js
new Vue({
    el:'#app',
    store,
    router,
    render: h => h(App)
})
复制代码
```

能用 new 那肯定是有一个构造函数的，我们来看一下

源码地址：`src/core/instance/index.js - 8行`

```js
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' && !(this instanceof Vue) ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}
initMixin(Vue)
复制代码
```

可以看出 new Vue() 的关键就是 `_init()` 了，来看一下它是哪来的，干了些什么

### _init()

源码地址：`src/core/instance/init.js - 15行`

我这里去掉了一些环境判断的，主要流程就是

- 合并配置，主要是把一些内置组件`<component/>`、`<keep-alive>`、`<transition>`、`directive`、`filter`、本文最开始的钩子函数名称列表等合并到 Vue.options上面
- 调用一些初始化函数，这里具体初始化了哪些东西我写在注释里了
- 触发生命周期钩子，`beforeCreate` 和 `created`
- 最后调用 `$mount` 挂载 进入下一阶段

```js
export function initMixin (Vue: Class<Component>) {
  // 在原型上添加 _init 方法
  Vue.prototype._init = function (options?: Object) {
    // 保存当前实例
    const vm: Component = this
    // 合并配置
    if (options && options._isComponent) {
      // 把子组件依赖父组件的 props、listeners 挂载到 options 上，并指定组件的$options
      initInternalComponent(vm, options)
    } else {
      // 把我们传进来的 options 和当前构造函数和父级的 options 进行合并，并挂载到原型上
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    vm._self = vm
    initLifecycle(vm) // 初始化实例的属性、数据：$parent, $children, $refs, $root, _watcher...等
    initEvents(vm) // 初始化事件：$on, $off, $emit, $once
    initRender(vm) // 初始化渲染： render, mixin
    callHook(vm, 'beforeCreate') // 调用生命周期钩子函数
    initInjections(vm) // 初始化 inject
    initState(vm) // 初始化组件数据：props, data, methods, watch, computed
    initProvide(vm) // 初始化 provide
    callHook(vm, 'created') // 调用生命周期钩子函数

    if (vm.$options.el) {
      // 如果传了 el 就会调用 $mount 进入模板编译和挂载阶段
      // 如果没有传就需要手动执行 $mount 才会进入下一阶段
      vm.$mount(vm.$options.el)
    }
  }
}
复制代码
```

这里需要记着上面流程说明，合并了哪些配置，初始化了什么东西，分别在什么时候调用了两个生命周期钩子函数，最后调用 `$mount` 挂载，进入下一阶段

### $mount()

源码地址：`dist/vue.js - 11927行`

```js
  Vue.prototype.$mount = function ( el, hydrating ) {
    el = el && query(el);
    var options = this.$options;
    // 如果没有 render 
    if (!options.render) {
      var template = options.template;
      // 再判断，如果有 template
      if (template) {
        if (typeof template === 'string') {
          if (template.charAt(0) === '#') {
            template = idToTemplate(template);
          }
        } else if (template.nodeType) {
          template = template.innerHTML;
        } else {
          return this
        }
      // 再判断，如果有 el
      } else if (el) {
        template = getOuterHTML(el);
      }
    }
    return mount.call(this, el, hydrating)
  };
复制代码
```

说白了 `$mount` 主要就是判断要不要编译，使用哪一个模板编译，需要注意的就是判断顺序了，我们来看一下这段代码

优先判断 `render` 是否存在，如果存在，就直接使用 render 函数了

如果没有，再判断 template 和 el，如果有 `template`，就不会管 `el` 了

所以优先级顺序是：`render > template > el`

因为不管是 `el` 挂载的，还是 `template` 最后都会被编译成 `render` 函数，而如果已经有了 `render` 函数了，就跳过前面的编译了

### 挂载阶段

这里阶段主要做的事有两件

1. 根据 render 返回的虚拟 DOM 创建真实的 DOM 节点，插入到视图中，完成渲染
2. 对模板中数据或状态做响应式处理

这里用了一个函数叫mountComponent()

主要做的事就是

- 调用钩子函数 `beforeMount`
- 调用 `_update()` 方法对新老虚拟 DOM 进行 `patch` 以及 `new Watcher` 对模板数据做响应式处理
- 再调用钩子函数 `mounted`

```
export function mountComponent (
  vm: Component,
  el: ?Element,
  hydrating?: boolean
): Component {
  vm.$el = el
  // 判断有没有渲染函数 render
  if (!vm.$options.render) {
    // 如果没有，默认就创建一个注释节点
    vm.$options.render = createEmptyVNode
  }
  // 调用生命周期钩子函数
  callHook(vm, 'beforeMount')
  let updateComponent
  updateComponent = () => {
    // 调用 _update 对 render 返回的虚拟 DOM 进行 patch（也就是 Diff )到真实DOM，这里是首次渲染
    vm._update(vm._render(), hydrating)
  }
  // 为当前组件实例设置观察者，监控 updateComponent 函数得到的数据，下面有介绍
  new Watcher(vm, updateComponent, noop, {
    // 当触发更新的时候，会在更新之前调用
    before () {
      // 判断 DOM 是否是挂载状态，就是说首次渲染和卸载的时候不会执行
      if (vm._isMounted && !vm._isDestroyed) {
        // 调用生命周期钩子函数
        callHook(vm, 'beforeUpdate')
      }
    }
  }, true /* isRenderWatcher */)
  hydrating = false
  // 没有老的 vnode，说明是首次渲染
  if (vm.$vnode == null) {
    vm._isMounted = true
    // 调用生命周期钩子函数
    callHook(vm, 'mounted')
  }
  return vm
}
```

### Watcher

这个阶段就是关于响应式原理的阶段

再下来就是更新的阶段

### _update()

关于diff算法和虚拟dom的流程

```js
Vue.prototype._update = function (vnode: VNode, hydrating?: boolean) {
    const vm: Component = this
    const prevEl = vm.$el // 当前组件根节点
    const prevVnode = vm._vnode // 老的 vnode
    vm._vnode = vnode // 更新老的 vnode
    // 如果是首次渲染
    if (!prevVnode) {
      // 对 vnode 进行 patch 创建真实的 DOM，挂载到 vm.$el 上
      vm.$el = vm.__patch__(vm.$el, vnode, hydrating, false /* removeOnly */)
    } else {
      // 修改的时候，进行新老 vnode 对比，并回修改后的真实 DOM
      vm.$el = vm.__patch__(prevVnode, vnode)
    }
    // 删除老根节点的引用
    if (prevEl) prevEl.__vue__ = null
    // 更新当前根节点的引用
    if (vm.$el) vm.$el.__vue__ = vm
    // 更新父级的引用
    if (vm.$vnode && vm.$parent && vm.$vnode === vm.$parent._vnode) {
      vm.$parent.$el = vm.$el
    }
  }
```

### $destroy()

这个阶段比较简单，源码不多，主要就是：

- 调用生命周期钩子函数 `beforeDestory`
- 从父组件中删除当前组件
- 移除当前组件内的所有观察者(依赖追踪)，删除数据对象的引用，删除虚拟 DOM
- 调用生命周期钩子函数 `destoryed`
- 关闭所有事件监听，删除当前根组件的引用，删除父级的引用

### 什么时候会调用 $destroy()

我在 patch 源码里看到有三次调用，源码地址：`src/core/vdom/patch.js`

- 新的 vnode 不存在，老的 vnode 存在的时候，就触发卸载老 vnode 对应组件的 destroy。702行
- 如果新的 vnode 根节点被修改的时候，调用老 vnode 对应组件的 destroy。767行
- 新老 vnode 对比结束后，调用老 vnode 对应组件的 destroy。795行

至此，一轮完整的 Vue 生命周期就走完了

## 响应式原理

Vue.js 是采用**数据劫持**结合**发布者-订阅者模式**的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。主要分为以下几个步骤：

1. 需要observe的数据对象进行递归遍历，包括子属性对象的属性，都加上setter和getter这样的话，给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化
2. compile解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图
3. Watcher订阅者是Observer和Compile之间通信的桥梁，主要做的事情是: ①在自身实例化时往属性订阅器(dep)里面添加自己 ②自身必须有一个update()方法 ③待属性变动dep.notify()通知时，能调用自身的update()方法，并触发Compile中绑定的回调，则功成身退。
4. MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。

![响应式原理流程.png](https://s2.loli.net/2022/07/14/ST2GmM76COwVEtU.webp)

依赖收集的核心是 Dep，而且它与 Watcher 也是密不可分的，我们来看一下

### Dep

源码地址：`src/core/observer/dep.js`

这是一个类，它实际上就是对 Watcher 的一种管理

这里首先初始化一个 subs 数组，用来存放依赖，也就是观察者，谁依赖这个数据，谁就在这个数组里，然后定义几个方法来对依赖添加、删除、通知更新等

另外它有一个静态属性 target，这是一个全局的 Watcher，也表示同一时间只能存在一个全局的 Watcher

### Watcher

源码地址：`src/core/observer/watcher.js`

Watcher 也是一个类，也叫观察者(订阅者)，这里干的活还挺复杂的，而且还串连了渲染和编译

我们自己组件里写的 watch，为什么自动就能拿到新值和老值两个参数？ 就是在 watcher.run() 里面会执行回调，并且把新值和老值传过去

为什么要初始化两个 Dep 实例数组 因为 Vue 是数据驱动的，每次数据变化都会重新 render，也就是说 vm.render() 方法就又会重新执行，再次触发 getter，所以用两个数组表示，新添加的 Dep 实例数组 newDeps 和上一次添加的实例数组 deps

依赖收集：

- 挂载之前会实例化一个渲染 `watcher` ，进入 `watcher` 构造函数里就会执行 `this.get()` 方法
- 然后就会执行 `pushTarget(this)`，就是把 `Dep.target` 赋值为当前渲染 `watcher` 并压入栈(为了恢复用)
- 然后执行 `this.getter.call(vm, vm)`，也就是上面的 updateComponent() 函数，里面就执行了 `vm._update(vm._render(), hydrating)`
- 接着执行 `vm._render()` 就会生成渲染 `vnode`，这个过程中会访问 vm 上的数据，就触发了数据对象的 getter
- 每一个对象值的 getter 都有一个 `dep`，在触发 getter 的时候就会调用 `dep.depend()` 方法，也就会执行 `Dep.target.addDep(this)`
- 然后这里会做一些判断，以确保同一数据不会被多次添加，接着把符合条件的数据 push 到 subs 里，到这就已经**完成了依赖的收集**，不过到这里还没执行完，如果是对象还会递归对象触发所有子项的getter，还要恢复 Dep.target 状态



## 模板编译原理

### 认识模板编译

我们知道 `<template></template>` 这个是模板，不是真实的 HTML，浏览器是不认识模板的，所以我们需要把它编译成浏览器认识的原生的 HTML

这一块的主要流程就是

1. 提取出模板中的原生 HTML 和非原生 HTML，比如绑定的属性、事件、指令等等
2. 经过一些处理生成 render 函数
3. render 函数再将模板内容生成对应的 vnode
4. 再经过 patch 过程( Diff )得到要渲染到视图中的 vnode
5. 最后根据 vnode 创建真实的 DOM 节点，也就是原生 HTML 插入到视图中，完成渲染

### 会有一个入口函数 baseCompile()

这就是模板编译的入口函数，它接收两个参数

- `template`：就是要转换的模板字符串
- `options`：就是转换时需要的参数

编译的流程，主要有三步：

1. 模板解析：通过正则等方式提取出 `<template></template>` 模板里的标签元素、属性、变量等信息，并解析成抽象语法树 `AST`
2. 优化：遍历 `AST` 找出其中的静态节点和静态根节点，并添加标记
3. 代码生成：根据 `AST` 生成渲染函数 `render

### AST抽象语法树的生成过程

比如一个div标签里面包一个h1标签，然后里面在包一段文本

主要就是一个栈的调用嘛，从div的开始标签会触发一个钩子函数start，触发之后就会构建一个div节点，此时发现这个栈里面是空的，说明这个div节点是根节点，因为它没有父节点。最后将这个节点推入栈中，然后将div节点的标签从模板里面截取掉嘛，

然后是h1的开始标签，也会触发start函数，然后构建一个h1的节点，这时会发现栈的最里面有一个节点是div节点，就说明div节点是h1的父节点，于是将h1添加到div节点中，然后将h1节点推入栈中，将模板里面的h1的开始标签截掉，

然后是一段文本嘛，会触发一个钩子函数叫chars，触发之后就会构建一个文本节点，发现栈里面的最后一个节点是h1，说明文本节点的父节点是h1，于是将文本节点添加到h1节点的子节点里面，因为文本节点没有子节点，所以文本节点不会被推入到栈中。

然后是h1的结束标签，触发函数end，把栈里面的最后一个节点弹出来，

然后是div的结束标签，也触发end函数，把栈里面的最后一个节点弹出来。

![image-20220714175650133](https://s2.loli.net/2022/07/14/3Ox4My978oKGfN2.png)

![image-20220714175705352](https://s2.loli.net/2022/07/14/7EFgWhH8ucJrGXo.png)

### 然后还会有一个这个解析器

### parse()

源码地址：`src/complier/parser/index.js - 79行`

就是这个方法就是解析器的主函数，就是它通过正则等方法提取出 `<template></template>` 模板字符串里所有的 `tag`、`props`、`children` 信息，生成一个对应结构的 ast 对象

`parse` 接收两个参数

- `template` ：就是要转换的模板字符串
- `options`：就是转换时需要的参数。它包含有四个钩子函数，就是用来把 `parseHTML` 解析出来的字符串提取出来，并生成对应的 `AST`

核心步骤是这样的：

调用 `parseHTML` 函数对模板字符串进行解析

- 解析到开始标签、结束标签、文本、注释分别进行不同的处理
- 解析过程中遇到文本信息就调用文本解析器 `parseText` 函数进行文本解析
- 解析过程中遇到包含过滤器，就调用过滤器解析器 `parseFilters` 函数进行解析



### 然后会有一个这个优化器

### 2. optimize()

这个函数就是在 `AST` 里找出静态节点和静态根节点，并添加标记，为了后面 `patch` 过程中就会跳过静态节点的对比，直接克隆一份过去，从而优化了 `patch` 的性能

函数里面调用的外部函数就不贴代码了，大致过程是这样的

- **标记静态节点(markStatic)**。就是判断 type，上面介绍了值为 1、2、3的三种类型
  - type 值为1：就是包含子元素的节点，设置 static 为 false 并递归标记子节点，直到标记完所有子节点
  - type 值为 2：设置 static 为 false
  - type 值为 3：就是不包含子节点和动态属性的纯文本节点，把它的 static = true，patch 的时候就会跳过这个，直接克隆一份去
- **标记静态根节点(markStaticRoots)**，这里的原理和标记静态节点基本相同，只是需要满足下面条件的节点才能算作是静态根节点
  - 节点本身必须是静态节点
  - 必须有子节点
  - 子节点不能只有一个文本节点

源码地址：`src/complier/optimizer.js - 21行`

### 然后还有一个这个文本解析器

之前那个解析器解析到文本的时候会触发这个chars函数嘛，然后得到文本，而如果有个文本不是纯文本，而是带有变量的话就需要文本解析器进行二次的这个解析，

先用正则表达式匹配出文本里面的变量，然后把变量坐标的文本添加到数组里面，变量的话就改成_s(x)的形式放到数组里面

然后用+号将数组元素连起来变成字符串

![image-20220714183617991](https://s2.loli.net/2022/07/14/dK9PA23p7W6IYn5.png)

然后这个字符串会放在一个with函数里面执行，这个 `with` 在 《**你不知道的JavaScript**》上卷里介绍的是，用来欺骗词法作用域的关键字，它可以让我们更快的引用一个对象上的多个属性

然后with有_c 是创建元素节点的函数， _v是创建文本节点的函数，里面又能分辨是动态还是静态的文本，还有个 _e是注释节点。

#### 做个总结的话

就是先通过模板得到AST抽象语法树，然后借助解析器通过解析器触发不同的钩子函数构建出不同的节点，得到一个带层次关系的AST（模板解析器的原理就是一段一段的截取模板字符串，每截取一小段字符串就会根据截取出来的字符串类型触发不同的钩子函数）解析到文本的时候会有文本解析器进行二次加工。

## Vue3 与 Vue2 区别详述

**1\. 生命周期**

对于生命周期来说，整体上变化不大，只是大部分生命周期钩子名称上 + “on”，功能上是类似的。不过有一点需要注意，Vue3 在组合式API（Composition API，下面展开）中使用生命周期钩子时需要先引入，而 Vue2 在选项API（Options API）中可以直接调用生命周期钩子，如下所示。

```
// vue3
<script setup>     
import { onMounted } from 'vue';   // 使用前需引入生命周期钩子
 
onMounted(() => {
  // ...
});
 
// 可将不同的逻辑拆开成多个onMounted，依然按顺序执行，不会被覆盖
onMounted(() => {
  // ...
});
</script>
 
// vue2
<script>     
export default {         
  mounted() {   // 直接调用生命周期钩子            
    // ...         
  },           
}
</script> 
```

常用生命周期对比如下表所示。

| vue2          | vue3            |
| ------------- | --------------- |
| beforeCreate  |                 |
| created       |                 |
| beforeMount   | onBeforeMount   |
| mounted       | onMounted       |
| beforeUpdate  | onBeforeUpdate  |
| updated       | onUpdated       |
| beforeDestroy | onBeforeUnmount |
| destroyed     | onUnmounted     |

> Tips： setup 是围绕 beforeCreate 和 created 生命周期钩子运行的，所以不需要显式地去定义。

**2\. 多根节点**

熟悉 Vue2 的朋友应该清楚，在模板中如果使用多个根节点时会报错，如下所示。

```
// vue2中在template里存在多个根节点会报错
<template>
  <header></header>
  <main></main>
  <footer></footer>
</template>
 
// 只能存在一个根节点，需要用一个<div>来包裹着
<template>
  <div>
    <header></header>
    <main></main>
    <footer></footer>
  </div>
</template>
```

但是，Vue3 支持多个根节点，也就是 fragment。即以下多根节点的写法是被允许的。

```
<template>
  <header></header>
  <main></main>
  <footer></footer>
</template>
```

**3\. Composition API**

Vue2 是选项API（Options API），一个逻辑会散乱在文件不同位置（data、props、computed、watch、生命周期钩子等），导致代码的可读性变差。当需要修改某个逻辑时，需要上下来回跳转文件位置。

Vue3 组合式API（Composition API）则很好地解决了这个问题，可将同一逻辑的内容写到一起，增强了代码的可读性、内聚性，其还提供了较为完美的逻辑复用性方案。

**4\. 异步组件（Suspense）**

Vue3 提供 Suspense 组件，允许程序在等待异步组件加载完成前渲染兜底的内容，如 loading ，使用户的体验更平滑。使用它，需在模板中声明，并包括两个命名插槽：default 和 fallback。Suspense 确保加载完异步内容时显示默认插槽，并将 fallback 插槽用作加载状态。

```
<tempalte>
  <suspense>
    <template #default>
      <List />
    </template>
    <template #fallback>
      <div>
        Loading...
      </div>
    </template>
  </suspense>
</template>
```

在 List 组件（有可能是异步组件，也有可能是组件内部处理逻辑或查找操作过多导致加载过慢等）未加载完成前，显示 Loading...（即 fallback 插槽内容），加载完成时显示自身（即 default 插槽内容）。

**5\. Teleport**

Vue3 提供 Teleport 组件可将部分 DOM 移动到 Vue app 之外的位置。比如项目中常见的 Dialog 弹窗。

```
<button @click="dialogVisible = true">显示弹窗</button>
<teleport to="body">
  <div class="dialog" v-if="dialogVisible">
    我是弹窗，我直接移动到了body标签下
  </div>
</teleport>
```

**6\. 响应式原理**

Vue2 响应式原理基础是 Object.defineProperty；Vue3 响应式原理基础是 Proxy。

-   Object.defineProperty

基本用法：直接在一个对象上定义新的属性或修改现有的属性，并返回对象。

```
let obj = {};
let name = 'leo';
Object.defineProperty(obj, 'name', {
  enumerable: true,   // 可枚举（是否可通过 for...in 或 Object.keys() 进行访问）
  configurable: true,   // 可配置（是否可使用 delete 删除，是否可再次设置属性）
  // value: '',   // 任意类型的值，默认undefined
  // writable: true,   // 可重写
  get() {
    return name;
  },
  set(value) {
    name = value;
  }
});
```

> Tips： `writable` 和 `value` 与 `getter` 和 `setter` 不共存。

搬运 Vue2 核心源码，略删减。

```
function defineReactive(obj, key, val) {
  // 一 key 一个 dep
  const dep = new Dep()
  
  // 获取 key 的属性描述符，发现它是不可配置对象的话直接 return
  const property = Object.getOwnPropertyDescriptor(obj, key)
  if (property && property.configurable === false) { return }
  
  // 获取 getter 和 setter，并获取 val 值
  const getter = property && property.get
  const setter = property && property.set
  if((!getter || setter) && arguments.length === 2) { val = obj[key] }
  
  // 递归处理，保证对象中所有 key 被观察
  let childOb = observe(val)
  
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    // get 劫持 obj[key] 的 进行依赖收集
    get: function reactiveGetter() {
      const value = getter ? getter.call(obj) : val
      if(Dep.target) {
        // 依赖收集
        dep.depend()
        if(childOb) {
          // 针对嵌套对象，依赖收集
          childOb.dep.depend()
          // 触发数组响应式
          if(Array.isArray(value)) {
            dependArray(value)
          }
        }
      }
    }
    return value
  })
  // set 派发更新 obj[key]
  set: function reactiveSetter(newVal) {
    ...
    if(setter) {
      setter.call(obj, newVal)
    } else {
      val = newVal
    }
    // 新值设置响应式
    childOb = observe(val)
    // 依赖通知更新
    dep.notify()
  }
}
```

那 Vue3 为何会抛弃它呢？那肯定是因为它存在某些局限性。

主要原因：无法监听对象或数组新增、删除的元素。

Vue2 相应解决方案：针对常用数组原型方法push、pop、shift、unshift、splice、sort、reverse进行了hack处理；提供Vue.set监听对象/数组新增属性。对象的新增/删除响应，还可以new个新对象，新增则合并新属性和旧对象；删除则将删除属性后的对象深拷贝给新对象。

-   Proxy

Proxy 是 ES6 新特性，通过第2个参数 handler 拦截目标对象的行为。相较于 Object.defineProperty 提供语言全范围的响应能力，消除了局限性。

局限性：

(1)、对象/数组的新增、删除

(2)、监测 .length 修改

(3)、Map、Set、WeakMap、WeakSet 的支持

基本用法：创建对象的代理，从而实现基本操作的拦截和自定义操作。

```
let handler = {
  get(obj, prop) {
    return prop in obj ? obj[prop] : '';
  },
  set() {
    // ...
  },
  ...
};
```

搬运 vue3 的源码 reactive.ts 文件。

```
function createReactiveObject(target, isReadOnly, baseHandlers, collectionHandlers, proxyMap) {
  ...
  // collectionHandlers: 处理Map、Set、WeakMap、WeakSet
  // baseHandlers: 处理数组、对象
  const proxy = new Proxy(
    target,
    targetType === TargetType.COLLECTION ? collectionHandlers : baseHandlers
  )
  proxyMap.set(target, proxy)
  return proxy
}
```

**7\. 虚拟DOM**

Vue3 相比于 Vue2，虚拟DOM上增加 patchFlag 字段。我们借助Vue3 Template Explorer来看。

```
<div id="app">
  <h1>vue3虚拟DOM讲解</h1>
  <p>今天天气真不错</p>
  <div>{{name}}</div>
</div>
```

渲染函数如下所示。

```
import { createElementVNode as _createElementVNode, toDisplayString as _toDisplayString, openBlock as _openBlock, createElementBlock as _createElementBlock, pushScopeId as _pushScopeId, popScopeId as _popScopeId } from vue
 
const _withScopeId = n => (_pushScopeId(scope-id),n=n(),_popScopeId(),n)
const _hoisted_1 = { id: app }
const _hoisted_2 = /*#__PURE__*/ _withScopeId(() => /*#__PURE__*/_createElementVNode(h1, null, vue3虚拟DOM讲解, -1 /* HOISTED */))
const _hoisted_3 = /*#__PURE__*/ _withScopeId(() => /*#__PURE__*/_createElementVNode(p, null, 今天天气真不错, -1 /* HOISTED */))
 
export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock(div, _hoisted_1, [
    _hoisted_2,
    _hoisted_3,
    _createElementVNode(div, null, _toDisplayString(_ctx.name), 1 /* TEXT */)
  ]))
}
```

注意第3个\_createElementVNode的第4个参数即 patchFlag 字段类型。

字段类型情况：1 代表节点为动态文本节点，那在 diff 过程中，只需比对文本对容，无需关注 class、style等。除此之外，发现所有的静态节点（HOISTED 为 -1），都保存为一个变量进行静态提升，可在重新渲染时直接引用，无需重新创建。

```
// patchFlags 字段类型列举
export const enum PatchFlags { 
  TEXT = 1,   // 动态文本内容
  CLASS = 1 << 1,   // 动态类名
  STYLE = 1 << 2,   // 动态样式
  PROPS = 1 << 3,   // 动态属性，不包含类名和样式
  FULL_PROPS = 1 << 4,   // 具有动态 key 属性，当 key 改变，需要进行完整的 diff 比较
  HYDRATE_EVENTS = 1 << 5,   // 带有监听事件的节点
  STABLE_FRAGMENT = 1 << 6,   // 不会改变子节点顺序的 fragment
  KEYED_FRAGMENT = 1 << 7,   // 带有 key 属性的 fragment 或部分子节点
  UNKEYED_FRAGMENT = 1 << 8,   // 子节点没有 key 的fragment
  NEED_PATCH = 1 << 9,   // 只会进行非 props 的比较
  DYNAMIC_SLOTS = 1 << 10,   // 动态的插槽
  HOISTED = -1,   // 静态节点，diff阶段忽略其子节点
  BAIL = -2   // 代表 diff 应该结束
}
```

**8\. 事件缓存**

Vue3 的`cacheHandler`可在第一次渲染后缓存我们的事件。相比于 Vue2 无需每次渲染都传递一个新函数。加一个 click 事件。

```
<div id="app">
  <h1>vue3事件缓存讲解</h1>
  <p>今天天气真不错</p>
  <div>{{name}}</div>
  <span onCLick=() => {}><span>
</div>
```

渲染函数如下所示。

```
import { createElementVNode as _createElementVNode, toDisplayString as _toDisplayString, openBlock as _openBlock, createElementBlock as _createElementBlock, pushScopeId as _pushScopeId, popScopeId as _popScopeId } from vue
 
const _withScopeId = n => (_pushScopeId(scope-id),n=n(),_popScopeId(),n)
const _hoisted_1 = { id: app }
const _hoisted_2 = /*#__PURE__*/ _withScopeId(() => /*#__PURE__*/_createElementVNode(h1, null, vue3事件缓存讲解, -1 /* HOISTED */))
const _hoisted_3 = /*#__PURE__*/ _withScopeId(() => /*#__PURE__*/_createElementVNode(p, null, 今天天气真不错, -1 /* HOISTED */))
const _hoisted_4 = /*#__PURE__*/ _withScopeId(() => /*#__PURE__*/_createElementVNode(span, { onCLick: () => {} }, [
  /*#__PURE__*/_createElementVNode(span)
], -1 /* HOISTED */))
 
export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock(div, _hoisted_1, [
    _hoisted_2,
    _hoisted_3,
    _createElementVNode(div, null, _toDisplayString(_ctx.name), 1 /* TEXT */),
    _hoisted_4
  ]))
}
```

观察以上渲染函数，你会发现 click 事件节点为静态节点（HOISTED 为 -1），即不需要每次重新渲染。

#### [vue2双端比较](https://sanhua0121.github.io/#/wudi/vue?id=vue2双端比较)

所谓`双端比较`就是**新列表**和**旧列表**两个列表的头与尾互相对比，，在对比的过程中指针会逐渐向内靠拢，直到某一个列表的节点全部遍历过，对比停止。

我们先用四个指针指向两个列表的头尾

```js
function vue2Diff(prevChildren, nextChildren, parent) {
  let oldStartIndex = 0,
    oldEndIndex = prevChildren.length - 1
    newStartIndex = 0,
    newEndIndex = nextChildren.length - 1;
  let oldStartNode = prevChildren[oldStartIndex],
    oldEndNode = prevChildren[oldEndIndex],
    newStartNode = nextChildren[nextStartIndex],
    newEndNode = nextChildren[nextEndIndex];
}CopyErrorCopied
```

我们根据四个指针找到四个节点，然后进行对比，那么如何对比呢？我们按照以下四个步骤进行对比

1. 使用**旧列表**的头一个节点`oldStartNode`与**新列表**的头一个节点`newStartNode`对比
2. 使用**旧列表**的最后一个节点`oldEndNode`与**新列表**的最后一个节点`newEndNode`对比
3. 使用**旧列表**的头一个节点`oldStartNode`与**新列表**的最后一个节点`newEndNode`对比
4. 使用**旧列表**的最后一个节点`oldEndNode`与**新列表**的头一个节点`newStartNode`对比

使用以上四步进行对比，去寻找`key`相同的可复用的节点，当在某一步中找到了则停止后面的寻找。

##### [非理想情况](https://sanhua0121.github.io/#/wudi/vue?id=非理想情况)

有一种特殊情况，当四次对比都**没找到**复用节点时，我们只能拿**新列表**的第一个节点去**旧列表**中找与其`key`相同的节点

找节点的时候其实会有两种情况：一种在**旧列表**中找到了，另一种情况是没找到

当我们在旧列表中找到对应的`VNode`，我们只需要将找到的节点的`DOM`元素，移动到开头就可以了。这里的逻辑其实和`第四步`的逻辑是一样的，只不过`第四步`是移动的尾节点，这里是移动找到的节点。`DOM`移动后，由我们将**旧列表**中的节点改为`undefined`，这是**至关重要**的一步，因为我们已经做了节点的移动了所以我们不需要进行再次的对比了。最后我们将头指针`newStartIndex`向后移一位

如果在**旧列表**中没有找到复用节点，直接创建一个新的节点放到最前面就可以了，然后后**移头指针`newStartIndex`**。

最后当**旧列表**遍历到`undefind`时就跳过当前节点。

#### [vue3 最长递增子序列](https://sanhua0121.github.io/#/wudi/vue?id=vue3-最长递增子序列)

### [vue3的优化](https://sanhua0121.github.io/#/wudi/vue?id=vue3的优化)

事件缓存：将事件缓存，可以理解为变成静态的了

添加静态标记：Vue2 是全量 Diff，Vue3 是静态标记 + 非全量 Diff

静态提升：创建静态节点时保存，后续直接复用

使用最长递增子序列优化了对比流程：Vue2 里在 updateChildren() 函数里对比变更，在 Vue3 里这一块的逻辑主要在 patchKeyedChildren() 函数里，具体看下面

在 Vue2 里 `updateChildren` 会进行

- 头和头比
- 尾和尾比
- 头和尾比
- 尾和头比
- 都没有命中的对比

在 Vue3 里 `patchKeyedChildren` 为

- 头和头比
- 尾和尾比
- 基于最长递增子序列进行移动/添加/删除

看个例子，比如

- 老的 children：`[ a, b, c, d, e, f, g ]`
- 新的 children：`[ a, b, f, c, d, e, h, g ]`

1. 先进行头和头比，发现不同就结束循环，得到 `[ a, b ]`
2. 再进行尾和尾比，发现不同就结束循环，得到 `[ g ]`
3. 再保存没有比较过的节点 `[ f, c, d, e, h ]`，并通过 newIndexToOldIndexMap 拿到在数组里对应的下标，生成数组 `[ 5, 2, 3, 4, -1 ]`，`-1` 是老数组里没有的就说明是新增
4. 然后再拿取出数组里的最长递增子序列，也就是 `[ 2, 3, 4 ]` 对应的节点 `[ c, d, e ]`
5. 然后只需要把其他剩余的节点，基于 `[ c, d, e ]` 的位置进行移动/新增/删除就可以了

**打包优化**

Tree-shaking：模块打包 webpack、rollup 等中的概念。移除 JavaScript 上下文中未引用的代码。主要依赖于 import 和 export 语句，用来检测代码模块是否被导出、导入，且被 JavaScript 文件使用。

以 nextTick 为例子，在 Vue2 中，全局API暴露在Vue实例上，即使未使用，也无法通过 tree-shaking 进行消除。

```
import Vue from 'vue';
 
Vue.nextTick(() => {
  // 一些和DOM有关的东西
});
```

Vue3 中针对全局和内部的API进行了重构，并考虑到 tree-shaking 的支持。因此，全局API现在只能作为ES模块构建的命名导出进行访问。

```
import { nextTick } from 'vue';   // 显式导入
 
nextTick(() => {
  // 一些和DOM有关的东西
});
```

通过这一更改，只要模块绑定器支持 tree-shaking，则Vue应用程序中未使用的 api 将从最终的捆绑包中消除，获得最佳文件大小。

受此更改影响的全局API如下所示。

-   Vue.nextTick
-   Vue.observable （用 Vue.reactive 替换）
-   Vue.version
-   Vue.compile （仅全构建）
-   Vue.set （仅兼容构建）
-   Vue.delete （仅兼容构建）

内部API也有诸如 transition、v-model 等标签或者指令被命名导出。只有在程序真正使用才会被捆绑打包。Vue3 将所有运行功能打包也只有约22.5kb，比 Vue2 轻量很多。

**11\. TypeScript支持**

Vue3 由 TypeScript 重写，相对于 Vue2 有更好的 TypeScript 支持。

-   Vue2 Options API 中 option 是个简单对象，而 TypeScript 是一种类型系统，面向对象的语法，不是特别匹配。

-   Vue2 需要vue-class-component强化vue原生组件，也需要vue-property-decorator增加更多结合Vue特性的装饰器，写法比较繁琐。

**二、Options API 与 Composition API**

Vue 组件可以用两种不同的 API 风格编写：Options API 和 Composition API。

1\. Options API

使用 Options API，我们使用选项对象定义组件的逻辑，例如data、methods和mounted。由选项定义的属性在 this 内部函数中公开，指向组件实例，如下所示。

```
<template>
  <button @click="increment">count is: {{ count }}</button>
</template>
 
<script>
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++;
    }
  },
  mounted() {
    console.log(`The initial count is ${this.count}.`);
  }
}
</script>
```

2\. Composition API

使用 Composition API，我们使用导入的 API 函数定义组件的逻辑。在 SFC 中，Composition API 通常使用

```
<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
 
<script setup>
import { ref, onMounted } from 'vue';
 
const count = ref(0);
 
function increment() {
  count.value++;
}
 
onMounted(() => {
  console.log(`The initial count is ${count.value}.`);
})
</script>
```

## react和vue

**相似之处：**

-   都将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库
-   都有自己的构建工具，能让你得到一个根据最佳实践设置的项目模板。
-   都使用了Virtual DOM（虚拟DOM）提高重绘性能
-   都有props的概念，允许组件间的数据传递
-   都鼓励组件化应用，将应用分拆成一个个功能明确的模块，提高复用性

**不同之处：**

**1）数据流**

Vue默认支持数据双向绑定，而React一直提倡单向数据流

**2）虚拟DOM**

Vue2.x开始引入"Virtual DOM"，消除了和React在这方面的差异，但是在具体的细节还是有各自的特点。

-   Vue宣称可以更快地计算出Virtual DOM的差异，这是由于它在渲染过程中，会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树。
-   对于React而言，每当应用的状态被改变时，全部子组件都会重新渲染。当然，这可以通过 PureComponent/shouldComponentUpdate这个生命周期方法来进行控制，但Vue将此视为默认的优化。

**3）组件化**

React与Vue最大的不同是模板的编写。

-   Vue鼓励写近似常规HTML的模板。写起来很接近标准 HTML元素，只是多了一些属性。
-   React推荐你所有的模板通用JavaScript的语法扩展——JSX书写。

具体来讲：React中render函数是支持闭包特性的，所以我们import的组件在render中可以直接调用。但是在Vue中，由于模板中使用的数据都必须挂在 this 上进行一次中转，所以 import 完组件之后，还需要在 components 中再声明下。

**4）监听数据变化的实现原理不同**

-   Vue 通过 getter/setter 以及一些函数的劫持，能精确知道数据变化，不需要特别的优化就能达到很好的性能
-   React 默认是通过比较引用的方式进行的，如果不优化（PureComponent/shouldComponentUpdate）可能导致大量不必要的vDOM的重新渲染。这是因为 Vue 使用的是可变数据，而React更强调数据的不可变。

**5）高阶组件**

react可以通过高阶组件（Higher Order Components-- HOC）来扩展，而vue需要通过mixins来扩展。

原因高阶组件就是高阶函数，而React的组件本身就是纯粹的函数，所以高阶函数对React来说易如反掌。相反Vue.js使用HTML模板创建视图组件，这时模板无法有效的编译，因此Vue不采用HOC来实现。

**6）构建工具**

两者都有自己的构建工具

-   React ==> Create React APP
-   Vue ==> vue-cli

**7）跨平台**

-   React ==> React Native
-   Vue ==> Weex

**React的更新粒度**

而 React 在类似的场景下是`自顶向下的进行递归更新的`，也就是说，React 中假如 `ChildComponent` 里还有十层嵌套子元素，那么所有层次都会递归的重新render（在不进行手动优化的情况下），这是性能上的灾难。（因此，React 创造了`Fiber`，创造了`异步渲染`，其实本质上是弥补被自己搞砸了的性能）。

他们能用收集依赖的这套体系吗？不能，因为他们遵从`Immutable`的设计思想，永远不在原对象上修改属性，那么基于 `Object.defineProperty` 或 `Proxy` 的响应式依赖收集机制就无从下手了（你永远返回一个新的对象，我哪知道你修改了旧对象的哪部分？）

同时，由于没有响应式的收集依赖，React 只能递归的把所有子组件都重新 `render`一遍（除了memo和shouldComponentUpdate这些优化手段），然后再通过 `diff算法` 决定要更新哪部分的视图，这个递归的过程叫做 `reconciler`，听起来很酷，但是性能很灾难。

**Vue的更新粒度**

那么，Vue 这种精确的更新是怎么做的呢？其实每个组件都有自己的`渲染 watcher`，它掌管了当前组件的视图更新，但是并不会掌管 `ChildComponent` 的更新。

## Demo实现



### 1.实现一个modal框

```js
<template>
  <div class="container">
    <transition name="modal-fade">
      <div class="modal-container" v-show="visible" :style="{ width: width }">
        <!-- 头部标题 -->
        <div class="modal-header">
          <div class="modal-title">{{ title }}</div>
          <i class="iconfont close-icon" @click="close">&#xe7fc;</i>
        </div>
        <!-- 内容区域 -->
        <div class="modal-content">
          <slot></slot>
        </div>
        <!-- 底部按钮 -->
        <div class="modal-footer" v-show="showOperation">
          <div class="modal-btn">
            <button class="cancel" ref="cancelBtn" @click="close" @mousedown="cancelMouseDown" @mouseup="cancelMouseUp">取消</button>
            <button class="ok" @click="ok">确认</button>
          </div>
        </div>
      </div>
    </transition>
    <!-- 遮罩层 -->
    <div class="mask" v-show="visible" @click="close"></div>
  </div>
</template>
 
<script>
export default {
  data() {
    return {}
  },
  props: {
    // 模态框标题
    title: {
      type: String,
      default: () => {
        return '模态框标题'
      },
    },
    // 显示隐藏控件
    visible: {
      type: Boolean,
      default: () => {
        return false
      },
    },
    // 隐藏底部区域
    showOperation: {
      type: Boolean,
      dafault: () => {
        return true
      },
    },
    // 宽度
    width: {
      type: String,
      default: '250px',
    },
  },
  methods: {
    // 取消
    close() {
      this.$emit('cancel')
      this.$emit('update:visible', false)
    },
    // 确认
    ok() {
      this.$emit('submit')
      this.$emit('update:visible', false)
    },
    // 取消按钮 鼠标按下事件
    cancelMouseDown() {
      this.$refs.cancelBtn.style.color = '#096dd9'
      this.$refs.cancelBtn.style.border = '1px solid #096dd9'
    },
    // 取消按钮 鼠标松开事件
    cancelMouseUp() {
      this.$refs.cancelBtn.style.color = '#595959'
      this.$refs.cancelBtn.style.border = '1px solid #d9d9d9'
    },
  },
  watch: {
    // 操作遮罩层的展示/隐藏
    visible() {
      if (this.visible == true) {
        document.querySelector('body').setAttribute('style', 'overflow:hidden !important;')
      } else {
        document.querySelector('body').removeAttribute('style')
      }
    },
  },
}
</script>
 
<style scoped>
.modal-container {
  z-index: 999;
  background-color: #fff;
  min-width: 250px;
  min-height: 180px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: none;
  border-radius: 4px;
  transition: 0.5s;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.modal-header {
  width: 100%;
  height: 50px;
  border: none;
  border-bottom: 1px solid #e8e8e8;
  padding: 20px 30px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.modal-title {
  color: #262626;
  font-weight: 600;
  font-size: 17px;
}
.close-icon {
  color: #4d4d4d;
  cursor: pointer;
  width: 80px;
  text-align: right;
}
.modal-content {
  width: 100%;
  min-height: 100px;
  border: none;
  border-radius: none;
  padding: 20px 30px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.modal-footer {
  width: 100%;
  height: 60px;
  border: none;
  border-top: 1px solid #e8e8e8;
  padding: 0 30px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
.modal-btn {
  width: 150px;
  display: flex;
  justify-content: space-between;
}
.cancel {
  border: 1px solid #d9d9d9;
  background-color: #fff;
  color: #595959;
  width: 70px;
  height: 32px;
  border-radius: 4px;
  font-size: 14px;
  transition: 0.5s;
}
.cancel :hover {
  border: 1px solid #40a9ff;
  color: #40a9ff;
}
.ok {
  border: 1px solid #1890ff;
  background-color: #1890ff;
  color: #ffffff;
  width: 70px;
  height: 32px;
  border-radius: 4px;
  font-size: 14px;
  transition: 0.5s;
}
.ok :hover {
  border: 1px solid #40a9ff;
  background-color: #40a9ff;
}
.mask {
  z-index: 998;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.3);
}
.modal-fade-enter-active {
  transition: all 0.3s ease;
}
.modal-fade-leave-active {
  transition: all 0.3s ease;
}
.modal-fade-enter-from,
.modal-fade-leave-to {
  transform: translateX(-400px);
  opacity: 0;
}
</style>
```



### 2.实现一个Confirm框

```vue
<template>
		<div class="content-pop" v-if="bool_show">
			<div class="result" v-html="content"></div>
			<div class="bottom">
				<div class="cancel" @click="cancel">取消</div>
				<div class="confirm" @click="confirm">确定</div>
			</div>
		</div>
</template>
 
<script>
export default {
	name: 'msgConfirmPro',
	data() {
		return {
			bool_show: false,
			content: '',
			cancelBack:undefined,
			confirmBack:undefined,
		}
	},
	methods: {
		// 打开弹窗
		show(content,confirm,cancel) {
			if(confirm){
				this.confirmBack = confirm;
			}
			if(cancel){
				this.cancelBack = cancel;
			}
			this.content = content;
			this.bool_show = true;
		},
		cancel() {
			this.bool_show = false;
			if(this.cancelBack){
			this.cancelBack();	
			}
			
		},
		confirm() {
			this.bool_show = false;
			if(this.confirmBack){
				this.confirmBack();
			}
			
		}
	}
};
</script>
 
<style scoped="">
.content-pop {
    width: 250px;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
    /* padding: 0.04rem 0 0 0; */
    box-sizing: border-box;
	background: #ffffff;
	    height: 149px;
	    border-radius: 24px;
}
 
.result {
overflow: auto;
    padding: 20px 30px;
    top: 0;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    /* bottom: 87px; */
    font-size: 16px;
    position: absolute;
    text-align: center;
    color: #333;
    box-sizing: border-box;
    -webkit-overflow-scrolling: touch;
}
.content-pop .bottom {
font-size: 16px;
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    border-top: 1px solid #f5f5f5;
}
 
.content-pop .bottom .cancel {
color: #666666;
    width: 50%;
    height: 100%;
    line-height: 48px;
    text-align: center;
    border-right: 01px solid #f5f5f5;
}
 
.content-pop .bottom .confirm {
    color: #2283e2;
    width: 50%;
    height: 100%;
    text-align: center;
    line-height: 48px;
}
 
.content-pop .title {
	box-sizing: border-box;
	padding: 40px 0;
	width: 100%;
	margin: 0 auto;
	text-align: center;
	color: #333;
	border-bottom: 0.003rem solid #f5f5f5;
}
</style>
```

使用

```vue
<template>
  <div id="app" style="background-color: #ff0000;height: 100vh;">
    
	<div @click="show()">点击出现弹窗</div>
	
	<ylConfirm ref="MSGCONFIRMPRO"></ylConfirm>
	
  </div>
</template>
 
<script>
import HelloWorld from './components/HelloWorld.vue'
import ylConfirm from './components/yl-confirm.vue'
 
export default {
  name: 'app',
  components: {
    HelloWorld,ylConfirm
  },methods:{
	  show() {
		  this.$refs.MSGCONFIRMPRO.show('我是弹窗里面的内容，我可以随便定义哦',
		  () => {
		  	// 确定的回调
			alert("点击了确认");
		  },
		  () => {
			  // 取消的回调
		  	alert("点击了取消");
		  })
	  }
	  
  }
}
</script>
 
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```



### 3.实现一个通用alert组件

[vue封装通用的通知组件alert](https://juejin.cn/post/6844904081085956109)

### 4.封装通用table组件

 [Vue封装通用table组件](https://juejin.cn/post/6990593017874743310)

```vue
<!-- src/components/table-slot/table.vue -->
<template>
  <table>
    <thead>
      <tr>
        <th v-for="col in columns">{{ col.title }}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, rowIndex) in data">
        <td v-for="col in columns">
          <template v-if="'render' in col">
            <Render :row="row" :column="col" :index="rowIndex" :render="col.render"></Render>
          </template>
          <template v-else-if="'slot' in col">
            <slot :row="row" :column="col" :index="rowIndex" :name="col.slot"></slot>
          </template>
          <template v-else>{{ row[col.key] }}</template>
        </td>
      </tr>
    </tbody>
  </table>
</template>
```

示例中在 `<table-slot>` 内的每一个 `<template>` 就对应某一列的 slot-scope 模板，通过配置的 `slot` 字段，指定具名的 slot-scope。可以看到，基本是把 Render 函数还原成了 html 的写法，这样看起来直接多了，渲染效果是完全一样的。在 slot-scope 中，平时怎么写组件，这里就怎么写，Vue.js 所有的 API 都是可以直接使用的。

````vue
<!-- src/views/table-slot.vue -->
<template>
  <div>
    <table-slot :columns="columns" :data="data">
      <template slot-scope="{ row, index }" slot="name">
        <input type="text" v-model="editName" v-if="editIndex === index" />
        <span v-else>{{ row.name }}</span>
      </template>

      <template slot-scope="{ row, index }" slot="age">
        <input type="text" v-model="editAge" v-if="editIndex === index" />
        <span v-else>{{ row.age }}</span>
      </template>

      <template slot-scope="{ row, index }" slot="birthday">
        <input type="text" v-model="editBirthday" v-if="editIndex === index" />
        <span v-else>{{ getBirthday(row.birthday) }}</span>
      </template>

      <template slot-scope="{ row, index }" slot="address">
        <input type="text" v-model="editAddress" v-if="editIndex === index" />
        <span v-else>{{ row.address }}</span>
      </template>

      <template slot-scope="{ row, index }" slot="action">
        <div v-if="editIndex === index">
          <button @click="handleSave(index)">保存</button>
          <button @click="editIndex = -1">取消</button>
        </div>
        <div v-else>
          <button @click="handleEdit(row, index)">操作</button>
        </div>
      </template>
    </table-slot>
  </div>
</template>
<script>
  import TableSlot from '../components/table-slot/table.vue';

  export default {
    components: { TableSlot },
    data () {
      return {
        columns: [
          {
            title: '姓名',
            slot: 'name'
          },
          {
            title: '年龄',
            slot: 'age'
          },
          {
            title: '出生日期',
            slot: 'birthday'
          },
          {
            title: '地址',
            slot: 'address'
          },
          {
            title: '操作',
            slot: 'action'
          }
        ],
        data: [
          {
            name: '王小明',
            age: 18,
            birthday: '919526400000',
            address: '北京市朝阳区芍药居'
          },
          {
            name: '张小刚',
            age: 25,
            birthday: '696096000000',
            address: '北京市海淀区西二旗'
          },
          {
            name: '李小红',
            age: 30,
            birthday: '563472000000',
            address: '上海市浦东新区世纪大道'
          },
          {
            name: '周小伟',
            age: 26,
            birthday: '687024000000',
            address: '深圳市南山区深南大道'
          }
        ],
        editIndex: -1,  // 当前聚焦的输入框的行数
        editName: '',  // 第一列输入框，当然聚焦的输入框的输入内容，与 data 分离避免重构的闪烁
        editAge: '',  // 第二列输入框
        editBirthday: '',  // 第三列输入框
        editAddress: '',  // 第四列输入框
      }
    },
    methods: {
      handleEdit (row, index) {
        this.editName = row.name;
        this.editAge = row.age;
        this.editAddress = row.address;
        this.editBirthday = row.birthday;
        this.editIndex = index;
      },
      handleSave (index) {
        this.data[index].name = this.editName;
        this.data[index].age = this.editAge;
        this.data[index].birthday = this.editBirthday;
        this.data[index].address = this.editAddress;
        this.editIndex = -1;
      },
      getBirthday (birthday) {
        const date = new Date(parseInt(birthday));
        const year = date.getFullYear();
        const month = date.getMonth() + 1;
        const day = date.getDate();

        return `${year}-${month}-${day}`;
      }
    }
  }
</script>
````



### 5.实现checkbox

```vue
<template>
  <div>
    <span>全选</span>
    <input type="checkbox" v-model="checkAll" />
    <div v-for="(item,index) in test" :key="index">
      <span>{{item.name}}</span>
	  <input type="checkbox" v-model="item.isSelected" />
    </div>
  </div>
</template>
<script>
  export default {
  data() {
    return {
      test: [
        { name: "测试1", isSelected: true },
        { name: "测试2", isSelected: true },
        { name: "测试3", isSelected: true },
        { name: "测试4", isSelected: true },
        { name: "测试5", isSelected: true }
      ]
    };
  },
  computed: {
    checkAll: {
      get() {
        // 返回什么结果接赋予给 checkAll 属性
        return this.test.every(item => item.isSelected);
      },
      set(val) {
        // val 是给 checkAll 赋予值的时候传递过来的
        return this.test.forEach(item => (item.isSelected = val));
      }
    }
  }
}
</script>
```

### 6.实现内容绑定

```js
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
		<div id="app">
			<h2>{{username}}</h2>
			<h1>单行文本输入框</h1>
			<input type="text" name="username" v-model.lazy.trim="username" value="" />
			<h1>多行文本输入框</h1>
			<textarea rows="" cols="" v-model="username"></textarea>
			
			<h1>复选框:选择喜欢的水果</h1>
			<span v-for="item in fruits">
				{{item}}
				<input  type="checkbox" name="fruit" v-model="checkFruits" :value="item" />
			</span>
			<h2>{{checkFruits}}</h2>
			
			
			<h1>单选框:选择最喜欢的水果</h1>
			<span v-for="item in fruits">
				{{item}}
				<input  type="radio" name="zfruit" v-model="radioFruits" :value="item" />
			</span>
			<h2>{{radioFruits}}</h2>
			
			
			<h1>选项框：选择你居住的城市</h1>
			<select v-model="chooseCity">
				<option disabled value="">请选择</option>
				<option v-for="item in citys" :value="item">{{item}}</option>
			</select>
			<h3>{{chooseCity}}</h3>
			
			
			<h1>选项框：选择你喜欢的城市</h1>
			<select v-model="moreCity" multiple="multiple">
				<option v-for="item in citys" :value="item">{{item}}</option>
			</select>
			<h3>{{moreCity}}</h3>
			
			<h1>将字符串变为数字获取</h1>
			<input type="text" name="age" v-model.number="age" value="" />
			
		</div>
		
		<script type="text/javascript">
			let app = new Vue({
				el:"#app",
				data:{
					username:"小明",
					fruits:['苹果','雪梨',"香蕉","葡萄"],
					checkFruits:[],
					radioFruits:"",
					citys:['北京',"上海","深圳","广州"],
					chooseCity:"",
					moreCity:[],
					age:16
				},
				watch:{
					age:function(val){
						console.log(val)
					}
				}
			})
		</script>
	</body>
</html>

```

### 7.实现todolist

```vue
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
		<link rel="stylesheet" type="text/css" href="css/style.css"/>
		<link rel="stylesheet" type="text/css" href="css/animate.min.css"/>
		<script src="js/lcXys.js" type="text/javascript" charset="utf-8"></script>
		<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
		
	</head>
	<body>
		<div id="app">
			<div class="main">
				<div class="header">
					<div class="logo">LcTodo</div>
					<!-- 绑定回车事件,v-model绑定输入框的value值 -->
					<input type="text" v-model="inputValue" id="input" @keydown.enter="enterEvent" placeholder="请输入待办事项" />
				</div>
				<div class="doing todo">
					<h3><span class="title">正在进行</span><span class="num">0</span></h3>
					<div class="list">
						<transition-group name="slide" mode="out-in" enter-active-class="animated bounceInLeft" leave-active-class="animated bounceOutRight">
						<div class="todoItem" v-for="item,index in doingList" :key="'doing'+index">
							<input @click.prevent="checkDone(item.id)" :data-id="item.id" type="checkbox">
							<div class="content">{{item.content}}</div>
							<div class="del" @click="deleteItem(item.id)">删除</div>
						</div>
						</transition-group>
					</div>
				</div>
				<div class="done todo">
					<h3><span class="title">正在进行</span><span class="num">0</span></h3>
					<div class="list">
						<transition-group name="slide" enter-active-class="animated bounceInLeft" leave-active-class="animated bounceOutRight">
						<div class="todoItem" v-for="item,index in doneList" :key="'done'+index">
							<input @click.prevent="checkDone(item.id)" :data-id="item.id" type="checkbox" checked="checked">
							<div class="content">{{item.content}}</div>
							<div class="del" @click="deleteItem(item.id)">删除</div>
						</div>
						</transition-group>
					</div>
				</div>
			</div>
		</div>
		<script type="text/javascript">
			var app = new Vue({
				el:"#app",
				data:{
					todoList:[],
					inputValue:""
				},
				computed:{
					//通过过滤todolist数据，得到未做好的列表和已做好的列表
					doingList:function(){
						let arr = this.todoList.filter(function(item,index){
							return !item.isDone
						})
						return arr;
					},
					doneList:function(){
						let arr = this.todoList.filter(function(item,index){
							return item.isDone
						})
						return arr;
					}
				},
				methods:{
					enterEvent:function(event){
						//将数据添加至todolist
						this.todoList.push({
							content:this.inputValue,
							isDone:false,
							id:this.todoList.length
						})
						//保存
						this.saveData()
						//清除输入框的值
						this.inputValue = "";
					},
					// 将数据保存到本地存储
					saveData:function(){
						localStorage.todoList = JSON.stringify(this.todoList)
					},
					checkDone:function(id){
						//console.log(id)
						this.todoList[id].isDone = !this.todoList[id].isDone;
						//每次修改都必须保存
						this.saveData()
					},
					deleteItem:function(id){
						this.todoList.splice(id,1)
						this.todoList.forEach(function(item,i){
							item.id = i;
						})
						this.saveData()
					}
					
					
				},
				mounted:function(){
					this.todoList = localStorage.todoList?JSON.parse(localStorage.todoList):[];
				}
			})
		</script>
	</body>
</html>
```

css

```css
*{
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

body{
	background-color: #efefef;
	font-size: 16px;
}
#app{
	width: 3.75rem;
}
.main{
	width: 3.75rem;
	/* height: 10vh; */
}

.header{
	width: 3.75rem;
	height: 0.5rem;
	background: deepskyblue;
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.header>.logo{
	width: 1.2rem;
	height: 0.5rem;
	text-align: center;
	line-height: 0.5rem;
	font-size: 0.25rem;
	font-weight: 900;
}
.header>input{
	width: 2.2rem;
	height: 0.3rem;
	margin: 0 0.2rem;
	border-radius: 0.05rem;
	border: none;
	outline: none;
	padding: 0 0.1rem;
}

.todo h3{
	height: 0.6rem;
	line-height: 0.6rem;
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 0  0.15rem;
}

.todo .list{
	padding: 0 0.15rem;
}
.todo .todoItem{
	display: flex;
	height: 0.38rem;
	line-height: 0.38rem;
	align-items: center;
	background: lightcyan;
	border-radius: 0.05rem;
	overflow: hidden;
	margin-bottom: 0.1rem;
}
.todo .todoItem:before{
	width: 0.04rem;
	height: 0.38rem;
	background: deepskyblue;
	content: "";
}
.todo .todoItem>input{
	width: 0.25rem;
	height: 0.25rem;
	margin: 0 0.1rem;
	
}
.todo .todoItem .content{
	width: 2.65rem;
	color:#333;
}
.todo .todoItem .del{
	background: orangered;
	width: 0.4rem;
	height: 0.2rem;
	font-size: 0.12rem;
	text-align: center;
	line-height: 0.2rem;
	border-radius: 0.05rem;
	margin: 0 0.1rem;
	color: #fff;
}

.done.todo .todoItem{
	opacity: 0.3;
	-webkit-filter:grayscale(1);
}

.pc{
	font-size: 200px !important;
	
}

.pc .main{
	margin:  0 auto;
}
```

