## 1.动画相关效果 (91-95)
略
- 1. 动画效果
- 2. 过渡效果
- 3. 多个元素过渡
- 4. 集成第三方动画
- 5. 总结过渡与动画



## 2.配置代理 (96-97)
发送ajax的方式：
1. xhr：XMLHttpRequest()、xhr.send()
2. jquery：封装了xhr；封住了80%的Dom操作、20%的周边包括封装xhr；
3. axios：封装了xhr；体积小
4. fetch：支持prpmise风格、与xhr平级；缺点是返回数据包了两层，致命的是兼容不行，Ie不支持

`不建议在vue等框架里使用jquery，原因是框架的目的是减少自己操作dom节点，  
而jquery的核心就是封装了dom操作，在设计理念上相背离`  

axios最简单的一个使用
```js
npm i axios
import axios from 'axios' //页面
// 发起请求
axios.get('http://localhost:8080/student').then(
    response => {
        console.log('请求成功了',response.data)
    },
    error => {
        console.log('请求失败了',error.message)
    }
)
```

跨域是指，浏览器向服务器发出了请求，服务器收到并返回了响应，被浏览器拦截的过程
解决跨域的方法有：
1. cors
    是真正意义上的解决跨域，服务器在返回给客户端时携带了响应头的字段，浏览器识别并放行
2. JsonP 
    借助了script srv属性，引入外部资源不受同源策略；  
    前端设置特殊写法，后端也要配合，且只能解决get跨域问题，几乎不用
3. 代理服务器  
    代理服务器是指在浏览器和服务器之间启动一个代理服务器，域名端口号和浏览器的请求的一模一样，然后接受转发给服务器，实现通信并返回给浏览器。  
    服务器之间传数据用http，不需要ajax，ajax是前端浏览器才用的。也就是说浏览器的同源策略对服务器不起作用。  
    一般有nginx、vue-cli两种方式
    前端采用vue-cli在解决开发过程中的跨域问题
    
vue-cli配置跨域代理在vue.config.js里

方式一：
```js
devServer:{
    //代服的目标地址,真实的服务器地址，只写到端口号
    proxy:'http://localhost:5000' 
//本身会启动一个与前端地址相同的代理服务器
//所以在接口请求里请求"http://localhost:8080/student" 请求的是与前端地址一样的域名和端口号的代理服务器的接口
//代理服务器会将到请求的前缀转成proxy的5000+请求里的/student发送到服务器
//若请求的数据在8080的public下本身就有，则不走代理服务器，即优先匹配前端资源
//public下的文件在都能在8080下直接找到

//这种方式简单，但不能配置多个代理，且不能控制是否走代理。只有当请求的东西在public里不存在时才走代理
}
```

方式二：
```js
devServer:{
    proxy:{
        //请求前缀，表示以atguigu开头的请求都走此项代理
        '/atguigu': {
                //代理服务器的目标地址，只写到端口号
                target: 'http://localhost:5000', 
				pathRewrite:{'^/atguigu':''}, //匹配以atguigu开头的请求，重写为''
                // ws: true, //用于支持websocket
                // changeOrigin: true //用于控制请求头中的host值
        // 关于pathRewrite
        // 前端请求 http://localhost:8080/atguigu/student，代理服务器会转成 http://localhost:5000/atguigu/student
        // 配置了pathRewrite，才能转成 http://localhost:5000/student

        // 关于changeOrigin
        // changeOrigin=true，代理服务器发送的请求头中的host是localhost:5000,撒谎，修改为服务器的端口号
        // changeOrigin=false，代理服务器发送的请求头中的host是localhost:8000，说实话，为自己的端口号
        // vue默认为true，react默认为false
      },
    }

}
```

## 3.github案例 (98-100)
引入第三方css的两种方式
1. 放在assets下，一般要在main.js引入，就要用import引入，import对引入的文件校验很严格，里面引入的文件不存在会报错
2. 放在public下，在index.html中引入，因为所有页面最终在编译完成后都会在index.html页面呈现，在此处引入的样式都会生效  
    `<link rel="stylesheet" href="<%= BASE_URL %>xx.css"> `  
    `<%= BASE_URL %>` 表示 `public/`  
    `<%= BASE_URL %>xx.css` 表示` public/xx.css`

1. 拆分静态页面：search、list
2. 考虑数据流向和传递方式：search发起请求后数据传递给list，还是search点击后传给list，list发起请求
3. 页面数据对不同情况的展示：
    - 1.初次展示 => isInit
    - 2.点击search后 => isLoading 
    - 3.拿到数据 => data.length
    - 4.error => errMsg

4. `this.obj= { ...this.obj,...data}`
    表示先解析原对象，在解析data对象，若两对象属性重复，以data为准，若data里属性不全，取obj

## 4.vue-resource (101)
1. 定义：vue封装的一个基于xhr的ajax插件库。vue后来交给第三方维护，vue现在不维护，基本不使用
2. 使用流程：
    - 1.安装 `npm i vue-resource`
    - 2.引入 
    ```js
        // main.js
        import vueResource from 'vue-resource'
        Vue.use(vueResource) // 至此，vm、vc身上多了一个$http的api
    ```
- 3.使用
    ```js
        // 组件内
        this.$http.get('http://192.168.1.22:8080/student').then(
            response => {},
            error => {},
        )
        // 与axios语法一样，都支持promise风格
    ```
    

## 5.插槽 (102-104)
### 1. 默认插槽
```html
<!-- 子组件：category -->
<div class="category">
    <h3>{{title}}分类</h3>
    <!-- 定义一个插槽 等着父组件里进行填充 -->
    <slot>我是一些默认值，当使用者没有传递具体结构时，我会出现</slot>
</div>

<!-- 父组件 -->
<!-- 传参在使用子组件标签时传递<Category> -->
<Category title="游戏"> 
    <!-- 传递自定义结构 -->
    <ul>
        <li v-for="(g,index) in games" :key="index">{{g}}</li>
    </ul>
</Category>
<!-- 
    插槽的自定义结构最终会塞到子组件的插槽位置上，
    但是结构是在当前父组件中完成解析后才塞入的，
    所以想要控制自定义结构的样式可以在当前组件里设置，即解析完当前父组件，带着样式传给子组件。
    样式写在子组件里，就是只传结构，在子组件里加载样式  -->
<!-- 插槽的自定义结构的数据一般维护在父组件里 -->
```

###  2. 具名插槽
1. 默认插槽只适用于1个插槽的情况  
如果定义了N个不具名插槽，那么在父组件里的自定义结构会生成N份结构，因为vue不知道具体往哪个slot塞入，索性每个slot都塞入。

2. 具名插槽的使用
```html
<!-- 子组件 -->
<div class="category">
    <h3>{{title}}分类</h3>
    <!-- 定义一个插槽 等父组件进行填充 -->
    <!-- 指定插槽名字 -->
    <slot name="center">当插槽对应的自定义模板结构未定义时会显示</slot>
    <slot name="footer">当插槽对应的自定义模板结构未定义时会显示</slot>
</div>
<!-- 父组件 -->
<div class="container">
    <Category title="游戏" >
        <ul slot="center">
            <li v-for="(g,index) in games" :key="index">{{g}}</li>
        </ul>
        <!-- 一般为了结构合理或调试样式，会嵌套一层结构，弊端是多了一层div结构，
        可以使用如下template标签替换div，作为最外层嵌套，template本身不渲染任何节点
        具名插槽本身可以定义多个同名插槽结构，最后会汇总到指定名字的插槽内
         -->
        <div class="foot" slot="footer">
            <a href="http://www.atguigu.com">单机游戏</a>
            <a href="http://www.atguigu.com">网络游戏</a>
        </div>
    </Category>

    <Category title="电影">
        <video slot="center" controls src="http://c.mp4"></video>
        <!-- 
            template本身不渲染任何节点，
            vue2.6之后新增api v-slot:slotName 只能搭配template使用 
            或者这种写法： <template slot="footer">
        -->
        <template v-slot:footer>
            <div class="foot">
                <a href="http://www.atguigu.com">经典</a>
                <a href="http://www.atguigu.com">热门</a>
                <a href="http://www.atguigu.com">推荐</a>
            </div>
            <h4>欢迎前来观影</h4>
        </template>
    </Category>
</div>
```

###  3. 作用域插槽
当数据不维护在父组件即传递自定义结构的组件里，而在定义slot的子组件里时，如何将子组件的数据传递给外面的自定义结构然后将其处理呢？作用域插槽就派上用场了
```html
<!-- 子组件 -->
<div class="category">
    <h3>{{title}}分类</h3>
    <!-- 定义games字段传递给自定义结构，字段值为game数组 -->
    <!-- 只有一个插槽所以不用指定插槽名字 -->
    <slot :games="game" msg="hello">我是默认内容</slot>
</div>

data(){
    return {
        game:[{},{},{}]
    }
}

<!-- 父组件 -->
<div class="container">
    <Category title="游戏">
        <!-- 作用域的接收外侧必须用template标签包裹，然后scope接收-->
        <!-- scope接收一个插槽内传过来的所有数据的对象，返回对象 -->
        <template scope="atguigu">
            <ul>
                <li v-for="(g,index) in atguigu.games" :key="index">{{g}}</li>
            </ul>
        </template>
    </Category>

    <Category title="游戏">
        <!-- 对象字段的简写写法，es6解构 -->
        <!-- {games:games}=>{games} -->
        <template scope="{games}">
            <ol>
                <li style="color:red" v-for="(g,index) in games" 
                :key="index">{{g}}</li>
            </ol>
        </template>
    </Category>

    <Category title="游戏">
        <!-- scope的另一个新的语法：<template slot-scope="atguigu"> -->
        <template slot-scope="{games}">
            <h4 v-for="(g,index) in games" :key="index">{{g}}</h4>
        </template>
    </Category>

</div>
```

## 6.Vuex (105-116)  

1. **定义**  
对Vue中多个组件共享数据进行集中管理(读写)的插件。适用于任何组件间通信。  
当多个组件共享数据时不太适用于全局总线传递数据，它适用于兄弟之间。

2. **什么时候用vuex**
    - 多个组件依赖于同一状态
    - 来自不同组件的行为需要变更同一状态

3. **vue版本求和案例**  
    - +： this.sum += this.n (n:下拉选择的数字，v-model.Number)  
    - -： this.sum -= this.name

4. **理解vuex：**
    - actions 动作 
        - 可以调用ajax、处理逻辑
        - commit提交到mutations
        - 返回对象
    - mutations 加工维护 
        - 可以处理state中的数据
        - 没有异步或逻辑可以直接用commit提交到mutations，mutations处理state的数据
        - devtools只与mutations会话
        - 返回对象
    - state 状态数据 
        - 返回对象    

        store管理以上三项  

        闭环：
        vc组件(dispatch(+,2)) -> actions(commit(+,2)) ->   
        mutations(处理state数据) -> state(render) -> vc组件

5. **搭建vuex环境🌟**
    - 安装
        - npm i vuex
    - 引入
        - import Vuex from 'vuex'
        - Vue.use(Vuex) 
            - 到这一步vc、vm身上就多了一个$store的属性，  
            同use(vuesource)多了$http一样
    - 实例化store
    -  `const store = new Vuex.Store({actions,mutations,state})`
        - 返回并导出 `export default store `
    - 将store挂载到$store身上
        - `new Vue({.., store:store, ..})`或es6简写`new Vue({ store })` 

    具体使用例子：
    ```js
    // 1.安装
    // vue2中，用vuex3
    // vue3中，用vuex4
    // 而2022年2月，vue3成为默认版本，vuex4则配合vue3成为了默认版本，所以安装时要注意版本号
    npm i vuex@3 // vuex3的最新版本

    // 2.引入 main.js
    import Vuex from 'vuex'
    Vue.use(Vuex)
    // 引入后就创建vm(new Vue)就可以传入store项了,即vmvc就有了$store了

    // 3.实例化store
    // 创建store文件夹，新建index.js，该文件是用于创建vuex中最核心的store
    import Vuex form 'vuex'

    const actions = {}
    const mutations = {}
    const state = {}

    // 默认导出
    export default new Vuex.Store({
        actions,mutations,state //es6简写
    })

    // 4. main.js
    import Vuex from 'vuex'
    import store from './store' // 引入store文件下的index文件的话可以简写，省略index文件
    Vue.use(Vuex) 

    //创建vm
    new Vue({
        // 避免干扰，注释掉
        // el:'#app',
        // render: h => h(App),
        // beforeCreate() {
        // 	Vue.prototype.$bus = this
        // }
        store // => store:store
    })
    // 理论上这样就OK了，引入并使用了vuex，也创建了store，挂载到vm上
    // 实际报错，Vue.use(Vuex) 要在实例化store之前

    // 关于import模块引入
    // 代码执行时会将所有import模块提至最前面，执行一遍，再去执行其他代码

    // 而在main.js中，调整顺序也没有用，始终会先执行import的实例化store，再执行Vue.use(Vuex)
    // 所以放在哪里能控制顺序呢，放在store/index.js里
    // 引入vuex，使用vuex => Vue.use(Vuex) , 然后实例化store，导出store

    // 回到main.js  
    // 去掉引入的vuex和使用vuex 因为不需要了
    // 引入 store
    // new Vue中挂载store
    ```
    最终版
    ```js
    //  store/index.js
    import Vue from 'vue'
    import Vuex from 'vuex'
    Vue.use(Vuex)

    const actions = {}
    const mutations = {}
    const state = {}

    // 默认导出
    export default new Vuex.Store({
        actions,mutations,state // es6简写
    })

    // main.js
    import store from './store'
    // 创建vm
    new Vue({
        el:'#app',
        render: h => h(App),
        store,
        beforeCreate() {
            Vue.prototype.$bus = this
        }
    })
    ```

6. **求和案例-vuex版（数据处理流程）**
    - `actions`的方法有两个参数，`context:ministore，有commit、dispatch、state等方法`。`value:接收传递过来的参数`；  
    - `mutation`里的方法同样有两个参数，`state和value`。一般用来直接处理state数据而不进行逻辑处理  
    - 注意：
        - `dispatch`是与`actions`会话, actions的方法都是小写jia, dispatch调用也应该是小写方法 => `dispatch('jia',2)`
        - `commit`是与`mutations`会话, mutations方法都是大写JIA, commit提交也应该是大写方法 => `commit('JIA',value)`
    - 读取state中属性：`this.$store.state.xxx`
    **修改数据处理流程：**  
    1. `vc组件`里调用`this.$store.dispatch('jia',2)`,提交到actions  
    2. `actions`里对应的`jia方法被调用`，`完成处理逻辑后`，提交到mutations: `context.commit('JIA',value)`
    3. `mutations`里对应的`JIA方法被调用`直接修改state的数据  
    4. 修改了state数据后，会重新渲染，引用到store.state中数据的组件都会更新    
    5. 若组件内明确不需要逻辑处理，直接操作数据可以直接commit到mutations:`this.$store.commit('JIA',2)`  
    6. 当actions中方法处理逻辑太复杂时，可以拆分功能到其他方法，使用context.dispatch()调取其他actions中的方法

        demo
        ```js
        // 组件
        methods: {
            increment(){
                this.$store.commit('JIA',this.n)//直接到mutations中处理数据
            },
            incrementWait(){
                this.$store.dispatch('jiaWait',this.n)//去actions中处理逻辑，再由actions中提交到mutations
            },
        },
        // store/index.js
        const actions = {
            jiaWait(context,value){
                console.log('actions中的jiaWait被调用了')
                setTimeout(()=>{
                    context.commit('JIA',value)
                },500)
            }
        }
        //准备mutations——用于操作数据（state）
        const mutations = {
            JIA(state,value){
                console.log('mutations中的JIA被调用了')
                state.sum += value
            }
        }
        //准备state——用于存储数据
        const state = {
            sum:0 //当前的和
        }

        //创建并导出store
        export default new Vuex.Store({
            actions,
            mutations,
            state,
        })
        ```

7. **vuex开发者工具**  
    ```js
    1. 就是vue开发者工具，第二项图标hover切换到vuex  
    2. 从左到右是组件关系、vuex、自定义事件  
    3. vuex的部分上方是选择区域、下面是数据展示区域  
    选择区域：  
    BASE state：默认state的数据
    JIA  => devotool只收集mutations中的方法操作。只与mutations会话
    JIA：绿色背景框停留在最后一次的mutations方法操作上，会关联state数据的变化。
    每行方法操作都有3个小图标，都会引起当前展示数据的变化。
    第三个图标：时光穿梭到当前方法上，页面呈现当前方法操作对应的数据。
    第二个图标：删除方法操作，并且依赖当前方法数据的后续方法操作也都会被清除，一般倒着删除，从中间删除后面的就都没了
    第一个图标：合并在此操作之前的所以操作，被合并的最后一次操作的数据作为新的Base state数据结果

    4. 右上角图标：  
    下载图标：合并所有，base state会被改变为最后一次的结果
    清空图标-禁止符：清空所有操作，但不会清掉因合并而被修改了的base state结果
    实时记录-实心圆：记录操作  
    导入、导出
    ```

8. **getter配置项**  
    与state、actions、mutations配置项同级  
    当store中state的数据需要加工且重复使用时，可以使用getters  
    在store/index中追加getters配置项

    ```js
    const getters:{
        bigSum(state){
        return state.sum * 10
        }
    }
    export default new Vuex.Store({
        state,actions,mutations,getters
    })
    ```

9. **mapState和mapGetters**
    - 1.定义
        - 映射`state`的数据到组件的`computed`，生成`计算属性`的get函数
        - 映射`getters`的数据到组件的`computed`，生成`计算属性`的get函数
    - 2.引入
        - `import { mapState, mapGetters } from 'vuex'`
    - 3.写法
        - 对象写法：`...mapState({he:'sum',xuexiao:'school'})`
        - 数组写法：`...mapState(['jia','jian'])`
        - 放在computed里直接使用
    - 4.mapGetters同mapState一模一样

    ```js
    // 使用
    computed:{
      // mapstate接收对象，key:输出的计算属性名，value:state寻找的属性
      // (去state里寻找value属性，返回给计算属性的get函数，起名为key)
      // 返回一个对象，包含若干个计算属性get方法
      // 解构可以分解开若干个计算属性方法

      // he <=> 'he', 'sum'必须是字符串，不能是变量
      ...mapState({he:'sum',xuexiao:'school'}) // 对象写法
      // <=>
      he(){
        return  this.$store.state.sum
      },
      xuexiao(){
        return  this.$store.state.school
      },
      // 上述写法为对象写法，当对象的key和value同名时，即以state的值作为计算属性名，可以采用数组写法
      ...mapState({sum:'sum',school:'school'}) // 对象写法
      ...mapState({'sum','school'}) // 数组写法
      // <=>
      sum(){
        return  this.$store.state.sum
      },
      school(){
        return  this.$store.state.school
      }

      // mapGetters
      // 借助mapGetters生成计算属性，从getters中读取数据。（对象写法）
      ...mapGetters({bigSum:'bigSum'})
      // 借助mapGetters生成计算属性，从getters中读取数据。（数组写法）
      ...mapGetters(['bigSum'])
      // <=>
      bigSum(){
        return  this.$store.getters.bigSum
      }
    }

    // 解构
    a = 1
    let obj = {
        a:a
    }
    // <=>
    let obj = {a} // 注意value的a是个变量才可以解构
    ```

10. **mapActions和mapMutations**
    - 1.定义
        - 映射`actions`的方法为当前组件的方法，写在`methods`里
        - 映射`mutations`的方法为当前组件的方法，写`在methods`里
    - 2.引入
        - `import { mapActions, mapMutations } from 'vuex'`
    - 3.写法
        - 对象写法：`...mapActions({he:'sum',xuexiao:'school'})`
        - 数组写法：`...mapActions(['jia','jian'])`
        - 对象写法：`...mapMutations({jia:'JIA',jian:'JIAN'})`
        - 数组写法：`...mapMutations(['JIAN'])`不推荐
        - 放在methods里直接使用

    ```js
    methods:{
        // mapActions
        ...mapActions({increment:'jia'})  // 对象写法
        // <=>
        increment(value){
            this.$store.dispatch('jia',value)
            // actions的方法是通过dispatch分发的，actions的方法是小写
        },
        ...mapActions(['jia'])  // 数组写法


        // mapMutations
        ...mapMutations({deincrement:'JIAN'}) // 对象写法
        // <=>
        deincrement(value){
            this.$store.commit('JIAN',value)
            // mutations的方法是通过commit提交的，mutations的方法是大写
        },
        ...mapMutations(['JIAN'])  //数组写法,会影响mthods方法命名，一般不用


      // 使用mapActions、mapMutations时，若需要传递参数，
      // 需要在模板里绑定事件时传递好参数，否则参数是事件对象
    }
    ```
11. **多数组共享数据略（114）**
略

12. **vuex模块化和命名空间**
```html
之前学到的
const store = new Vuex.strore({actions,mutations,state,getters})
一个store里有一组完整的store数据。
this.$store.state.sum
this.$store.getters.bigSum
this.$store.dispatch('jia',2)
this.$store.commit('JIA',2)
属性和方法都是直接从store读取的
<<script>
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
const actions = {}
const mutations = {}
const state = {}
const getters = {}
export default new Vuex.Store({
    actions,mutations,state,getters
})
</script>
```
随着项目模块的增多，将数据分类模块化会更清晰好维护

```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
// countOptions模块数据:每个模块都有一组完整的store数据
const countOptions = {
    namespaced:true, //开启命名空间，开启后map函数可以使用模块名查找下面的属性或方法: ...mapstate('countOptions',{sum:'sum})
    actions:{ 
        jia(context,value){}
    },
    mutations:{
        JIAN(state,value){
           // state:模块化内部的state表示模块内的数据，可以直接使用 
           return state.sum * 10
        }
    },
    state:{sum:'888'},
    gatters:{
        bigSum(state){
           // state:模块化内部的state表示模块内的数据，可以直接使用 
        }
    }
}
// personOptions模块数据
const personOptions = {
    namespaced:true,
    actions:{},
    mutations:{},
    state:{},
    gatters:{}
}
export default new Vuex.Store({
    // actions,mutations,state,getters
    modules:{
        countOptions:countOptions,
        personOptions:personOptions  
        // <=>
        // countOptions,personOptions
    }
})
模块化后vuex后，引用的store数据方法有所变化。
读取state数据:
    this.$store.state.countOptions.sum // 自己调用
    ...mapState('countOptions',{sum:'sum', ...}) // 要开启命名空间，寻找countOptions下的state的sum属性，生成并返回对象，对象包含sum计算属性
    ...mapState('countOptions',['sum']) // 要开启命名空间
    ...mapState({countOptions:'countOptions', personOptions:'personOptions'}) // 模板使用：countOptions.sum
    ...mapState(['countOptions','countOptions']) // 模板使用：countOptions.sum

读取getters数据:
    this.$store.getters['countOptions/bigSum'] // 自己调用
    ...mapGetters('countOptions',{bigSum:'bigSum', ...}) // 要开启命名空间，寻找countOptions下的getters的bigSum属性，生成并返回对象，对象包含bigSum计算属性
    ...mapGetters('countOptions',['bigSum']) // 要开启命名空间

读取actions数据:
    this.$store.dispatch['countOptions/jia',params] // 自己调用
    ...mapActions('countOptions',{jia:'jia'}) // 要开启命名空间，寻找countOptions下的actions的jia方法，生成并返回对象，对象包含jia方法
    ...mapActions('countOptions',['jia']) // 要开启命名空间

读取mutations数据:
    this.$store.commit['countOptions/JIA',params]
    ...mapMutations('countOptions',{jia:'JIAN'}) // 要开启命名空间，寻找countOptions下的actions的jia方法，生成并返回对象，对象包含jia方法
    ...mapMutations('countOptions',['JIAN']) // 要开启命名空间

注意：
模块内的getters、mutations的方法state数据是模块内部的数据；
devtools只与mutations进行会话
```


