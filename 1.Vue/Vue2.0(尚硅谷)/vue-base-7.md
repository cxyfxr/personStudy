## 7.Vue-router (117-133)
### 1.路由基本概念
1. 路由：一组映射关系，多个路由需要路由器管理
2. 前端路由:key:路径,value:组件
3. 安装:
    - `npm i vue-router@3` 
    - vue3成为默认安装版本后，vue-router也成为了默认安装版本，且只能用于vue3；所以安装时需要注意指定版本
4. 应用:
    - `import VueRouter from 'vue-router'`
    - `Vue.use(VueRouter)'`
5. 编写配置项：main.js
6. 实现切换:<router-link active-class="设置高亮样式" to="/path">xxx</router-link >
7. 展示位置:<router-view></router-view>

### 2.路由的基本使用

1.创建router/index.js （需要先创建需要的组件文件）
```js
// 该文件专门用于创建整个应用的路由器
import VueRouter from 'vue-router'
//引入组件
import About from '../components/About'
import Home from '../components/Home'

//创建并暴露一个路由器
export default new VueRouter({
	routes:[
		{
			path:'/about', //key是路径
			component:About //value是组件
		},
		{
			path:'/home',
			component:Home
		}
	]
})
```

2. 引入和使用 main.js
```js
//引入VueRouter
import VueRouter from 'vuex-router'
//引入路由器
import router from './router'
Vue.use(VueRouter)
//创建vm
new Vue({
	el:'#app',
	render: h => h(App),
	router:router
})
// 至此路由器就挂载且配置好了，接下来做页面跳转和设置路由组件位置
```

3. 切换路由和路由展示位置
```js
 // 父组件或app组件
 
 //切换路由：
 <router-link active-class="配置高亮样式类名" to="/about">About</router-link>
// 最终生成的是a标签
// active-class表示被点击或浏览器输入路径时，a标签会应用active-class的样式
// to表示前往的路径 与路由器的path相呼应

 //路由视图展示位置：
 <router-view></router-view>
```


### 3.几个注意点+多级路由（嵌套路由）(119-120)
1. components里放一般组件、page里存放路由页面

2. 路由切换实际是组件被挂载和销毁的过程
3. 每个路由组件都有自己的 `$route` ,保存着自己的路由规则
	```js
	 {
		fullpath:'/home',
		hash:'',
		matched:[{...}],
		name:undefined,
		params:{},
		path:'/home',
		query:{},
		_proto_:object
	}
	```
4. 整个应用只有一个 `$router`,保存着整个路由器规则，可以通过组件的`$router`获取
	```js
	{
		afterhooks:,
		apps:[],
		history:,
		mathcher:,
		mode:'hash',
		options:{routes:[{...}]}
	}
	```
5. 嵌套路由（多级路由）(120)

	页面跳转：
	```html
		<!-- 多级路由的页面跳转的to，要写全路径 -->
		<router-link active-class="" to="/home/message">跳转message</router-link>
	```
	路由配置
	```js
	new VueRouter({
		routes:[
			{
				path:'/about',
				component:About,
				
			},
			{
				path:'/home', // 一级路由加/
				component:Home,
				// 以children来展开子路由的配置，自路由的path不加/，因为vue遍历路由的children时给path加了/
				children:[ 
					{
						path:'message',
						component:Message,
					},
					{
						path:'news',
						component:News,
					}
				]
			}
			
		]
	})
	```

### 4.路由的query参数(121)
1. 跳转路由时携带参数可以通过query和params方式传递，  
这两种方式会在浏览器的路径显示出区别，  
`query`方式是`/home/message?id=001&title=新闻`  
`params`方式是`/home/message/001/新闻  `

2. query方式的传参和接收  
	跳转标签：
	```html
	<!-- 方式一: to是字符串写法 -->
	<router-link :to='`/home/message/detail?id=${m.id}&title=${m.title}`'>跳转</router-link>
	<!-- to加了冒号后：`/home/message/detail?id=${m.id}&title=${m.title}`将会以js表达式的方式来解析 -->
	<!-- 解析时发现是模板字符串，包含变了${m.id}和${m.title} -->

	<!-- 方式二: to是对象写法 -->
	<router-link :to="{
		path:'/home/message/detail',
		query:{ 
			id:m.id,
			title:m.title
		}
	}">跳转</router-link>

	<!-- 方式一和方式二的生成路径最终都是/home/message?id=001&title=新闻 -->
	```

	路由组件接收：  
	```js
	// 路由组件内部
	this.$route.query
	```

### 5.命名路由
1. 就是配置项里定义路由的name属性
2. 作用：可以简化路由的跳转。
```js
routes:[
	{
		name:'guanyu',
		path:'/about',
		component:About
	},{
		name:'index',
		path:'/home',
		component:Home,
		children:[{
			name:'xiaoxi',
			path:'message',
			component:Message,
			children:[
				{
					name:'xiangqing',
					path:'detail',
					component:Detail
				}
			]

		}]
	}
]
```
```html

	<!-- 简化前，需要写完整的路径 -->
	<router-link to="/demo/test/welcome">跳转</router-link>
	
	<!-- 有了名字的路由，在router-link中的to可以使用name来替代path -->
	<router-link :to="{name:'hello'}">跳转</router-link>
	
	<!-- query配合命名路由的对象写法 -->
	<router-link 
	:to="{
		name:'hello',
		query:{
			id:666,
			title:'你好'
		}
	}"
	>跳转</router-link>
```

### 6.路由的params参数
1. 配置路由，声明接收params参数
	```js
	{
		path:'/home',
		component:Home,
		children:[
			{
				path:'news',
				component:News
			},
			{
				component:Message,
				children:[
					{
						name:'xiangqing',
						//使用占位符声明接收params参数
						path:'detail/:id/:title', 
						component:Detail
					}
				]
			}
		]
	}
	```
2. 传递参数
   ```html
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <router-link :to="/home/message/detail/666/你好">跳转</router-link>
   				
   <!-- 跳转并携带params参数，to的对象写法 -->
   <router-link 
   	:to="{
   		name:'xiangqing',
   		params:{
   		   id:666,
               title:'你好'
   		}
   	}"
   >跳转</router-link>
   ```

   > 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

3. 接收参数：

   ```js
   $route.params.id
   $route.params.title
   ```



### 7.路由的props配置参数
​	作用：让路由组件更方便的收到参数

```js
{
	name:'xiangqing',
	path:'detail/:id',
	component:Detail,

	//第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
	// props:{a:900}

	//第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
	// props:true //path:'detail/:id',
	
	//第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
	// path:'detail',
	props(route){
		return {
			id:route.query.id,
			title:route.query.title
		}
	},
	//<=>
	props({query:{id,title}}){
		return {
			id,title
		}
	}
}
// 路由组件里可以使用props:[id,title]使用id和title的路由传参
```

### 8.router-link的replace属性(125)

1. 浏览器的前进后退都是依赖于浏览器的历史记录的。
2. 浏览器保存历史记录的解构是栈，栈的特点是先入后出，先入的在最底下，后入的在上面
3. 浏览器历史记录的添加有两种模式：push压栈和replace替换。push是不破坏原纪录，追加新的历史记录在上面；
	replace是添加一条新记录的同时，替换掉当前记录。
4. 如何开启replace模式，  
`<router-link replace to="></router-link>`  
`<router-link>默认是push模式`

### 9.编程式路由导航(126)
1. 是一种全新的路由跳转方式-编程式
2. push和replace是VueRouter(new VueRouter的实例)的原型方法。第一对实例原型方法

```js
// 以push模式添加记录，跳转name为guanyu的路由组件，传参为query对象
this.$router.push({
	name:'guanyu',
	query:{
		id:'',
		title:''
	}
})
// 以replace模式添加记录且替换当前记录的方式，跳转name为guanyu的路由组件，传参为query对象
this.$router.replace({
	name:'guanyu',
	query:{
		id:'',
		title:''
	}
})

this.$router.back()  // 浏览器记录回退一步
this.$router.forward() // 浏览器记录前进一步
this.$router.go(n)   // 若n为正数，则前进n步，若为负数，后退n步
```

### 10.缓存路由组件(127)
1. `<keep-alive/>`标签让不展示的路由组件保持挂载，不被销毁
2. 在想要缓存的路由组件上层展示<router-view>的地方用<keep-alive>包裹
3. `include` 表示想要缓存/保持活跃的`路由组件名`，注意`不是路由名字`。  
如果`不写该字段表示经过这里的所有路由都将被缓存`，即不被销毁。    
`include="guanyu"`，缓存一个组件  
`:include="[]"`，缓存多个组件

```html
<keep-alive include="guanyu">
	<router-link></router-link>
</keep-alive>

<keep-alive :include="['guanyu','shouye']">
	<router-link></router-link>
</keep-alive>
```

### 11.两个新的生命周期钩子(128)

1. 三个没在生命周期里出现的钩子，路由钩子占了两个，还有一个特殊的是nextTick()
2. 路由钩子用来捕获路由组件的激活状态  
	- `activated(){}`表示路由组件被激活了
	- `deactivated(){}`表示路由组件失活了  

可以用来实现被keep-alive包裹的路由组件，在切换走时清除定时器，切换回来时创建定时器等操作。

### 12.路由守卫(129)

1. 前端路由的工作原理：
点击导航菜单，引起浏览器地址的变化(不会发起请求)，路由器监测到进行规则匹配，在相应的位置上展示路由组件。  
2. 作用：对路由进行权限控制
3. 分类：全局守卫、独享守卫、组件内守卫
	- `router.beforeEach((to,from,next)=>{})` 全局守卫前置
	- `router.afterEach((to,from)=>{})` 全局守卫后置
	- `beforeEnter(to,from,next){}) `独享守卫
	- `beforeRouteEnter(to,from,next){})` 组件内守卫
	- `beforeRouteLeave(to,from,next){})` 组件内守卫
	前三个都在路由配置文件里写，后面在组件里写		

4. 全局守卫:

	```js
	// 创建路由实例
	const router = new Router({
		routes:[
			{
				name:'',
				path:'',
				component:'',
				// meta对象可以用来创建自定义属性，在路由跳转时用to来获取，用来进行某些鉴权判断
				meta:{
					isAuth:'',
					title:''
				}
			}
		]
	})
	// 全局前置路由守卫
	// 所有在router里配置过的路由，在初始化时、路由跳转之前，都会进入该方法
	// next放行后，才会修改页面url
	router.beforeEach((to,from,next)=>{
		// to:去哪,from:来自哪,next():放行
		if(to.meta.isAuth){ // 判断当前路由是否需要进行权限控制

			if(localStorage.getItem('school') === 'atguigu'){ // 权限控制的具体规则
				next() //放行
			}else{
				alert('暂无权限查看')
				// next({name:'guanyu'})
			}

		}else{
			next() //放行
		}
	})

	//全局后置守卫：
	// 所有在router里配置过的路由，在初始化时、路由跳转之后，都会进入该方法

	router.afterEach((to,from)=>{
		if(to.meta.title){ 
			document.title = to.meta.title || 'title'
			 // 修改网页的title或者面包屑等，切换完了说明过权限校验了
		}else{
			document.title = 'vue_test'
		}
	})
 

	export default router
	```

5. 独享守卫:
	独享守卫只有前置，没有后置。beforeEnter(to,from,next)
	可以和全局前置、后置一起合用
   ```js
   const router = new Router({
		routes:[
			{
				name:'',
				path:'',
				component:'',
				// meta对象可以用来创建自定义属性，在路由跳转时用to来获取，用来进行某些鉴权判断
				meta:{
					isAuth:'',
					title:''
				}，
				children:[
					{
						name:'',
						path:'',
						component:'',
						beforeEnter(to,from,next){
							if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
								if(localStorage.getItem('school') === 'atguigu'){
									next()
								}else{
									alert('暂无权限查看')
									// next({name:'guanyu'})
								}
							}else{
								next()
							}
						}
					}
				]
			}
		]
	})
   
   ```


6. 组件内路由守卫:
```js
	//通过路由规则，进入该组件时被调用
	beforeRouteEnter (to, from, next) {
		if(to.meta.isAuth){ //判断是否需要鉴权
			if(localStorage.getItem('school')==='atguigu'){
				next()
			}else{
				alert('学校名不对，无权限查看！')
			}
		}else{
			next()
		}
	},

	//通过路由规则，离开该组件时被调用
	beforeRouteLeave (to, from, next) {
		next()
	}

```


### 15.history模式与hash模式

**路由器的两种工作模式:**

1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。
2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。
3. hash模式：
   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
   3. 兼容性较好。
4. history模式：
   1. 地址干净，美观 。
   2. 兼容性和hash模式相比略差。
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

5. 设置路由器工作模式，默认是hash
	```js
	const router =  new VueRouter({
		mode:'history', //设置路由器工作模式，默认是hash
		routes:[
			{
				name:'guanyu',
				path:'/about',
				component:About,
				meta:{isAuth:true,title:'关于'}
			},
		]
	})
	```
6. hash和history的区别  
- 体现在开发完成后打包部署到服务器时： 
	- history：路由跳转正常跳转到路由页面，但是一旦刷新页面，就会报错404找不到页面。  

	- hash：路由跳转正常跳转到路由页面，刷新页面不会404，因为hash值从#到最后都不会传给服务器

浏览器的地址栏实际是浏览器历史记录，指针指向的记录就是地址栏当前的路径。    
点击路由跳转实际是修改（添加了）历史记录，指针指向最新一条，即当前地址栏的路径，但是并没有引起接口请求；但是当刷新页面时，是发起请求的，此时historty的地址就会发送到服务器，但是服务器却找不到资源而报错404

## 8.element-ui (133-135)

1. 完整引入

```js
// 引入ElementUI组件库
import ElementUI from 'element-ui';
// 引入ElementUI全部样式
import 'element-ui/lib/theme-chalk/index.css';

//关闭Vue的生产提示
Vue.config.productionTip = false

//应用ElementUI
Vue.use(ElementUI);
```

2. 按需引入

- 借助 babel-plugin-component，我们可以只引入需要的组件，以达到减小项目体积的目的。

- 安装 babel-plugin-component：
	- npm install babel-plugin-compo nent -D
- 在.babelrc.js里配置如下:  
[vue-cli的版本应该不受vue版本的制约，vue-cli版本5.0.4可以安装vue2的版本，vue-cli5的项目babel配置文件改名为babel.config.js]
	```js
	module.exports = {
	presets: [
		'@vue/cli-plugin-babel/preset',
			["@babel/preset-env", { "modules": false }],
	],
		plugins:[
		[
		"component",
		{
			"libraryName": "element-ui",
			"styleLibraryName": "theme-chalk"
		}
		]
	]
	}

	```
- main.js配置
	```js
	import { Button,DatePicker } from 'element-ui';
	//关闭Vue的生产提示
	Vue.config.productionTip = false

	Vue.component(Button.name, Button); // el-button 使用<el-button>
	Vue.component('atguigu-date-picker', DatePicker); // 使用atguigu-date-picker<el-button>

	```