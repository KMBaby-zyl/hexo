# webpack 优化

## 0、调试
webpack --config file --colors --profile --display-modules

- --colors 输出结果带彩色，比如：会用红色显示耗时较长的步骤

- --profile 输出性能数据，可以看到每一步的耗时
- --display-modules 默认情况下 node_modules 下的模块会被隐藏，加上这个参数可以显示这些被隐藏的模块


## 1、添加 alise

主要是 node_modules 部分。 可以加快查询速度。 （感觉无所谓）

## 2、避免比不要的babel

对于很多的 npm 包来说，他们完全没有经过 babel 的必要（成熟的 npm 包会在发布前将自己 es5，甚至 es3 化），让这些包通过 babel 会带来巨大的性能负担，毕竟 babel6 要经过几十个插件的处理，虽然 babel-loader 强大，但能者多劳的这种保守的想法却使得 babel-loader 成为了整个构建的性能瓶颈。所以我们可以使用 exclude，大胆地屏蔽掉 npm 里的包，从而使整包的构建效率飞速提高。

	loaders: [{
        test: /\.js[x]?$/,
        exclude: /(node_modules)|(global\/lib\/)/,
        loader: 'babel-loader'
    },


## 3、 DllReferencePlugin

对于jquery，react这种第三方库重复编译会造成时间上的浪费。

所以我们把这些文件打包成 lib.js 文件 插件命名参考c++。  dll代表用来被引用的文件。

1、 创建一个 webpack.dll.conf.js

	var vendors = [
    'jquery',
    'underscore',
    'react',
    'react-dom',
	];

	module.exports = {
	    output: {
	        path: path.resolve(process.cwd(),'build/lib/'),
	        filename: '[name].js',
	        library: '[name]',
	    },
	    entry: {
	        "lib": vendors,
	    },
	    resolve: {
	        alias: ...
	    },
	    plugins: [
	        new webpack.DllPlugin({
	            path: path.resolve(process.cwd(),'build/lib/manifest.json'),
	            name: '[name]',
	            context: __dirname,
	        }),
	    ],
	};

我们把vendors中的文件打包为一个 lib.js

加入了 webpack.DllPlugin 插件

output 中增加了 library

运行后会在lib目录 生成一个 lib.js 和 manifest.json
lib.js 是编译后的文件
manifest.json是用来告诉项目的 webpack 相关模块对应lib.js。


在webpack.conf.js中增加

		 new webpack.DllReferencePlugin({
                context: __dirname,
                manifest: require('./lib/manifest.json')
            }),

完成后先编译一次dll 再 执行 项目编译。dll文件编译一次后除非变更否则不需要再编译。

对项目中的 common.js dll前后 做了一次对比

dll前 186秒
dll后 92秒

## 4、HappyPack
专门写个篇章


