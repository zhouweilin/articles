目录结构如下：
```
projects    //把gulp和gulp的插件安装在工程根目录
    |-- node_modules      
    |-- gulp_test
		  |-- public
		  |-- source
			  |-- scripts
			      |-- test.js
		  |-- test.html
	      |-- gulpfile.js
    |-- package.json
    |-- README.md
```  

> 注：本文下面的内容，均以此结构为基础。如果没安装nodejs，请先安装[nodejs](https://nodejs.org/en/)
##gulp 安装 
* 安装gulp-cli
```
  $ npm install --global gulp-cli
```
* 	在projects根目录下，安装gulp devDependencies。如果没有package.json文件，可以先使用 `npm init`命令进行初始化
```
$ npm install --save-dev gulp
```
* 在gulp_test目录下create a gulpfile.js
```javascript
const gulp = require('gulp');

//新建一个default任务
gulp.task('default', function(){

//place code for your default task here

});

//其他任务，如：uglify
gulp.task('uglify', function(){
//code goes here
});
```
 当运行`default`任务的时候，直接命令行输入`gulp`命令就行。运行其他任务，则需在gulp后面加上相应的任务id，如`gulp uglify`。_gulp_的使用只需要掌握四个函数：
> 1. gulp.src(globs[, options])  ---- 指定文件
>  
>2.  gulp.dest(path[, options]) ---- 文件输出目录
>       
> 3. gulp.task(name[,deps],fn) ---- 创建task
>       
> 4. gulp.watch(glob[, opts], tasks) or gulp.watch(glob[, opts, cb])  ---- 监听文件改动，自动执行指定任务

具体参数的使用，可以查看[gulp中文网站的文档](http://www.gulpjs.com.cn/docs/api/)

##简单实例
      
###gulp结合[browser-sync](https://www.browsersync.io/)，自动刷新浏览器
* 在安装完gulp后，安装browser-sync
```
$ npm install browser-sync --save-dev
```
* gulpfile.js 代码
```javascript
const gulp = require('gulp'),
	  bs = require('browser-sync').create();

//创建简单的本地http服务器
gulp.task('bs', function(){
	bs.init({
		server: {
			baseDir: './',         // 指定服务器目录
			index: 'test.html'     // 指定访问localhost:3000默认打开的页面
		}
	});
});

gulp.task('watch', function(){
	gulp.watch('test.html', bs.reload);  //如果test.html改动，则自动刷新浏览器
});

gulp.task('default', ['bs', 'watch']);

```
在命令行中，进入到gulp_test目录，运行`gulp`命令，即可开启服务器，并监听test.html的改变并自动刷新页面

###uglify & watch  & sourcemap & browser-sync
* 安装`gulp-sourcemaps` `gulp-uglify`
```
$ npm install --save-dev gulp-sourcemaps gulp-uglify
```
* gulpfile.js code
```javascript
const gulp = require('gulp'),
	  bs = require('browser-sync').create(),
	  maps = require('gulp-sourcemaps'),
	  uglify= require('gulp-uglify');

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

gulp.task('watch', function(){
	gulp.watch('source/scripts/**/*.js', function(evt){
		gulp.src(evt.path, {
			base: 'source'    //配置base参数，以source为相对路径，在public路径下面，
							  //创建与source下面路径相同的子路径，否则会直接输出到指定的文件夹下，不会生成子目录
		})
		.pipe(maps.init())
		.pipe(uglify())
		.pipe(maps.write('./maps'))
		.pipe(gulp.dest('public'))
		.pipe(bs.stream());   //有改动，则刷新浏览器
	});
	gulp.watch('test.html', bs.reload);  //如果test.html改动，则自动刷新浏览器
});

gulp.task('default', ['bs', 'watch']);
```
 
 
