#	开发套件之 {{ book.PACKER }}

{{ book.PACKER }} 是一个轻量级的 React Native 项目构建工具。利用这一工具，你可以基于一个标准的 React Native 项目输出多个 bundle 文件，包括公包（基础包）和一个或多个业务包。如果提供预制公包，那么你也可以基于一个经过精减（删除了 ios / android 目录及 node_modules 目录下被公包所依赖的模块）的项目，输出业务包。

{{ book.PACKER }} 是 {{ book.MOLES }} 工具链的关键环节之一，通过 [NPM](http://npmjs.com/) 发布，拥有两个版本：
*   __moles-packer__  
    普通版，提供 CLI 和 API；

*   __moles-packer-bin__  
    二进制版，只提供 CLI，安装迅速且不显式依赖其他模块。  
    适用于 Windows / Linux / Mac OS X 。

上述两个模块的 __版本号是同步的__ ，moles-packer-bin 是基于 moles-packer 的同版本编译的。

{{ book.PACKER }} 目前仍然是一个处于快速迭代中的项目。为了避免误导读者，__如有必要，章节起始处将标示当前内容适用的版本范围__ ，请读者多加留意。本书的相关内容可能 __滞后__ 或 __超前__ 于 {{ book.PACKER }} 的发布进度，超前部分将注明“拟适用版本”，其内容综合了网友的建议和笔者的预测，并不代表未来版本的正式承诺。如果未来发布的版本与本书不符，本书将在发布后短时间内予以更正，并将“拟适用版本”变更为“适用版本”。

（拟）适用版本的表示方法遵从语义化版本[范围（Ranges）语法](https://github.com/npm/node-semver#ranges)及[高级范围语法（Advanced Range Syntax）](https://github.com/npm/node-semver#advanced-range-syntax)。[^1]

[^1]: 语义化版本（Semantic Versioning）是由 GitHub 共同创办者 Tom Preston-Werner 所发起的软件版本号命名规范，[官方文档](http://semver.org)并不冗长，有[中文版](http://semver.org/lang/zh-CN/)。
