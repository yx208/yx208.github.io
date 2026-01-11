# JSDoc 编写 JavaScript 文档

## 简介

本文将介绍 JSDoc 的常用标签和最佳实践，帮助你编写清晰、专业的代码文档。

## 基础标签

### @param - 参数说明

用于描述函数的参数：

```javascript
/**
 * 计算两个数的和
 * @param {number} a - 第一个数字
 * @param {number} b - 第二个数字
 * @returns {number} 两数之和
 */
function add(a, b) {
  return a + b;
}
```

### @returns - 返回值说明

描述函数的返回值：

```javascript
/**
 * 获取用户名
 * @returns {string} 用户的全名
 */
function getUserName() {
  return 'John Doe';
}
```

### @type - 类型注解

为变量指定类型：

```javascript
/**
 * 用户年龄
 * @type {number}
 */
let userAge = 25;

/**
 * 配置对象
 * @type {{name: string, age: number}}
 */
const config = { name: 'Alice', age: 30 };
```

## @link - 创建文档链接

`@link` 标签是 JSDoc 中非常实用的功能，用于创建指向其他文档或 API 的链接，提高文档的关联性和可读性。

### 基本用法

在文档中引用其他类、方法或模块：

```javascript
/**
 * 查看 {@link MyClass} 获取更多信息
 */

/**
 * @see {@link MyClass}
 */
```

### 链接到特定方法或属性

使用不同的符号来表示不同类型的成员：

```javascript
/**
 * 类似于 {@link MyClass#myMethod}
 * 或者 {@link MyClass.staticMethod}
 * 或者 {@link MyClass~privateMethod}
 */
```

**符号说明：**
- `#` - 实例成员
- `.` - 静态成员  
- `~` - 内部/私有成员

### 自定义链接文本

使用竖线 `|` 分隔链接目标和显示文本：

```javascript
/**
 * 查看 {@link MyClass|这里} 获取详情
 * 或者：{@link MyClass|点击这里}
 */
```

### 链接到外部 URL

```javascript
/**
 * 更多信息请访问 {@link https://example.com}
 * 或带文本：{@link https://example.com|官方文档}
 */
```

### 实际应用示例

```javascript
/**
 * 用户管理类
 * @class
 */
class UserManager {
  /**
   * 获取用户信息
   * 内部使用 {@link UserManager#fetchFromDatabase|数据库查询}
   * @param {string} userId - 用户 ID
   * @returns {Promise<Object>} 用户对象，结构参考 {@link User}
   * @see {@link https://api.example.com/docs|API 文档}
   */
  async getUser(userId) {
    return await this.fetchFromDatabase(userId);
  }

  /**
   * 从数据库获取数据
   * 该方法被 {@link UserManager#getUser} 调用
   * @private
   * @param {string} id - 记录 ID
   * @returns {Promise<Object>}
   */
  async fetchFromDatabase(id) {
    // 实现代码
  }
}

/**
 * 用户数据模型
 * 通常由 {@link UserManager#getUser|UserManager.getUser()} 返回
 * @typedef {Object} User
 * @property {string} id - 用户 ID
 * @property {string} name - 用户名
 * @property {string} email - 邮箱地址
 */
```

## 其他常用标签

### @typedef - 自定义类型

定义可复用的类型：

```javascript
/**
 * 配置选项
 * @typedef {Object} Config
 * @property {string} host - 服务器地址
 * @property {number} port - 端口号
 * @property {boolean} [ssl=false] - 是否使用 SSL
 */

/**
 * 初始化服务器
 * @param {Config} config - 配置对象
 */
function initServer(config) {
  // 实现代码
}
```

### @example - 使用示例

提供代码使用示例：

```javascript
/**
 * 格式化日期
 * @param {Date} date - 日期对象
 * @param {string} format - 格式字符串
 * @returns {string} 格式化后的日期
 * @example
 * // 返回 "2024-01-15"
 * formatDate(new Date(), 'YYYY-MM-DD');
 * @example
 * // 返回 "15/01/2024"
 * formatDate(new Date(), 'DD/MM/YYYY');
 */
function formatDate(date, format) {
  // 实现代码
}
```

### @throws - 异常说明

描述函数可能抛出的异常：

```javascript
/**
 * 解析 JSON 字符串
 * @param {string} jsonString - JSON 字符串
 * @returns {Object} 解析后的对象
 * @throws {SyntaxError} 当 JSON 格式无效时
 */
function parseJSON(jsonString) {
  return JSON.parse(jsonString);
}
```

### @deprecated - 标记废弃

标记已废弃的 API：

```javascript
/**
 * 旧版本的用户认证方法
 * @deprecated 自 v2.0.0 起，请使用 {@link authenticateUser} 替代
 * @param {string} username
 * @param {string} password
 */
function login(username, password) {
  // 旧实现
}
```

## 最佳实践

### 1. 保持一致性

在整个项目中使用统一的文档风格和标签顺序：

```javascript
/**
 * [简短描述]
 * 
 * [详细描述]
 * 
 * @param {type} name - 描述
 * @returns {type} 描述
 * @throws {ErrorType} 描述
 * @example
 * // 示例代码
 * @see {@link RelatedFunction}
 */
```

### 2. 使用 TypeScript 类型

对于复杂类型，考虑使用 TypeScript 语法：

```javascript
/**
 * @param {Array<string>} items - 字符串数组
 * @param {{ name: string, age: number }} user - 用户对象
 * @returns {Promise<User[]>} 用户列表的 Promise
 */
```

### 3. 添加有意义的描述

不要只重复参数名，要说明参数的用途和限制：

```javascript
// ❌ 不好的例子
/**
 * @param {string} name - 名字
 */

// ✅ 好的例子
/**
 * @param {string} name - 用户的全名，长度不超过 100 个字符
 */
```

### 4. 标记可选参数

使用方括号标记可选参数：

```javascript
/**
 * 创建用户
 * @param {string} name - 用户名
 * @param {number} [age] - 年龄（可选）
 * @param {string} [email=''] - 邮箱地址（可选，默认空字符串）
 */
function createUser(name, age, email = '') {
  // 实现代码
}
```

## 生成文档

安装 JSDoc：

```bash
npm install -g jsdoc
```

生成文档：

```bash
jsdoc src/ -d docs/
```

配置文件 `jsdoc.json`：

```json
{
  "source": {
    "include": ["src"],
    "includePattern": ".+\\.js(doc|x)?$"
  },
  "opts": {
    "destination": "./docs",
    "recurse": true
  },
  "plugins": ["plugins/markdown"]
}
```

使用配置文件：

```bash
jsdoc -c jsdoc.json
```

## 总结

JSDoc 是提升 JavaScript 代码可维护性的重要工具。通过合理使用 `@link`、`@param`、`@returns` 等标签，可以创建清晰、专业的 API 文档。关键是保持一致性，提供有价值的信息，并定期维护文档与代码的同步。

## 参考资源

- [JSDoc 官方文档](https://jsdoc.app/)
- [TypeScript JSDoc 参考](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)
- [Google JavaScript 风格指南](https://google.github.io/styleguide/jsguide.html)
