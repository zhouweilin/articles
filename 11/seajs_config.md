#seajs quick guide
目录结构：
```
project
	|-- app
		|-- main.js
	|-- public
		|-- scripts
			|-- modules
				|-- seajs_moduleone.js
				|-- seajs_moduletwo.js
		|-- styles
		|-- sea.js
		|-- seajs.config.js
	|-- index.html		
```
###seajs.config.js
让项目保持一个统一的配置文件是非常有必要的，所以把config独立成一个js文件，提高项目的可维护性。seajs.config.js的配置如下
```javascript
seajs.config({
    base: './public',  //相当于requirejs 的 baseUrl
    paths: {      //设置路径，避免过长的路径
        modules: 'scripts/modules',
        styles: 'styles'
    },
    alias: { //设置别名，相当于requirejs的paths，可直接使用paths配置的路径名称 + 子目录或文件名，不需要js后缀
        modone: 'modules/seajs_modOne',
        modtwo: 'modules/seajs_modtwo'
    }
});
```
具体的[seajs](http://seajs.org/)的[config](https://github.com/seajs/seajs/issues/262)参数可以访问链接       
在index.html中`</body>`前引入sea.js和seajs.config.js
```html
<script type="text/javascript" src="public/sea.js"></script>
<script type="text/javascript" src="public/seajs.config.js"></script>
```
###模块书写规范
seajs的模块遵循统一的书写[规范(CMD)](https://github.com/seajs/seajs/issues/242)，一般来说一个模块就是一个文件，seajs_modone.js代码：
```javascript
define(function(require, exports, moudle){
	console.log('this is module one');
	
	exports.name = "zhou";
	exports.importModTwo = function(){
		require('modtwo');
	};
	
	//以上的模块对外接口与下面的写法一致
	//var obj = {
	//	name: 'zhou',
	//	importModTwo: function(){
	//		require('modtwo');
	//	}
	//};
	//module.exports = obj;
	
	//或者直接return接口
	//return obj;
});
```
`define(factory)` **factory**中的三个参数_require_、_exports_、_module_可酌情忽略，如果直接return对外接口，则可以不需要_exports_ 和 _module_，如果该模块没有依赖的模块，则可以忽略_require_。但一旦写参数，则必须要按照顺序根据固定的命名（require, exports, module），遵循[书写约定](https://github.com/seajs/seajs/issues/259)。写法可以根据跟人习惯而定，建议使用module.exports进行统一输出。     
seajs_modtwo.js中代码：
```javascript
define(function(require, exports, module){
    console.log('this is module two');
    module.exports = {
	    moduleName: 'module two'
    };
});
```
####使用seajs.use 在页面中引入本页面的入口文件
入口文件main.js
```javascript
define(function(require){
    var obj = require('modone'); //引入模块1，在这里并不会加载模块2，只有在模块1接口的方法调用后，才会加载模块2
    
    console.log(obj);  
    //上面运行的结果是模块1，通过exports返回的对外接口 
    //{name: 'zhou', importModTwo: function}

    obj.importModTwo(); //本句执行后，浏览器加载模块2，这是seajs与requirejs的一个主要区别
});
```
页面引入入口文件
```html
<script type="text/javascript" src="public/sea.js"></script>
<script type="text/javascript" src="public/seajs.config.js"></script>
<script type="text/javascript">
    //main.js的路径相对对于本页面
    seajs.use('./app/main');
</script>
```
这样就可以实现模块的复用，统一配置，各个页面有各自独立的入口文件。另外，官方提供了[css](https://github.com/seajs/seajs-css/blob/master/README.md)加载等一些不多的[插件](http://seajs.org/docs/#docs)

##seajs不足之处
由于各种原因，seajs已经渐渐被淘汰，这里列出以下几点不足：

*	只在国内流行了一段时间，并没有跟国际接轨，社区不够活跃，所以很难有发展
*	没有好的方式实现打包和自动化构建
*	没有像requirejs的shim一样解决非模块化的js文件的依赖问题

>对于seajs和requirejs的区别，可以参考seajs的作者在知乎上的回答[知乎------AMD 和 CMD 的区别有哪些？](https://www.zhihu.com/question/20351507)
