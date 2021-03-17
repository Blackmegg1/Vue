## Data and Methods

当一个 Vue 实例被创建时，它将我们所创建的temp对象中的所有的 property 加入到 Vue 的响应式系统中。

```js
let temp={ a:1 };
let vm=new Vue({data:temp});
```
同时需要注意，temp对象中在实例创建时就已存在的property值发生改变时，Vue实例也会同步地更新，反之亦然。所以可以通过设置初始值的方式来保证同步更新。

```js
let temp={
	a:1,
	unknow:"",
	anumber:0
}
```

使用Object.freeze()会使这个同步失败。

```js
Object.freeze(temp);
```

Vue实例也暴露了一些有用的实例与方法，他们通过前缀** $ **标识出，例如：

```js
let vm =new Vue({
el:'#example'
})
```

**el** property绑定了`id='exmple'`的DOM元素。

```js
vm.$watch('unknow',newValue,oldValue){
// vm.unknow 发生改变时调用这个回调
}
```

## Lifecycle Hooks

Vue的实例通常有以下几个生命周期钩子：

```js
beforeCreate();
created();
beforeMount();
mounted();
beforeUpdate();
updated();
beforeDestroy();
destroyed();
```

这些被称为Lifecycle Hooks的函数也是上文所说的Vue实例暴露出的方法，因此在使用它们时需要注意格式。

![Vue 实例生命周期](https://cn.vuejs.org/images/lifecycle.png)
