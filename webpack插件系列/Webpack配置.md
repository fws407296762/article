# Webpack 配置

如果需要将多个配置同时执行的时候，可以这么写：

	module.exports = [config_1,config_2];

## context

#### 说明

 这个是给 `entry` 选项的基本目录（绝对路径）。如果 设置了`output.pathinfo`，那么引入的路径信息就是 `entry` 的路径信息。

> 默认：process.cwd()

## entry

#### 说明

bundle 的入口点

如果你的属性值是一个字符串：这个字符串就应该是启动就加载的模块

如果你的属性值是一个数组：数组中所有的模块都是在启动的时候就加载。然后将最后一个 export.

	entry: ["./entry1", "./entry2"]

如果你的属性值是一个对象：将创建多个入口 bundles。属性名则就是文件名。属性值可以是字符串也可以是一个数组。

	{
	    entry: {
	        page1: "./page1",
	        page2: ["./entry1", "./entry2"]
	    },
	    output: {
	        filename: "[name].bundle.js"，
			filename: "[id].bundle.js",
			chunkFilename: "[id].bundle.js"
	    }
	}

`filename` 中必须有 **[name]** 或者 **[id]** ，不然只有 entry 中的第一个属性创建文件。

如果是 **[name]**,那么 **[name]** 会被 `entry` 中对应的 **属性名** 代替。
如果是 **[id]**, id 的计算方式是：最后一位是 0 ，然后递增到第一位。例如上面例子： `page2 = 0` , `page1 = 1`。

## output

#### 说明

处理编译的结果。 `ouput` 配置项用来处理编译后的文件。 `entry` 可以有多个，但是 `output` 配置只有一个！

> 上面一句话的意思就是：多个文件只能有一个输出口

### output.filename

指定输出的文件名。你不能指定一个绝对路径！`output.path` 指定文件存放的目录，`filename` 仅用于对文件进行命名。

##### 单个 entry

	{
	  entry: './src/app.js',
	  output: {
	    filename: 'bundle.js',
	    path: './built'
	  }
	}

	// 输出的文件目录: ./built/bundle.js

##### 多个 entry

如果你的配置生成了多个 "chunk" (通过多个 `entry` 或者 `CommonsChunkPlugin` 插件)，那么你就应该用 `[name]`、`[hash]`、`[chunkhash]` 代替文件名，确保文件名唯一。

`[name]` 文件名

`[hash]` 编译的 hash 值

`[chunkhash]` 文件的 hash 值
	
	{
	  entry: {
	    app: './src/app.js',
	    search: './src/search.js'
	  },
	  output: {
	    filename: '[name].js',
	    path: __dirname + '/built'
	  }
	}
	
	// 输出: ./built/app.js, ./built/search.js

### output.path

输出的目录必须是 **绝对路径**

`[hash]` 编译的 hash 值



## loader

## plugins



