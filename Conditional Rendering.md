# Conditional Rendering

## v-if

**v-if**指令用于条件性渲染，被绑定的内容只会在指令表达书返回**truthy**真值的时候被渲染。

```html
<p v-if="awesome">Vue is awesome!</p>
<p v-else>
    React is awesome too
</p>
```

如果我们希望将多个元素绑定在一个**v-if**上，可以使用**<template>**元素做一个包裹元素，最终渲染的结果不包含<**template>**元素。

```html
<template v-if="visible">
<h1>Title</h1>
    <p>
        Paragrap 
    </p>
</template>
```

**v-else**元素必须紧跟在**v-if**或者**v-else-if**的元素后，否则它将不被识别。

```html
<template v-if="loginType==='username'">
<label>Username</label>
<input placehoder="Enter your username">
</template>	
<template v-else>
<label>Email</label>
<input placehoder="Enter your email address">
</template>	
```

在上面的代码切换中，并不会清楚**input**中用户已输入的内容，这是因为两个**template**使用了相同的elements，逻辑上只是替换了**input**的placeholder。

在Vue中，我们通过为**input**元素添加一个具有唯一值的**key**attribute来实现元素的重新渲染：

```html
<template v-if="loginType==='username'">
<label>Username</label>
<input placehoder="Enter your username" key="username-type">
</template>	
<template v-else>
<label>Email</label>
<input placehoder="Enter your email address" key="email-type">
</template>	
```
## v-show

另一个根据条件展示元素的指令是**v-show**：

```html
<p v-show="visible">hello</p>
```

与**v-if**不同的是**v-show**所绑定的元素会始终保留在DOM中。也就是说v-show只是简单地切换了元素CSS property **display**。

**v-if**是真正意义上的条件渲染，它会确保在切换过程中元素被适当地销毁重建。同时**v-if**也是**惰性的**，即直到条件第一次为真值时才会渲染条件快。**v-show**则不管初始条件如何，总会渲染元素，并只是对CSS property进行更改。

<u>需要注意的是v-show不支持v-else以及template</u>



## 不要将v-if与v-for同时用在一个元素上

一般在遇到两种情况我们会倾向这么做：

1.希望过滤列表中的条件项目

```html
<ul>
    <li
        v-for="user in users"
        v-if="user.isActive"
        :key="user.id"
        >
    {{user.name}}
    </li>
</ul>
```

因为在Vue中v-for比v-if具有更高地优先级，所以会优先进行以下运算：

```js
this.users.map(function(user){
    if(user.isActive){
        return user.name
    }
})
```

因此即使isActive为真值地只是users内的一小部分user，Vue也得在每次重新渲染时遍历整个列表，之后的**v-if**重复检查了isActive。且无论isActive有没有发生改变，重渲染时都得遍历整个列表。

因此我们**让v-for在计算属性的基础上进行遍历**：

```js
computed:{
    activeUsers:function(){
        return this.users.filter(function(user){
            return user.isActive
        })
    }
}//Array.filter()方法将创建一个新数组，新数组中的元素是通过检查指定数组（users）中符合条件（function(user)...）的所有元素
```

```html
<ul>
    <li
        v-for="activeUsers"
        :key="user.id">
    {{user.name}}</li>
</ul>
```

引入计算属性后的代码不会重复检查property isActive ,且只有在users数组发生相关变化时才会被重新渲染，如此**解耦**了渲染层的逻辑，可维护性增加了。

2.避免渲染本该被隐藏的列表

```html
<ul>
  <li
    v-for="user in users"
    v-if="shouldShowUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

由于**v-for**的优先级大于**v-if**，因此v-for会对所有的用户检查shouldShowUsers

```html
<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

通过将**v-if**移至父级元素，我们只检查一次，且不会在 `shouldShowUsers` 为否的时候运算 **v-for**。
