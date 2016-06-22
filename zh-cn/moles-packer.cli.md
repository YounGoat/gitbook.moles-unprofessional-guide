#	{{ book.PACKER }} 的命令调用

##	快速入门

{{ book.PACKER }} [安装](moles-packer.install.md)完成后，将创建两个命令：
*	```moles-pack```  
	用于项目构建，可编译业务代码并将其打包，也可同时创建公包。
	
*	```moles-pack-common```  
	仅用于创建公包及相应的元数据文件。

```bash
# 打印帮助信息。
moles-pack --help

# 在当前目录下执行构建。
moles-pack

# 针对指定目录执行构建，并将结果输出到指定文件。
moles-pack --input ./foo/bar/ --entry index.ios.js --output ./output

# 仅创建公包。
moles-pack-common
```

我们可以通过命令行参数指挥 {{ book.PACKER }} 完成简单的、目标明确的构建任务，也可以在项目中添加 ```.molesrc``` 配置文件，定义更加复杂的构建任务，请参考“[构建配置](moles-packer.spec.md)”一节。

配置文件中的配置项与命令行参数出现冲突时， __命令行参数的优先级高于配置文件。__

##	参数详解

__缺省值__ 是指该选项缺省时，程序默认的选项值。__选项值缺省__ 是指该选项存在但未赋值时，程序默认的选项值。以 ```--bundle``` 选项为例：
```bash
moles-packer
# 此时，--bundle 选项值为其“缺省值”。

moles-packer --bundle
# 此时，--bundle 选项值为其“选项值缺省”值。
```
没有值的选项是开关量，缺省代表关闭，附加该参数则代表打开。

*	__--bundle__ [&lt;*bundle.js*&gt;]  
	__缺省值__：```false```（即不执行代码合并）  
	__选项值缺省__：```index.jsbundle```  
	
	将构建结果合并输出至一个文件，该文件将被保存在 ```--output``` 指定的目录中，文件名可以缺省。  
	bundle 文件中默认不包含公包模块，除非使用了 ```--standalone``` 选项。

*	__--common__ &lt;*path/to/existing/common/jsbundle*&gt;  
	__缺省值__：```false```  
	__选项值缺省__：无  
	
	使用预生成的公包文件。__如果选项值是相对路径__，{{ book.PACKER }} 会 __按以下优先级__ 检索预生成文件：  
	　　__优先__：```PATH.JOIN(--input, --common)```  
	　　__其次__：```PATH.JOIN(CWD, --common)```  
	该选项应当与 ```--common-meta``` 选项配合使用。

*	__--common-bundle__ [&lt;*common.jsbundle*&gt;]  
	__缺省值__：```false```（即不创建公包文件）  
	__选项值缺省__：```common.jsbundle```  
	
	将属于公包的模块合并输出至一个文件，该文件将被保存在 ```--output``` 指定的目录中，文件名可以缺省。 {{ book.PACKER }} 在生成公包的同时，会在同一目录下生成一个与公包文件同名、后缀为 *.meta.json* 的元数据文件。
	
	```bash
	moles-packer --common-bundle common/index.js
	# 除了业务文件以外，将生成以下公包文件和元数据文件：
	# 　　./build/common/index.js
	# 　　./build/common/index.meta.json
	```
	未添加该选项时，构建结果中不包含独立的公包文件。

*	__--common-meta__ &lt;*path/to/existing/common/modules/json*&gt;  
	__缺省值__：```false```  
	__选项值缺省__：无，或根据 ```--common``` 选项值生成  
	
	使用预生成的公包元数据。如果指定了 ```--common``` 却未指定 ```--common-meta```，那么 {{ book.PACKER }} 会在前者指定文件所在目录下，检索同名且后缀为 *.meta.json* 的元数据文件。  
	该选项与 ```--common-bundle``` 选项不兼容。

*	__--dev__  
	保持输出代码的可读性，不对其进行压缩和混淆。

*	__--entry__ &lt;*entry/file/basename*&gt;  
	__缺省值__：```index.js```  

	指定入口文件名称。

*	__--exec-on-required__  
	当且仅当模块首次被实际使用时，执行其相应的定义程序。  
	对于基于 CMD 的 NodeJS 程序，这是不言而喻的事，但是为了兼容基于网络的应用场景（比如 web app）， {{ book.PACKER }} 在构建过程中按照 AMD 模式改造了程序的模块化结构。标准的 AMD 模式在模块下载完成时执行其定义函数，对于那些互相耦合、顺序敏感的模块，这会导致代码的逻辑错误，在这种情形下，可以打开 ```--exec-on-required``` 开关，以使构建输出代码的逻辑忠实于源代码。

*	__--help__  
	打印帮助信息。  
	出现该参数时，其他所有参数将被忽略。

*	__-h__  
	等同于 ```--help``` 。

*	__--input__ &lt;*path/to/project/dir*&gt;  
	__缺省值__：```.```（当前目录）

	指定项目目录。  

*	__--output__ &lt;*path/to/output/dir*&gt;  
	__缺省值__：```./build```（当前目录的 build 子目录）  
	
	指定输出目录。  
	注意：如果该目录不存在，{{ book.PACKER }} 会自动创建。 __如果该目录已经存在，其中的文件有可能被覆盖。__

*	__--standalone__  
	是否输出可独立运行的 bundle 文件。  
	该选项必须配合 ```--bundle``` 选项使用，否则将被忽略。
