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

