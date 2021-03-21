# Class and Style Bindings

## 绑定HTML Class

```html
	<div id="test" v-bid:class="{ active: isActive,danger: hasError }" class="static">       
</div>
```

```js
let vm=new Vue({
    el:"#test",
    data:{
        isActive:true,
        hasError:true
    }
})
```

**v-bind:class**向对象中传入了多个 class，且该指令可以与普通的class attribute共存。结果被渲染为：

```html
<div class="static active danger"></div>
```

当Vue实例中的**isActive**或**hasError**变化时，class列表也将同步更新。

结合上一节所学习的计算属性，我们可以创造出一个更强大的模式：

```html
<div id="test" v-bind:class="classObject"></div>
```



```js
let vm=new Vue({
    el:"#test",
    data:{
        isActive:true,
        error:false
    },
    computed:{
        classObject:function(){
            active=this.isActive&&!this.error;
            danger=this.error;
            return {active,danger}
        }
    }
})
```

**v-bind**可以绑定一个对象，自然也可以绑定一个数组：

```html
<div v-bind:"[activeClass,errorClass]"></div>
```

也可以数组与对象相结合：

```html
<div v-bind:"[activeClass, {error:hasError} ]"></div>
```

## 绑定内联样式

使用**v-bind:style**绑定内联样式，这与CSS十分相似：

```html
<div v-bind:style="{color:activeColor,fontSize:fontSize+'px'}"></div>
```

```js
data:{
activeColor:'red',
fontSize:30
}
```

通常直接绑定一个样式对象，这样使得模板更加清晰：

```html
<div v-bind:style="styleObject"></div>
```

```js
data:{
	styleObject:{
	color:'red',
	fontSize:'30px'
	}
}
```

同样的,我们也可以绑定数组：

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

从 2.3.0 起你可以为 `style` 绑定中的 property 提供一个包含多个值的数组：

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

>>>>>>> VueBasicLearning/main
这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 `display: flex`。