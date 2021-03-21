# Template Syntax

## 插值

首先解释“Mustache”语法的文本插值，它体现在`{{}}`:

```html
<p> Notice:{{noticeMessage}} </p>
```

`{{noticeMessage}}`会被替换为对应数据对象上`noticeMessage`property的值，且会保持同步更新。

使用**v-once**指令可以实现一次性插值，而不会保持同步更新：

```html
<span v-onve>不会同步更新的插值:{{frozen}}</span>
```

使用**v-html**指令可以输出真正的HTML：

```html
const rawHtml="<p>这是一段html语句</p>"
<span v-html="rawHtml"></span> 
```

**注意防范XSS攻击**

> **Cross-site scripting** (**XSS**) is a type of security [vulnerability](https://en.wikipedia.org/wiki/Vulnerability_(computer_science)) typically found in [web applications](https://en.wikipedia.org/wiki/Web_application). XSS attacks enable attackers to [inject](https://en.wikipedia.org/wiki/Code_injection) [client-side scripts](https://en.wikipedia.org/wiki/Client-side_script) into web pages viewed by other users. 

对于HTML attribute ，Mustache语法无能为力，因此我们使用**v-bind**指令实现数据同步：

```html
<span v-bind:id="dynamicId"></span>
```

上述代码实现了HTML attribute的绑定，<u>需要注意的是在面对布尔类型的attribute时，v-bind工作起来会有些不同。</u>

使用Mustache语法和**v-bind**可以使用JS表达式插值：

```html
{{ number++ }}
{{ msg.split('').reverse().join('') }}
<div v-bind:"'name:'+username "></div>
```

<u>需要注意的是每个绑定都是单个表达式，且不能再模板表达式中试图访问用户定义的全局变量</u>

## 指令

指令的职责是，当其attribute的值发生改变时，将其产生地连带影响，同步地作用于DOM：

```html
<p v-if:"seen">seen的值决定了该标签是否被插入</p>
```

指令所接收的一个“参数”在指令之后以冒号表示：

```html
<a v-bind:href='url'>a标签的href attribute与表达式url的值绑定</a>
<a v-on:click="doSomething">监听DOM事件</a>
```

使用方括号`[]`括起来的js表达式会被视为一个**动态参数**：

```html
<a v-on:[eventName]="doSomething"> eventName此时是一个动态事件参数，当eventName的值为click时等效于上个代码块的v-on:click </a>
```

<u>需要注意的几点：</u>

1. 动态参数预期求出一个String，异常状态下value=null
2. 动态参数表达式中不应该出现空格或引号
3. 避免用大写字母命名键名，因为browser会将attribute名强制转化为小写

使用修饰符`.`告诉指令进行特殊的绑定：

```html
<form v-on:submit.prevent="onSubmit"> 对于触发的事件调用event.preventDefault() </form>	
```

## 缩写

**v-bind**的缩写

```html
<a :[arg]='url'> v-bind动态参数缩写 </a>
```

**v-on**的缩写

```html
<a @[event]='doSomething'> v-on动态参数缩写 </a>
```

>>>>>>> VueBasicLearning/main
