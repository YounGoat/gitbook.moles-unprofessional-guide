#   {{ book.PACKER }} 的命令调用
*   适用版本：>=0.3.0

自 0.3.0 版本起，参数有较大幅度的调整，如欲了解 0.2.1 及之前版本的参数，请查阅“[命令调用的早期格式](moles-packer.cli-0.1.9.md)”一节。

---

##	快速入门

{{ book.PACKER }} [安装](moles-packer.install.md)完成后，将创建两个命令：
*   ```moles-packer```  
    用于项目构建，可编译业务代码并将其打包，也可同时创建公包。

*   ```moles-packer-common```  
    仅用于创建公包及相应的元数据文件。

__注意__：v0.4.0 之前的版本中，一度曾使用命令名 ```moles-pack``` 和 ```moles-pack-common```。这种命令思路以动词替代命令名中的名词，固然有其合理性，但是违背了命令名与包名尽可能一致的惯例，极易引起混淆，对于使用者不友好，故后来的版本中又恢复了原先的名称。为了向上兼容，0.4.0 及稍后的几个版本仍保留了 ```moles-pack``` 和 ```moles-pack-common``` 作为正式命令的别名。

```bash
# 打印帮助信息。
moles-packer --help

# 显示版本。
moles-packer --version
```

我们先来完成一些准备工作：
```bash
# 创建一个 React Native 项目，如果已有项目，也可以跳过此步骤。
react-native init rn26

# 复制一个 React Native 项目并进行删减。
cp -R rn26 rn26-lite
rm -fr rn26-lite/android
rm -fr rn26-lite/ios
rm -fr rn26-lite/node_modules
# 如果是已有项目，请保留开发过程中自行安装的模块。
```

通过以下命令，你可以大致了解 {{ book.PACKER }} 的功能和使用方法：
```bash
# 针对 ./rn26 项目执行拆分和打包。
# 指定入口文件为 index.ios.js (即针对 iOS 版本)。
moles-pack \
    --input ./rn26 \
    --entry index.ios.js \
    --output ./build \
    --bundle

# 利用原生的 React Native 项目，生成公包及元数据文件。
moles-pack-common \
    --input ./rn26 \
    --output ./build \
    --platform ios \
# 完成后应在 ./build 目录下创建以下文件：
# 　　 ./build/moles.common/common.ios.jsbundle
# 　　 ./build/moles.common/common.ios.json

# 利用已创建的公包，针对精减版项目执行拆分和打包。
moles-pack \
    --input ./rn26-lite \
    --common ./build/common \
    --entry index.ios.js
    --output ./build2 \
    --bundle

# 不拆分公包，输出可独立执行的业务包文件。
moles-pack \
    --input ./rn26 \
    --entry index.ios.js \
    --output ./build \
    --standalone
```

我们可以通过命令行参数指挥 {{ book.PACKER }} 完成简单的、目标明确的构建任务，也可以在项目中添加 ```.molesrc``` 配置文件，定义更加复杂的构建任务，请参考“[构建配置](moles-packer.spec.md)”一节。

配置文件中的配置项与命令行参数出现冲突时， __命令行参数的优先级高于配置文件。__

<a name="options"></a>
##    参数详解

__缺省值__ 是指该选项缺省时，程序默认的选项值。__选项值缺省__ 是指该选项存在但未赋值时，程序默认的选项值。以 ```--bundle``` 选项为例：
```bash
moles-packer
# 此时，--bundle 选项值为其“缺省值”。

moles-packer --bundle
# 此时，--bundle 选项值为其“选项值缺省”值。
```
没有值的选项是开关量，缺省代表关闭，附加该参数则代表打开。

*   __--bundle__ [&lt;*bundle.js*&gt;]  

    指示将构建结果合并输出，该文件将被保存在 ```--output``` 指定的目录中：
    ```bash
    OUTPUT/index.ios.jsbundle       # 适用于 iOS 平台的业务包或可独立运行的程序包
    OUTPUT/index.android.jsbundle   # 适用于 Android 平台的业务包或或独立运行的程序包
    OUTPUT/index.jsbundle           # 跨平台的业务包
    ```
    注意：{{ book.PACKER }} 构建的可独立运行的程序包，必须是平台相关的，故其文件名中也包括平台名称。有关平台兼容性的设计，请参考“[构建输出的兼容性](moles-packer.bundle.compatibility.md)”一节。  
    bundle 文件中默认不包含公包模块，除非使用了 ```--standalone``` 选项。

*   __--common-input__ &lt;*path/to/existing/common/dir*&gt;  
    __缺省值__：```false```  
    __选项值缺省__：```moles.common```  

    指定公包目录，并在构建过程中使用该目录中的预制公包及其元数据文件。__如果选项值是相对路径__，{{ book.PACKER }} 会 __按以下优先级__ 检索：  
    　　__优先__：```PATH.JOIN(--input, --common-input)```  
    　　__其次__：```PATH.JOIN(CWD, --common-input)```  

    公包目录中应当存在以下文件组合之一：  
    ```bash
    # 组合一：跨平台公包
    [ common.ios.jsbundle     ] # iOS 平台公包文件  
    [ common.android.jsbundle ] # Android 平台公包文件  
      common.json               # 跨平台公包元数据文件  

    # 组合二：iOS 平台专用公包
    [ common.ios.jsbundle     ] # iOS 平台公包文件   
      common.ios.json           # iOS 平台公包元数据文件

    # 组合三：Android 平台专用公包
    [ common.android.jsbundle ] # Android 平台公包文件  
      common.android.json       # Android 平台公包元数据文件
    ```
    公包目录中的文件组合应当与 ```--platform``` 指定的平台匹配。如果不需要创建可独立运行的程序包（未开启 ```--standalone```），可以省略 ```.jsbundle``` 文件。

    __公包目录应由 {{ book.PACKER }} 创建，强烈建议不要对其进行修改。__

*   __--common-modules__ &lt;*module,names,seperated,by,comma*&gt;  
    __缺省值__: ```react,react-native```

    指定公包中所包含的模块，多个模块之间使用逗号分隔。被指定的模块及其依赖模块都将被纳入公包的范围，同时也被排除业务包之外。react 和 react-native 及其依赖模块、附属模块默认包含在公包内，且不能排除，因此自定义公包模块时，无须添加这两个模块。

*   __--common-output__ &lt;*path/to/export/common/bundle/and/meta*&gt;  
    __缺省值__：```moles.common```  

    指定公包输出目录。  
    当选项值是相对路径时，如果以 ```.``` 或 ```..``` 起始，则相对于当前工作目录；否则认为相对于 ```--output``` 选项所指定的输出目录。

*   __--dev__  
    开发模式下构建的代码，在运行时可以输出更多的调试信息。

*   __--entry__ &lt;*entry/file/basename*&gt;  
    __缺省值__：```index.js```  

    指定入口文件名称。

*   __--exec-on-required__  
    当且仅当模块首次被实际使用时，执行其相应的定义程序。  
    对于基于 CMD 的 NodeJS 程序，这是不言而喻的事，但是为了兼容基于网络的应用场景（比如 web app）， {{ book.PACKER }} 在构建过程中按照 AMD 模式改造了程序的模块化结构。标准的 AMD 模式在模块下载完成时执行其定义函数，对于那些互相耦合、顺序敏感的模块，这会导致代码的逻辑错误，在这种情形下，可以打开 ```--exec-on-required``` 开关，以使构建输出代码的逻辑忠实于源代码。

*   __--help__  
    打印帮助信息。  
    出现该参数时，其他所有参数将被忽略。

*   __-h__  
    等同于 ```--help``` 。

*   __--input__ &lt;*path/to/project/dir*&gt;  
    __缺省值__：```.```（当前目录）

    指定项目目录。  

*   __--minify__  
    默认情形下，{{ book.PACKER }} 将保持输出代码的可读性，不对其进行压缩和混淆。如需获得更小尺寸的构建结果，请打开 ```--minify``` 开关。

*   __--output__ &lt;*path/to/output/dir*&gt;  
    __缺省值__：```./build```（当前目录的 build 子目录）  

    指定输出目录。  
    注意：如果该目录不存在，{{ book.PACKER }} 会自动创建。 __如果该目录已经存在，其中的文件有可能被覆盖。__

*   __--platform__ *ios* | *android* | *cross*  
    __缺省值__: ```cross```

    指定构建输出结果适用的操作系统平台。  
    如果打包结果需要跨平台通用，开发人员须确保业务代码中没有引用平台相关的模块，否则请分别执行命令，以获取平台相关的业务包。

*   __--standalone__  
    是否输出可独立运行的 bundle 文件。  
    该选项应与 ```--bundle``` 和 ```--platform``` 选项配合使用。

##  与早期格式的差异

与 v0.2.0 相比，v0.3.0 的设计思路有所调整，对构建结果的跨平台兼容性予以了更多的关注，这种调整也体现在命令参数的增删之中。

[^1]: 参见“[构建输出的兼容性](moles-packer.bundle.compatibility.md)”一节。
