---
layout: post
title: "正则表达式的学习总结"
date: 2017-04-17
tag: JavaScript
---

#### ## RegExp 对象

JavaScript通过内置对象 RegExp 支持正则表达式

两种方式实例化 RegExp 对象

a. 字面量 `var expression = /pattern/ flags;`

	var reg1 = /\bis\b/;
	var reg2 = /\bis\b/g;
	var reg3 = /\bis\b/gi;
	'This is is Is a dog'.replace(reg1, 'X'); // "This X is Is a dog"
	'This is is Is a dog'.replace(reg2, 'X'); // "This X X Is a dog"
	'This is is Is a dog'.replace(reg3, 'X'); // "This X X X a dog"

RegExp 对象属性

* g: global 全文搜索，若不添加，则搜索到第一个匹配停止
* i: ignore case 忽略大小写(默认为大小写敏感)
* m: multiline 多行搜索
* [lastIndex](#lastindex)
* source

b. 构造函数

	var reg4 = new RegExp('\\bis\\b', 'g');
	'This is is Is a dog'.replace(reg4, '2'); // "This 2 2 Is a dog"

#### ## 元字符

元字符：在正则表达式中有特殊含义的非字母字符

> 元字符含义并非唯一，不同场景，可能会有不同含义
> eg : ^ 可以表示..开始，也可表示取反 ( [ ] 中)

| 字符 |                           |预定义类|                |                                  |量词 |       |
|------|-------------------------- |--------|----------------|----------------------------------|-----|-------|
|  \t  | 水平制表符 tab            |.       | [^\r\n]        |任意字符(除回车换行) any character|?    |{0,1}  |
|  \v  | 垂直制表符                |\d      | [0-9]          |digit                             |+    |{1,}   |
|  \n  | 换行符                    |\D      | [^0-9]         |non-digit                         |*    |任意次 |
|  \r  | 回车符                    |\w      | [a-zA-Z_0-9]   |word 字母/数字/下划线             |{n}  |n      |
|  \0  | 空字符                    |\W      | [^a-zA-Z_0-9]  |non-word                          |{n,m}|n~m    |
|  \f  | 换页符                    |\s      | [\t\n\x0B\f\r] |空白符(white space)               |{n,} |>=n    |
|  \cX | 与X对应的控制字符(ctrl+X) |\S      | [^\t\n\x0B\f\r]|non-white space                   |     |       |

量词
![](/images/post/RegExp1.jpg)

| 边界   |                            |
|--------|----------------------------|
|^       |以xxx开始 Start of line     |
|$       |以xxx结束 End of line       |
|\b      |单词边界 word boundary      |
|\B      |非单词边界 non-word boundary|

	边界
	  'This is is a dog'.replace(/is/g, '0'); // "Th0 0 0 a dog"
	  'This is is a dog'.replace(/\bis\b/g, '0'); // "This 0 0 a dog"
	  'This is is a dog'.replace(/\Bis\b/g, '0'); // "Th0 is is a dog"

	  '@abc@123@'.replace(/@./g, 'Q'); // "QbcQ23@"
	  '@abc@123@'.replace(/^@./g, 'Q'); // "Qbc@123@"
	  '@abc@123@'.replace(/.@$/g, 'Q'); // "@abc@12Q"

|范围      |                           |分组|                |
|----------|---------------------------|----|----------------|
|[ ]       |类：用 - 来指定一个字符范围|( ) |使量词作用于分组|
|[ ]中的 ^ |反向类/负向类              |    |                |

	范围 []
	  'a1b2c3d4'.replace(/[abc]/g, 'Q'); // "Q1Q2Q3d4"
	  'a1b2c3d4'.replace(/[^abc]/g, 'Q'); // "aQbQcQQQ"
	  '20-12-20'.replace(/[0-9]/g, 'X'); // "XX-XX-XX"
	  '20-12-20'.replace(/[0-9-]/g, 'X'); // "XXXXXXXX"
	  // []中在后面直接加'-'就可以匹配到'-'

	分组 ()
	  'a1b2c3d4'.replace(/[a-z]\d{3}/g, 'A'); // "a1b2c3d4"
	  // 此时3次作用在\d上，是找不到的
	  'a1b2c3d4'.replace(/([a-z]\d){3}/g, 'A'); // "Ad4"
	  'AppleOrange'.replace(/Apple|Orange/g, 'A'); // "AA"
	  'AppleangeAppOrange'.replace(/App(le|Or)ange/g, 'A'); // "AA"
	  // | 表示'或'

	反向引用/分组捕获
	  '2012-12-20'.replace(/\d{4}-\d{2}-\d{2}/g, '$1'); // "$1"
	  '2012-12-20'.replace(/(\d{4})-(\d{2})-(\d{2})/g, '$1'); // "2012"
	  '2012-12-20'.replace(/(\d{4})-(\d{2})-(\d{2})/g, '$2/$3/$1'); // "12/20/2012"

	分组忽略(分组内加 ?: )
	  '2012-12-20'.replace(/(?:\d{4})-(\d{2})-(\d{2})/g, '$1'); // "12"

#### ## 贪婪/非贪婪模式

贪婪模式：在匹配成功的前提下，尽可能多的匹配

非贪婪模式：尽可能少的匹配，即一旦匹配成功不在继续尝试，在量词后加？

	'12345678'.replace(/\d{3,6}/g, 'A'); // "A78"
	'12345678'.replace(/\d{3,6}?/g, 'A'); // "A45678"
	'12345678'.match(/\d{3,6}?/g); // ["123", "456"]

#### ## 前瞻/后顾

|正向前瞻|exp(?=assert)||
|负向前瞻|exp(?!assert)||
|正向后顾|exp(?<=assert)|JavaScript不支持|
|负向后顾|exp(?<!assert)|JavaScript不支持|

	'a2*34v8vv'.replace(/\w(?=\d)/g, '@'); // "@2*@4@8vv"
	'a2*34v8vv'.replace(/\w(?!\d)/g, '@'); // "a@*3@v@@@"

<h4 id="lastindex">## 使用正则表达式</h4>

可被用于：

* RegExp 的 [exec](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) 和 [test](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test) 方法
* String 的 [match](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match) / [replace](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace) / [search](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/search) / [split](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split) 方法

##### RegExp.prototype.test(str)

	var reg1 = /\w/;
	reg1.test('ab'); // true
	reg1.test('ab'); // true
	reg1.test('ab'); // true
	reg1.test('$'); // false

=> 不加 g 的情况下，无匹配结果则返回 false , 有匹配结果则总是返回 true

	var reg2 = /\w/g;
	reg2.test('ab'); // true
	reg2.test('ab'); // true
	reg2.test('ab'); // false
	reg2.test('ab'); // true
	reg2.test('ab'); // true
	reg2.test('ab'); // false

=> **使用 g 的情况下**，有匹配结果却出现了返回 false 的情况，这就让人很没有安全感了。这其实和 RegExp 的属性 `lastIndex` 有关。

`lastIndex:` 当前表达式匹配内容的最后一个字符的下一个位置；可以理解为正则表达式开始下一次查找的索引位置。

	while(reg2.test('ab')) {
	  console.log(reg2.lastIndex);
	}
	// 1
	// 2

栗子中：第一次匹配到 a，返回 true， 然后下一次就从 b 开始匹配，此时 lastIndex 为 b 的 index : 1，故 console.log() 输出1，第二次匹配到 b，返回 true，lastIndex 变为2，console.log() 输出2，第三次没有匹配到，故返回 false，lastIndex 则会重置为 0 ，所以就出现 true true false 循环的结果。

	(/\w/g).test('ab'); // true
	(/\w/g).test('ab'); // true
	(/\w/g).test('ab'); // true

那能不能不出现这种“不稳定”呢？可以每次实例化一个新的对象，**BUT** 每次实例化对象的时候会有内存开销，不好不好，也没必要这么做，对于 test 方法本意是 正则表达式与指定字符串能不能匹配上，也就是测试有没有，我们只要知道有没有就好了，若只想用 test 原义的话，就不要加 g 就好了。

如果不仅仅想知道有没有匹配上，还想知道第几个匹配上的，匹配的是什么balabala... 这时需要用 exec 方法。

##### RegExp.prototype.exec(str)

使用正则表达式模式对字符串执行搜索，并将更新全局 RegExp 对象属性 ( lastIndex )。若没有匹配的文本则返回 null ，若有则返回一个结果数组。

数组会增加2个属性：

* -index 声明匹配文本的第一个字符的位置
* -input 存放被检索的字符串 string

非全局栗子 (不加 g )

	var reg3 = /\d(\w)(\w)\d/;
	var str = '$$1az2bx3ce4dt5ey';

	var matches = reg3.exec(str);
	console.log(reg3.lastIndex + '\t' + matches.index + '\t' + matches.toString());
	// "0  2 1az2,a,z"
	console.log(reg3.lastIndex + '\t' + matches.index + '\t' + matches.toString());
	// "0  2 1az2,a,z"

	matches // ["1az2", "a", "z"]

*非全局下*

lastIndex 不生效，故 **0** ，这里执行多次 lastIndex 也不会变化。据上文，匹配到文本第一个字符的位置(即 1 的位置)为 **2** 。

返回数组

* 第一个元素是与正则表达式相匹配的文本
* 第二个元素是与 RegExp 对象的第一个 *子表达式* (也就是分组)相匹配的文本(若有)，栗子中为 (\w)
* 第三个元素是与 RegExp 对象的第二个子表达式相匹配的文本(若有)，以此类推

全局栗子 ( g )

	var reg4 = /\d(\w)(\w)\d/g;
	var str = '$$1az2bx3ce4dt5ey';
	while(matches = reg4.exec(str)){
	  console.log(reg4.lastIndex + '\t' + matches.index + '\t' + matches.toString());
	}
	// "6  2 1az2,a,z"
	// "12 8 3ce4,c,e"

这里匹配了2次，lastIndex 为 6 ( b 的位置)，12 ( d 的位置)；据上文，index 为匹配结果的第一个字符，分别为 1 (位置为 2 ) 和 3 (位置为 8 )，后面依次也是分组的内容。

##### String.prototype.search(reg)

* search() 方法执行正则表达式和 String对象之间的一个搜索匹配，如果传入一个非正则表达式对象，则会使用 new RegExp(obj) 隐式地将其转换为正则表达式对象
* 返回：**第一个**匹配结果 index ，查找不到返回 -1
* 不执行全局匹配，**忽略 g** ，并总是从字符串的开始进行检索

栗子

	'a1b2c3d4'.search('2'); // 3
	'a1b2c3d4'.search(2); // 3
	'a1b2c3d4'.search(/2/); // 3
	// 传入的不管是字符串还是数字，都会尝试转为正则
	'a1b2c3d4'.search('20'); // -1

##### String.prototype.match(reg)

* match() 方法将检索字符串，以找到一个or多个与 regexp 匹配的文本
* regexp 是否有 **g 对结果影响很大**

非全局

* 仅在字符串中执行一次
* 若没找到匹配文本，返回 null
* 若找到，返回**数组**，存放：
	1. 第一个元素存放匹配文本，其余元素存放与正则表达式的子表达式匹配的文本
	2. 数组会增加2属性
		* index 匹配文本的起始字符在字符串的位置
		* input 原字符串的引用

*返回和 RegExp.exec() 相同的结果*

	var reg5 = /\d(\w)\d/;
	var str = '$$1a2b3c4d5e';

	var matches = str.match(reg5);
	console.log(matches); // ["1a2", "a", index: 2, input: "$$1a2b3c4d5e"]
	console.log(matches.index + '\t' + reg5.lastIndex); // 2	0

全局

* 若没找到匹配文本，返回 null
* 若找到1or多个，返回数组
* 数组中存放 字符串中所有匹配字串，没有 index 和 input 属性

栗子

	var reg6 = /\d(\w)\d/g;
	var str = '$$1a2b3c4d5e';

	var matches = str.match(reg6);
	console.log(matches); // ["1a2", "3c4"]
	console.log(matches.index + '\t' + reg5.lastIndex); // undefined	0

##### String.prototype.split(reg)

split() 方法将字符串分割成字符数组，分割可以用字符串or正则表达式

	'a,b,c,d'.split(','); // ["a", "b", "c", "d"]

	var names = "Harry Trump ;Fred Barney; Helen Rigby ; Bill Abel ;Chris Hand";
	var re = /\s*;\s*/;
	var nameList = names.split(re);
	console.log(nameList);
	// ["Harry Trump", "Fred Barney", "Helen Rigby", "Bill Abel", "Chris Hand"]

	var myString = "Hello 1 word. Sentence number 2.";
	var splits = myString.split(/(\d)/);
	console.log(splits); // ["Hello ", "1", " word. Sentence number ", "2", "."]

##### String.prototype.replace()

	'a1b1c1'.replace('1', 'M'); // "aMb1c1"
	'a1b1c1'.replace(/1/g, 'M'); // "aMbMcM"

	'a1b2c3d4'.replace(/\d/g, function(match, index, origin){
	  console.log(index);
	  return parseInt(match) + 1;
	});
	// 1
	// 3
	// 5
	// 7
	// "a2b3c4d5"

	'a1b2c3d4e5'.replace(/(\d)(\w)(\d)/g, function(match, group1, group2, group3, index, origin){
	  console.log(match);
	  return group1 + group3;
	});
	// "1b2"
	// "3d4"
	// "a12c34e5"

* String.prototype.replace(str, replaceStr);
* String.prototype.replace(reg, replaceStr);
* String.prototype.replace(reg, function);
	* function 4 个参数
		1. 匹配字符串
		2. 正则表达式分组内容，若没有分组则无此参数
		3. 匹配项在字符串中 index
		4. 原字符串