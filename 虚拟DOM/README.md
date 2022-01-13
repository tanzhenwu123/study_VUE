##### 提示:学习这章节可以试图了解vue框架设计采用的设计模式
### 什么是虚拟DOM
```
举个例子：
户口本，描述一个人的家庭详细信息，出生年月日，家庭关系，家住何处,性别，所属民族

示例:
<script>
export default{
    //在组件挂载的时候
    mounted(){
        //这里只是做一个示例，在平常的开发时，vnode是不需要我们去触碰的
        console.log(this._vnode)
        //输出:含有dom元素信息的js对象
    }
}
</script>

在vue实例中，每个组件内都含有一个render函数，render函数向外返回一个虚拟dom对象
这个对象只用来描述，并不参与生成结果

示例:
<script>
export default{
    render(h){
        return('div',[h('h1','你好'),h('h2','大帅哥')])
    }
}
</script>
```

### 为什么需要虚拟dom
```
为什么需要虚拟don？
场景:1000条数据需要渲染到页面上的时候
创建对象和创建dom的时间做一个比较

const num = 1000
console.time(js Object)
for(let i = 0 ; i < num; i++){
    const obj = {}
}

for(let j = 0 ; j < num; j++){
    const dom = document.creatElement("div")
}
console.timeEnd('dom Object')

这里所需要的时间甚至还没有加上渲染到页面所需要的时间
```

### 虚拟dom转换成为真实dom
```
这里会不会有杠精在问：最后还不是会成为真实dom吗
没错，还真是。
vue的不足点在于初次加载需要多一个步骤，就是虚拟dom转换成为真实dom的一个步骤
但是接下来二次渲染的时间相对真实dom的整体渲染效率将会大幅的提升

举个例子：
当组件产生变化时，组件将会重新触发render函数产生一个新的虚拟dom树去对应旧的dom树
由于他们两产生的地址一致，所以两个对象理论上是绝对相等的。新的dom树与旧的dom树相对比，两者之间的不同点将会被重新渲染。
最后，这些更改过后的节点会在已有的真实dom的基础上，更改所需要更改的部分
相对于重新创建一个新的真实dom，它节省的时间不是一般的多
```

### 模板和虚拟dom的关系
```
提到模板就该提到vue-cli提供的compile编译模块

模板是什么?
两种体现:
1. <template></template>
2. <script>
    template:`
    <div class="hello" >
    <p> hello world </p>
    </div>
    `
</script>

第一种模板是为了美观好看，以及方便开发者书写
第二种模板是在少数情况下(我没用过，不方便例举)

compile模块主要负责将模板转换为render函数
编译的过程分为两步:
1. 将模板字符串转换为AST(抽象语法树)
2. 将AST转换成render函数(这里涉及源码，建议百度一下)

Vue引用方式:
分两种:
1.开发模式(带脚手架,这个模式称之为模板预编译):编译发生在打包和npm run serve运行项目时
打包产生文件
运行仅在内存中进行打包，不产生实际文件夹

2.生产模式(传统引用,不带脚手架,这个模式称之为运行时编译):第一次运行时进行打包

因此通过预编译可以在运行代码整个程序之前完成编译，极大的减少运行时造成的性能损耗

在项目的vue.config.js中进行配置可以选择编译模式

//vue.config.js
export default{
    //为ture时启用运行时编译，false相反

    runtimeCompiler:true

    //为ture时 使用<script>
    //  template:`<h1> hello world </h1>`
    //</script>
    //
    //为false时
    //<template></template>
    //为ture的话同时会将编译器同时打包到打包结果
}

模板的存在只是为了方便开发人员书写界面代码
```