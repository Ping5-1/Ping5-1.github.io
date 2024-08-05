---
title: Lodash
published: 2022-07-01
tags: [Lodash,笔记]
category: 笔记
draft: false
---
# <font color="DarkSlateBlue"> lodash </font>
### 语言
##### <font color="DarkSlateBlue">`_.castArray(val)`</font>  
- 作用：强制转为数组
- 返回：转换后的数组(arr)

注：Array类型将为<u>浅拷贝</u>


```js
var array = [1, 2, 3];
console.log(_.castArray(arr) === array);	// => true
```

#### <font color="DarkSlateBlue">强制转数组：`_.castArray(val)` 与 `_.toArray(val)`</font>  

| 数据类型  | castArray()返回结果 | toArray()返回结果      |
| --------- | ------------------- | ---------------------- |
| 数字      | [数字]              | []                     |
| 对象      | [对象]              | [属性值1，属性值2,...] |
| 字符串    | [字符串]            | [字符1,字符2,...]      |
| null      | [null]              | []                     |
| undefined | [undefined]         |                        |
| 空        | []                  |                        |
| 数组      | 数组                |                        |



#### <font color="DarkSlateBlue">复制：</font>  

##### <font color="DarkSlateBlue">` _.clone(val) `与 `_.cloneDeep(val)` </font>  

-  支持 <u>arrays</u>、array buffers、 booleans、 <u>date objs</u>、<u>maps</u>、 numbers， <u>`obj` 对象</u>, regexes, sets, <u>strings</u>, symbols, typed arrays。
-   `arguments`对象的可枚举属性会拷贝为普通对象
- 一些不可拷贝的对象，例如error objs、<u>functions</u>, <u>DOM nodes</u>, 以及 WeakMaps 会返回空对象。

##### <font color="DarkSlateBlue">`_.cloneWith(val, [customizer])`</font>

- `customizer` (Function)： *(val [, index|key, obj, stack])*。
- `customizer` 返回 `undefined` 将会使用拷贝方法代替处理。

##### <font color="DarkSlateBlue">`_.conformsTo(obj, src)`</font>

- 检查 `obj` *(obj)*是否符合 `src` *(obj)*。
- 返回：*Boolean*  符合true否则false
- 当`src`偏应用时，这种方法和[`_.conforms`](https://www.lodashjs.com/docs/lodash.conformsTo#conforms)函数是等价的。



#### <font color="DarkSlateBlue">比较大小</font>

| 方法名        | 作用       | 注                                                           |
| ------------- | ---------- | ------------------------------------------------------------ |
| `_.eq(a, b)`  | a等于b     | 执行[`SamevalZero`](http://ecma-international.org/ecma-262/6.0/#sec-samevalzero) 比较两者的值，来确定它们是否相等。 |
| `_.gt(a, b)`  | a大于b     |                                                              |
| `_.gte(a, b)` | a大于等于b |                                                              |
| `_.lt(a, b)`  | a小于b     |                                                              |
| `_.lte(a, b)` | a小于等于b |                                                              |



#### <font color="DarkSlateBlue">判断类型</font>

| 方法名               | 作用                         | 注               |
| -------------------- | ---------------------------- | ---------------- |
| `_.isArguments(val)` | 是否是类 `arguments` 对象。  | 来自函数         |
| `_.isBoolean(val)`   |                              |                  |
| `_.isString(val)`    |                              |                  |
| `_.isDate(val)`      |                              | 只能判断new Date |
| `_.isBuffer(val)`    |                              |                  |
| `_.isLength(val)`    |                              |                  |
| `_.isFunction(val)`  |                              |                  |
| `_.isNative(val)`    | 是否是一个原生函数           |                  |
| `_.isSymbol(val)`    | 是否是原始 `Symbol` 或者对象 |                  |
| `_.isElement(val)`   | 是否是DOM 元素               |                  |
| `_.isRegExp(val)`    |                              |                  |



##### <font color="DarkSlateBlue">功能类型</font>

| 方法名                | 作用                                                         | 注                                                           |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `_.isEmpty(val)`      | 是否是空对象，集合，映射或者set。<br/> 非空：有枚举属性的对象，length 大于 0 的 arguments, array, string 或类jquery选择器。 | 类数组值，如`arguments`，array，buffer，string或者类jQuery集合的**`length` 为 `0`，判空**。<br/>map（映射）和set 的**`size` 为 `0`，判空**。<br/>对象如果被认为为空，那么他们没有自己的可枚举属性的对象。 |
| `_.isEqual(val, oth)` | 执行**深比较**来确定两者的值是否相等。                       | 这个方法支持比较 <u>arrays</u>, array buffers, booleans, <u>date对象</u>, error对象, <u>maps</u>, numbers,<br/> <u>`Object` 对象</u>, regexes, sets, strings, symbols,  typed arrays. <br/>`Object` 对象只比较自身的属性，**不**包括继承的和可枚举的属性。<br/> **不**支持函数和DOM节点比较。<br/>`_.isEqualWith(val, oth, [customizer])` |
| `_.isError(val)`      | 是否是 `Error`, `EvalError`, `RangeError`, `ReferenceError`,`SyntaxError`, `TypeError`, 或者 `URIError`对象。 |                                                              |
| `_.isFinite(val)`     | 是否是原始有限数值。                                         |                                                              |
| `_.isMatch(obj, src)` | 执行深比较**，来确定 `object` 是否含有和 `source` 完全相等的属性值。 | `_.isMatchWith(obj, src, [customizer])`                      |

isEmpty判空：null、Boolean类型、数字

```js
_.isEmpty(null);	// => true
_.isEmpty(true);	// => true 
_.isEmpty(1);		// => true
```



##### <font color="DarkSlateBlue">Array类型</font>

| 方法名                  | 作用                                                         | 注   |
| ----------------------- | ------------------------------------------------------------ | ---- |
| `_.isArray(val)`        |                                                              |      |
| `_.isArrayBuffer(val)`  |                                                              |      |
| `_.isArrayLike(val)`    | 是否是类数组。 若是类数组，则val不可能是函数，并且`val.length`是个整数，≥ `0`，≤ `Number.MAX_SAFE_INTEGER`。 |      |
| `_.isArrayLikeobj(val)` | 是否是 类数组且是对象。                                      |      |
| `_.isTypedArray(val)`   | 是否是typed array，如new Uint8Array                          |      |



##### <font color="DarkSlateBlue">数字类型</font>

| 方法名                 | 作用                                                      | 注                            |
| ---------------------- | --------------------------------------------------------- | ----------------------------- |
| `_.isInteger(val)`     |                                                           |                               |
| `_.isNumber(val)`      | 是否是原始`Number`数值型 或者 对象。                      | _.isNumber('3');*// => false* |
| `_.isSafeInteger(val)` | 是否是安全整数。 即，符合 IEEE-754 标准的非双精度浮点数。 |                               |



##### <font color="DarkSlateBlue">Map和Set</font>

| 方法名             | 作用 | 注                     |
| ------------------ | ---- | ---------------------- |
| `_.isMap(val)`     |      | WeakMap与Map是两种类型 |
| `_.isWeakMap(val)` |      | WeakMap与Map是两种类型 |
| `_.isSet(val)`     |      |                        |
| `_.isWeakSet(val)` |      |                        |



##### <font color="DarkSlateBlue">对象类型</font>

| 方法名                 | 作用                                                         | 注   |
| ---------------------- | ------------------------------------------------------------ | ---- |
| `_.isObject(val)`      | 是否为 `Object` 的[language type](http://www.ecma-international.org/ecma-262/6.0/#sec-ecmascript-language-types)。 *(例如： arrays, functions, objects, regexes,`new Number(0)`, 以及 `new String('')`)* |      |
| `_.isObjectLike(val)`  | 是否是 类对象。非 `null`，且 `typeof` 结果是 "object"。      |      |
| `_.isPlainObject(val)` | 是否是**普通对象**。即该对象由 `Object` 构造函数创建，或者 `[[Prototype]]` 为 `null` 。 |      |



##### <font color="DarkSlateBlue">判空值</font>

| 方法名               | 作用                             | 注                                      |
| -------------------- | -------------------------------- | --------------------------------------- |
| `_.isNaN(val)`       | 是否是 `NaN`。                   | NaN，new Number(NaN)，undefined均为true |
| `_.isNil(val)`       | 是否是 `null` 或者 `undefined`。 |                                         |
| `_.isNull(val)`      | 是否是 `null`。                  |                                         |
| `_.isUndefined(val)` | 是否是 `undefined`.              | null返回false                           |



#### <font color="DarkSlateBlue">类型转换</font>

| 方法名                 | 作用           | 注                                                           |
| ---------------------- | -------------- | ------------------------------------------------------------ |
| `_.toArray(val)`       |                | 对象取值，字符串取字符，数字和null为空                       |
| `_.toInteger(val)`     |                |                                                              |
| `_.toSafeInteger(val)` |                |                                                              |
| `_.toNumber(val)`      |                |                                                              |
| `_.toString(val)`      |                | `null` 和 `undefined` 将返回“”。`-0` 将被转换为字符串`"-0"`。 |
| `_.toLength(val)`      |                |                                                              |
| `_.toPlainobj(val)`    | 转为普通对象。 | 包括继承的可枚举属性。                                       |
| `_.toFinite(val)`      | 转为有限数字。 |                                                              |

```js
_.toArray({ 'a': 1, 'b': 2 });	// => [1, 2]
_.toArray('abc');				// => ['a', 'b', 'c']
_.toArray(1);					// => []
_.toArray(null);				// => []
```



### 数学与数字

##### <font color="DarkSlateBlue">数学计算</font>

| 方法名|   作用   |   注   |
| ------------------------------------------------------ | ---- | ---- |
| `_.add(a, b)` | 两数之和 | a+b |
| `_.subtract(a, b)` | 两数之差 | a-b |
| `_.multiply(a, b)` | 两数之积 | a*b |
| `_.divide(a, b)` | 两数之商 | a/b |
| `_.ceil(number, [precision=0])` | 向上舍入 | `precision`（精度）可以理解为保留几位小数。 |
| `_.round(number, [precision=0])` | 四舍五入 | |
| `_.floor(number, [precision=0])` | 向下舍入 |      |
| `_.max(arr)` | 取大 | `_.maxBy(arr, [iter=_.identity])` |
| `_.min(arr)` | 取小 | `_.minBy(arr, [iter=_.identity])` |
| `_.sum(arr)` | 求和 | `_.sumBy(arr, [iter=_.identity])` |
| `_.mean(arr)` | 平均 | `_.meanBy(arr, [iter=_.identity])` |

关于精度：

```js
_.ceil(4.006);		// => 5
_.ceil(6.004, 2);	// => 6.01
_.ceil(6040, -2);	// => 6100
```

关于迭代器：iter*(Function)*: 调用每个元素的迭代函数。

```js
var objects = [{ 'n': 1 }, { 'n': 2 }];
 
_.maxBy(objects, function(o) { return o.n; });		// => { 'n': 2 }
_.maxBy(objects, 'n');								// => { 'n': 2 }
```



##### <font color="DarkSlateBlue">数字</font>

| 方法名                                       | 作用                                                         | 注   |
| -------------------------------------------- | ------------------------------------------------------------ | ---- |
| `_.clamp(num, [lower], upper)`          | **返回限制**在 [`lower` , `upper` ]区间的值。      | 返回num经限制后所得到的值 |
| `_.inRange(num, [start=0], end)`       | **检查** `n` 是否在 [`start` , `end` )区间 | 若 `start` 大于 `end`，那么参数会交换以便支持负范围。 |
| `_.random([lower=0], [upper=1], [floating])` | **产生**[`lower` , `upper` ]区间内的随机值。 |若只提供一个参数，返回一个`0`到提供数之间的数。|

注：[]表示可选



### 对象

##### <font color="DarkSlateBlue">复制、合并、转换</font>

| 方法名                                               | 作用                                                         | 注                                                           |
| ---------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `_.assign(obj, [srcs])`                              | 分配来源对象的**可枚举属性**到目标对象上。<br/>源对象从左到右分配，后面对象的属性会覆盖前面对象的属性。 | 会改变 `obj`，参考自[`obj.assign`](https://mdn.io/obj/assign). |
| `_.assignIn(obj, [srcs])`                            | 会遍历并继承来源对象的属性。                                 | 会改变 `obj`。<br/>`_.assignInWith(obj, srcs, [customizer])`<br/> `customizer` 参数： *(objVal, srcval, key, obj, src)*。 |
| `_.merge(obj, [srcs])`                               | 递归合并 `srcs` 对象<u>自身和继承的</u>可枚举属性到 `obj` 目标对象。<br/>**若目标值存在，被解析为`undefined`的`srcs` 来源对象属性将被跳过**。<br/>**数组和普通对象会递归合并，其他对象和值会被直接分配覆盖。**<br/>源对象从左到右分配，后面对象的属性会覆盖前面对象的属性。 | `_.mergeWith(obj, srcs, customizer)`<br/>`customizer`参数：*(objVal, srcval, key, obj, src, <u>stack</u>)*。 |
| `_.transform(obj, [iter=_.identity], [accumulator])` | `_.reduce`的替代方法；此方法将转换`obj`对象为一个新的`accumulator`对象，结果来自`iter`处理自身可枚举的属性。<br/> 每次调用可能会改变 `accumulator` 对象。若不提供`accumulator`，将使用与`[Prototype]`相同的新对象。 | `iter`参数：*(accumulator, val, key, obj)*。若返回 `false`，`iter` 会提前退出。 |



##### <font color="DarkSlateBlue">根据键值对创建</font>


| 方法名                               | 作用                                                         | 注                                                           |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `_.at(obj, [paths])`                 | 创建数组，值来自 `obj` 的`paths`路径相应的值。               |                                                              |
| `_.create(prototype, [properties])`  | 创建继承 `prototype` 的对象。<br/> 若提供了 `prototype`，它的可枚举属性会被分配到创建的对象上。 |                                                              |
| `_.toPairs(obj)`                     | 创建`obj`对象自身可枚举属性的键值对数组。<br/>这个数组可以通过`_.fromPairs`撤回。<br/>若`obj` 是 map 或 set，返回其条目。 | `_.toPairsIn(obj)`同`_.toPairs(obj)`，创建`obj`对象自身和<u>继承的</u>可枚举属性的键值对数组。 |
| `_.fromPairs(pairs)`                 | 与`_.toPairs`相反；返回由键值对`pairs`构成的对象。           |                                                              |
| `_.value(obj)`                       | 创建 `obj` 自身可枚举属性的值为数组。                        | 非对象的值会强制转换为对象。`_.valuesIn(obj)：`创建 `obj` 自身和<u>继承的</u>可枚举属性的值为数组 |
| `_.invert(obj)`                      | 创建`obj`键值倒置后的对象。 若 `obj` 有重复的值，后面的值会覆盖前面的值。 |                                                              |
| `_.invertBy(obj, [iter=_.identity])` | 倒置对象 是 `collection`（集合）中的每个元素经过 `iter`（迭代函数） 处理后返回的结果。每个反转键相应反转的值是一个负责生成反转值key的数组。`iter` 会传入3个参数：*(val)* 。 |                                                              |



##### <font color="DarkSlateBlue">根据key创建</font>

| 方法名                               | 作用                                                         | 注                             |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------ |
| `_.keys(obj)`                        | 创建 `obj` 的自身可枚举属性名为数组。                        | 非对象的值会被强制转换为对象。 |
| `_.keysIn(obj)`                      | 创建 `obj` 自身 和 继承的可枚举属性名为数组。                | 非对象的值会被强制转换为对象。 |
| `_.mapValue(obj, [iter=_.identity])` | 创建对象，这个对象的key与`obj`对象相同，值是通过 `iter` 运行 `obj` 中每个自身可枚举属性名字符串产生的。 `iter`调用三个参数： *(val, key, obj)*。 |                                |
| `_.mapKeys(obj, [iter=_.identity])`  | 反向版`_.mapValues`。 这个方法创建对象，对象的值与`obj`相同，并且 key 是通过 `iter` 运行 `obj` 中每个自身可枚举属性名字符串 产生的。`iter`调用三个参数： *(val, key, obj)*。 |                                |



##### <font color="DarkSlateBlue">与初始化</font>


| 方法名                    | 作用                                                         | 注                                                           |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `_.functions(obj)`        | 创建函数属性名称的数组，函数属性名称来自`obj`对象自身可枚举属性。 | `_.functionsIn(obj)`函数属性名称来自`obj`对象自身和继承的可枚举属性。 |
| `_.defaults(obj, [srcs])` | 分配来源对象的可枚举属性到目标对象所有解析为 `undefined` 的属性上。<br/> 来源对象从左到右应用。 一旦设置了相同属性的值，**后续的将被忽略掉。** | 会改变 `obj`.<br/>`_.defaultsDeep(obj, [srcs])`会递归分配默认属性。 |



##### <font color="DarkSlateBlue">查找</font>


| 方法名                                 | 作用                                                         | 注   |
| -------------------------------------- | ------------------------------------------------------------ | ---- |
| `_.findKey(obj, [pre=_.identity])`     | 类似`_.find` 。 返回最先被 `pre` 判断为真值的元素 **key**，而不是元素本身。 |      |
| `_.findLastKey(obj, [pre=_.identity])` | 反方向开始遍历。                                             |      |



##### <font color="DarkSlateBlue">遍历</font>

| 方法名                             | 作用                                                  | 注                                                         |
| ---------------------------------- | ----------------------------------------------------- | ---------------------------------------------------------- |
| `_.forIn(obj, [iter=_.identity])`  | 使用 `iter` 遍历对象的<u>自身和继承</u>的可枚举属性。 | `_.forInRight`反方向开始遍历`obj`                          |
| `_.forOwn(obj, [iter=_.identity])` | 使用 `iter` 遍历<u>自身的</u>可枚举属性。             | `_.forOwnRight(obj, [iter=_.identity])`反方向开始遍历`obj` |

注:`iter` 参数：*(val, key, obj)*。若返回 `false`，`iter` 会提前退出遍历。



##### <font color="DarkSlateBlue">指定路径：取值、赋值、调用方法、查属性、删除属性</font>

| 方法名                              | 作用                                                         | 注                                                           |
| ----------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `_.get(obj, path, [defaultval])`    | **取值**。 若解析 val 是 `undefined` 会以 `defaultval` 取代。 |                                                              |
| `_.result(obj, path, [defaultVal])` | 类似`_.get`，若解析到的值是一个**函数**的话，就<u>绑定 `this` 到这个函数并返回执行后的结果</u>。 |                                                              |
| `_.set(obj, path, val)`             | **设值**。若`path`不存在，则创建。 缺少的索引属性会创建为数组，而缺少的属性会创建为对象。 | `_.setWith(obj, path, val, [customizer])`                    |
| `_.update(obj, path, updater)`      | 该方法类似`_.set`，除了接受`updater`以生成要设置的值。       | 会改变 `obj`<br/>`_.updateWith(obj, path, updater, [customizer])` |
| `_.invoke(obj, path, [args])`       | **调用**`obj`对象`path`上的方法。                            | `obj` *(Object)*: 要检索的对象。 `path` *(Array|string)*: 用来调用的方法路径。 `[args]` *(...\*)*: 调用的方法的参数。 |
| `_.has(obj, path)`                  | **检查** `path` 是否是`obj`对象的直接属性。                  | `_.hasIn(obj, path)`检查 `path` 是否是`obj`对象的**直接或继承**属性。 |
| `_.unset(obj, path)`                | **移除**`obj`对象 `path` 路径上的属性。                      |                                                              |

**result**

```js
var object = { 'a': [{ 'b': { 'c1': 3, 'c2': _.constant(4) } }] };
 
_.result(object, 'a[0].b.c1');
// => 3
 
_.result(object, 'a[0].b.c3', 'default');
// => 'default'
 
_.result(object, 'a[0].b.c3', _.constant('default'));
// => 'default'

```

**invoke**

```js
var object = { 'a': [{ 'b': { 'c': [1, 2, 3, 4] } }] };
 
_.invoke(object, 'a[0].b.c.slice', 1, 3);
// => [2, 3]

```

**updateWith**

```js
var object = {};
 
_.updateWith(object, '[0][1]', _.constant('a'), Object);
// => { '0': { '1': 'a' } }

```



##### <font color="DarkSlateBlue">取/删部分属性</font>

| 方法名                            | 作用                                                         | 注   |
| --------------------------------- | ------------------------------------------------------------ | ---- |
| `_.pick(obj, [props])`            | 创建从 `obj` 中选中的属性的对象。                            |      |
| `_.pickBy(obj, [pre=_.identity])` | 创建对象，取`obj` 中经 `pre` 判断为真值的属性。              |      |
| `_.omit(obj, [props])`            | 忽略/删除`obj`对象的属性。反向版`_.pick`，对象由忽略属性之外的`obj`<u>自身和继承的</u>可枚举属性组成。 |      |
| `_.omitBy(obj, [pre=_.identity])` | 忽略`pre`（断言函数）为真值的属性，返回余下的自身和继承的可枚举属性。 |      |

注： `pre`调用2个参数：*(val, key)*。



**pick：**拿到data，取部分有用值

```js
var object = { 'a': 1, 'b': '2', 'c': 3 };
 
_.pick(object, ['a', 'c']);		// => { 'a': 1, 'c': 3 }
_.pickBy(object, _.isNumber);	// => { 'a': 1, 'c': 3 }
```

**omit：**提交数据，删除多余值

```js
var object = { 'a': 1, 'b': '2', 'c': 3 };

_.omit(object, ['a', 'c']);		// => { 'b': '2' }
_.omitBy(object, _.isNumber);	// => { 'b': '2' }
```





