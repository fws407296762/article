# 注意要点

* 开发尽量使用ES2015，遵循CommonJS规范
* 切勿直接操作DOM，要操作数据
* 尽量使用Vue的语法糖，比如可以用:style代替v-bind:style；用@click代替v-on:click
* 刚开始使用Vue比较容易进入的一个误区，Vue是以数据驱动的，所以只用关系数据的变化即可，不是万不得已，千万不要主动操作DOM；
* 不要在JS里绑定跟业务相关的事件，业务事件及逻辑，应该在HTML上绑定。在JS里绑定事件应该用于使用了第三方的插件等场景，如果主动绑定了事件，记得在相关生命周期接触绑定以及销毁相关实例，比如在组件内创建了一个百度echarts，并加了一个定时器来更新数据，在组件销毁时，应该销毁这个echarts实例，并将定时器clear；

# 规范

1. **命名**。组件名称（包括路由组件）使用"-"来分割，比如persons-new-poi.vue，不推荐使用驼峰，详见 http://cn.vuejs.org/guide/components.html#资源命名约定
2. **事件**。在派发和广播事件时，事件的名称也使用"-"来分割，而且前缀为该组件的名称，**不能使用驼峰** ，例如当前组件为open-layer.vue，则事件名称为 **open-layer-close**

# 数据

## 不要将HTML的Attributes和Vue的Model混用
比如最终要实现的代码是：

	<img src=123.jpg>
	<a href="index.html?id=123"></a>
	<div id="id-123"></div>

比如Vue实例为：

	new Vue({
	    el: 'body',
	    data: {
	        id: 123
	    }
	})

错误 的使用是：
	
	<img src={{ id }}.jpg>
	<a href="index.html?id={{ id }}"></a>
	<div id="id-{{ id }}"></div>

正确 的使用是：

	<img :src=id + '.jpg'>
	<a :href="'index.html?id=' + id"></a>
	<div :id="'id-' + id "></div>

## class和style的使用

静态的class放在HTML的class特效内，而动态的应该使用:class，比如

	new Vue({
	    el: 'body',
	    data: {
	        list: [
	            {
	                name: '《HTML权威指南》',
	                is_read: 0
	            },
	                        {
	                name: '《深入浅出NodeJS》',
	                is_read: 1
	            },
	        ]
	    }
	})

	<div v-for="item in list" class="book_item" :class="{'off': !item.is_read}"></div>

# 在组件中使用第三方插件

比如组件初始化为：

	<style>
	 
	</style>
	<template>
	 
	</template>
	<script>
	    import echarts from 'echarts';
	    export default {
	        data () {
	            return {
	 
	            }
	        },
	        ready: {
	 
	        },
	        destroyed: {
	 
	        },
	        methods: {
	 
	        }
	    }
	</script>

要创建一个echarts实例，应该在ready里面完成，但代码可能很多而且需要拆分，所以可以在methods里定义许多方法，最终代码如下：

	<style></style>
	<template>
	    <div class="chart" v-el:dom-line></div>
	</template>
	<script>
	    import echarts from 'echarts';
	    import $ from 'jquery';
	 
	    export default {
	        data () {
	            return {
	                chartData: {}
	            }
	        },
	        ready: {
	            this.getData();
	        },
	        beforeDestroy: {
	            // 销毁定时器
	            if (this.chartTimeout) {
	                clearTimeout(this.chartTimeout);
	            }
	            // 销毁echart实例
	            if (this.myChart) {
	                this.myChart.dispose();
	            }
	        },
	        methods: {
	            initChart () {
	                if (!this.myChart) {
	                    this.myChart = echarts.init(this.$els.domLine);
	                }
	                var option = {
	                    // ...
	                }
	                this.myChart.setOption(option);
	            },
	            getData() {
	                var _this = this;
	                $.ajax({
	                    url: '',
	                    type: 'get',
	                    data: {},
	                    success (data) {
	                        // 每分钟更新一次数据
	                        _this.data = data;
	                        Vue.nextTick(function() {
	                            _this.initChart();
	                        });
	                        _this.chartTimeout = setTimeout(function() {
	                            _this.getData();
	                        }, 1000 * 60);
	                    }
	                })
	 
	            }
	        }
	    }
	</script>

# 资源的高度可复用

## 组件、指令、过滤器

为了使组件、自定义指令、自定义过滤器高度可复用，需要尽可能地将可复用的内容单独拆离，将 组件 放置在 components 目录内（如果需要，可以在components目录内再创建目录），将 自定义指令 放置在 directives 目录内（如果需要，可以在directives目录内再创建目），将自定义过滤器放置在 filters 目录内（如果需要，可以在filters目录内再创建目）。使用方法举例：

	<script>
	    import Picker from '../picker'
	    import Cell from '../cell'
	    import Popup from '../popup'
	    import Flexbox from '../flexbox'
	    import FlexboxItem from '../flexbox-item'
	 
	    import array2string from '../../filters/array2String'
	    import value2name from '../../filters/value2name'
	 
	    module.exports = {
	        components: {
	            Picker,
	            Cell,
	            Popup,
	            Flexbox,
	            FlexboxItem
	        },
	        filters: {
	            array2string,
	            value2name
	        },
	        data: function() {
	            return {}
	        },
	        methods: {
	 
	        }
	    }
	</script>

## CSS的使用

可将业务型的CSS代码单独写一个css文件，比如首页，index.css，放置在 styles 目录内（如果需要，可以在styles目录内再创建目录），在使用index.css的组件内，使用@import引入css，，对于局部的css，可以在style上增加scoped，举例:

	<style scoped>
	    @import('reset.css');
	    @import('index.css');
	    h1{
	        color: red;
	    }
	</style>
	<template>
	    <h1>title</h1>
	</template>
	<script>
	     
	</script>

功能型的css，建议和组件一起，不推荐拆离，比如一个通用的confirm确认框。

持续更新...