## 1.todolist案例（70-77）

### 1、组件化开发流程：  
1. 实现静态组件，划分功能，抽取组件，实现静态效果  
2. 展示动态数据，不同的数据用在哪个组件上：一个组件在用，放在组件自身上，多个组件在用，放在共同的父组件上，（状态提升），父组件数据改了其他地方都改了  
3. 交互、组件间传值

技巧：开发完成的静态页面可以都拷贝到父组件中，分组件模块分别拷贝到个组件，然后拷贝样式。

### 2、props数据传递：
1. 父组件 ——> 子组件，子组件props接收，然后使用
2. 子组件 ——> 父组件，父组件定义并传给组件一个函数， 子组件可以调用该函数并传参,父组件接收参数
并执行。
3. vue对props的基本类型、对象（浅层次监听，只监听整个对象的引用），修改会报错
所以props数据不建议使用v-model赋值

若在子组件item里，将input改为v-model，修改复选框状态，vue也能件听到数据改变渲染且不报错，数据改变正常渲染是因为修改复选框的值本身是对对象进行修改，vue能够监听到并渲染。  
不报错是因为目前vue本身对props的数据对象只做了浅层次的监听判断其是否改变，修改属性发现不了，修改整个对象才会报错。  
但是不建议修改props的传值，因为item数据是从父组件接收过来的，不建议修改props数据。因为props本意就是父向子单向数据传递的一个操作方式。

todolist  
add：top  
delete：item  
show：list  
update：item

app-top  
app-list-item  
app-footer  

因为数据是多个组件公用所以放在了app身上。
都是props传值


### 3.底部统计
reduce：数组条件统计方法  

1. `reduce`的`回调函数执行的次数`是数组的`length`次数，`第1次执行`对`第1个元素`执行回调，`第n次`对`第n个元素`执行回调

2. 该方法有两个参数，第一个是`回调函数`，第二个是`初始值,一般为0`(类似于for i=0)
- 回调函数有也有两个参数，`pre是上一次执行的返回值`，`current则是每次执行的元素对象`

    ```js
    arr=[{
            id:1,
            done:true
        },
        {
            id:2,
            done:false
        },{
            id:3,
            done:true
        }]
    // 回调执行3次，第1次对id:1元素执行···
    // 第1次执行pre是0，返回1；第2次pre是1，返回1；第3次pre是1，返回2；
    // 最后一次返回值作为reduce方法的返回值，最终统计done为true的元素为2
    this.arr.reduce((pre,current)=>{
        return pre + (current.done?1:0)
    },0)

    ```
3. 函数初始执行，pre是初始值0，函数返回值未指定为undefined，最后一次执行的返回值作为reduce方法的返回值，所以回调函数需要显示指定` return `

### 4.底部交互

1. 全部次数：数组长度  
已完成：计算属性计算，这样复选框被修改时会重新计算  
全部已完成的复选框：计算属性，当已完成的数量==todos.length时；存在bug，长度为0时，也被勾选了，判断长度不为0  
尽量使数据发生变化，引起相关联的计算属性发生变化，计算属性可以叠加计算。  

2. v-model是双向数据绑定，进行读写操作，绑定计算属性时就不能使用简写形式(只针对读)，get和set都要加

3. props从父传孙，要经过子组件，子组件被打扰，子组件可能不需要数据但要接收再传孙，逐层传递有这个弊端




## 2.浏览器本地存储（78）
**1. localStorage**
1. localStorage特点是浏览器关闭缓存不会清除
2. localStorage相关API     
    ```js
    // 新增缓存项
    localStorage.setItem('msg','hello') 
    localStorage.setItem('msg',666)  
    localStorage.setItem('msg',obj) // 非string会强制转成string
    // 存对象要JSON.stringify()转成字符串
    localStorage.setItem('p', JSON.stringify({age:18})) 
    
    // 读取缓存项
    localStorage.getItem('msg')
    // 读对象要JSON.parse()解析成json对象
    const person = localStorage.getItem('person')
    JSON.parse(person) 

    // 读取不存在的缓存项为null，JSON.parse(null)还是null
    JSON.parse(null) => null

    // 删除缓存项
    localStorage.removeItem('p')

    // 删除所有缓存项
    localStorage.clear()
    ```

**2. sessionStorage**

  sessionStorage的api与localStorage一模一样，  
  区别在于sessionStorage特点是浏览器关闭缓存会被清除


## 3.todolist本地存储（79）

## 4.组件自定义事件（80-82）

1. 自定义事件是相对于js内置事件，js内置事件click、keyup事件是给元素用的；
2. `自定义事件`是给`组件`使用的，`本质是操作组件` ，被用来作为`子组件` ——> `父组件`传递数据的一种通信方式。

3. 绑定和使用
- 第一种：
    - 父组件：使用`@或v-on`给`子组件绑定自定义事件`，设置自定义事件被触发时的`回调方法(data)`
    - 子组件：通过`this.$emit('自定义事件名',data)`触发自定义事件，父组件的回调就会接收到data
    
    ```html
    <!-- 给student组件的实例对象vc绑定atguigu自定义事件，回调是getStudentName -->
    <!-- 因为student本身是构造函数，只有在使用组件标签时才会携带参数(数据、方法)被实例化 -->
    <Student @atguigu="getStudentName" />
    ```
    ```js
    // 父组件回调
    getStudentName(data){ //... }
    // 子组件触发
    this.#emit('atguigu',666)
    ```

- 第二种：ref
    - 父组件：给子组件`绑定ref`，绑定自定义事件`this.$refs.student.$on('自定义事件名',回调函数)` ，`设置回调`
    - 子组件：通过`this.$emit('自定义事件名',data)`触发自定义事件，父组件的回调就会接收到data
    - 备注：
        - `this.$refs.student`表示组件实例
        - 通过ref可以更灵活的控制绑定自定义事件的时机，可以等父组件的接口请求完成之后在绑定等，一般写在mounted里...
        - ref写法只是在父组件里使用子组件标签时处理不同，子组件触发与第一种一致
        - 使用ref需要注意this问题
        ```js
        method:{
            this.$refs.student.$on('atguigu',function(data){
                this.xxx = data // this指的是student组件实例对象
            }) 
            // 之前说的vue管理的函数要写成普通函数
            // 这里vue设计使用ref绑定自定义事件时，谁触发了自定义事件，this就指向谁

            // 箭头函数没有this，向外找到了methods的this是当前组件实例对象
            this.$refs.student.$on('atguigu',((data))=>{
                this.xxx = data // this指的是student组件实例对象
            }) 
            this.$refs.student.$on('atguigu',this.getStudentName) 
            // this.getStudentName函数的this本应是student实例，但是将方法提到了vue的methods里且是普通函数，那么this就是当前组件实例对象，冲突了，从student改变为当前实例
        }
        ```
    ```html
    <!-- 父组件：给子组件绑定ref（第二种写法，使用ref） -->
    <Student ref="student"/>

    <script>

        // 一般在mounted里用ref绑定自定义事件
        mounted() {

            this.$refs.student.$on('atguigu',this.getStudentName) 
            this.$refs.student.$once('atguigu',this.getStudentName) 
            //绑定自定义事件（一次性,子组件只能触发一次就失效）
        
        },
        methods:{
            getStudentName(data){} // 自定义事件回调
        }
    </script>
    ```


4. 解绑自定义事件
- 为哪个组件绑定，就去哪个组件里解绑，一般都是子组件

-  API
    - `this.$off('atguigu')`  解绑一个自定义事件：atguigu
    - `this.$off(['atguigu','demo'])`  解绑多个自定义事件：atguigu、demo
    - `this.$off()` 解绑当前组件实例上的所有自定义事件

-  组件实例对象调用`this.$destroy()`销毁后，原生js事件依然存在，只是页面不更新渲染了；自定义事件被清除了，因为整个vc都没了，vc上的东西自然也就没了

    - 销毁会依次触发`beforeDestroy`和`destroyed`钩子函数，销毁前即beforeDestroy钩子里，可以获取data等数据，只是页面不再触发更新渲染机制了
    - 销毁分为**主动销毁**和**被动销毁**，主动是调用`this.$destroy()`；被动是`路由切换或者页面关闭`。
    - 所以一般可以放在`beforeDestroy`钩子解绑自定义事件。


    ```html
    <!-- 子组件 -->
    <button @click="unbind">

    <script>
       beforeDestroy() {
            unbind(){
                this.$off('atguigu') // 解绑atguigu自定义事件
                this.$off(['atguigu','demo']) // 解绑atguigu、demo自定义事件
                this.$off() //解绑当前组件实例上的所有自定义事件
            }
        }
    </script>

    ```
5. {{}}的数据来源于data、props、computed。不包含来自子组件传递过来的数据，应存到自己的data里
6. 给子组件设置原生事件
    ```html
    <!--  
    若给子组件绑定一个click事件，默认认为是自定义事件，添加.native修饰符：表示原生事件
    可以操作的元素是组件的最外层盒子，符合vue设计根元素有且只有一个节点 -->
    <Student  @click.native="show"/>
    ```
    ```js
    // 父组件回调
    show(data){ //... }
    // 子组件触发
    this.#emit('show',888)
    ```

## 5.todolist自定义事件（83）
1. 所有的子组件 ——> 父组件，改为自定义事件
2. 把原来父组件传递函数改为@xx=xxx,回调方法保留；子组件props去掉，调用父组件方法改为$emit('自定义事件名',data)

## 6.全局事件总线（84-85）

### 1.定义： 实现任意组件件通信的一种方式
### 2.引言
1. x是一个中间件  
    A组件里给x身上绑定自定义事件demo，事件回调留在A组件里。
    D —> A传数据，D触发x身上的demo自定义事件，携带传参。
    因为demo事件回调在A组件里，A可以接收到。  
    
    就是说，谁想接收数据，谁就在自己组件里给x中间件身上注册自定义事件，且事件回调留在自己组件里，  
    谁想传数据，谁就触发中间件x的自定义事件，携带参数，接收者就会接收。  

    那么，中间件x需要满足2个条件：
    - 1.所有组件都能看到找到它 => windows 或 vue.prototype
    - 2.身上要有$on、$off、$emit方法，才能操作自定义事件。=> 这些方法都在vue.prototype身上，也会在vm、vc身上
    - 3.应该让 vue.prototype.x = vc ,原型属性x就是组件实例对象，继承了Vue原型方法，所有vm和vc就都可以通过原型链找到事件方法了

2. 基础实现：
    ```js
    // main.js
    const D = Vue.extend({}) // 创建一个VueComponent的构造函数
    const d = new D() // 实例化VueComponent组件对象
    Vue.prototype = d 

    // 接收的组件这样写：
    this.x.$on('demo',this.getDemo) 
    // 组件中没有定义x，找到了原型链上的x，即组件实例对象，而on，则是实例对象的原型链上vue的原型方法
    getDemo(){}

    // 发送数据的组件这样写：
    this.x.$emit('demo',666)//触发x身上的自定义事件，传递参数，愿组件就可以接收到

    // 所有组件都能使用x，也能调用on、off等方法，x身上可以绑定自定义事件
    ```

3. 优雅的写法：**安装全局事件总线**
    ```js
    // d是vc，vc的方法vm都有，所以也可以用new Vue初始化的vm放在原型上

    Vue.prototype.x = vm // 不行，此时vm还没生成
    const vm = new Vue({
                    el:'#root',
                    render:h => h(App),
                    beforeCreate(){
                // Vue.prototype.x = this 
                //this就是vm，且此时的data还不能访问，因为数据监测、数据代理还没创建
                        Vue.prototype.$bus = this  
                        // 安装全局事件总线
                        // bus有总线的意思，$是为迎合vue程序员用api都带$的设计理念

                    }
                })
    Vue.prototype.x = vm // 也不行，此时vm已经实例化完了，晚了
    ```

4. 使用事件总线

    ```js
    // 接收：A组件想接收，则在A中给$bus绑定，事件回调留在A中
    methods:{
        getDemo(data){}
    }
    mounted(){
        this.$bus.$on('demo',this.getDemo)
    }

    // 发送、提供数据的组件：
    this.$bus.$emit('demo',[1,2,3])
    ```

5. 解绑在全局事件总线身上的自定义事件  
    **▶️最好在绑定全局自定义事件的组件销毁时，解绑在$bus上的自定义事件**
    ```js
        beforeDestroy(){
            this.$bus.$off('demo') //注意不要写错，不写事件名就是全解绑了
        },
        // 这点不同于组件自定义事件，组件不需要写解绑操作，因为组件被销毁后vc都没了，身上的自定义事件也就没了
    ```

6. 比较适用于使用全局事件总线的场景  

    孙 -> 爷，不用 爷 -> 父 -> 孙，逐层传递函数，尽量保证传过来的props都为自己所用，不用也不接收


7. 全局事件本质就是自定义事件，只不过给`$bus(组件)`<=>`vm`绑定自定义事件  

    自定义事件是给子组件绑定自定义事件，回调在父，  
     - 子组件`this.$emit('eventName',data)`
     - 父组件`<student @eventName="callfunName">`或`<student ref="refName">`
        - `this.$refs.refName.$on('eventName',this.callfunName)`
        - `callfunName(data){}`


## 7.todolist事件总线（86）
 孙发，爷收 
 App里接收，item发送


## 8.消息订阅与发布（87）
1. 概念 
- 一种组件间通信的方式，适用于任意组件间通信
- 消息订阅与发布是一种设计理念，借助第三方工具`pubsub-js`，该库适用于angular、vue、react
- 订阅者：需要数据，接收数据的人，需要指定`消息名`和收到消息后的`回调函数`
- 发布者：发送数据的人，发送`消息名`和`数据`

2. 安装引入和使用
- `npm i pubsub-js`
- `import pubsub from 'pubsub-js'`发送和接收组件都要引入
- 订阅者：
    - 订阅`pubsub.subscribe('msgName',this.handleMsg)`，订阅返回订阅的id
    - 回调`this.handleMsg(msgName,data){}`，
        - 订阅回调接收2个参数，消息名和data，一般用(_,data)占位符
    - 需要取消订阅`pubsub.unsubscribe(msgId)`使用订阅id取消订阅
- 发布者：发布`pubsub.publish('msgName',data)`
```js
// 订阅与取消订阅
import pubsub from 'pubsub-js'
	export default {
		name:'School',
		data() {
			return {
				name:'老王'
			}
		},
		mounted() {
            // 订阅消息和回调
            // pubsub的订阅回调接收两个参数，消息名和发送数据，虽然有点没必要，但人家就是这么设计的
            // 订阅返回一个订阅id
			this.pubId = pubsub.subscribe('hello',function(msgName,data){
				console.log(this) // undefined因为第三方库vue不维护
            })
            this.pubId = pubsub.subscribe('hello',(msgName,data)=>{
				console.log(this) // 当前vc，箭头函数本身没有this向外层找
            })
            this.pubId = pubsub.subscribe('hello',this.handleHello) // 使用vue函数
        },
        // 销毁之前取消自定义、订阅等..
		beforeDestroy() {
            // 使用订阅id来取消订阅
			pubsub.unsubscribe(this.pubId)
        },  
```

```js
// 发布
import pubsub from 'pubsub-js'
	export default {
		name:'Student',
		methods: {
			send(){
                // 发布
				pubsub.publish('hello',666)
			}
		},
	}
```


## 9.todolist-pubsub（89）
略

## 10.nextTick (90)
```js
// vue的更新渲染机制，是函数代码执行完成后再重新解析模板，如v-show=true输入框，然后获取焦点，
// 也就是说输入框设置显示后，vue并没有真的渲染页面让输入框显示，而是等到函数的代码执行完毕，才解析渲染，但是这会input没显示自然无法获取焦点
// 也是为了避免函数内部操作多次数据，更新多次浪费资源、效率也低
// 所以在函数里内部里操作dom节点操作，一般要等下次dom渲染完成后再操作。可以用延时器或nextTick

// nextTick接收一个回调
nextTick(()=>{})
```