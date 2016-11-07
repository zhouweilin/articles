###gulp 安装 
1. 安装gulp-cli
```
$ npm install --global gulp-cli
```
2. 在npm工程下，安装安装gulp devDependencies
```
$ npm install --save-dev gulp
```
3. 在app根目录下create a gulpfile.js
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
 
 
