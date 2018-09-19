---
title: Vue 2.x中组件通信的那些事儿
date: 2018-01-13 20:45:20
tags:
- Vue.js
---
组件(component)是Vue.js里最核心的功能。但是组件里最重要的部分是组件间的相互通信，包括一些**父子组件通信**、**兄弟组件通信**、**跨级组件通信**等。

<!--more-->

<span id="props">props</span>
--


要说组件通信就离不开Vue里一个关键的属性：```props```<br />
在组件中，使用选项props来声明需要从父级接收的数据，props的值可以是两种：一种是字符串数组，一种是对象。

	<div id="app">
		<my-component hello="一句问候"></my-component>
	</div>
	<script>
		Vue.component('my-component',{
			props:['hello'], //以字符串数组为例
			template:'<div>{{hello}}</div>'
		});
		var app = new Vue({
			el:'#app'
		})
	</script>

*注：由于HTML特性不区分大小写，当使用DOM模板时，驼峰命名(camelCase)的props名称要转为短横分割命名(kebab-case)*

有的时候传递的数据不是写死的，而是来自父级的动态数据，这时可以使用v-bind(:)来动态绑定props的值，当父组件数据变化时，也会传递给子组件。

	<div id="app">
		<input type="text" v-model="msg" />
		<my-component :hello="msg"></my-component>
	</div>
	<script>
		Vue.component('my-component',{
			props:['hello'], //以字符串数组为例
			template:'<div>{{hello}}</div>'
		});
		var app = new Vue({
			el:'#app',
			data:{
				msg:''
			}
		})
	</script>

*注：如果要传递数字、布尔型、数组、对象的话，而且不使用v-bind，那么传递的只是字符串，所以在props的值前面加上v-bind，如上。*

> Vue2.x通过props传递数据是单向的，也就是父组件数据变化时会传递给子组件，但是反过来不行。而在Vue1.x里提供了.sync修饰符来支持双向数据绑定。

数据验证
--

在props属性里还有一个重要的作用是```数据验证```,当props需要验证时，就需要用对象写法。

	Vue.component('my-component',{
		props:{
			//必须是数字类型
			propA:Number,
			//必须是字符串或数字类型
			propB:[String,Number],
			//布尔值，如果没有定义，默认值就是true
			propC:{
				type:Boolean,
				default:true
			},
			//数字，而且是必传
			propD:{
				type:Number,
				required:true
			},
			//如果是数组或对象，默认值必须是一个函数来返回
			propE:{
				type:Array,
				default:function(){
					return []
				}
			},
			//自定义一个验证函数
			propF:{
				validator:function(value){
					return value > 10
				}
			}
		}
	})


自定义事件
--

前面我们已经了解到从父组件向子组件通信，通过[```props```](#props)传递数据就可以了。接下来要说的是当子组件需要向父组件传递数据时，就要用到```自定义事件```。

>在Vue组件中，子组件用$emit()来触发事件，父组件用$on()来监听子组件的事件；父组件也可以直接在子组件的自定义标签上使用v-on来监听子组件触发的自定义事件。

	<div id="app">
		{{total}}
		<my-component @increase="handle"></my-component>
	</div>
	<script>
		Vue.component('my-component',{
			template:'\
			<div>\
				<button @click="handleIncrease">+1</button>\
			</div>',
			data:function(){
				return {
					count :0
				}
			},
			methods:{
				handleIncrease:function(){
					this.count++;
					//用$emit触发自定义事件，并传递数据
					this.$emit('increase',this.count)
				}
			}
		});
		var app = new Vue({
			el:'#app',
			data:{
				total:0
			},
			methods:{
				//自定义事件传递过来的数据total，赋给this.total
				handle:function(total){
					this.total = total
				}
			}
		})
	</script>

v-model
--

在Vue2.x中可以在自定义组件上使用v-model指令，上面的例子可以修改为：

	<div id="app">
		{{total}}
		<my-component v-model="total"></my-component>
	</div>
	<script>
		Vue.component('my-component',{
			template:'\
			<div>\
				<button @click="handleIncrease">+1</button>\
			</div>',
			data:function(){
				return {
					count :0
				}
			},
			methods:{
				handleIncrease:function(){
					this.count++;
					//这儿的自定义事件是input，在父组件里直接用指令v-model，相当于一个语法糖
					this.$emit('ipnut',this.count)
				}
			}
		});
		var app = new Vue({
			el:'#app',
			data:{
				total:0
			}
		})
	</script>

非父子组件通信
--

在实际业务中，除了父子组件通信，还有很多非父子组件通信的场景，非父子组件一般有两种，```兄弟组件```和```跨多级组件```。

*注：在Vue1.x中，除了$emit()方法，还有$dispatch()和$broadcast()。$dispatch()用于向上级派发事件，只要是它的父级(一级或多级以上)，都可以在Vue实例的events选项内接收。同理，$broadcast()是由上级向下级广播事件的，用法一致，只是方向相反。*

在Vue2.x中，推荐使用一个空的Vue实例作为中央事件总线(bus)，也就是'中介'。

	<div id="app">
		{{message}}
		<component-a></component-a>
	</div>
	<script>
		var bus = new Vue();
		Vue.component('component-a',{
			template:'<button @click="handle">传递事件</button>',
			methods:{
				handle:function(){
					bus.$emit('on-change','来自组件component-a的内容')
				}
			}
		});
		var app = new Vue({
			el:'#app',
			data:{
				message:''
			},
			mounted:function(){
				var self = this;
				//在实例初始化时，监听来自bus实例的事件
				bus.$on('on-change',function(msg){
					self.message = msg
				})
			}
		})
	</script>

>以上这种方法巧妙而轻量的实现了任何组件间的通信，包括，父子组件、兄弟组件、跨级组件等。而且Vue1.x和Vue2.x都适用。而且如果想要深入使用，还可以扩展bus实例，给它添加data、methods、computed等选项。


除了中央事件总线(bus)外，还有两种方法可以实现组件通信：[```父链```](#parent)和[```子组件索引```](#child_index)。

<span id="parent">父链</span>
--

在子组件中，使用this.$parent可以直接访问该组件的父实例或父组件，父组件也可以通过this.$children访问它所有的子组件，而且可以递归向上或者向下无限访问，直到根实例或最内层组件。

	<div id="app">
		{{message}}
		<component-a></component-a>
	</div>
	<script>
		Vue.component('component-a',{
			template:'<button @click="handle">通过父链直接修改数据</button>',
			methods:{
				handle:function(){
					//访问到父链后可以做任何操作，比如直接修改数据
					this.$parent.message = '通过父链$parent修改的数据'
				}
			}
		});
		var app = new Vue({
			el:'#app',
			data:{
				message:''
			}
		})
	</script>

*注：以上操作虽然Vue允许，但是在业务中不建议这样操作。*

<span id="child_index">子组件索引</span>
--

Vue提供了子组件索引的方法是：用特殊的属性ref来为子组件指定一个索引名称。

	<div id="app">
		<button @click="handle">通过ref获取子组件实例</button>
		<component-a ref="comA"></component-a>
	</div>
	<script>
		Vue.component('component-a',{
			template:'<div>子组件</div>',
			data:function(){
				return {
					message:'子组件内容'
				}
			}
		});
		var app = new Vue({
			el:'#app',
			methods:{
				handle:function(){
					//通过ref来访问指定实例
					var msg = this.$refs.comA.message
					console.log(msg)
				}
			}
		})
	</script>

>在父组件模板中，子组件标签上使用ref指定一个名称，并在父组件内通过this.$refs来访问指定名称的子组件。

*注：$refs只在组件渲染完成后才填充，并且它是非响应式的。它仅仅作为一个直接访问子组件的应急方案，应当避免在模板或计算属性中使用$refs。*


**以上就是我了解的一部分Vue.js里的的组件间的通信。后续有什么新发现还会继续添加修改。**