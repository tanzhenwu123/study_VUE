## 父子组件通信
- 注意: 绝大多数vue本身提供的通信方式，基本都属于父子组件通信

### prop
```
最常见的组件通信方式之一，由父组件传递到子组件
示例:

父组件:
<template>
<sonCmp :data="data" />
</template>

<script>
import sonCmp from './sonCmp.vue'
export default{
    data(){
        return{
            data:[]
        }
    },
    components:{
        sonCmp
    }
}
</script>


子组件:
<template>
<p v-for="(item,index) in data" :key="index" >{{item}}</p>
</template>

<script>
export default{
    props:{
        data:Array
    }
}
</script>
```

### event
```
由子组件通过事件，将条件传递给父组件

注意:子组件不能直接对父组件传递的数据进行更改

示例:

子组件:
<template>
<button @click="changeNum('+') :disable="Num == 0" > add </button>
{{Num}}
<button @click="changeNum('-') > reduct </button>
</template>

<script>
export default{
    props:{
        Num:number
    },
    methods:{
        changeNum(e){
            if(e == "+") this.$emit("NumAddFn",1)
            else this.$emit("NumReductFn",-1)
        }
    }
}
</script>


父组件:
<templaate>
<SonCmp 
:Num="number" 
@NumAddFn="addNum" 
@NumReductFn="reductNum" />
</template>

<script>
import SonCmp from './SonCmp.vue'
export default{
    data(){
        return{
            number:0
        }
    },
    methods:{
        addNum(e){
            this.number += e
        },
        reductNum(e){
            this.number += e
        }
    },
    components:{
        SonCmp
    }
}
</script>
```

### style & class
```
父组件可以给子组件传递style和class，他们会合并到子组件的根元素中

示例:

父组件:
<template>
<SonCmp
class="hello"
style="color:red"
msg="1234567" />
</template>

<script>
import SonCmp from './SonCmp.vue'
export default{
    components:{
        SonCmp
    }
}
</script>

子组件:
<template>
<div class="tzw" >
{{msg}}
</div>
</template>

<script>
export default{
    props:{
        msg:string
    }
}
</script>

编译过后:
<div class="hello tzw" style="color:red >
1234567
</div>
```

### attribute
```
这玩意很少见

举个例子:

父组件:
<template>
<SonCmp
data-a="1"
data-b="2"
msg="哈哈哈" />
</template>

<script>
import SonCmp from './SonCmp.vue'
export default{
    components:{
        SonCmp
    }
}
</script>

子组件:
<template>
<div class="son">
{{msg}}
</div>
</template>

<script>
export default{
    props:{
        //未被声明传递的属性统称为attribute属性,这里指data-a，data-b
        msg:string
    },

    //这里的配置是指禁止attribute属性默认附着在子组件的根目录上
    inheritAttrs:false
    created:{
        console.log(this.$attrs)  //得到1,2
    }
}
</script>

编译后:
<div class="son">哈哈哈</div>

附言：this.$attrs获取不到父组件传递的class和style，它们会做另行处理
```

### navite修饰符
```
再注册事件的时候，父组件可以使用native修饰符,将事件注册到子组件的根元素上

这是一种可以处理简单交互，比如说一张商品卡，商品卡内无其他交互事件(按钮点击，表单事件等),仅做最简单的事件时可以使用，减少不必要的代码量

示例:
父组件:
<template>
<div id="app" >
<div v-for="item of 4" :key="item" >
<SonCmp @click.native="handleClick(item)" />
</div>
</div>
</template>

<script>
import SonCmp from './SonCmp.vue'
export default{
    methods:{
        handleClick(e){
            console.log(`我是${e}号组件`)
        }
    },
    components:{
        SonCmp
    }
}
</script>

子组件:
<template></template>

渲染结果
子组件一点击输出:我是一号组件
子组件二点击输出:我是二号组件
子组件三点击输出:我是三号组件
子组件四点击输出:我是四号组件
```

### $listeners
```
子组件可以通过$listeners获取父组件传递过来的所有事件处理函数
```

### v-model
```
分开做一个md文档讲解
```

### sync修饰符
```
和v-model作用相似，都是用来双向绑定，只不过v-model对绑定对象数量有限制，而sync修饰符没有限制

示例：
父组件：
<template>
<SonCmp :num1.sync="number1" :num2.sync="number2" />
<!-- <SonCmp @update:num1="$event" @update:num2="$event" /> -->
</template>
<script>
import SonCmp from './SonCmp.vue'
export default{
    components:{
        SonCmp
    },
    data(){
        return{
            number1:1,
            number2:2
        }
    }
}
</script>

子组件:
<template>
<div>
<p>
<button @click="$emit('uploadNum1',1)" >+</button>
{{num1}}
<button @click="$emit('uploadNum1',-1)" >-</button>
</p>
<p>
<button @click="$emit('uploadNum2',+1)" >+</button>
{{num2}}
<button @click="$emit('uploadNum2',-1)" >-</button>
</p>
</div>
</template>

<script>
export default{
    props:["num1","num2"]
}
</script>
```

### $parent$和$children$
```
$parent 得到父组件的实例
$children 得到子组件的实例

示例:

父组件:
<template>
<SonCmp />
</template>

<script>
import SonCmp from './SonCmp.vue'
export default{
    components:{
        SonCmp
    },
    mounted(){
        //输出:嘿嘿嘿
        console.log(this.$children.msg)
    },
    data(){
        return{
            msg:'哈哈哈'
        }
    }
}
</script>

子组件:
<template>

</template>

<script>
export default{
    mounted(){
        //输出:哈哈哈
        console.log(this.$parent.msg)
    },
    data(){
        return{
            msg:'嘿嘿嘿'
        }
    }
}
</script>
```

### slot和scopedSlots
```
另立章节讲解插槽
```

### ref
```
父组件可以通过ref获取到子组件的实例

示例:

父组件:
<template>
<SonCmp  ref="son" />
</template>
<script>
import SonCmp from './SonCmp.vue'
export default{
    components:{
        SonCmp
    },
    mounted(){
        //输出:子组件实例上的msg 哈哈哈
        console.log(this.$refs["son"].msg)
    }
}
</script>

子组件:
<template>
</template>
<script>
export default{
    data(){
        return{
            msg:'哈哈哈'
        }
    }
}
</script>
```

## 跨组件通信

### $provide$ 和 $inject$
```
注意:除开父子组件关系不可提供，但是两个组件一定要在一条组件关系链上

上级组件提供:
<template>

</template>
<script>
export default{
    provide:{
        msg
    },
    data(){
        return{
            msg:'你真帅'
        }
    }
}
</script>

下级组件注入:
<template>
</template>
<script>
export default{
    inject:['msg'],
    mounted(){
        //打印:你真帅
        console.log(this.msg)
    }
}
</script>
```

### router
```
简单的说明:当一个组件改变了当前地址栏
举个例子：商品页面分页组件翻页会更改地址栏信息，而商城页面的商品列表会监听地址栏信息

再举个最最最简单的例子:

App.vue

<template>
<div id="app">
<div style="display:flex;" >
<router-link 
v-for="(item,index) in list" 
:key="index"
:to="item.link" >
{{item.text}}
</router-link>
</div>
<router-view />
</div>
</template>
<script>
export default{
    data(){
        return{
            list:[
                {
                    text:'首页',
                    link:'/'
                },
                {
                    text:'分类',
                    link:'/type'
                },
                {
                    text:'特卖',
                    link:'/pre'
                },
                {
                    text:'我的',
                    link:'/my'
                }
            ]
        }
    }
}
</script>

当店里router-link的时候，切换不同的页面，router-view会根据监听不同的地址栏锚点进行渲染
等于说是组件之间间接的进行了联系
```

### vuex
```
这踏马还需要讲？？
```

### store
```
官方推荐的中小型的数据仓库
//store.js
export default{
const store = {
    msg:'哈哈哈',
    num:0
}
}

//cmpA
<script>
import store from './store.js'
export default{
    data(){
        msg:store.msg
    }
}
</script>

//cmpB
<script>
import store from './store.js'
export default{
    data(){
        num:store.num,
        msg:store.msg
    }
}
</script>

当引入store.js的时候，并将store.js内的对象属性注入到实例对象中的data里的时候
store.js里的对象属性也就成了响应式的数据,并且组件之间都有权利共享和更改数据
```

### eventbus
```
中小型的项目可以用它
同上，换成了监听事件

组件A和B同时监听某个事件,组件A触发了某个事件并告知事件总线，事件总线内所有监听该事件的函数同时执行该函数
```