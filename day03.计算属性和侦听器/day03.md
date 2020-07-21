# 计算属性 computed

> 计算属性可用于快速计算视图（View）中显示的属性。这些计算将被缓存，并且只在需要时更新

* 先来看一个简单操作
  - 我们做一个简单的计算，让计算后的数据是原数据的2倍

```
原数据：<input type="number" value="" v-model="num"/>
computed:<input  type="number" :value="computed_num"/>
```
```
const app=new Vue({
			el:'#app',
			data:{
				num:1
			},
			computed:{
				computed_num(){
					return this.num*2
				}
			}
		})
```

> 1. 计算属性可以依赖多个 Vue 实例的数据，只要其中任一数据变化，计算属性就会重新执行，视图也会更新
> 2. 计算属性可以依赖其他计算属性；
> 3. 计算属性不仅可以依赖当前 Vue 实例的数据，还可以依赖其他实例的数据

* 我们也可以在methods中定义方法来实现上面的内容
```
原数据：<input type="number" value="" v-model="num" @input="bindinput"/>
methods:<input type="number" :value="methods_num" />
```
```
bindinput(){
	this.methods_num=this.num*2
}
```

> 1. 页面中的方法： 如果是调用方法，只要页面重新渲染，方法就会重新执行；若不需要渲染，则不需要重新执行。
> 2. 计算属性：不管是否渲染，只要计算属性依赖的数据（缓存）未发生变化，就永远不变。
> 3. 假设我们有一个性能开销比较大的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 A。如果没有缓存，我们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用方法来替代。

[代码示例](https://github.com/yuan525/vue-notes/blob/master/day02/instruction/v-html.html)

# 侦听器 watch

* 上面那个例子我们也可以用watch来实现
```
原数据：<input type="number" value="" v-model="num"/>
watch:<input  type="number" :value="watch_num"/>
```
```
		const app=new Vue({
			el:'#app',
			data:{
				num:1,
				watch_num:1
			},
			watch:{
				num(){
					this.watch_num=this.num*2
				}				
			}
		})
```

* 还可以做一些改变，因为watch可以监听多个值
```
			原数据：<input type="number" value="" v-model="num"/>
			原数据2：<input type="number" value="" v-model="num2"/>
			watch:<input  type="number" :value="watch_num"/>
```
```
		const app=new Vue({
			el:'#app',
			data:{
				num:1,
				num2:1,
				watch_num:1
			},
			watch:{
				num(){
					this.watch_num=this.num*2
				},
				num2(){
					this.watch_num=this.num*10
				}
			}
		})
```

**那么计算属性computed和侦听属性watch有什么区别呢？**
> 1. 我们可以看到，计算属性computed一般书写为一个函数，返回一个值，这个值不在data中，而在computed中.
> 2. watch侦听data中的数据，所以watch的对象必须与所侦听的属性同名
> 2. 需要在数据变化时执行异步或开销较大的操作时，watch方式是最有用的