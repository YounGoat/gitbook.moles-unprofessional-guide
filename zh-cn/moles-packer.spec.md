#	Moles Packer 的构建配置

除了通过命令行参数控制 Moles Packer 之外，还可以通过在 __项目根目录__ 中添加 .molesrc 配置文件约束其行为，我们称之为“构建配置”。命令行参数是一次性的，而 .molesrc 是项目的一部分。

##	构建配置的基本格式

基于可移植性和控制学习成本的考虑，Moles Packer 选择 JSON 作为构建配置文件的基本格式，并借鉴了 RequireJS 配置选项[^1]的若干理念。一个典型的构建配置文件大约是这样的：[^2]
```js
{
	// 配置格式版本
	"version" : "1.0",

	// 项目目录
	// 如果是相对路径，则相对于配置文件所在目录。
	"input" : "./rnDemo/",

	// 输出目录
	// 如果是相对路径，则相对于配置文件所在目录。
	"output" : "./build/",

	// 定义别名
	// 由一组键值对表示，key 代表别名，value 代表模块名或者脚本文件相对路径
	"paths" : {
		// value 值如果是一个相对路径，则相对于项目根目录。
		"M1" : "foo/bar/index.js",
		"M2" : "some-module"
	},

	// 公包信息
	"common" : {
		// 指定公包的输出文件名，默认为 common.js
		// 如果不希望输出公包，则将该值设为 false
		"output" : "common.js",

		// 指定公包所包含的模块名称（或脚本文件路径）。
		"include" : [ "moles-ui" ]
		// 公包默认包含 react, react-native 及其依赖模块，且不得排除，因此这里毋须复述。
	},

	// 合并文件
	"bundle" : [
		{
			"entry"   : "./index.ios.js",
			"output"  : "bundle.js",
			"exclude" : [ /* names of modules to be excluded */ ]
		}
	],

	// 以便于调试的方式输出构建结果。
	"dev" : true
}
```

##	配置选项：bundle
*	格式版本：1.0+
*	数据类型：字符串 / 数组
*	缺省配置：```bundle.js```



##	配置选项：common
*	格式版本：1.0+
*	数据类型：布尔值 / 字符串 / 对象
*	缺省配置：```false```

指定与公包有关的信息。
*	输出公包至默认位置，公包中仅包含默认模块：  
	```javascript
	{
		"common" : true,
		// ...
	}
	```
	公包的默认位置为 ```<output>/common.js```，默认包含 *react*、*react-native* 及其依赖模块。

*	输出公包至指定文件：
	```javascript
	{
		"common" : "common.bundle.js",
		// ...
	}
	```

*	指定公包所包含的模块：
	```javascript
	{
		"common" : {
			"include" : [ /* names of modules to be included */ ],
			"exclude" : [ /* names of modules to be excluded */ ]
		}
	}
	```
	1.	公包默认包含 *react*、*react-native* 及其依赖模块，且这些模块不能被排除；
	2.	如果模块被指定包含（include），那么它和它所依赖的模块都将被添加到公包中，除非被显式地排除（exclude）；
	3.	被指定包含的模块及其依赖模块，默认将被排队在业务 bundle 文件之外；
	4.	```exclude``` 子选项用于从公包中排除某些依赖模块。


##	配置选项：input
*	格式版本：1.0+
*	数据类型：字符串
*	缺省配置：```"."```

##	配置选项：output
*	格式版本：1.0+
*	数据类型：字符串
*	缺省配置：```"./build"```

##	配置选项：version
*	格式版本：所有版本
*	数据类型：字符串
*	缺省配置：```"1.0"```


[^1]: 如果读者希望了解 Require.JS 配置方法的细节，可阅读其官方文档：http://requirejs.org/docs/api.html#config 。
[^2]: JSON 格式不支持注释。示例中的伪配置代码包含注释，只是为了方便读者理解。真实的配置文件中不包含注释。
