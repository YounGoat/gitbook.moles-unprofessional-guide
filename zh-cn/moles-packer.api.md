#	{{ book.PACKER }} 应用程序接口

除了支持命令行调用之外，{{ book.PACKER }} 也提供 Node.js 应用程序接口。

```javascript
var mp = require('moles-packer');

var options = {
	'input'         : './rn26',
	'entry'         : 'index.ios.js',
	'output'        : './build',
	'bundle'        : true,
	'common-bundle' : true
	};

var callback = function(err) {
    if (err) { /* 构建失败 */ }
    else { /* 构建成功 */ }
};

// 同时支持回调函数和 Promise。
var promise = mp(options, callback);

promise
    .then(function() { /* 构建成功 */ })
    .catch(function(err) { /* 构建失败 */ })
    ;
```

API 的方法参数与命令行参数兼容。
