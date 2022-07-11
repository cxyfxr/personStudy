### 1.收集表单数据
ajax：可以在不刷新页面的情况下获取服务器数据
```html
收集表单数据：
    若：<input type="text"/>，则v-model收集的是value值，用户输入的就是value值。
    若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。
    若：<input type="checkbox"/>
        1.没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
        2.配置input的value属性:
            (1)v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
            (2)v-model的初始值是数组，那么收集的的就是value组成的数组
    备注：v-model的三个修饰符：
        lazy：失去焦点再收集数据
        number：输入字符串转为有效的数字
        trim：输入首尾空格过滤

<label for="demo">账号</label> 
<!-- for关联input的id，作用是点击账号名字时，input获取焦点 -->

<!-- form的基础写法： -->
<!-- 表单提交的默认行为是刷新页面，可以在提交事件里阻止 -->
<form @submit.prevent="">
    <!-- 普通文本 .trim去掉前后的空格 -->
    <input type="text" v-model.trim="userInfo.account">
    <!-- 密码形式的文本 -->
    <input type="password" v-model="userInfo.password">
    <!-- 
        type=number是原生写法,限制输入的是数字，但data里绑定的值依然是字符串，vue认为文本框的内容就是字符串
        .number是vue修饰符,不限制输入，但输入的不是数字，数据就不更新了，二者配合使用 
    -->
    <input type="number" v-model.number="userInfo.age">
    <!-- 单选 v-model绑定的一般都是value，radio默认没有value，需配置 -->
    <input type="radio" name="sex" v-model="userInfo.sex" value="male">男
    <input type="radio" name="sex" v-model="userInfo.sex" value="female">女
    <!-- 复选 v-model对checkbox的处理是：
        没有配置value取checked值；
        若配置了value：
            v-model绑定的是数组，则取以value组成的数组
            v-model绑定的字符串，则取checked布尔值
    -->
    <input type="checkbox" v-model="userInfo.hobby" value="study">学习
    <input type="checkbox" v-model="userInfo.hobby" value="game">打游戏
    <!-- 下拉 项里有value值，v-model绑定value，若多选，绑定以value组成的数组 -->
    <select v-model="userInfo.city">
        <option value="">请选择校区</option>
        <option value="beijing">北京</option>
        <option value="shanghai">上海</option>
    </select>
    <!-- 多行 v-mode默认数据发生改变就变化，多行里不能这样，可以.lazy修饰符控制在多行里失去焦点时收集数据 -->
    <textarea v-model.lazy="userInfo.other"></textarea>
    <!-- 协议前的复选框：一般是单选，所以可以不配置value，v-model就取checked -->
    <input type="checkbox" v-model="userInfo.agree">阅读并接受<a href="http://www.atguigu.com">《用户协议》</a>
    <!-- 表单里的button 默认触发表单的提交，而表单提交的默认行为是刷新页面，可以在button里阻止默认行为，也可在form里阻止 -->
    <button>提交</button>
</form>

```

### 2.过滤器

```html
过滤器：
    定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理），如时间。
    语法：
        1.注册过滤器：Vue.filter(name,callback) 或 new Vue{filters:{}}
        2.使用过滤器：{{ 初始值 | 过滤器名}}  或  v-bind:属性 = "xxx | 过滤器名"
    备注：
        1.过滤器也可以接收额外参数、多个过滤器也可以串联，按顺序过滤
        2.并没有改变原本的数据, 是产生新的对应的数据
```
```html
<h3>现在是：{{time | timeFormater}}</h3>
<!-- 过滤器实现（传参） -->
<h3>现在是：{{time | timeFormater('YYYY_MM_DD') | mySlice}}</h3>
<h3 :x="msg | mySlice">哈哈哈</h3>
<script type="text/javascript">
   
    //全局过滤器 value是初始值，可以再设传参
    Vue.filter('mySlice',function(value){
        return value.slice(0,4)
    })
    
    new Vue({
        el:'#root',
        data:{
            time:1621561377603, //时间戳
            msg:'你好，www'
        },
        
        //局部过滤器
        filters:{
            // 过滤器默认有一个参数，就是初始值，第二个是输出格式或者其他
            timeFormater(value,str='YYYY年MM月DD日 HH:mm:ss'){
                // console.log('@',value)
                return dayjs(value).format(str)
            }
        }
    })

    new Vue({
        el:'#root2',
        data:{
            msg:'hello,atguigu!'
        }
    })
</script>
```

### 3.我们学过的指令：
- v-bind	: 单向绑定解析表达式, 可简写为 :xxx
- v-model	: 双向数据绑定
- v-for  	: 遍历数组/对象/字符串
- v-on   	: 绑定事件监听, 可简写为@
- v-if 	 	: 条件渲染（动态控制节点是否存存在）
- v-else 	: 条件渲染（动态控制节点是否存存在）
- v-show 	: 条件渲染 (动态控制节点是否展示)
- v-text指令：
    - 1.作用：以文本形式向其所在的节点中渲染文本内容。
    - 2.与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会。
- v-html指令：
    - 1.作用：向指定节点中渲染包含html结构的内容。
    - 2.与插值语法的区别：
        - (1).v-html也会替换掉节点中所有的内容，{{xx}}则不会。
        - (2).v-html可以识别html结构。
    - 3.严重注意：v-html有安全性问题！！！！
        - (1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。如在贴吧里输入恶意链接，其他用户点击可被入侵
        - (2).一定要在可信的内容上使用v-html，永不要用在用户提交的内容上！
- v-cloak指令（没有值）：
    - 1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性。
    - 2.使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题。
        - `[v-cloak]{display:none;}`
- v-once指令：
    - 1.v-once所在节点在初次动态渲染后，就视为静态内容了。只渲染一次data设置的初始值？应该是
    - 2.以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能。
- v-pre指令：
    - 1.跳过所在节点的编译过程，作为静态节点渲染。
    - 2.可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译。



### 4.自定义指令：
指令语法的作用：解析标签，包括标签体内容、标签属性、绑定事件;
指令绑定的值是表达式：  `v-bind:'n++' v-bind:'n'`
- 一、定义语法：
    - (1).局部指令：
    ```js
    new Vue({			                        	
        //directives:{指令名:配置对象}  			
        directives:{
            big:{} //复杂写法
        }  			
    }) 
     //或   
    new Vue({	
        //directives:{指令名:回调函数}
        directives:{
            big:function(){},//简化写法
            small(){}       //简化写法的简化语法
        }  
    })
    ```
    - (2).全局指令：
        - `Vue.directive(指令名,配置对象) 或   Vue.directive(指令名,回调函数)`
- 二、配置对象中常用的3个回调：
    - (1).bind：指令与元素成功绑定时调用（一上来）。
    - (2).inserted：指令所在元素被插入页面时调用（第一次dom生成？）。
    - (3).update：指令所在模板结构被重新解析时调用。

- 三、回调函数配置：  
    一般来说，配置对象的bind和update里的内容是一样的，只有inserted内容会不一样，而inserted是一般是处理dom节点渲染出来后实现的交互行为，所以回调函数简化了语法，只执行配置对象里的bind和update，进而不能处理交互式的细节问题
    - 1.不能处理细节问题，不能实现inserted的内容
    - 2.只执行bind和update函数，即一上来执行一次，和模板重新解析时执行
- 四、行参
    - 第1个行参是指令关联的element真实dom
    - 第2个行参是指令绑定的对象，包含指令名，指令绑定的值等等..

- 五、this
    - 指令directives对象里的this都是window，vue开放了指令关联的dom和绑定值对象，但不维护this

- 六、备注：
    - 1.指令定义时不加v-，但使用时要加v-；
        - `<input type="text" v-big="n"></input>`
        - `big(){} 或 big:{}`
    - 2.指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名。
        - `<input type="text" v-big-number="n"></input>`
        - `'big-number'(){} 或 'v-big-number':{}`


```js
    //局部配置
    new Vue({	
        // 指令配置对象		                        	
        directives:{  
            // 指令函数配置法，简化写法
            'big'(ele,bind){ // ele：关联的真实dom bind：绑定的值对象

                // 1、this指向:directives对象里的this是window
                // 2、return:指令方法里面不需要return
                // computed、filter等需要返回值 需要return
                // directives、methods等处理逻辑不需要指定返回return
            },
            //指令名多个单次用-连接
            'big-number':{     // 指令对象配置法，复杂写法
                bind(ele,bind){}, // 指令与元素成功绑定时，一上来
                inserted(ele,bind){}, // 指令所在元素被插入到页面时
                update(ele,bind){}, // 指令所在模板被重新解析时
            }
// bind和update函数内容一般一致，函数式写法就是只包含了bind和update方法，忽略了inserted方法，
// 所以像操作焦点，父元素等都需要放入页面才能进行的细节操作无法执行
        }  			
    }) 

    //全局配置
    //对象写法
    Vue.directive('fbind',{
        //指令与元素成功绑定时（一上来）
        bind(element,binding){
            element.value = binding.value
        },
        //指令所在元素被插入页面时
        inserted(element,binding){
            element.focus()
        },
        //指令所在的模板被重新解析时
        update(element,binding){
            element.value = binding.value
        }
    })
    //函数写法
    Vue.directive('fbind',function(){})

```

```html
    数据有更新时，模板重新解析，生成虚拟dom，对比虚拟dom，复用或生成。
    模板解析，就是将模板通过js指令或语法逻辑转成html的过程。
    生成虚拟节点，就是经过render函数输出vnode虚拟节点。
    对比新旧虚拟dom，取发生变化的节点进行重新生成真实dom。

    那之前说的methods和自定义指令在模板解析时就会重新调用，应该指的是，模板解析时通过js语法转成html，
    再生成虚拟dom对比等等..
    而computed则只在第一次和后续依赖的属性发生变化而重新调用，原理应该是，初次解析生成新节点；
    依赖的属性没变化，其他属性发生变化时，该处的模板解析也不生成虚拟代码，相当于独立出来了，
    只有在依赖的属性发生变化时，引起该处模板重新解析，生成虚拟dom...重新生成节点。

    不同的指令，v-bind、v-model、v-for、v-on、{{}}、v-text、v-html、methods、computer、filter...
    有不同的处理逻辑

    关于为何初始化时，自定义指令函数式写法没有获取到焦点，而data的数据发生变化时，指令绑定的input就能获取到焦点？
    因为初始化时，模板经过解析，先调用方法，再生成虚拟dom、真实dom，但是调用方法时还没有dom生成，就失效了
    data数据发生变化时，指令方法重新解析调用，这时可以获取到第一次生成的dom进行焦点操作，随后不管该数据是否更新节点，焦点事件依然存在？
```


### 5.生命周期

- 生命周期：
    - 1.又名：生命周期回调函数、生命周期函数、生命周期钩子。
    - 2.是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数。
    - 3.生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。
    - 4.生命周期函数中的this指向是vm 或 组件实例对象。

- 常用的生命周期钩子：
    - 1.mounted: 发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
    - 2.beforeDestroy: 清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。

- 关于销毁Vue实例
    - 1.销毁后借助Vue开发者工具看不到任何信息。
    - 2.销毁后自定义事件会失效，但原生DOM事件依然有效。
    - 3.一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。

```
函数中的定时器千万不能在插值里面调用，初次解析，调用定时器，定时器里面改变值，又触发模板更新，
模板更新又重新调用定时器，反复循环且是指数级，因为每个定时器里面都在定时改变值  

mounted：vue解析模板，生成虚拟dom，转成真实dom并挂载到页面时、即初始化页面加载完成时。
el：'root'没有模版template，是包括id=root元素的
有template是直接替换页面的，且只有一个根元素，不能使用`<template>`标签作为根元素

判断元素是否是真实dom：元素 instanceof HTMLElement 
beforeUpdate中数据和页面不同步
vue.$destroy()调用之后，vue不再维护页面组件，但之前的工作成果还在
调用后，销毁组件，清理他和其他组件的连接，接棒她的全部指令和时间监听器，
事件监听器指的是自定义事件监听而不是原生dom事件，比如@click事件
vue文档和报错都是以组件的角度上说的
mounted:设置定时器，订阅事件，自定义事件监听
beforeDestroy：移除定时器，取消订阅，等自定义事件

调用Vue.$stroy()之后，进入beforeDestroy和destroy事件的监听，
beforeDestroy():在此阶段还能获取data.methods指令，都是可用的，在此阶段调用方法等使数据更新的话不会在触发模板更新渲染流程了，一般只做移除定时器，取消订阅，等自定义事件等收尾工作
destroy():一般不用，react里也没有该阶段

生命周期总结：
8个钩子，4对  
beforeCreate():谁创建之前？这里的this是谁？数据监测、数据代理添加之前，this指的是vm。此时无法通过vm访问data数据和methods方法  
created():数据监测、数据代理创建完毕，此时可以获取data数据和method方法
beforeMount():挂载dom之前，此时页面展示的是未经vue编译的dom结构，所有对dom的操作均不奏效
mounted():第一次dom加载完毕，在生命周期里该方法只调用一次，此时页面展示的是经vue编译的dom结构，一般做初始化工作：设置定时器，订阅事件，自定义事件监听
beforeUpdate():此时数据是新的，页面是旧的，页面和数据未同步，
updated():页面更新完成，数据是新的，页面是新的，页面和数据保持同步，
beforeDestroy():在此阶段还能获取data.methods指令，都是可用的，在此阶段调用方法等使数据更新的话不会在触发模板更新渲染流程了，一般只做移除定时器，取消订阅，等自定义事件等收尾工作
destroyed():一般不用，react里也没有该阶段
```
