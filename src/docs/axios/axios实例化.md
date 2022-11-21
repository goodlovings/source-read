阅读`./lib/axiso.js`源码

# 1、定位文件“导出（export）”
导出一般都在文件结尾：
```javascript
module.exports = axios;

// Allow use of default import syntax in TypeScript
module.exports.default = axios;
```

# 2、定位导出变量的定义位置
导出变量为`axios`，找出`axios`变量定义的代码位置：
```javascript
// Create the default instance to be exported
var axios = createInstance(defaults);
```
`axios`通过`createInstance`函数创建；

其中参数`defaults`为引入的变量:
```javascript
var defaults = require('./defaults');
```

# 3、createInstance（创建实例）
```javascript
/**
 * 创建一个axios实例
 * @param {Object} defaultConfig 当前实例默认的配置
 * @return {Axios} 一个新的axios实例
 */
function createInstance(defaultConfig) {
  // 实例化Axios
  var context = new Axios(defaultConfig);
  // 执行bind函数
  var instance = bind(Axios.prototype.request, context);

  // 执行extend函数：Copy axios.prototype to instance
  utils.extend(instance, Axios.prototype, context);

  // 执行extend函数 Copy context to instance
  utils.extend(instance, context);

  // Factory for creating new instances
  instance.create = function create(instanceConfig) {
    return createInstance(mergeConfig(defaultConfig, instanceConfig));
  };
  return instance;
}
```

## 3.1 defaults
```javascript
var defaults = require('./defaults');
```
## 3.2 Axios
```javascript
var Axios = require('./core/Axios');
```
## 3.3 bind
```javascript
var bind = require('./helpers/bind');
```
## 3.4 utils.extend
```javascript
var utils = require('./utils');
```
## 3.5 mergeConfig
```javascript
var mergeConfig = require('./core/mergeConfig');
```

# 4、定义axios实例的其他属性/方法
通过`axios.`实现，如：`axios.Axios = Axios;`等

# 附上源码
```javascript
'use strict';

var utils = require('./utils');
var bind = require('./helpers/bind');
var Axios = require('./core/Axios');
var mergeConfig = require('./core/mergeConfig');
var defaults = require('./defaults');
var formDataToJSON = require('./helpers/formDataToJSON');
/**
 * Create an instance of Axios
 *
 * @param {Object} defaultConfig The default config for the instance
 * @return {Axios} A new instance of Axios
 */
function createInstance(defaultConfig) {
  var context = new Axios(defaultConfig);
  var instance = bind(Axios.prototype.request, context);

  // Copy axios.prototype to instance
  utils.extend(instance, Axios.prototype, context);

  // Copy context to instance
  utils.extend(instance, context);

  // Factory for creating new instances
  instance.create = function create(instanceConfig) {
    return createInstance(mergeConfig(defaultConfig, instanceConfig));
  };

  return instance;
}

// Create the default instance to be exported
var axios = createInstance(defaults);

// Expose Axios class to allow class inheritance
axios.Axios = Axios;

// Expose Cancel & CancelToken
axios.CanceledError = require('./cancel/CanceledError');
axios.CancelToken = require('./cancel/CancelToken');
axios.isCancel = require('./cancel/isCancel');
axios.VERSION = require('./env/data').version;
axios.toFormData = require('./helpers/toFormData');

// Expose AxiosError class
axios.AxiosError = require('../lib/core/AxiosError');

// alias for CanceledError for backward compatibility
axios.Cancel = axios.CanceledError;

// Expose all/spread
axios.all = function all(promises) {
  return Promise.all(promises);
};
axios.spread = require('./helpers/spread');

// Expose isAxiosError
axios.isAxiosError = require('./helpers/isAxiosError');

axios.formToJSON = function(thing) {
  return formDataToJSON(utils.isHTMLForm(thing) ? new FormData(thing) : thing);
};

module.exports = axios;

// Allow use of default import syntax in TypeScript
module.exports.default = axios;

```