# Components Basics

## 组件是什么？

组件是**可以复用的Vue实例**，它们与new Vue接收相同地选项，例如：daata、computed、methods以及lifecycle hooks。<u>仅有的例外是**el**这样根实例的特有选项</u>。我们可以通过一个new Vue创建的根实例中把该组件作为自定义元素使用。

```js
Vue.component('button-counter',{
    data:function(){
        return {
            count:0
        }
    },
    template:'<button v-on:click="count++">Click it {{count}} times<button>'
})
```

```html
<div id="copmonent-demo">
    <button-counter></button-counter>
    <button-counter></button-counter>
    <button-counter></button-counter>
</div>
```

```js
new Vue({
el:"#component-demo"
})
```

因为组件是一个可复用的Vue实例，因此它的**data选项应该是一个函数**，这样每个实例就可以独立维护一个被返回的对象，而不用担心影响到其他实例。

## 组建的注册

为了能再模板中使用吗、，这些组件需要先注册以便Vue可以识别出它们。至此，我们的组件都是通过Vue.component**全局注册**的：

```js
Vue.component('my-component-name',{
//...options...
})
```

组件也可以使用**局部注册**的方式，这在后续会提到。

## 通过props向子组件传递数据

**props**是你可以在组件上注册的一些自定义attribute。当一个值传递给props attribute时，它就成为了那个组件实例的一个property。

```js
Vue.component({
props:['title'],
template:'<h3>{{title}}</h3>'
})
```

一个组件可以接收任意数量的**props**，且任何值都可以传递给**props**。我们可以像访问**data**一样访问这个值。

因为HTML中的attribute对于大小写都是不敏感的，因此**在使用DOM模板**时，最好使用kebab-case（短横线分隔命名法）命名，使用字符串模板 `` 时不存在这个限制。

**强调**：props只应该用于子组件接收父组件的数据，它的改变应该由父组件的变化而触发，这是一个**单向下行绑定**，因此不应该在子组件中内部直接更改props。

```js
new Vue({
    el:'blog-post-demo',
    data:{
        posts:[
            {id:1,title:'post1'},
            {id:2,title:'post2'},
            {id:3,title:'post3'}
        ]
    }
})
```

```html
<blog-post
           v-for="post in posts"
           :key='post.id'
           :title='post.title'>
</blog-post>
```

## 模板要求单个根元素、props传对象

仍然以博文为例，当情况复杂时，博文不会仅含标题，这个时候就需要修改更复杂的模板：

```js
Vue.component('blog-post',{
	props:['post'],
	template:
    `<div class="post">
		<h3>{{ title }}</h3>
		<div v-html='post.content'><div>
	</div>`
})
```

```html
<blog-post
           v-for="post in posts"
           :key='post.id'
           :post='post'>
</blog-post>
```

现在无论在何时为**post**对象添加一个新的property，都会被自动地在**blog-post**中可用。

## 监听子组件事件

```js
new Vue({
	el:'#blog-posts-events-demo',
    data:{
        posts:['title','content'],
        pstFontSize:1
    }
})
```

```html
<div id='blog-posts-events-demo'>
    <div :style="{fontSize: postFontSize + 'em'}">
        <blog-post
                   v-for='post in posts'
                   :key='post.id'
                   :post='post'
                   v-on:enlarge-text='postFontSize+=0.1'
    </div>
</div>
```

```js
Vue.component('blog-post',{
    props:['post'],
    template:`
	<div class='blog-post'>
	<h3>{{post.title}}</h3>
	<button v-on:click="$emit('enlarge-text')">Enlarge text</button>
	<div v-html='post.content'></div>
	</div>`
})
```

这里我们通过在父级组件上使用 **v-on** 来监听子组件触发的任意事件，同时在子组件调用内建的**$emit**方法向父组件<u>通知</u>事件的发生。

**$emit**方法不仅可以<u>通知事件发生</u>还可以抛出特定的值。

```html
<button v-on:click="$emit('enlarge-text', 0.1)">Enlarge Text <button>
```

```js
v-on:enlarge-text='postFontSize += $emit'
```

当事件处理函数是一个方法而非上述语句，则这个值会作为第一个参数传入这个方法

```js
v-on:enlarge-text='onEnlargeText'
```

