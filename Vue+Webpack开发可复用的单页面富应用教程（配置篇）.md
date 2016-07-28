7月16日在斗鱼直播了一场关于Vue+Webpack的技术分享，对基于vue的组件化开发有了一个初步的在线demo演示，[点击查看直播录像](https://www.talkingcoder.com/article/live1)。本文则会在此基础上，对Webpack的安装和基础配置进行较全面的讲解。

本系列文章所讲解内容，依赖于https://github.com/icarusion/vue-vueRouter-webpack这套架构，开发者可以前往进行clone或下载（记得star哦）。在使用中有疑问或更好的优化方案，可在https://github.com/icarusion/vue-vueRouter-webpack/issues这里提交。

# 关于vue+vueRouter+webpack

[本项目](https://github.com/icarusion/vue-vueRouter-webpack)可以帮你快速搭建一个基于Vue的单页面富应用，目前已将开发环境和生产环境进行单独配置（webpack.dev.config.js和webpack.prod.config.js），当然也可根据需要自行添加灰度配置文件。目前的开发环境文件名使用默认命名（比如1.chunk.js），生产环境使用带hash值的命名（比如1.0891dcfed66cf89e6e98.chunk.js）,可根据自己需要修改,但不建议修改本地环境为带hash的。 入口的html文件由webpack生成，模板在src/template/index.html内,可自行修改。默认已将dist目录、生成的两个index文件加入忽略列表，使用者可根据自己项目来修改。

本项目默认将组件（components）、路由（routers）、自定义过滤器（filters）、自定义指令（directives）等单独拆成目录。在数据获取方面用户可以根据自己需要使用 [vue-resource](https://github.com/vuejs/vue-resource) 或者 [jQuery](https://github.com/jquery/jquery) 等，如果是大型项目，还可以使用 [vuex](https://github.com/vuejs/vuex) 来维护状态和数据。当然，这并不意味着本项目是最佳实践，所有的目录结构和webpack配置都可以根据自己需要来修改。

#先把项目跑起来

##准备工作

使用前需要确保你安装了最新版的 [Node.js](https://nodejs.org/en/) 和 [npm](https://www.npmjs.com/)。

全局安装 `webpack` 和 `webpack-dev-server`
	
	npm install webpack -g
	npm install webpack-dev-server -g

安装成功后，再安装所有依赖

	npm install

# 运行

**开发环境：**

首次运行需要执行 `init` 命令生成 html 入口文件，以后不用再执行

	npm run init

然后执行 `dev`，本地环境就可以跑起来了，默认端口是8080，如果有冲突请先kill

	npm run dev

**生产环境：**

直接执行build方法即可编译打包：

	npm run build

生产环境默认使用hash模式的路由，开发环境是History模式。直接打开index_prod.html即可访问生产环境。

# Webpack配置详解

**webpack.base.config.js** 为开发和生产环境通用的配置，**webpack.dev.config.js** 和 **webpack.prod.config.js** 分别是开发和生产环境的单独配置。下面一一介绍每个配置的作用。

	entry: {
	    main: './src/main',
	    vendors: ['vue', 'vue-router']
	}

`entry` 是入口的配置项，这里我们使用数组来定义了两个入口，`main` 是我们的主入口，主要是引入 `vue-router` 路由和 `app.vue` 入口组件。其中 app.vue 提供了路由的挂载元素，以及通用的组件，比如 header 和 footer 什么的。其实 vue 的路由也是组件了，跟普通的组件并没有什么特殊。

	output: {
	    path: path.join(__dirname, './dist'),
	    publicPath: '/dist/',
	    filename: '[name].js',
	    chunkFilename: '[name].[hash].js'
	}

`output` 是输出配置，在本项目中将其分发到了不同的环境里。`path` 是文件输出到本地的路径，`publicPath` 是文件的引用路径，比如开发环境可将其配置为 cdn 路径，`filename` 是主入口的文件名，`chunkFilename` 是每个路由编译后的文件名，其中 `[hash]` 是用来唯一标识文件，主要用来防止缓存。

	module: {
	    loaders: [
	        { test: /\.vue$/, loader: 'vue' },
	        { test: /\.js$/, loader: 'babel', exclude: /node_modules/ },
	        { test: /\.css$/, loader: 'style!css!autoprefixer'},
	        { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
	        { test: /\.(gif|jpg|png|woff|svg|eot|ttf)\??.*$/, loader: 'url-loader?limit=8192'},
	        { test: /\.(html|tpl)$/, loader: 'html-loader' }
	    ]
	}

`loaders` 就是 webpack 最强大的地方了，不同的 loaders 通过正则来对不同模块文件进行处理，比如我们的 vue-loader 来处理 .vue 格式的模块。需要特殊说明的是 url-loader，它会将小于 8kb 的图片、iconfont 字体都转为 base64 ，超过 8kb 的才会生成具体文件，当然这个参数我们也是可以配置的。另外需要说明的就是 webpack 编译完，最终我们需要的其实就是一个入口 html 和一个 dist 文件夹，所有的 css、js、图片、iconfont字体 以及 入口js 都会重命名后存在 dist 里，这也是为什么 webpack 特别擅长做 SPA 。

下面的配置就是单独对不同的 loader 的设置，比如 vue 的可以提取写在 `<style>` 里的 css 为单独的 css 文件，webpack 默认是以 js 方式动态创建 style 标签或 link 标签的形式引入 css 的，这样做 css 也是可以按需加载了，但其实项目中我们还是喜欢把 css 独立出来，所以需要单独配置，下文也有具体说明。

babel 很熟悉了吧，就是将 ES6 转 ES5 。

`resolve.extensions` 是对模块后缀名的简写，配置后，原本是 `require('./components/app.vue')` 可以简写为 `require('./components/app')`。

`resolve.alias` 是别名，配置后，比如原本是 `require('moment/min/moment-with-locales.min.js')` 可以简写为 `require('moment')`。

`plugins` 是一些插件，webpack 本身也自带了很多插件。在本项目中，我们在开发和生产环境中使用了不同的插件：

	new ExtractTextPlugin("[name].css",{ allChunks : true,resolve : ['modules'] })  // 开发
	new ExtractTextPlugin("[name].[hash].css",{ allChunks : true,resolve : ['modules'] })  // 生产

这两个插件就是用来提取 css 的，目前只能提取使用 @import 形式和 .vue 里面 style 内的 css。

	new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.js')  // 开发
	new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.[hash].js')  // 生产

这两个插件是提取 js 公共组件的，比如 vue,vue-router,jquery,echarts 等，我们可以在入口的 vendors 里进行配置。

	// 开发
	new HtmlWebpackPlugin({
	    filename: '../index.html',
	    template: './src/template/index.html',
	    inject: 'body'
	})
	// 生产
	new HtmlWebpackPlugin({
	    filename: '../index_prod.html',
	    template: './src/template/index.html',
	    inject: 'body'
	})

这两个插件依赖 `html-webpack-plugin`，是用来生产 html 的，其中 filename 是生产的文件路径和名称，template 是使用的模板，inject 是指将 js 放在 body 还是 head 里。生成的 index.html 是开发环境用的，index_prod.html 是生产环境用的，生产环境里引用的 css 和js都是使用 hash 命名的，而且进行了压缩。

	// 生产
	new webpack.optimize.UglifyJsPlugin({
	    compress: {
	        warnings: false
	    }
	})

这个插件在生产时使用，是将代码进行压缩。

	// 开发
	fs.open('./src/config/env.js', 'w', function (err, fd) {
	    var buf = 'module.exports = "development";';
	    fs.write(fd,buf,0,buf.length,0,function(err,written,buffer){});
	});

	// 生产
	fs.open('./src/config/env.js', 'w', function (err, fd) {
	    var buf = 'module.exports = "production";';
	    fs.write(fd,buf,0,buf.length,0,function(err,written,buffer){});
	});

这两个跟 webpack 无关了，主要是在编译前用 node 生成一个 env.js 的文件用来标明当前是开发还是生产环境。这样我们在一些 config.js 可以通过它来配置 ajax 的 API地址 和参数等等。

webpack 的配置就是这些，基本已经满足我们开发大型项目的需求了，当然这只是一些最基本的配置，如果你使用 babel 可以进一步对其进行配置，使用单元测试可以再配置 Karma 等等。

在下一篇中，我讲重点介绍关于 vue 组件化的一些内容。