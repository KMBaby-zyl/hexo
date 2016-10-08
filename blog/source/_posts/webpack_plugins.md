title: webpack插件 和 loader 篇
date: 2016-10-09
tags: webpack
---

# webpack插件 和 loader 篇


## 插件

webpack入门中 列出了下面这些插件

	plugins: [
        	CopyWebpackPlugin...,
        	webpack.ProvidePlugin...,
        	webpack.optimize.DedupePlugin...,
        	ExtractTextPlugin...,
        	webpack.optimize.CommonsChunkPlugin,
        	WebpackNotifierPlugin...,
        	webpack.DllReferencePlugin...
        ],


本篇则对它们做一个介绍

插件分文webpack内置插件 和 要外部插件。

内置插件通过 webpack.xxx来使用
外部插件要npm install来安装require

## CopyWebpackPlugin 拷贝资源插件

顾名思义 对资源文件进行拷贝黏贴

	CopyWebpackPlugin([
	        {
	            from :'html',
	            to:'html'
	        },
	        {
	            context: 'global/img',
	            from: '**/*',
	            to:'img/common'
	        },
	        {
	            from: 'img',
	            to:'img'
	        },
	        {
	            from :'global/lib/es5-shim-sham.js'
	        }
	])

from	定义要拷贝的源目录
to	定义要拷盘的目标目录
context 上下文
flatten	只拷贝文件不管文件夹	默认是false
ignore	忽略拷贝指定的文件	可以用模糊匹配
force	强制覆盖先前的插件	可选 默认false

## webpack.ProvidePlugin 全局挂载插件

	new webpack.ProvidePlugin({
	    $: "jquery",
	    jQuery: "jquery",
	    "window.jQuery": "jquery"
	}))

当模块使用这些变量的时候,wepback会自动加载 不用再去requre

## webpack.NoErrorsPlugin

不显示错误插件

## webpack.optimize.DedupePlugin

查找相等或近似的模块，避免在最终生成的文件中出现重复的模块

##	 webpack.optimize.UglifyJsPlugin 丑化js 混淆代码

## webpack.optimize.CommonsChunkPlugin 提取公共代码的插件

	new webpack.optimize.CommonsChunkPlugin('common.js')

当你的 webpack 构建任务中有多个入口文件，而这些文件都 require 了相同的模块，如果你不做任何事情，webpack 会为每个入口文件引入一份相同的模块，显然这样做，会使得相同模块变化时，所有引入的 entry 都需要一次 rebuild，造成了性能的浪费，CommonsChunkPlugin 可以将相同的模块提取出来单独打包，进而减小 rebuild 时的性能消耗。

## WebpackNotifierPlugin 编译完成后给一个Notifier提示

	new WebpackNotifierPlugin({
        title: 'Webpack 编译成功',
        contentImage: path.resolve(process.cwd(), './global/img/logo.png'),
        alwaysNotify: true
    }),

## webpack.DllReferencePlugin

除了正在开发的源代码之外，通常还会引入很多第三方 NPM 包，这些包我们不会进行修改，但是仍然需要在每次 build 的过程中消耗构建性能，那有没有什么办法可以减少这些消耗呢？DLLPlugin 就是一个解决方案，他通过前置这些依赖包的构建，来提高真正的 build 和 rebuild 的构建效率。

具体在 webpack优化中讲

## ExtractTextPlugin
独立出css样式

如果我们希望样式通过\<link\>引入，而不是放在 \<style\>标签内呢，即使这样做会多一个请求。

这个时候我们就要配合插件一起使用啦。

	var ExtractTextPlugin = require("extract-text-webpack-plugin");
	module.exports = {
	    // ...省略各种代码
	    module: {
	        loaders: [
	            {test: /\.js$/, loader: "babel"},
	            {test: /\.css$/, loader: ExtractTextPlugin.extract("style-loader", "css-loader")}
	        ]
	    },
	    plugins: [
	        new ExtractTextPlugin("[name].css")
	    ]
	}


## extract-text-webpack-plugin 提取样式插件
项目中没有使用

功能是 将css放到index.html的body上面


## 栗子

		plugins: [
            new CopyWebpackPlugin([
                    {
                        from :'html',
                        to:'html'
                    },
                    {
                        context: 'global/img',
                        from: '**/*',
                        to:'img/common'
                    },
                    {
                        from: 'img',
                        to:'img'
                    },
                    {
                        from :'global/lib/es5-shim-sham.js'
                    },
                    {
                        context :'global/lib/swfupload',
                        from: '**/*',
                        to:'swfupload'
                    }
            ]),
            new webpack.ProvidePlugin({
                $: 'jquery',
                jQuery: 'jquery',
                _: 'underscore',
                React: 'react',
                ReactDOM: 'react-dom'
            }),
            new webpack.optimize.DedupePlugin(),
            new WebpackNotifierPlugin({
                title: 'Webpack 编译成功',
                contentImage: path.resolve(process.cwd(), './global/img/logo.png'),
                alwaysNotify: true
            }),
            new ExtractTextPlugin("[name].css"),
            new webpack.optimize.CommonsChunkPlugin({
                name: 'common',
                minChunks: Infinity
            }),
            new webpack.DllReferencePlugin({
                context: __dirname,
                manifest: require('./lib/manifest.json')
            }),
        ]



## loader

loader 本身是一个javascript模块 需要通过npm install 对应的loader

如

	babel-loader ,
	css-loader,
	less-loader,
	sass-loader,
	file-loader,
	html-loader,
	url-loader,
	style-loader,
	json-loader

## js
	{
        test: /\.js[x]?$/,
        exclude: /(node_modules)|(global\/lib\/)/,
        include: '',
        loader: 'babel-loader'
    }

遇到 .js 和 .jsx 后缀的用 babel-loader来解析
跳过exclude中的文件。
包含include中的文件

## css等
style-loader 会将css代码打入html的style标签里
css-loader 会生成一个css文件

在栗子中， 我们用了 ExtractTextPlugin.extract 插件

ExtractTextPlugin的extract方法有两个参数，第一个参数是经过编译后通过style-loader单独提取出文件来，而第二个参数就是用来编译代码的loader。

	{
        test: /\.css$/,
        loader: ExtractTextPlugin.extract('style-loader', 'css-loader?sourceMap&-convertValues')
    },{
        test: /\.rcss$/,
        loader: ExtractTextPlugin.extract('style-loader', 'css-loader?modules&sourceMap&-convertValues!sass-loader?sourceMap')
    }, {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract('style-loader', 'css-loader?sourceMap&-convertValues!less-loader')
    }, {
        test: /\.scss$/,
        loader: ExtractTextPlugin.extract('style-loader', 'css-loader?sourceMap&-convertValues!sass-loader?sourceMap')
    }


css-loader!xxx-loader 表示先执行xxx-loader再css-loader

.rcss中 加了一个 modules 参数 代表开启 css modules

-convertValues 是为了解决css压缩有的一个坑。 css 压缩有会把 px 转化为 pc pt

详见 https://github.com/camsong/blog/issues/5?utm_source=tuicool&utm_medium=referral



## 图片loader 包括 各种其他文件
	{
	    test: /\.(png|jpg|gif|woff|woff2|ttf|eot|svg|swf)$/,
	    loader: "file-loader?name=[name]_[sha512:hash:base64:7].[ext]"
	}

输出 name_ + 7位base64编码 + 后缀


## json文件

	{
     test: /\.json$/,
     loader: "json"
    }


## 栗子

	module: {
            loaders: [{
                test: /\.js[x]?$/,
                exclude: /(node_modules)|(global\/lib\/)/,
                loader: 'babel-loader'
            }, {
                test: /\.css$/,
                loader: ExtractTextPlugin.extract('style-loader', 'css-loader?sourceMap&-convertValues')
            },{
                test: /\.rcss$/,
                loader: ExtractTextPlugin.extract('style-loader', 'css-loader?modules&sourceMap&-convertValues!sass-loader?sourceMap')
            }, {
                test: /\.less$/,
                loader: ExtractTextPlugin.extract('style-loader', 'css-loader?-convertValues!less-loader')
            }, {
                test: /\.scss$/,
                loader: ExtractTextPlugin.extract('style-loader', 'css-loader?sourceMap&-convertValues!sass-loader?sourceMap')
            }, {
                test: /\.(png|jpg|gif|woff|woff2|ttf|eot|svg|swf)$/,
                loader: "file-loader?name=[name]_[sha512:hash:base64:7].[ext]"
            }, {
                test: /\.html/,
                loader: "html-loader?" + JSON.stringify({
                    minimize: false,
                    attrs:false
                })
            },
                {
                 test: /\.json$/,
                 loader: "json"
                }
            ]
        }

