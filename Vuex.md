# Vuex



[B站视频：Vuex从入门到实战](https://www.bilibili.com/video/BV1h7411N7bg)

## 1. Vuex的基本使用



### 1.1 安装Vuex



```bash
npm install vuex --save
```



### 1.2 导入Vuex包



```javascript
import Vuex from 'vuex'
Vue.use(Vuex))
```



### 1.3 创建store对象



``````javascript
const store = new Vuex.Store({
	// state 中存放的就是全局共享的数据
	state: {count: 0}
})
``````



### 1.4 将store对象挂载到vue实例中

``````javascript
new Vue({
	el: '#app',
	render: h => (app),
	router,
	
	// 将创建的共享数据对象，挂载到Vue实例中，
	// 所有的组件，就可以直接从store中获取全局的数据
	
	store
})
``````





## 2. 示例

``````javascript
# 根目录下 store.js 文件

import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({

	state: {
		
	},
	muatations:{
	
	},
	actions:{
	
	}
})

export default store
``````



``````javascript
# main.js

import Vue from 'vue'
import App from './App.vue'
import store from './store'  # store.js中的store实例对象

Vue.config.productionTip = false

# 全局共享store了
new Vue({
	store, # 挂载了store
	render: h => h(App)
}).$mount('#app')
``````



App.vue

``````vue


<template>
	使用<div></div> 保证只有一个根元素
	
	<div>
        	<my-addition></my-addition>

            <p>
                ----------------------
            </p>


            <my-subtraction></my-subtraction>
    </div>

</template>

<script>
    导入两个组件
    
    import Addition from './components/Addition.vue';
    import Subtraction from './components/Subtraction.vue';
    
    
	export default {
        data(){
            return {
                
            };
        },
        注册为App.vue的私有子组件
        components: {
            'my-addition': Addition,
        	'my-subtraction': Subtraction
        }
    }
</script>
``````



components-> Addition.vue

``````vue
<template>
	<div>
        <h1>
            当前最新的count值为：{{this.$store.state.count}}
        </h1>
        
        <button>
            +1
        </button>
    </div>
</template>

<script>
	export default {
        data(){
            return {
                
            };
        }
    }
</script>
``````

components-> Subtraction.vue

``````vue
<template>
    <div>
        <h1>
            当前最新的count值为：
        </h1>
        <button>
            -1
        </button>
    </div>
	
</template>

<script>
    
    
    import {mapState} from 'vuex'
    
	export default {
        data(){
            return {
                
            };
        },
        computed: {
            ...mapState(['count'])
        }
    }
</script>
``````



代码格式化

``````
.prettierrc

{
	"semi": false,  不是用分号
	"singleQuote": true 不是用双引号
}
``````





## 3. Vuex的核心概念

### 3.1 state

state提供唯一的公共数据源，所有的共享数据都统一放到store的state中存储

``````js
// 创建store数据源，提供唯一公共数据

const store = new Vuex.Store({
	state: { count: 0 }
})
``````





``````js
# 根目录下 store.js 文件

import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({

	state: {
		count: 0 // 全局共享数据count
	},
	muatations:{
	
	},
	actions:{
	
	}
})
``````



组件访问State中数据的**第一种方式**：

`````js
this.$store.state.全局数据名称
`````



 组件访问State中数据的**第二种方式**：

````js
// 1. 在Vue文件里 从vuex中按需导入mapState函数

import { mapState } from 'vuex'

// 2.通过刚导入的mapState函数，将当前组件需要的全局数据，映射为当前组件的computed计算属性

computed: {
    ...mapState(['count'])  // ...三个点为展开运算符
}
````



### 3.2 用Mutation变更Store中的数据

components-> Addition.vue

该方式非法

````vue
<template>
	<div>
        <h1>
            当前最新的count值为：{{this.$store.state.count}}
        </h1>
        
        <button @click="btnHandler1">
            +1
        </button>
    </div>
</template>

<script>
	export default {
        data(){
            return {
                
            };
        },
        methods: {
            btnHandler1(){
                this.$store.state.count++;
            }
        }
    }
</script>
````

* 通过mutation变更Store数据，不可以直接操作Store中的数据
* 通过这种方式操作虽然繁琐，但可以集中监控所有数据的变化





````js
# 根目录下 store.js 文件

import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store =  new Vuex.Store({

	state: {
		count: 0
	},
	muatations:{
        // 形参state代表的就是上边的state对象
		add(state){
            //变更状态
            state.count++
        }
	},
	actions:{
	
	}
})
export default store
````





````vue

// components-> Addition.vue
<template>
	<div>
        <h1>
            当前最新的count值为：{{this.$store.state.count}}
        </h1>
        
        <button @click="btnHandler1">
            +1
        </button>
    </div>
</template>

<script>
	export default {
        data(){
            return {
                
            };
        },
        methods: {
            // 触发mutation
            btnHandler1(){
                // 触发mutations的第一种方式
                this.$store.commit('add')
            }
        }
    }
</script>
````



### 3.3 Mutation传参



```js
const store = new Vuex.Store({
	state: {
		count: 0
	},
	mutations: {
        add(state){
			// 变更状态
			state.count ++
		},
		addN(state, step){
			// 变更状态
			state.count += step
		}
	}
})
```



```js
// 触发mutation
methods: {
	handle2(){
		// 在调用commit函数
		// 触发mutations时携带参数
		// commit 的作用就是调用某个mutation函数
		this.$store.commit('addN', 3)
	}
}
```



### 3.4 Mutation触发的第二种方式

````js
// this.$store.commit()是第一种方式

// 第二种方式：

// 1. 导入mapMutations函数
import { mapMutations } from 'vuex'

// 2. 将指定的mutations函数，映射为当前组件的methods函数

methods: {
    ...mapMutations(['add', 'addN'])
}
````





例子

````js
const store = new Vuex.Store({
	state: {
		count: 0
	},
	mutations: {
        add(state){
			// 变更状态
			state.count ++
		},
		addN(state, step){
			// 变更状态
			state.count += step
		},
		sub(state){
			state.count --
		},
        subN(state, step){
			state.count -= step
		}
	}
})
````





components-> Subtraction.vue

``````vue
<template>
    <div>
        <h1>
            当前最新的count值为：
        </h1>
        <button @click="btnHandler1">
            -1
        </button>
         <button @click="btnHandler2">
            -N
        </button>
    </div>
	
</template>

<script>
    
    
    import {mapState, mapMutations} from 'vuex'
    
	export default {
        data(){
            return {
                
            };
        },
        computed: {
            ...mapState(['count'])
        },
        methods:{
            ...mapMutations(['sub', 'subN']),
            btnHandler1(){
                
            },
            btnHandler2(){
                this.subN(3)
            }
        }
    }
</script>
``````





### 3.5 Actions 异步操作

mutations函数不能使用异步操作： 延时操作

````js
# 根目录下 store.js 文件

import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({

	state: {
		
	},
	muatations:{
	
	},
	actions:{
	
	}
})

export default store
````



Action用于处理异步操作

如果使用异步操作变更数据，必须使用action，不能使用mutations，但是action中还是要通过触发mutation的方式间接变更数据。

```js
// 定义action

const store = new Vuex.Store({
    
    // 只有mutations中定义的函数，才有权利修改state中的数据
	mutations: {
		add(state){
			state.count++
		}
	},
    
	actions: {
		addAsync(context){
            // 在actions中，不能直接修改state中的数据
            // 必须通过context.commit()触发某个mutation函数
			setTimeout(() => {
				context.commit('add')
			},1000)
		}
	}
})
```





```js
// 触发Action

methods: {
	handle(){
		//触发actions的第一种方式
		this.$store.dispatch('addAsync')
	}
}
```





### 3.6 Action 携带参数





```js
// 定义action

const store = new Vuex.Store({
    
    // 只有mutations中定义的函数，才有权利修改state中的数据
	mutations: {
		addN(state, step){
			state.count += step
		}
	},
    
	actions: {
		addNAsync(context, step){
            // 在actions中，不能直接修改state中的数据
            // 必须通过context.commit()触发某个mutation函数
			setTimeout(() => {
				context.commit('addN', step)
			},1000)
		}
	}
})

export default store
```





```js
// 触发Action

methods: {
	handle(){
		//触发actions 携带参数
		this.$store.dispatch('addNAsync', 3)
	}
}
```



### 3.7 Getter

Getter 对Store中的数据进行加工处理形成新的数据

* Getter可以对Store中已有的数据加工处理之后形成新的数据，类似Vue的计算属性
* Store中的发生变化，Getter的数据也变化



```js
const store = new Vuex.Store({
	state: {
		count: 0
	},
	getters: {
		showNum: state => {
			return '当前最新的数量是【' + state.count + '】'
		}
	}
})
```





```js
// 定义action

const store = new Vuex.Store({
    
    // 只有mutations中定义的函数，才有权利修改state中的数据
	mutations: {
		addN(state, step){
			state.count += step
		}
	},
    
	actions: {
		addNAsync(context, step){
            // 在actions中，不能直接修改state中的数据
            // 必须通过context.commit()触发某个mutation函数
			setTimeout(() => {
				context.commit('addN', step)
			},1000)
		}
	},
    
    getters: {
        showNum(state){
            return '当前最新的数量是【' + state.count + '】'
        }
    }
})

export default store
```





使用Getter的第一种方式



```js
this.$store.getters.名称
```



第二种

````js
import {mapGetters} from 'vuex'

computed: {
	...mapGetters(['showNum'])
}
````





## 4. 基于Vuex的案例



**ToDoList**

