title: webpack入门
date: 2016-10-08 15:56:23
tags: webpack
---

# webpack 入门

官网doc http://webpack.github.io/docs/tutorials/getting-started/

	npm install webpack -g

添加一个 webpack.conf.js

//webpack配置文件栗子

	module.exports =  {
        watch: true,
        entry: entry_file,
        debug: true,
        devtool: 'source-map',
        output: {
            path: path.resolve(process.cwd(),'dist/'),
            filename: '[name].js',
            chunkFilename: '[name].min.js',
            publicPath: path.resolve(process.cwd(),'buildPath/')
        },
        resolve: {
            alias: {
				    jquery: 'global/lib/jquery.js',
				    template: 'global/lib/template.js',
				    underscore: 'global/lib/underscore.js',
			  }
        },
        plugins: [
        	CopyWebpackPlugin...,
        	webpack.ProvidePlugin...,
        	webpack.optimize.DedupePlugin...,
        	ExtractTextPlugin...,
        	webpack.optimize.CommonsChunkPlugin,
        	WebpackNotifierPlugin...,
        	webpack.DllReferencePlugin...
        ],
        module: {
	        loaders: [{
                test: /\.js[x]?$/,
                exclude: /(node_modules)|(global\/lib\/)/,
                loader: 'babel-loader'
            },
            ...
            ]
        }
	}



## entry

	{
		page1: 'path/to/page1',
		page2: 'path/to/page2',
		common: ['jquery', 'react', 'react-dom']
	}

entry 的格式如上，指定了要导出3js文件。page1、2都对应一个入口文件。 common对应了几个文件。

最后输出 page1.js page2.js common.js。

## watch 是否watch文件变化

## debug 是否开启debug

### devtool 选择一个 devtool 来增强 debug
参考 https://segmentfault.com/a/1190000004280859

cheap-source-map 方便调试
然带上 eval 参数的可以快更多，但是这种 sourceMap 只能看，不能调试，得不偿失。



## output 文件输出的配置

> path:

输出文件的目录， 这里为 dist目录
> filename:

'[name].js' [name]对应enrty的key
> chunkFilename:

异步加载的文件 这里的[name]该文件在编译后对应的一个模块数字 生成如 1.min.js
> publicPath:

异步的加载文件对应的build目录

比如 imgae: url(./img/bg.png); 编译后则为 imgage: url(/buildPath/bg.png);

## resolve  配置模块路径alias

	{
	    jquery: 'global/lib/jquery.js',
	    template: 'global/lib/template.js',
	    underscore: 'global/lib/underscore.js',
   }

不详述。配置更多的alias可以方便代码书写，提高webpack寻找模块的速度，提高编译速度。

## plugins: 配置webpack插件
插件系列会新开篇章





## loaders: 配置 文件对应的loaders
也在插件篇章


## 调优
会新开优化篇章






