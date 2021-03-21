# Computed Properties and Watchers

## 计算属性

 对于复杂逻辑，都应该使用计算属性，仍然以翻转字符串为例：

```html
<div id="example">
<p>{{message}}</p>
<p>{{reversedMessage}}</p>
</div>
```

```js
let vm=new Vue({
el:"#example",
data: {
message:'hello Vue!'
},//注意这里的','
computed:{
reversedMessage:function(){
return this.message.split('').reverse().join('')//这里的this指向vm实例
}
}
})
```

vm.reversedMessage依赖于vm.message，这种依赖关系没有side effect。

<u>计算属性与直接调用方法的区别</u>：

```js
computed:{
now:function(){
return Date.now()
}
}
```

**计算属性基于它的响应式依赖进行缓存**这意味着上述计算属性不会更新，因为Date.now()没有任何依赖关系。所以只要所依赖的（如message相对于reversedMessage）没有发生改变，那么访问计算属性便会立刻返回之前的值，而不需要执行函数。

```html
<p> when? {{getTimeNow()}} </p>
```

```js
methods: {
getTimeNow:function(){
return Data.now()
}
}
```

上述则采用了调用方法的方式实现，这意味着每次触发渲染时总会执行函数。

> 我们为什么需要缓存？假设我们有一个性能开销比较大的计算属性 **A**，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 **A**。如果没有缓存，我们将不可避免的多次执行 **A** 的 getter！如果你不希望有缓存，请用方法来替代。

当你有一些数据需要同步与其他数据时，你可能会滥用**watch**（<u>侦听属性</u>）这是一个命令式的回调。

```js
let vm=Vue({
	data:{
		e1:'a',
		e2:'b',
		s:'3'
},
watch:{
	e1:function(val){
		this.s=val+this.e2
},
	e2:function(val){
		this.s=this.e1+val
}
}
})
```

val是对应监听属性改变后的值，可以发现上述代码是命令式且重复的，不妨使用计算属性：

```js
computed:{
    s:function(){
        return this.e1+this.e2
    }
}
```

我们知道s依赖于e1与e2，那么如果我们希望s改变的同时e1与e2也改变，实现这样一个双向绑定，计算属性能做到吗？计算数学默认只有getter，此时我们需要增加setter：

```js
computed:{
    s:{
        get:function(){
            return this.e1+this.e2
        }
        set:function(val){
            let newVal=val.split('');
            this.e1=newVal[0];
            this.e2=newVal[1];
        }
    }
}
```

如此，计算属性也实现了双向绑定。

## 侦听器

**在需要数据变化时执行异步或开销较大的操作时**，侦听器时最有用的方法。

```html
<div id="watch-example">
    <p>
        Let me take over decision-making :
            <input v-model="question">
    </p>
    <p>
        {{answer}}
    </p>
</div>
```

```html
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
//分别引入了axios以及lodash
<script>
let watchExampleVM=new Vue({
    el:"watch-example",
    data:{
        question:'',
        answer:'I cannot make decision until you ask a question! '
    },
    watch:{
        question:function(newVal,oldVal){
            this.answer="Waiting for you to stop typing...";
            this.debouncedGetAnswer();//防抖函数
        }
    },
    created:function(){
        this.debouncedGetAnswer=_.debounce(this.getAnswer,500);
    },
    methods:{
        getAnswer:function(){
            if(this.question.indexOg('?')===-1){
                this.answer="Question always contain a '?' ";
                return
            }
            this.answer='Thinking...';
            const vm=this;
            axios.get('https://yesno.wtf/api')
            .then(function(response){
                vm.answer=_.capitalize(response.data.answer);
            })
            .catch(function(error){
                vm.answer='Error! Could not reach to the API'+error
            })
        }
    }
    
})
</script>
```

**watch**允许我们执行异步操作，同时在得到最终结果前设置了中间状态。这些都是computed properties无法做大的。

在上述代码中使用了lodash库中的debounce函数，这个函数限制了访问API的频率，称为**防抖**，通常与**节流**一并被提及。关于这两个概念详细内容以及具体实现可以参考这篇文章：[Debouncing Throttling Explained Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)

>>>>>>> VueBasicLearning/main
