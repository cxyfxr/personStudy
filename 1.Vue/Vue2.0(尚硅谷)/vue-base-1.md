### 1.1 vue简介

#### 1. vue特点
- 采用组件化模式，提高代码复用率、切让代码更好维护
    - page.vue[每个页面相当于一个组件]
        - html
        - js
        - css
- 声明式编码无需直接操作dom，提高开发效率
    - 一步一步 dom拼接 塞数据 是命令式
    - 列表遍历
- 使用虚拟dom+优秀的diff算法，尽量复用dom节点
    - 数据更新时，虚拟dom重新生成时会和之前的虚拟dom比较diff，发现数据有新的，有之前存在过的，旧的接着用，新的再生成，达到dom复用
    - js过滤数据也可以实现，但比较麻烦，且数据量过大时效率低
    
#### 2. vue官网的使用-导航
- 1、学习
    - 教程：入门*
    - Api：字典*
    - 风格指南：优雅代码
    - 示例
    - cookbook：技巧js、vue
- 2、生态 
    - 帮助：不重要，略过
    - 信息：不重要，略过
    - 工具
        - Devtools
        - vuecli
        - vueLoader
    - 核心插件
        - vueRouter
        - vueX
        - vue服务器渲染
- 3、资源列表
    - AwesomeVue：强大生态
        -  成型的被vue推荐的第三方库

---------------------------------

### 1.2 初识vue
#### 1、新手入门建议引入单文件，脚手架过于复杂不推荐使用
1、步骤:
- 1、本地引入vue文件或者cdn引入线上链接
- 2、创建容器，root容器里的代码遵循html规范，只不过混入一些vue语法的代码，root容器里的代码叫做vue模板	
- 3、创建vue实例，传入配置对象，挂载容器

2、备注：
- 1、vue实例和容器是一一对应的；
- 2、真实开发中，只有一个vue实例，并且配合组件一起使用
- 3、{{xxx}}xxx是js表达式，且可以读取data数据
- 4、data数据更新时，模板中对应的属性也会同步更新
- 5、注意区分：js表达式和js语句（代码）

3、example：
```js
//js表达式和js语句（代码）
	1、表达式
		一个表达式会产生一个值，可以放在任意一个需要值的地方
			1.a
			2.a+b
			3.demo(1)
			4.x=1?1:0
	2、js语句
			if(){}
			for(){}
	
Vue.config.productionTip=false //关闭启动时生成生产提示

//创建vue实例
const vm=new Vue({
//el指定当前vue挂载的容器，写法通常是css选择器，可以document.getElementById('root')
	el:'#root', 
	data:{ //data的值用于绑定vue容器内使用，暂时写成一个对象
		name:'老李'
	}
})
```
---------------------------------


### 1.3 模板语法：2大类

#### 1.插值语法：
- 功能：用于解析标签体内容。
- 写法：{{xxx}}，写在标签体里，能写什么呢
    - js表达式：2+2；三目；函数调用（但必须是methods中的方法）
    - data属性
    - 所有vm自身内容和原型方法

#### 2.数据绑定指令语法：
- 单向绑定(`v-bind`)：数据只能从data流向页面。
    - v-bind:xxx <=> :xxx
    - xxx同样要写js表达式、且可直接读取data中所有属性。 
    - 备注：属性名前没有冒号，属性值是字符串；属性名前有冒号，属性值是vue语法了：data属性或表达式
- 双向绑定(`v-model`)：数据不仅能从data流向页面，还可以从页面流向data。
    -  v-model:value <=> v-model，因为v-model默认收集的就是value值。
    -  双向绑定一般都应用在表单类元素上（如：input、select等）

```js
//：属性名的值就是字符串，加了就是变量
<a v-bind:href="url" x="hello">点我去</a>
<a :href="url" x="hello">点我去</a>
```

#### 3、el和data的两种写法

1.el有2种写法
- (1).new Vue时候配置el属性。
- (2).先创建Vue实例，随后再通过vm.$mount('#root')指定el的值。

2.data有2种写法
- (1).对象式
- (2).函数式

如何选择：目前哪种写法都可以，以后学习到组件时，data必须使用函数式，否则会报错。

3.一个重要的原则：
- 由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了。

```js
//el的两种写法：
//第一种写法
const v = new Vue({
    el:'#root', 
    data:{
        name:'尚硅谷'
    }
})

//第二种写法
v.$mount('#root')  //$mount有挂载的意思


//data的两种写法：
//data的第一种写法：对象式
new Vue({
	el:'#root',
	data:{
	    name:'尚硅谷'
	}
})

//data的第二种写法：函数式
new Vue({
	el:'#root',
	data(){
    	console.log('@@@',this) //此处的this是Vue实例对象
    	return{
            name:'尚硅谷'
    	}
	}
})

//函数定义
data:function(){}
//函数简写：es6
data(){}
```

#### 4、打印vue实例
```js
const vm=new Vue({
    el:'#root',
    data:{
        name:'li'
    }
})
console.log(vm)
//控制台以$开头的前几个方法都是给程序员使用的，
//往下是data的属性和方法
//以_开头的原型方法是vue底层使用的
```
---------------------------------



### 1.4 mvvm
1、vue在一定程度上参考了mvvm模型
- M-模型model -> 对应data中数据
- V-视图view-> 对应模板(DOM)
- VM-视图模型viewmodel -> vue实例对象

2、vm视图模型
- 数据(model)通过vue将数据放到页面指定位置上(view)
- 页面(view)内数据发生变化，被vue监听映射到数据(Model)中，一般是表单类会有输入变化。
---------------------------------


### 1.5 数据代理
#### 1、Object.defineProperty()
1、Object.defineProperty()相关知识点
```js
//调用该方法，一般只传{value:''}, 其他不传的默认为false，不可枚举、不可删除、值不能被修改
//此处的默认指的是调用函数时不传参数的默认值
//不可枚举的属性通过console.log打印出来是灰色的

Object.keys()//返回以对象的可枚举的key组成的数组
for..in //既能遍历数组，也能遍历对象,遍历可枚举项
```
2、使用Object.defineProperty()
```js
let number = 18
let person = {
	name:'张三',
	sex:number,
}
//但number修改了，sex并不能拿到更新的nimber，因为程序只执行一次结束了
//使对象的age和number关联起来
let number = 18
let person = {
	name:'张三',
	sex:'男',
}

Object.defineProperty(person,'age',{
	// value:18,
	// enumerable:true, //控制属性是否可以枚举，默认值是false
	// writable:true, //控制属性是否可以被修改，默认值是false
	// configurable:true //控制属性是否可以被删除，默认值是false

	//当有人读取person的age属性时，get函数(getter)就会被调用，且返回值就是age的值
	get(){
		console.log('有人读取age属性了')
		return number
	},

	//当有人修改person的age属性时，set函数(setter)就会被调用，且会收到修改的具体值
	set(value){
		console.log('有人修改了age属性，且值是',value)
		number = value
	}

})
```

#### 2、数据代理
- vue通过数据代理将data的属性全部都出现在vm身上
- 数据代理的底层是用到了`Object.defineProperty()`
- 不光是`数据代理`、`数据劫持`、`计算属性`等都用到了该方法。
- 概念：通过一个对象代理到另一个对象中属性的操作（读写）

#### 3、vue中的数据代理原理
```js
const vm=new Vue({
    el:'#root',
    data:{
        name:"name",
        addredd:"青岛"
    }
})
//配置对象后，vue创建实例vm，
{
    $attrs:...,
    $children:...,
    ...
    _data:{//vue将配置对象data的数据存放到_data里
        name:"name",
        addredd:"青岛"
    }
    //这时候页面就可以使用_data.name获取数据
    //但也阅读不友好，所以vue做了数据代理
    //将_data数据代理到vue对象上
     name:"name",
     addredd:"青岛"
     //赋值data对象到_data里，遍历拿到每个属性，为其调用Object.defineProperty方法，添加上get和set属性
     //这样页面{{name}}是从vm上的属性，vm从_data中代理，_data又从data中获取
     //vm修改属性，就是修改_data，_data修改后，更新到页面，这一步原理待讲
}
```
---------------------------------



### 1.6 事件的基本使用
#### 1、事件的基本使用
- 1.使用` v-on:xxx ` 或` @xxx `绑定事件，其中` xxx `是事件名；
- 2.事件的回调需要配置在methods对象中，最终会在vm上；
- 3.methods中配置的函数，不要用箭头函数！否则this就不是vm了；
- 4.methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象；
- 5.` @click="demo" `和` @click="demo($event，params)" ` 效果一致，但后者可以传参；
- 事件可以写多条简单语句,分号间隔，但不推荐 @click="ishot=!ishot;x++"
- `@click="alert()"`用了@vue标识符，vue就会去vm上寻找自有方法，找不到再去vue原型链找，不会找到window
- data里可以定义window:window（全局window）

#### 2、Vue中的事件修饰符：
- 1`.prevent`：阻止默认事件（常用）；[e.stopPropation()]
- 2`.stop`：阻止事件冒泡（常用）；[e.preventDafault()]
- 3`.once`：事件只触发一次（常用）；
- 4`.capture`：使用事件的捕获模式；
- 5`.self`：只有event.target是当前操作的元素时(点击自己)才触发事件；
- 6`.passive`：事件的默认行为立即执行，无需等待事件回调执行完毕；
- 修饰符可以连续使用   `@click.stop.prevent=xx` 按照顺序，先阻止冒泡再阻止默认行为

#### 3、example：
```html
<!-- 阻止事件冒泡（常用） -->
<div class="demo1" @click="showInfo">
	<button @click.stop="showInfo">点我提示信息</button>
	<!-- 修饰符可以连续写 -->
	<!-- <a href="http://www.atguigu.com" @click.prevent.stop="showInfo">点我提示信息</a> -->
</div>

<!-- 事件只触发一次（常用） -->
<button @click.once="showInfo">点我提示信息</button>

<!-- 使用事件的捕获模式 -->
<div class="box1" @click.capture="showMsg(1)">//捕获是向下走，直到找到目标元素；冒泡是找到目标元素后向上走。使用捕获模式会在向下走时就调用；事件默认走的是冒泡
	div1
	<div class="box2" @click="showMsg(2)">
		div2
	</div>
</div>

<!-- 只有event.target是当前操作的元素时才触发事件； -->
<!-- 只有点击自己才触发事件； -->
<div class="demo1" @click.self="showInfo">
	<button @click="showInfo">点我提示信息</button>
</div>

<!-- 事件的默认行为立即执行，无需等待事件回调执行完毕； -->
<ul @wheel.passive="demo" class="list">
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
</ul>

<!--
    demo(){
        for (let i = 0; i < 100000; i++) {
            console.log('#')
        }
        console.log('累坏了')
    } 
-->


<!-- 
scroll:滚动条滚动事件
上下键触发函数，即使滚动条发生微小变化
滚轮一次可触发多次函数，滚动条在非底部时
滚动条在底部时，滚轮向下滚不会触发事件，因为滚动条没有发生变化
只要滚动条变化了，就触发，一般可能触发多次

wheel:鼠标滚轮的滚动事件
鼠标滚动一下触发一次函数
拖动滚动条不会触发
在滚到滚动条底部时，鼠标滚动也会触发
只要滚就触发一次

事件：有click、dbclick、wheel...
事件在触发时调用回调函数，有些事件有默认行为，当默认行为和回调函数同时存在时，哪一个会被先执行呢？
wheel事件会先执行回调函数，在执行默认行为
当它的回调函数处理量过大时，会造成滚动条延时下滑，用户体验不好
这种情况应该先执行默认行为，再执行回调函数的处理
passives事件标识符就是先执行默认行为再执行回调函数，当然要有默认行为的事件才可以用
手机端 移动端，向下滑动时，应该先处理默认事件，再处理回调函数 
-->
```

---------------------------------



### 1.7 键盘事件
#### 键盘事件
1、@keydown按键按下事件、@keyup按键抬起事件
2、配合原始键名Enter可实现指定按键事件(e.key键名，e.keyCode键码)，注意kebab-case写法
- ` 键名Enter => @keyup.Enter ="xxx"` 
- ` 键名capsLock => @keyup.caps-lock="xxx" ` 

3、vue为使用方便提供了常用别名按键事件
- ` @keyup.enter ="xxx"` 回车
- ` @keyup.delete ="xxx"` 删除（删除和退格）
- ` @keyup.esc ="xxx"` 退出
- ` @keyup.space ="xxx"` 空格
- ` @keyup.tab ="xxx"` 换行(配合keydown事件)
- ` @keyup.up ="xxx"` 上
- ` @keyup.down ="xxx"` 下
- ` @keyup.left ="xxx"` 左
- ` @keyup.right ="xxx"` 右  

4、不是所有按键都可以绑定键盘事件，比如音量、调光，一般通用按键才有
5、比较特殊的小兄弟
- 1、`tab`：上述tab按键，本身有默认行为换行，在按下时就转移光标了，可以配合`keydown`事件
- 2、系统修饰键：`ctrl、alt、shift、meta`
    - 配合keydown事件使用 `@keydown.ctrl=xxx`
    - 配合keyup使用时，需按下当前系统修饰键和任意其他按键同时使用，用户使用模糊且麻烦，不推荐

6、系统修饰键可以连续使用
- ` @keyup.ctrl.y='xxx' ` 指定ctrl+y
- ` @keyup.ctrl='xxx' ` ctrl+任意按键，包含y和其他，用户使用模糊
7、可以通过keyCode直接绑定按键事件
- ` @keyup.13='xxx' ` 但该特性已被废弃，且不同键盘键码也不统一，不建议使用
8、设置vue别名，自定义按键别名
- ` Vue.config.keyCodes.huiche=13(键码) `

### 1.8 计算属性
#### 1.一点原理
1、“data中数据更新时，vue模板都会重新解析一遍：模板用到了哪些属性，就会去读那些属性”
个人理解：
- 1、data数据更新时，vue模板会重新读取它用到的那些属性，然后生成虚拟dom，对比之前的dom，如果列表三个数据变成了四个数据，只有最后一个是新增的，那就只添加最后一个，哪个dom节点改了就更新哪个dom节点；
- 2、data更新时，vue模板是拿不到更新的具体属性，不然就可以拿模板用到的数据和更新的属性做比较了，不对，应该可以拿到更新的具体属性，因为模板的属性有可能是依赖其他属性，若对比更新的属性是否被模板用到了，没用到就不更新模板，那依赖的数据就拿不到新值
2、vue里的计算属性、method方法，都是如何获取或者更新的呢？
个人理解：
- 1、data属性更新：如上1，data数据只要有更新，模板就会重新读取模板里用到的属性，然后生成虚拟dom再对比，虚拟dom有更新他就更新；
- 2、method方法：模板中的mthods方法通常被{{}}包裹，因为是方法，vue不知道依赖了哪些属性，所以模板每次用到了都重新调用方法。
- 3、计算属性：根据计算属性更新的两个原则，推测，读取计算属性时：
    - 先查询该属性的缓存是否存在，若不存在，则调用该属性get方法，get方法内置缓存机制：get拿到返回值后将其放置该属性缓存里。
    - 所依赖的属性发生更新时，推送给计算属性，调用其get方法，同时再更新缓存，下次再读该属性时，直接返回缓存。


#### 2.计算属性定义的使用
- 1.定义：要用的属性不存在，要通过已有属性计算得来。
- 2.原理：底层借助了Objcet.defineproperty方法提供的getter和setter。
- 3.get函数什么时候执行？
    - (1) 初次读取时会执行一次。
    - (2) 当依赖的数据发生改变时会被再次调用。
- 4.优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便。
- 5.备注：
    - 1.计算属性最终会出现在vm上，直接读取使用即可。
    - 2.如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变。

```js
	computed:{
        // 完整写法，是个对象
        fullName:{
            //get有什么作用？当有人读取fullName时，get就会被调用，且返回值就作为fullName的值,放到vm上
            //get什么时候调用？1.初次读取fullName时。2.所依赖的数据发生变化时。
            get(){
                console.log('get被调用了')
                return this.firstName + '-' + this.lastName
            },
            //set什么时候调用? 当fullName被修改时。
            set(value){
                console.log('set',value)
                const arr = value.split('-')
                this.firstName = arr[0]
                this.lastName = arr[1]
            }
        },
        // 简写:只读不改才能简写get()
        fullName(){
            console.log('get被调用了')
            return this.firstName + '-' + this.lastName
        }
        //data属性在_data里，以get和set代理到vm上
        //methods方法在vm上直接存在，没有get和set
        //计算属性是通过已有的属性进行计算存到vm上的
    }
```
---------------------------------



### 1.9 侦听属性
#### 1.开发者工具的一个bug
>若修改的属性，在模板中没有使用，开发者不动态展示修改变化的过程，因为它认为在页面里没使用，就没有必要再更新了  

#### 2.定义和使用

- 1.定义：当被监视的属性变化时, 回调函数自动调用, 进行相关操作
- 2.监视的属性必须存在，才能进行监视
- 3.监视的两种写法：
    - (1).new Vue时传入watch配置
    - (2).通过vm.$watch监视(创建vue实例时不知道要监测谁，后续根据用户的行为菜进行监视)
- 4、watch完整配置:对象法
    - (1).`immediate属性`初始化调用handle，默认是false
    - (2).`deep属性`开启深度监听，默认为false;(Vue中的watch默认不监测对象内部值的改变，只监测一层)
    - (3).`handler(newValue,oldValue){}`回调函数，传参为更新后的值和更新前的值，若传参只有一个则是更新后的值
- 5、watch两种方式的简写
    - 只能在immediate和deep为false时，才可以使用简写`isHot(newValue,oldValue){}`
    - vm.$watch的监测属性必须加引号，函数回调不能是箭头函数，要这样写`vm.$watch('isHot',function(newValue,oldValue){ }) `
- 6、computed和watch之间的区别：
    - 1.computed能完成的功能，watch都可以完成。
    - 2.watch能完成的功能，computed不一定能完成，
    - `例如：watch可以进行异步操作。computed不可以异步是因为需要有return返回值，但是异步里的return不是get的返回，不能把异步的返回作为get的返回，所以不可以`
- 7、两个重要的小原则：
    - 1.所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm或组件实例对象。
    - 2.所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，
        这样this的指向才是vm或组件实例对象。
- 8、备注：
    - (1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不监测对象内部值的改变(一层)！因为默认监听多级，效率低
    - (2).使用watch时根据数据的具体结构，决定是否采用深度监视。
    
#### 3.example实例说明
```js
data:{
    isHot:true,
    numbers:{
        a:1,
        b:1,
        c:{
            d:{
                e:100
            }
        }
    }
},
computed:{
    info(){
        return this.isHot ? '炎热' : '凉爽'
    }
},
watch:{
    //完整写法
    numbers:{ //监听计算属性，应该是字符串'numbers',属性名可以省略引号
        immediate:true, //初始化时让handler调用一下
        deep:true, //deep默认false，监视多级结构中所有属性的变化；若对象不开启deep，则只有当整个对象被重新赋值时，才能被监听到，因为监听的是对象的引用地址，即1层
        //handler什么时候调用？当被监听的属性numbers发生改变时。
        handler(newValue,oldValue){
            console.log('numbers被修改了',newValue,oldValue)
        }
    },
    //简写：不需要deep和immediate时才能简写
    isHot(newValue,oldValue){
        console.log('isHot被修改了',newValue,oldValue,this)
    } 
    //监视多级结构中某个属性的变化,一般不使用
    'numbers.a':{
        handler(){
            console.log('a被改变了')
        }
    },
    //不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，因为this是上层，即vue管理的函数的this，vm
    firstName(val){
        setTimeout(()=>{
            console.log(this)
            this.fullName = val + '-' + this.lastName
        },1000);
    }, 
} 
//vm正常写法
vm.$watch('isHot',{
    immediate:true, //初始化时让handler调用一下
    deep:true,//深度监视
    //handler什么时候调用？当isHot发生改变时。
    handler(newValue,oldValue){
        console.log('isHot被修改了',newValue,oldValue)
    }
})
//vm简写:这里回调不能写为箭头函数,否则this是window
vm.$watch('isHot',function(newValue,oldValue){
    console.log('isHot被修改了',newValue,oldValue,this)
}) 
```

---------------------------------




---------------------------------

### 1.10 class、style样式绑定

```html
<!-- 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
<div class="basic" :class="mood">{{name}}</div> <br/><br/>
<!-- mood:normal 类名 -->
<!-- vue可以将class和:class中的样式合并 -->

<!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
<div class="basic" :class="classArr">{{name}}</div> <br/><br/>
<!-- :classArr=["normal","small","important"] -->
<!-- :classArr=[a,b,c]  a:normal,b:normal,c:important-->

<!-- 绑定class样式--对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
<div class="basic" :class="classObj">{{name}}</div> <br/><br/>
<!-- classObj={normal:true,small:false} -->
<!-- normal属性名就是类名，因为属性名本身就是字符串 -->



<!-- 绑定style样式--对象写法 -->
<!-- style样式对象的key不能瞎写，得是存在的css样式名 -->
<div class="basic" :style="styleObj">{{name}}</div> <br/><br/>
    <!-- 
        styleObj={
            fontSize:'14px',
            color:'red'
        }
         -->
<div :style="{
        width:obj.width + 'px',
        position:'absolute',
        top:obj.top?obj.top + 'px':''
    }"></div>


<!-- 绑定style样式--数组写法 -->
<div class="basic" :style="styleArr">{{name}}</div>
<!-- 数组是样式对象 -->
<!-- 
    styleArr=[{
        fontSize:'14px'
    },{
        color:'red'
    }] 
    -->
```

