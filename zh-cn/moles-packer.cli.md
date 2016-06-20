#	Moles Packer 的命令调用

```bash
# 打印帮助信息。
moles-packer --help

# 在当前目录下执行构建。
moles-packer

# 针对指定目录执行构建，并将结果输出到指定文件。
moles-packer --input ./foo/bar/ --entry index.ios.js --output ./output
```

我们可以通过命令行参数指挥 ```moles-packer``` 完成简单的、目标明确的构建任务，也可以在项目中添加 ```.molesrc``` 配置文件，定义更加复杂的构建任务，请参考“[构建配置](moles-packer.spec.md)”一节。

配置文件中的配置项与命令行参数出现冲突时， __命令行参数的优先级高于配置文件。__

##	参数详解

*	__--bundle__ [&lt;bundle.js]  
	选项缺省值：```bundle.js```  
	将构建结果合并输出至一个文件，该文件将被保存在 ```--output``` 指定的目录中，文件名可以缺省。  
	bundle 文件中默认不包含公包模块，除非使用了 ```--standalone``` 选项。

*	__--common__ [&lt;common.js&gt;]  
	选项缺省值：```common.jsbundle```  
	将属于公包的模块合并输出至一个文件，该文件将被保存在 ```--output``` 指定的目录中，文件名可以缺省。  
	未添加该选项时，构建结果中不包含独立的公包文件。

*	__--dev__  
	保持输出代码的可读性，不对其进行压缩和混淆。

*	__--entry__ &lt;basename&gt;  
	默认值：```index.js```  
	指定入口文件名称。

*	__--exec-on-required__  
	当且仅当模块首次被实际使用时，执行其相应的定义程序。  
	对于基于 CMD 的 NodeJS 程序，这是不言而喻的事，但是为了兼容基于网络的应用场景（比如 web app）， Moles Packer 在构建过程中按照 AMD 模式改造了程序的模块化结构。标准的 AMD 模式在模块下载完成时执行其定义函数，对于那些互相耦合、顺序敏感的模块，这会导致代码的逻辑错误，在这种情形下，可以打开 ```--exec-on-required``` 开关，以使构建输出代码的逻辑忠实于源代码。

*	__--help__  
	打印帮助信息。  
	出现该参数时，其他所有参数将被忽略。

*	__-h__  
	等同于 ```--help``` 。

*	__--input__  &lt;path&gt;  
	默认值：```.```（当前目录）
	指定项目目录。  

*	__--output__ &lt;path&gt;  
	默认值：```./build```（当前目录的 build 子目录）  
	指定输出目录。  
	注意：如果该目录不存在，Moles Packer 会自动创建。 __如果该目录已经存在，其中的文件有可能被覆盖。__

*	__--standalone__  
	是否输出可独立运行的 bundle 文件。该选项必须配合 ```--bundle``` 选项使用，否则将被忽略。
