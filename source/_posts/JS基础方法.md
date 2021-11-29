---
title: JS基础方法
date: 2020-03-06 17:50:43
---

#### 基本类型

基本类型：number, boolean, string, undefined, null, Symbol

引用类型：Array, Object, Function

|     x     | number | string | boolean | undefined |
| :-------: | ------ | ------ | ------- | --------- |
| typeof  x | number | string | boolean | undefined | 

|     x     | Object | null   | Array  | Function | Symbol |
| :-------: | ------ | ------ | ------ | -------- | ------ |
| typeof  x | object | object | object | function | symbol |

注意：typeof null 返回 object，typeof Array返回object

一般用null表示“没有对象”，用undefined表示“没有值”

null == undefined   

null !== undefined

null === null  

undefined === undefined

Number(null) === 0   

Number(undefined)  === NaN

typeof NaN === 'number'

NaN !== NaN

NaN != NaN

<u>作判断时可被默认转换为false的有</u>    **0、-0、 null、''、false、undefined、NaN**

| x         | null | ''   | []   | undefined | {}   | false |
| --------- | ---- | ---- | ---- | --------- | ---- | ----- |
| Number(x) | 0    | 0    | 0    | NaN       | NaN  | 0     |
|           |      |      |      |           |      |       |


#### this指向

类的普通方法内部如果含有this，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错

1、一个比较简单的解决方法是，在构造方法中绑定this

2、另一种解决方法是使用箭头函数，箭头函数内部的this总是指向定义时所在的对象，也就是实例对象



#### super指向

super作为对象时

在普通方法中指向父类原型

在静态方法中指向父类



super()作为函数时，只能用在子类的构造函数之中，用在其他地方就会报错

super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B的实例

因此super()在子类里面相当于A.prototype.constructor.call(this)，将父类原型上的构造函数绑定到子类this实例上。



#### 静态方法

ES6类中的静态方法是属于原型类的，可以直接使用类名调用

如果静态方法中使用this，这个this是指的是类，而不是实例

静态方法可以与非静态方法重名，父类的静态方法可以被子类继承

子类中，静态方法也是可以从super对象上调用的



#### 静态属性

实例属性除了定义在constructor()方法里面的this上面，**也可以定义在类的最顶层**。

静态属性指的是 Class 本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性。



#### **Array**的方法

##### **会改变原数组**

`copyWithin(targetIndex, start, end)` 会改变原数组，无论怎么拷贝，数组的长度不变，多的内容会被截取掉。

`fill(value, start, end)` 会改变原数组，start和end可选，默认全部填充。

`pop()` 删除数组最后一个元素，并返回删除的元素

`shift()` 删除数组的第一个元素，并返回删除的元素

`push(item1,item2…)` 向数组末尾添加一个或多个元素，并返回数组新的长度

`unshift(item1,item2…)` 向数组开头添加一个或多个元素，并返回数组新的长度

`splice(start, length, items)` 只能操作数组，从start处截取length长度的元素后，返回含有截取元素的数组，再从start处添加items,，直接修改原数组。start和length必填, 若length<=0,则返回空数组，但是依然可以直接从start下标添加items, start为负数代表倒数第几位开始

`reverse()` 反转数组中的内容，会改变原数组，返回值也是一个顺序反转的数组

##### **不会改变原数组**

`arr1.concat(arr2,arr3,..arrn)` 不会改变原数组，但是连接后的数组只是浅拷贝，如果原数组中存在对象类型的元素，那么concat后的数组也只是拿到对象的引用。参数可以是具体的值，也可以是数组对象。

`includes(searchValue, start)` 从指定索引处查找是否存在searchValue

`indexOf(searchValue, start)` 找第一次出现的位置，从指定索引处查找searchValue所在位置的下标值，如果没找到返回-1

`lastIndexOf(searchValue, start)` 找最后一次出现的位置，从字符串的后面开始找，没有规定start就从末尾开始，没有找到返回-1

`Array.isArray(obj)` 静态方法，判断obj是否是数组

`join(separator)` 按照指定分隔符将数组中的元素转换为一个字符串，如果省略该参数，则使用**逗号**作为连接符

`keys()` 返回数组键值的可迭代对象

`entries()` 返回数组的迭代对象，key值就是下标索引

`toString()` 把数组转换为字符串，并返回结果,数组中的元素之间用逗号分隔。

`valueOf()` 返回 Array 对象的原始值,通常由 JavaScript 在后台自动调用，并不显式地出现在代码中

`slice(start, end)` 前包后不包，可操作字符串和数组，可以用负数表示倒数第几位，start>=end时，start和end不会互换

##### 高阶函数

**注意**：

- thisValue用来指定回调函数所属的上下文环境，一般如果我们使用的函数特属于某个对象（非全局对象），就要指定，否则会报错。
- 对空数组是不会执行回调函数的，一般使用new Array(length)创建的数组未经初始化赋值都属于空数组

**（1～5函数参数中的回调函数返回值类型是boolean）**

1、`every(function(currentValue, index, arr), thisValue)` 不会改变原数组，用来判断数组中所有的值是否都满足条件，一旦有一个不满足就会返回false;

2、`some(function(currentValue, index, arr), thisValue)` 不会改变原数组，用来判断数组中是否存在某个值满足条件，一旦有一个满足就会返回true;

3、`filter(function(currentValue, index, arr), thisValue)` 不会改变原数组，返回符合条件的所有元素构成的数组。

4、`find(function(currentValue, index, arr), thisValue)` 不会改变原数组，返回符合条件的第一个元素，一旦遇到符合值，后续元素就不再执行回调函数，如果没有符合条件的元素，返回undefined

5、`findIndex(function(currentValue, index, arr), thisValue)` 不会改变原数组，返回符合条件的第一个元素的索引值，一旦遇到符合值，后续元素就不再执行回调函数，如果没有符合条件的元素，返回-1

**以下方法可能会改变原数组**

6、`forEach(function(currentValue, index, arr), thisValue)` 遍历所有元素执行回调函数，可能会改变原数组的值。

7、`Array.from(object, mapFunction, thisValue)` 静态方法，可以使拥有 length 属性的对象或可迭代的对象生成一个数组。object不能是普通对象，因为普通对象没有length属性。mapFunction是数组中每个元素都要调用的回调函数。

8、`sort(function(a,b))` sort默认是按照字母升序排序的，如果要自定义排序规则（比如按照数字大小排序），sort接受一个回调函数，默认顺序是ab，如果回调函数返回值大于0，则调换顺序为ba；如果返回值小于0，则位置不变。所以function(a,b){return a-b}表示数字升序排序。

**不会改变原数组**

9、`map(function(currentValue, index, arr), thisValue)` 返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。

10、`reduce(function(previousValue, currentValue, currentIndex, arr), initialValue)` 不会改变原数组，接收一个函数作为累加器，数组中的每个值（从左到右）依次调用，previousValue保存上一次调用回调函数时的返回值，initialValue(可选的初始值。作为第一次调用回调函数时传给previousValue的值)。最后返回一个值。如果没有提供initialValue，reduce 会从索引1的地方开始执行 callback 方法，索引0的值作为initialValue的初始值，如果提供initialValue，从索引0开始遍历执行回调函数， 因此当数组为空且没有提供initialValue的时候，调用reduce方法就会报错

11、`reduceRight(function(previousValue, currentValue, currentIndex, arr), initialValue)` 功能和reduce()功能是一样的，不同的是 reduceRight() 从数组的末尾向前将数组中的元素做累加。(从右到左)



#### **String**的方法

**[注] 所有方法都不会改变原始字符串**

`charAt(index)`  返回指定位置的字符

`charCodeAt(index)`  返回指定位置的字符的unicode编码

`String.fromCharCode(unicode1, unicode2…)` 静态方法，返回一个或多个unicode值对应的字符组成的字符串

`toLowerCase()`

`toUpperCase()`

`toLocaleLowerCase()`

`toLocaleUpperCase()`

`trim()`  去除字符串两边的空格

`valueOf()` 返回 String 对象的原始值。

`toString()`

`indexOf(searchValue, start)`  返回某个指定的字符串值在字符串中首次出现的位置

`lastIndexOf(searchValue, start)`  返回某个指定的字符串值在字符串中最后出现的位置，从后向前找

`startsWith(searchValue, start)`  查看子字符串是否以指定字符串开头

`includes(searchValue, start)` 查找字符串中是否存在指定子字符串

`string1.concat(string2, string3, string4,….)` 返回连接两个或更多字符串的新字符串

`repeat(count)`  返回复制指定次数的字符串

`substr(start, length)` length<=0时返回空, start为负数时表示倒数第几位，未来可能会被废弃

`substring(start, end)` 截取字符串，前包后不包。

- start或end为负数或NaN时，会在执行前将负数变为0;
- start或end大于stringName.length时，会在执行前变为stringName.length;
- 如果省略end，默认提取到字符串末尾；
- start>end时，start和end会互换，start=end时，返回空字符串；

`slice(start, end)` 前包后不包，可操作字符串和数组，可以用负数表示倒数第几位，start>=end时，start和end不会互换



##### 与正则相关的函数

**[注] 以下方法都不会改变原字符串**

1、 **`split(separator, limit)`**  返回 separator分割的子串组成的数组，separator可以是**字符串或正则表达式**， limit会限制返回的数组长度。

- 如果分割符是空字符串，则返回字符串中所有字符组成的数组；
- 如果分割符省略不写，则返回数组包含一个由整个字符串组成的元素；
- 如果字符串仅由一个分隔符实例组成，则返回的数组由两个空字符串组成，也就是说匹配到开头和结尾，开头的左侧和结尾的右侧都以空字符串的形式放到数组中。
- 当字符串为空时，split（）返回一个包含一个空字符串的数组，而不是一个空数组，如果字符串和分隔符都是空字符串，则返回一个空数组。

**`2、replace(regexp|substr, newSubStr|function)`** 不会改变原来的字符串，而是返回一个新的替换后的字符串

**regexp**: 一个正则对象或其字面量

**substr**: 一个字符串，仅第一个匹配项会被替换

**newSubStr**: 用于替换掉第一个参数在原字符串中的匹配部分的字符串，该字符串中可以内插一些特殊的变量名：

| 变量名 | 代表的值                                                     |
| ------ | ------------------------------------------------------------ |
| $$     | 插入一个 "$"。                                               |
| $&     | 插入匹配的子串。                                             |
| $`     | 插入当前匹配的子串左边的内容。                               |
| $'     | 插入当前匹配的子串右边的内容。                               |
| $n     | 假如第一个参数是 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp)对象，并且 n 是个小于100的非负整数，那么插入第 n 个括号匹配的字符串。提示：索引是从1开始 |

**function**：指定一个函数作为第二个参数。在这种情况下，当匹配执行后，该函数就会执行。 函数的返回值作为替换字符串,  (注意：上面提到的特殊替换参数在这里不能被使用。) 另外要注意的是，如果第一个参数是正则表达式，并且其为全局匹配模式，那么这个方法将被多次调用，每次匹配都会被调用。

| 变量名            | 代表的值                                                     |
| ----------------- | ------------------------------------------------------------ |
| `match`           | 匹配的子串。（对应于上述的$&。）                             |
| `p1,p2, ...`      | 假如replace()方法的第一个参数是一个[`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 对象，则代表第n个括号匹配的字符串。（对应于上述的$1，$2等。）例如，如果是用 `/(\a+)(\b+)/` 这个来匹配，`p1` 就是匹配的 `\a+`，`p2` 就是匹配的 `\b+`。 |
| `offset`          | 匹配到的子字符串在原字符串中的偏移量。（比如，如果原字符串是 `'abcd'`，匹配到的子字符串是 `'bc'`，那么这个参数将会是 1） |
| `string`          | 被匹配的原字符串。                                           |
| NamedCaptureGroup | 命名捕获组匹配的对象                                         |

`3、search(searchValue)` 返回正则表达式在字符串中**首次**匹配项的**索引**，没找到返回-1，searchValue可以是**字符串或正则表达式**

`4、match(regExp)` regExp是**正则表达式**，当regExp是**字符串**时，会自动转成非全局匹配的正则表达式

- 没有匹配返回null，
- 全局匹配，返回一个数组存储着正则匹配的多个结果；
- 非全局，那么返回第一次匹配的结果和它相关的捕获组。

#### RegExp的属性

| 简写 | 属性         | 解释                                                         |
| ---- | ------------ | ------------------------------------------------------------ |
| g    | global       | 全局匹配                                                     |
| i    | ignoreCase   | 忽略大小写                                                   |
| m    | multiline    | 多行匹配                                                     |
| s    | dotAll       | .字符可以匹配任意字符                                        |
| y    | sticky       | 下次匹配必须从lastIndex处开始                                |
| u    | unicode      | 可以正确匹配UTF-16编码的四个字节的字符                       |
| $&   | lastMatch    | 静态只读属性，匹配的内容，结合replace使用                    |
| $'   | rightContext | 静态只读属性，匹配的子串右边的内容，结合replace使用          |
| $`   | leftContext  | 静态只读属性，匹配的子串左边的内容，结合replace使用          |
| $_   | input        | 静态只读属性，当正则表达式上搜索的字符串发生改变，并且字符串匹配时，input 属性值会修改 |

其他属性：

lastIndex: 下一次匹配开始的位置

flags: 返回由当前正则表达式对象的标志组成的字符串，例如‘gimsuy’

**正则匹配规则:**

a(?=b) 匹配后面有b的a

a(?!b) 匹配后面没有b的a

(?<=a)b 匹配前面有a的b

(?<!a)b 匹配前面没有a的b

#### RegExp的方法

**[注]下面两个方法在执行全局匹配时，每次执行都会更新正则对象的lastIndex属性作为下一次匹配的开始**

`1、reg.test(string)` 从lastIndex开始，如果找到匹配项，则返回true，否则返回false。

`2、reg.exec(string)` 返回一个数组，第一项存储匹配到的字符串，如果是分组捕获，第二项开始分别是各个捕获组的值。这个数组还拥有两个属性分别是index和input，index代表匹配的开始位置，input表示输入的字符串参数。

#### Number对象

##### 静态属性：

Number.MAX_VALUE   :   JavaScript 中可表示的最大的数,它的近似值为 1.7976931348623157 x 10 +308

Number.MIN_VALUE    :   JavaScript 中可表示的最小的数（接近 0 但不是负数）它的近似值为 5 x 10-324

Number.NEGATIVE_INFINITY：-Infinity负无穷大

Number.NaN:用于指示某个值不是数字值

Number.POSITIVE_INFINITY:  Infinity正无穷大

Number.EPSILON: 表示 1 和比最接近 1 且大于 1 的最小 Number 之间的差别,等于Math.pow(2,-52)

Number.MIN_SAFE_INTEGER: 表示在 JavaScript中最小的安全的 integer 型数字 -(2^53 - 1)

Number.MAX_SAFE_INTEGER: 表示在 JavaScript 中最大的安全整数2^53- 1

##### 静态方法：

Number.isInteger(): 用来判断给定的参数是否为整数

Number.isSafeInteger(): 判断传入的参数值是否是一个"安全整数"

Number.isFinite()   :  非数值或者无限大返回false,只有数值返回true，

Number.isNaN()  : 非NaN一律返回false，只有对于`NaN`才返回true，就算是字符串'NaN'也是false

【注释】相比与全局方法isFinite()和isNaN()，这两个方法不会先调用Number()试图将参数转换成数值类型；

全局方法isFinite()和isNaN()函数会试图判断参数能否通过Number()转化为合法的数字
null、空串''、空数组[]、都可以被Number()合法转化为0,它们被isNaN认为是数，返回false
undefined、空对象{}、空函数等无法转化为数字，返回true

##### 非静态方法：

toExponential(x)：把对象转换为科学计数法，x表示小数的位数，返回值类型是string

toFixed(x): 将数字四舍五入为指定小数位数的字符串，x表示小数的位数，返回值类型是string

toPrecision(x): 将数字四舍五入为指定位数的字符串，x一般在1~21之间，表示数字位数，返回类型是string

toString()

valueOf()

#### Math对象

|属性 | 解释|
| ---- | --- |
|E|自然对数的底数e值|
|LN2|2的自然对数|
|LN10|10的自然对数|
|LOG2E|以2为底的e的对数|
|LOG10E|以10为底的e的对数|
|PI|圆周率|
|SQRT1_2|2的平方根的倒数|
|SQRT2|2的平方根|


|方法 | 解释|
| ---- | --- |
|abs(x)|返回x的绝对值|
|ceil(x)|向上取整|
|floor(x)|向下取整|
|exp(x)|返回以e为底的指数值|
|log(x)|返回x的自然对数值(底为e)|
|max(x1,x2,x3,…,xn)|返回最大值|
|min(x1,x2,x3,…,xn)|返回最小值|
|pow(x,y)|返回x的y次幂|
|random()|返回0~1之间的随机数，不包含1|
|round(x)|四舍五入|
|aqrt(x)|返回x的平方根|

#### Date对象
四种方式创建Date对象

```js
var d = new Date(); //返回当前的时间的对象
var d = new Date(milliseconds);
var d = new Date(dateString);  //传入日期对象字符串
var d = new Date(year, month, date, hours, minutes, seconds, milliseconds); 
//month是0~11，一月Jan是0
```
| 方法 | 解释 |
| ---- | --- |
|set/get[UTC]FullYear() |四位数字的年份|
|set/get[UTC]Month() |月份(0\~11) ,一月份为0|
|set/get[UTC]Date() |一个月中的某一天(1\~31) |
|set/get[UTC]Hours() |小时(0\~23) |
|set/get[UTC]Minutes() |分钟(0\~59) |
|set/get[UTC]Seconds() |秒(0\~59) |
|set/get[UTC]Milliseconds() |毫秒(0\~999) |
|get[UTC]Day() |返回周几(0\~6),周日为0|
|set/getTime() |1970年1月1日至今的毫秒数|
|Date.parse(dateString) |静态方法，解析日期或表示日期的字符串为1970年1月1日至今的毫秒数|
|to[Locale]DateString() |把Date对象的日期部分转换成字符串|
|to[Locale]TimeString() |把Date对象的时间部分转换成字符串|
|to[Locale]String() |把Date对象转换为字符串|
|toISOString() |使用ISO标准返回Date对象的字符串格式|
|toJSON()|以 JSON 数据格式返回日期字符串|



