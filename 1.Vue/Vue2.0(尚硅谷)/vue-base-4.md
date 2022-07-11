## 1.组件（53）
组件定义：
组件优点：
组件与模块化区别：
组件分类：
- 非单文件组件(.html)
- 单文件组件(.vue)

## 2.非单文件组件 (.html)（54-55）

1、Vue中使用组件的三大步骤：
- 一、定义组件(创建组件)
- 二、注册组件
- 三、使用组件(写组件标签)

2、如何定义一个组件？
- 1.使用Vue.extend(options)创建，其中options和new Vue(options)传入的options几乎一样，区别如下：
    - 1.el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
    - 2.data必须写成函数，为什么？ ———— 避免组件被复用时，数据存在引用关系。
    - 备注：使用template可以配置组件结构,只是过渡，真实开发不用。
        - template:'' 写单行html结构 
        - template:`` 写多行html结构 
        - template中必须有一个根节点，template不渲染自身标签

- 2.注册组件
    - 1.局部注册：靠new Vue的时候传入components:{}选项
    - 2.全局注册：靠Vue.component('组件名',组件)  

- 3.使用组件标签：
    - `<school></school>`
    - `<school/>`[脚手架专用，不使用脚手架导致后续组件无法渲染]

3、组件几个注意点：
- 1、注册名命名（注册名和使用标签始终是对应的）
    - 1个单词
        - 纯小写，使用标签也是纯小写
        - 首个字母大写，使用标签也是首字母大写(跟开发者工具呼应)
    - 多个单词
        - kabab-case:全小写中间用- `my-school`，使用标签同样对应,`<my-school>`
        - camelCase每个单次首字母大写，`MySchool`，使用标签同样对应,`<MySchool>` [脚手架专用]
    - 备注：
        - 1.禁止使用html已有标签名作为注册组件名   
        - 2.关于name
            - 若定义组件时指定了name，则开发者工具使用name作为呈现的名字，首字母依旧会大写
            - 若定义组件时未指定name，则开发者工具使用注册的组件名作为呈现名字，首字母大写
- 2、关于组件标签
    - `<school></school>`
    - `<school/>` [脚手架专用]
- 3、一个简写形式
    -  `const school = Vue.extend(options)` 生成并返回VC构造函数，再拿过来用
        - 显示使用Vue.extend会立即生成并返回VueComponent构造函数。components对象只是做了引用，页面使用标签时(页面渲染)再new实例化
    -  `const school = options` 用的时候再调用Vue.extend生成并返回VC构造函数，再拿过来用
        - 创建school时只是对象，componnets引入对象，在页面使用组件标签时再判断对象或VC构造函数，对象再调用Vue.extend，然后实例化

```js
// 1、创建组件
//显示使用Vue.extend会立即生成并返回VueComponent构造函数
//components对象只是做了引用，页面使用标签时(页面渲染)再new实例化
const s = Vue.extend({
    name:'',//组件名称
    //模板结构
    template:` 
        <div>
            <h1>你好{{msg}}</h1>
        </div>
    `,
    data(){
        return{
            msg:'tom'
        }
    }
})

1.配置对象与new Vue()传入对象几乎一致，区别有两点：
    - 1.el属性不能写，因为组件不属于谁，vm可以将其用在任何处，而el只能用于new创建的实例身上。
    - 2.data对象要用函数返回，否则报错。vue这样设计的原因是，对象传递的是引用，修改组件会影响到其他组件。而函数返回{}，就是返回一个新对象，组件内可以维护自己的私有变量。
2.非单文件html中，模板结构只能写template，只是过渡，真实开发不用。
    template:'' //写单行html结构 
    template:`` //写多行html结构 
    template:'<div></div>' //template中必须有一个根节点，template不渲染自身标签


// 2、注册组件
// 局部注册
new Vue({
    el:'#root',
    components:{
        school  // school:school的简写：注册的名字和引用的变量同名可以使用 school:school
        school:s //school为注册的组件名字，s为中转变量
    },
})
//全局注册
Vue.component('school',s)//school:注册的组件名，s:引入的中转变量
```
```html
<!-- 3、使用组件 -->
<div id="root">
    <!-- 使用组件标签 -->
    <school></school>
</div>
```

## 3.组件嵌套（56）
- 嵌套
    - 组件注册给谁了，就在谁那使用
- 引出app概念
    - 一般采用app管理所有组件，vm只管理app组件，app是vm之下，组件之上
- template
    - template里必须有一个根节点，vm中使用template会覆盖el挂载的节点

```js
    //定义student组件
    const student = Vue.extend({
        name:'student',
        template:`
            <div>
                <h2>{{name}}</h2>
            </div>`,
        data(){
            return { 
                age:18 
            }
        }
    })
		
    //定义school组件
    const school = Vue.extend({
        name:'school',
        template:`
            <div>
                <h2>学校名称：{{name}}</h2>	
                <student></student>
            </div>
        `,
        data(){
            return { 
                name:'尚硅谷'
            }
        },
        components:{
            student //注册组件（局部）
        }
    })

    //定义hello组件
    const hello = Vue.extend({
        template:`<h1>{{msg}}</h1>`,
        data(){
            return { msg:'欢迎来到尚硅谷学习！' }
        }
    })
    
    //定义app组件
    const app = Vue.extend({
        template:`
            <div>	
                <hello></hello>
                <school></school>
            </div>
        `,
        components:{
            school,hello
        }
    })

    //创建vm
    new Vue({
        //注意，vm使用template显示内容，会替换掉root的div
        //如果在div里使用组件标签，则包含root的div
        template:'<app></app>',
        el:'#root',
        components:{app} //注册组件（局部）
    })
    // vm => school => student
    // 组件注册给谁了，就在谁那使用
    // 一般采用app管理所有组件，vm只管理app组件，app是vm之下，组件之上
```



## 5.VueComponent（57）

- 1.Vue.extend调用后生成并返回一个名为VueComponent的构造函数，school组件实际就是VueComponent的构造函数。

- 2.我们只需要写`<school/>`或`<school></school>`，Vue解析时会帮我们创建school组件的实例对象，
    即Vue执行：new VueComponent(options),携带传给子组件的传参option。

- 3.特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！因为代码里是var sub，每次都生成一个新的函数

- 4.组件执行的原理总结：
    - 1、Vue.extend生成返回不同的构造函数作准备。
    - 2、数据改变引起模板解析。模板解析时，寻找components对象的组件引用，
        - 若是构造函数传递不同参数实例化对象；
        - 若是对象，调用Vue.extend返回构造函数再实例化

- 5.关于this指向：
    - (1).组件配置中：
        data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】。
    - (2).new Vue(options)配置中：
        data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】。

- 6.VueComponent的实例对象，以后简称vc（也可称之为：组件实例对象）。
    Vue的实例对象，以后简称vm。


## 6.一个重要的内置关系（58-59）

1.一个重要的内置关系：VueComponent.prototype.__proto__ === Vue.prototype
2.为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue原型上的属性、方法。
3.vm与vc区别在于，vc不能传el属性，且vc的data必须函数式返回对象，其他都一样。  
防止组件被多次引用时数据存在引用关系  

```js
//创建一个Demo的实例对象
const d = new Demo() //构造函数

console.log(Demo.prototype) //构造函数的原型对象，也叫显示原型属性

console.log(d.__proto__) //实力对象的原型对象，也叫隐式原型属性

console.log(Demo.prototype === d.__proto__) // 指向同一对象

Vue.prototype === vm.__proto__ //vue构造函数原型对象也是实力对象的原型对象
vm.__proto__.__proto__ === Vue.prototype.__proto__ === Object //vue原型对象是继承自object

VueComponent.prototype.__proto__ === Vue.prototype 
//VueComponent原型对象是继承自vue原型对象，所以VueComponent原型对象的原型对象是vue的原型对象
//这样VueComponent组件就有了vue的方法和属性


```

## 7.单文件组件 (.vue)（60）
- 1、命名
    - school my-school
    - School My-School 好处是与开发者工具呈现呼应
    - name最好与文件名一致
- 2、**.vue文件**
- 3、针对html、js、css，vue设计了`<template><style><script>`对应三种标签，其中template标签本身不参与编译，编译该标签是有package.json的vue模版编译插件工具完成的
- 3、vscode默认不识别vue文件，安装插件vetur
- 4、非单文件组件html，弊端是样式不好维护到页面里面，违背了组件将html、jss、css维护到一起的初衷
- 5、单文件组件，需要配合脚手架或webpack才能打包运行，否则浏览器不识别文件
- 6、引入文件的后缀
    - 脚手架的组件引入文件可以省略后缀名 `import a from './a' `等价于`import a from './a.vue'`
    - js引入文件不可以省略后缀名
    - 脚手架引入模块包时可以`import a form 'vue'` vue默认引用的是vue的package.json中的mudule的包
- 7、单文件组件结构
    - App: 汇总所有组件
    - index.html: 容器
    - main.js: 入口文件
    - student.vue: 子组件,基础功能模块  

注意：单文件组件只是为了熟悉脚手架基本结构而练习的，并不能被浏览器所识别渲染，要想被运行渲染要配合webpack/脚手架执行
- 8、关于template
    - 单文件组件的`<template>`、new Vue的template、Vue.extend()的template都是需要一个根节点。
    - 但是单文件组件的`<template>`是用package.json中的插件解析的，new Vue的template是用模板解析器解析或render函数
    - 项目引入的vue一般是只有核心代码的精简版，不是完整版。完整包含核心功能+模板解析器。开发过程中会使用到模板解析器，但是开发完成会交给webpack打包编译，模板解析器就失去作用了，不应该留在项目里？？
    
```js
// 模块化的组件简写形式
	//1、定义组件
    const s = Vue.extend({
        name:'atguigu',
        template:`<div>hi</div>`
    })
    export default s

    //2、引入组件
    export school form '/s'
    new Vue({
        el:'#root',
        components:{ // 注册组件
            school:s
        }
    })

    // 因为单文件组件只有一个组件，所以暴露可以使用默认暴露，可以这样简写

    // 第一步：默认暴露 export default 替换  const s =
    export default Vue.extend({
        name:'atguigu',
        template:`<div>hi</div>`
    })
    //第二步：Vue.extend(）可以被省略
    export default {
        name:'atguigu',
        template:`<div>hi</div>`
    }
    // 引入
    export school form '/s'

```

```html
 <!-- student.vue子组件，基础功能模块 -->
<template>
	<div>
		<h2>学生姓名：{{name}}</h2>
		<h2>学生年龄：{{age}}</h2>
	</div>
</template>
<script>
	 export default {
		name:'Student',
		data(){
			return {
				name:'张三',
				age:18
			}
		}
	}
</script>
```

```html
<!-- App.vue 一个维护所有组件的组件 -->
<template>
	<div>
        <!-- 使用组件 -->
		<School></School>
		<Student></Student>
	</div>
</template>
<script>
	// 引入组件
	import School from './School.vue'
	import Student from './Student.vue'
	export default {
        name:'App',
        // 注册组件
		components:{
			School,
			Student
		}
	}
</script>
```

```js
// main.js 
import App from './App.vue'

new Vue({
	el:'#root',//挂载容器
	template:`<App></App>`,//模板，替换el
	components:{App},
})
```

```html
<!-- html -->
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>练习一下单文件组件的语法</title>
	</head>
	<body>
		<!-- 提供容器 -->
        <div id="root"></div>
<!-- 需要先引入vue文件， 再引入main，这样不会在main入口中还没引入vue就先调用vue方法而报错 -->
		<script type="text/javascript" src="../js/vue.js"></script>
		<script type="text/javascript" src="./main.js"></script>
	</body>
</html>
```

```js
// 模块化
const a={}
export default a
import a from './'


export function a(){}
export function b(){}

import {a,b} from './'

```

## 8.创建脚手架 (.vue)（61）

>mkdir fileName 创建文件夹  
环境准备 nvm node npm cnpm   
vue create 项目名 回车

1. 文件结构
- node_modules：包依赖文件
- public     
- src
    - assets：静态资源
    - components： 组件
- App.vue： 汇总组件
- main.js： 入口文件
- .gitgnore： git忽略文件
- babel.config.js： babel配置 es6 -> es5
- package-lock.json： 包版本文件
- package.json： 包依赖文件
- Readme.md：
- vue.config.js：脚手架配置文件

```html
<!-- 针对IE浏览器的一个特殊配置，含义是让IE浏览器以最高的渲染级别渲染页面 -->
<!-- 但vue不支持IE8 所以没啥用-->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<!-- 开启移动端的理想视口 -->
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<!--
    注意%前后空格 
    为了避免服务器文件混乱，html页面不要写相对地址用这种方式 
    BASE_URL指public文件路径
-->
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
<!-- 引入第三方样式 -->
<link rel="stylesheet" href="<%= BASE_URL %>css/bootstrap.css">

<!-- 当浏览器不支持js时noscript中的元素就会被渲染 -->
<noscript>
    <!-- 指package.json中的name项目名 -->
    <strong> <%= htmlWebpackPlugin.options.title %></strong>
</noscript>
```

## 9.Render函数（63）

1. 关于不同版本的Vue：
- 1.vue.js与vue.runtime.xxx.js的区别：
    - (1).vue.js是完整版的Vue，包含：核心功能+模板解析器。
    - (2).vue.runtime.xxx.js是运行版的Vue，只包含：核心功能；没有模板解析器。
        - vue.runtime.esm.js是es6语法的运行版的Vue，浏览器不识别
        - vue.runtime.js 是es5语法的运行版的Vue
- 2.因为vue.runtime.xxx.js没有模板解析器，所以不能使用template配置项，需要使用
			render函数接收到的createElement函数去指定具体内容(只在new vue使用一次)。
2. 引用第三方库模块包
- 模块包都会有`package.json`配置文件，里面定义了`modules`，表示使用es6模块化引入包时引用的具体文件。 
3.不同时期的模板时如何被解析的
- 完整版vue包含核心功能+模板编译器，在开发时使用模板编译器解析模板；生产环境时使用webpack打包编译输出html、js、css等。
- 精简版vue只有核心功能，
    - 开发时`.vue`文件里的`<template>`使用package.json的devDependencies下的vue-template-compiler插件解析模板；生产环境时使用webpack打包编译。
    - 开发时`template`配置项无法解析，但是简单结构，像new New时的挂载app组件可以通过render函数返回虚拟dom节点；生产环境时使用webpack打包编译。


1.脚手架之前单文件组件学到的template写法是：
```js
//曾经分析app应该这样注册
import Vue from 'vue' 
import app form './app' // 引入组件
new Vue({
    el:'#root',
    template:'<app></app>', //使用组件
    components:{app}, // 注册组件
})

/*
但是页面报错：
vue.runtime.esm.js?430a:619 [Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.

你使用的是运行时编译的vue版本，模板编译器不可用的。
除非使用预编译的render函数，或者使用完成版的vue
*/

 import Vue from 'vue'
// 引入vue时没有具体指定vue路径，笼统的引入vue，就是使用的modules文件，而vue的package.json下的modules引入的文件是`vue.runtime.esm.js`，这就解释了为什么报错时提示你的vue是该文件。
```

2.按照提示的解决方法都可以解决
```js
// 1.修改引入的vue文件为完成版的vue，
   import Vue from 'vue/dist/vue'
// 2.使用render函数
    // 创建元素函数
    render(createElement){
        // 传入一个已有的html元素，内容是3333
        return createElement('h1','3333')
    }
    // 可以简写为：
    render( p => p('h1','3333'))
    // render函数完成了注册并塞入容器功能：将App组件放入容器中
```

3.为什么不直接引入完整版vue，而要引入精简版，配合render函数这么麻烦
```js
// 完整版相比精简版多了几十kb的代码量，就是模板解析器的代码量。
// 在开发阶段，完整版的vue是没有问题的，开发时使用模板解析器解析结构。但是开发结束，使用webpack打包编译后，就不需要模板解析器这部分内容了，此时包含在项目里是不合适的。
```

4.引用精简版的vue需要考虑的问题：开发模板如何解析
```js
// 那么使用精简版，生产环境使用webpack打包编译；开发时的template配置项和<template>如何解析呢？
// 使用<template>标签vue使用package.json的devDependencies下的vue-template-compiler插件解析的，开发阶段解析，生产阶段由webpack编译，而该工具包在生产环境里不打包进去。
// vm里的template配置项只能用完整版的模板编译器解析，或者使用render函数生产虚拟dom节点，不能做很复杂的dom编译。
```
 
## 10.修改默认配置（64）

1. 使用`vue inspect > output.js`可以查看到Vue脚手架的默认配置输出为`output.js`。
2. 使用`vue.config.js`可以对脚手架进行自定义配置，与package.json同级。
    - `vue.config.js`与`vue.config`不同
        - 前者是脚手架配置文件
        - 后者是vue构造函数
    - 模块暴露方法与es6模块不同，是`commonjs`模块语法`module.export = { }`
        - 因为该配置文件是给webpack使用的，而webpack是基于node，而node是采用commonjs模块化。
    - vue将`vue.config.js`与默认配置整合，开放的自定义属性若被修改，则覆盖默认相关配置，而不是把默认配置开放，避免用户操作不慎损坏配置文件。
    - 详情见：https://cli.vuejs.org/zh


## 11.ref属性（65）

1. 定义：被用来给元素或子组件注册引用信息（id的替代者）  
    应用在**html元素**上获取的是**真实的dom元素** 
    应用在**组件**上获取的是**组件实例对象**

2. 使用：
    - 打标识：
        - `<h1 ref="title"></h1>`或`<school ref="school"></school>`
    - 获取：
        - `this.$refs.school`


## 12.props属性（66）

1. {{}}能读取所有实例身上的内容
2. props传递的内容都保存在了实例对象身上

3. 小知识：
    - age*1 => 强制转换num
    - age+1 => 强制转换string（-、*、/、%）

4. 父组件传值：
    ```html
    <template>
        <div>
            <Student name="李四" sex="女" :age="18"/>
        </div>
    </template>
    ```

5. 子组件接收：
    ```js
        // 简单声明接收 指明这几个字段从父组件接收，传什么就是什么，没传用了就报错
        props:['name','age','sex'] 

        // 接收的同时对数据进行类型限制
        props:{
            name:String,
            age:Number,
            sex:String
        }

        //接收的同时对数据：进行类型限制+默认值的指定+必要性的限制
        props:{
            name:{
                type:String, //name的类型是字符串
                required:true, //name是必要的
            },
            age:{
                type:Number,
                default:99 //默认值,一般设置了必传就不用设置required了 已经必传了 没传就会报错 没必要设置default
            }
        }
    ```
6. > 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。



## 13.mixin（67）

1. 介绍
- 1、vue中提供了一种混合机制--`mixins`，可以把多个组件共用的配置提取成一个混入对象，用来更高效的实现组件内容的复用
- 2、一个混入对象可以包含任意组件选项。
- 3、当组件使用混入对象时，混入对象的属性和方法都被合并到组件里对应的选项

2. 合并规则
- 1、数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。
- 2、同名钩子函数将合并为一个数组，因此都将被调用。但混入对象的钩子将在组件钩子之前调用。
- 3、值为对象的选项，例如` methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象里的键名冲突时，取组件对象的键值对。

3. 混入方式
- 局部混入
    1. 新建一个mixins.js文件
    ```js
    export const mymixin={
        //采用分别暴露
    }
    ``` 

    2. 引入
    ```js   
    import { mymixin } from '../../mixins/TestFunMixin'
    export default {
        mixins: [mymixin],
    }
    ```
- 全局混入
    - 使用全局混入将影响每个vm和vc实例，谨慎使用！
    - 大多数情况下，只应当应用于自定义选项，就像上面示例一样。（局部混入？）
    - 推荐将其作为插件发布，以避免重复应用混入（全局混入建议作为插件？好处？）。

4. 与Vuex的区别
- Vuex是状态共享管理，所以Vuex中的变量和方法一旦发生改变所有引用该变量或方法的地方都会改变
- 而mixins中的实例不共享且互不影响，相当于每个应用mixin的实例都独立开辟了一片内存空间用于存储该mixin。

5. 与父子组件之间的区别
- 子组件在引用之后相当于在父组件内开辟了一块单独的空间，来根据父组件props过来的值进行相应的操作，但本质上两者还是相对独立。
- 而mixins则是在引入组件之后，将组件内部的内容如data等方法、method等属性与父组件相应内容进行合并。相当于在引入后，父组件的各种属性方法都被扩充了。
- mixin作用：多个组件可以共享数据和方法，在使用mixin的组件中引入后，mixin中的方法和属性也就并入到该组件中，可以直接使用。钩子函数会两个都被调用，mixin中的钩子首先执行。


## 14.插件（68）

功能：用于增强vue  
本质：包含install方法的对象，install的第一个参数是vue构造函数，后续是使用时的传参  
定义插件：  
- 1.创建一个名为plugins.js文件  
    ```js
        export default {
            install(vue){
                //添加全局过滤器、注册全局组件、全局指令、全局混入、实例方法...
            }
        }
    ```
- 2.使用插件
    ```js
        import plugins from './'
        Vue.use(plugins)
    ```


## 15.scope（69）

1. npm 载包
- npm 安装包不知道包版本时默认安装最新版本的包  
- npm i less-loader@7 npm安装包指定了大版本，没指定具体版本时默认安装该大版本下的最新版本
- npm view webpack versions 查看目前webpack更新的所有版本

2. 组件的类名样式都会被汇总到一起，那么不同组件的相同类名就会出现问题  
   不同组件的相同类名会根据`在父组件引入子组件的顺序不同`而受影响  

3. `<style scoped>表示样式私有化`
    scoped原理？
4. `<style lang="less">`表示使用less预处理器，而less需要按照less-loader 
目前最新版的less-loader是为webpack5准备的，所以可能不兼容，需要降低版本安装
5. 详见笔记`vue样式私有化`