---
title: 生成npm包--vue
date: 2017-12-19 10:42:30
tags: vue
categories: vue
---

#### 介绍
###### 用vue-cli打包生成vue组件并且上传到npm上
#### 前期准备
	vue-cli
	npm账号
	webpack
###### 首先，用vue-cli生成vue项目，并修改如下所示
	{
	  "name": "picture-upload",
	  "version": "1.0.9",
	  "description": "This is a component for picture uploaded by base64",
	  "author": "zfl <1171067930@qq.com>",
	  "main": "dist/picture-upload.min.js",
	  "license": "MIT",
	  "private": false,
	  "scripts": {
	    "dev": "node build/dev-server.js",
	    "start": "node build/dev-server.js",
	    "build": "node build/build.js"
	  },
	  "dependencies": {
	    "vue": "^2.3.3",
	    "vue-router": "^2.6.0"
	  }
	  ...
	}
###### “private”: false 把这个包改成公用的
###### “main”: “dist/picture-upload.min.js”, 这里的配置意思是，别人用这个包 import PictureUpload from ‘picture-upload’; 时，引入的文件。可以看出，这个picture-upload最终需要打包出一个js文件，所以我们需要修改webpack.prod.config.js文件
	var webpackConfig = merge(baseWebpackConfig, {
	  module: {
	    rules: utils.styleLoaders({
	      sourceMap: config.build.productionSourceMap,
	      extract: true
	    })
	  },
	  devtool: config.build.productionSourceMap ? '#source-map' : false,
	  // output: {
	  //   path: config.build.assetsRoot,
	  //   filename: utils.assetsPath('js/[name].[chunkhash].js'),
	  //   chunkFilename: utils.assetsPath('js/[id].[chunkhash].js')
	  // },
	   output: {
	    path: config.bundle.assetsRoot,
	    publicPath: config.bundle.assetsPublicPath,
	    filename: 'picture-upload.min.js',
	    library: 'PictureUpload',
	    libraryTarget: 'umd'
	  },
	  plugins: [
	    new webpack.DefinePlugin({
	      'process.env': env
	    }),
	    new webpack.optimize.UglifyJsPlugin({
	      compress: {
	        warnings: false
	      },
	      sourceMap: true
	    }),
	    // extract css into its own file
	    new ExtractTextPlugin({
	      // filename: utils.assetsPath('css/[name].[contenthash].css')
	       filename: 'picture-upload.min.css'
	    }),
	    new OptimizeCSSPlugin()
	  ]
	})
	module.exports = webpackConfig
###### 主要是修改两个地方output里面的filename,就是打包生成的js文件的名字;library是引入时的模块名;extracttextpligin里的filename改成picture-upload.min.css;然后很多不需要的模块都注解掉了
###### 下面是config/index.js
	bundle: {
	    env: require('./prod.env'),
	    assetsRoot: path.resolve(__dirname, '../dist'),
	    assetsPublicPath: '/',
	    assetsSubDirectory: '/',
	    productionSourceMap: true,
	    productionGzip: false,
	    productionGzipExtensions: ['js', 'css'],
	    bundleAnalyzerReport: process.env.npm_config_report
	}
###### 在build的下面加上这段代码,上面webpack.prod.config.js里面output里面配置的都是bundle
###### 编写组件,组件编写完成后,在main.js里面加上,其他的注释掉与不注释掉都可以
	import PictureUpload from './components/PictureUpload.vue'
	export default PictureUpload
###### 最后执行npm run build
###### 发布到npm上
	npm login
	npm adduser
	npm publish
###### 使用在下面项目链接的readme里面有
<a href="https://github.com/zhangfuli/picture-upload">点击这里</a>

###### 注意:由于webpack最近的升级webpack组件用2.0写的在最新vue-cli生成的webpack3.0的项目中貌似不能用