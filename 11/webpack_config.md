#webpack quick guide
目录结构如下：
```
projects    //把模块安装在这个根目录
    |-- node_modules      
    |-- gulp_test
		  |-- public
			  |-- bundle.js
		  |-- source
			  |-- scripts
			      |-- entry.js
			      |-- mod.js
			  |-- styles
				  |-- pulbic.scss
		  |-- test.html
	      |-- gulpfile.js
    |-- package.json
    |-- README.md
```  
###install
*	直接使用webpack的安装
	1. 首先全局安装[webpack](http://webpack.github.io/docs/tutorials/getting-started/)以便使用`webpack`命令
	
			$ npm install -g webpack 
		
	2. 在projects目录下安装 `webpack` 
	
		
			$ npm install --save-dev webpack
		
	
	3. 	安装webpack自带的server
			
			$ npm install webpack-dev-server -g
			
*	在gulp使用webpack-stream插件的安装

	在projects目录下安装[webpack-stream](https://www.npmjs.com/package/webpack-stream)
	
		$npm install webpack-stream --save-dev
		
###简单实例

这里实例以gulp插件`webpack-stream`的方式使用，其配置方式与直接使用webpack相差不大，只需将配置项简单的经过转换，放到webpack.config.js里面即可。webpack主要使用[loaders](http://webpack.github.io/docs/using-loaders.html) 和 [plugins](http://webpack.github.io/docs/using-plugins.html) 对模块进行处理，最终输出打包后的文件

#### [bable-loader](https://github.com/babel/babel-loader) & browser-sync & webpack watch 
1. install

		$ npm install babel-core babel-loader babel-preset-es2015 --save-dev
 
2. config
```javascript
const gulp = require('gulp'),
	  bs = require('browser-sync').create(),
	  webpack = require('webpack-stream');

//gulpfile.js里面的相对路径是以它所在的文件夹为基准的

//创建简单的本地http服务器
gulp.task('bs', function(){
	bs.init({
		server: {
			baseDir: './',         // 以当前目录为服务器根目录
			index: 'test.html'     // 指定访问localhost:3000默认打开的页面
		}
	});
});

gulp.task('wp', function(){
	//监听多个文件改动，第一个文件为入口文件
	gulp.src(['source/scripts/entry.js', 'source/scripts/*.js'])  
	.pipe(webpack({
		watch: true,   //webpack watch
		output: {
			filename: 'bundle.js'   //指定文件名，否则将自动生成hash文件名 或者 main.js
		},
		module: {
			loaders: [
				{   //babel loader 配置
					test: /\.js$/,
					loader: 'babel',
					query: {
						presets: ['es2015']
					}
				}
			]
		}
	}))
	.pipe(gulp.dest('public/scripts'))
	.pipe(bs.stream());  //webpack的watch模式同样可以重新触发刷新
});

gulp.task('default', ['bs', 'wp']);

```

### [sass-loader](https://www.npmjs.com/package/sass-loader)
* install 
		
		$ npm install --save-dev node-sass sass-loader style-loader css-loader
				
* loader config， 其他配置保持不变，在`wp` task增加如下配置
```javascript
gulp.task('wp', function(){
	gulp.src(['source/scripts/entry.js', 'source/scripts/*.js', 'source/styles/*.scss'])  //增加对scss文件的监听
	.pipe(webpack({
		watch: true,   //webpack watch
		output: {
			filename: 'bundle.js'   
		},
		module: {
			loaders: [
				{   
					test: /\.js$/,
					loader: 'babel',
					query: {
						presets: ['es2015']
					}
				},
				//sass-loder config
				{
					test: /\.scss$/,
					loaders: ['style', 'css', 'sass']
				}
			]
		}
	}))
	.pipe(gulp.dest('public/scripts'))
	.pipe(bs.stream());  
});

``` 
* html中的代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>webpack-test</title>
</head>
<body>
  <p>hello webpack</p>
  <script type="text/javascript" src="public/scripts/bundle.js"></script>
</body>
</html>
```
* js文件中的代码
```javascript
//entry.js
require('../styles/public.scss');

import {obj} from './mod';
console.log(obj);

//mod.js
let obj = {
    name: 'test',
    color: 'blue'
};

export {obj};
```
