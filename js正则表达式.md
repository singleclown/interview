基础知识js系列七正则
修饰符： /i (忽略大小写)
/g (全文查找出现的所有匹配字符)
/m (多行查找)
/gi(全文查找、忽略大小写)
/ig(全文查找、忽略大小写)
正则中使用了^和$分别表示字符串开头和结尾
\b指靠后面的位置
(?=p)位置的后面要匹配p，也就是p的前面那个位置
正则match：匹配表达式后，整个串就减去匹配过的，剩下减去的串
1.match使用：用正则表达式表达要查找的子串-匹配字符（串）方式
2.replace使用：使用^或者$表示位置 以整个字符串为参考，位置处子字符串为正则书写，以锚点出发整个字符串一起考虑，正则是整个字符串的表达形式
3.test使用：匹配字符串格式是否正确 ，正则是整个字符串的表达形式
/DingTalk/.test(navigator.useragent)
6个锚^、$、\b、\B、(?=p)、(?!p) 
多行匹配模式(即有修饰符 m)时,二者是行的概念 
var result = "I\nlove\njavascript".replace(/^|$/gm, '#');
\b 是单词边界 
(?=p) 查找哪个子字符串的位置比如 (?=p),一般都理解成:要求接下来的字符与 p 匹配,但不能包括 p 匹配的那些字符 
^(脱字符)匹配开头,在多行匹配中匹配行开头。 
$(美元符号)匹配结尾,在多行匹配中匹配行结尾 
用（？=$）子串结尾位置
match 返回的一个数组,第一个元素是整体匹配结果,然后是各个分组(括号里)匹配的 NOTE 内容,然后是匹配下标,最后是输入的文本。另外,正则表达式是否有修饰符 g,match 
返回的数组格式是不一样的。 
Object.getOwnPropertyNames(Promise)
Reflect.ownKeys(Promise)
正则事例：
例如：http://192.168.1.24:3000/?corpid=ding6e43340ac6c10fea35c2f4657eb6378f&appid=3378&apptype=0&devtype=1&showmenu=false#/overview?_k=l8xbmj
取到overview
replace(/[^#]*#\/([^#\/?]+)\?[\s\S]*/,'$1')
示例2.
2018年1月1日-》2018/1/1,替换年月，删除日字
replace(/([\d]{4})[^\d]*([\d]{1,2})[^\d]*([\d]{1,2})[^\d]*/,'$1/$2/$3')
m
示例3.
[(\(\d\))]会把里面括号里的当成一个集合，每个元素单独匹配[^\(\)]*(\(\d\))
 var regex1 = /[^\(\)]*(\((\d)\))/g
示例4.
aaaddccaccfgh查最多的字符
（[a-z])\1*用match分割
正则就是看目标字符串里是否有满足匹配的子串。因此,“匹配”的本质就是“查找” 
第一章
正则表达式是匹配模式，要么匹配字符，要么匹配位置
精确、横向(字符串长度)和纵向(多个字符中一个，字符不确定[a,b,c])模糊匹配 
字符组【a-z】排除a-z[^a-z],\d,\w,\s
对惰性匹配的记忆方式是:量词后面加个问号 
多选分支 |(管道符) 分支结构也是惰性的 
※匹配字符,无非就是字符组、量词和分支结构的组合使用罢了 
var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;匹配16进制颜色
var regex = /^([01][0-9]|[2][0-3]):[0-5][0-9]$/;24小时制，^代表这个串是整个串，不是子串
/^[a-zA-Z]:\\([^\\:*<>|"?\r\n/]+\\)*([^\\:*<>|”?\r\n/]+)?$/;文件夹路径
使用match去匹配，查找子串；
疑问
var regex = /id="[^"]*"/
  var string = '<div id="container" class="main"></div>';

第二章匹配位置
6个锚：^、$、\b、\B、(?=p)、(?!p) 
多行匹配模式(即有修饰符 m) 
\b 是单词边界,具体就是 \w 与 \W 之间的位置 
(?=p),其中 p 是一个子模式,即 p 前面的位置 
对于位置的理解,我们可以理解成空字符 "" 
字符之间的位置,可以写成多个 
同时包含具体两种字符 
var regex = /(?=.*[0-9])(?=.*[a-z])^[0-9A-Za-z]{6,12}$/; 

第三章分组(())和分支结构（|）

match 返回的一个数组,第一个元素是整体匹配结果,然后是各个分组(括号里)匹配的  内容,然后是匹配下标,最后是输入的文本。另外,正则表达式是否有修饰符 g,match 
返回的数组格式是不一样的。 
提取内容方法RegExp.$1
还可以传个方法
var result = string.replace(regex, function () {
      return RegExp.$2 + "/" + RegExp.$3 + "/" + RegExp.$1;
  });
反向引用 \1 第一个分组
括号嵌套 以左括号(开括号)为准 
分组后面有量词的话,分组最终捕获到的数据是最后一次的匹配 
如果只想要括号最原始的功能,但不会引用它 var regex = /(?:ab)+/g;
第5章结构和操作符
字符字面量、字符组、量词、锚、分组、选择分支、反向引用 转义符 -》括号和方括号 -》量词限定符 -》位置和序列 -》管道符 

第6章表达式构建
平衡法则 6.1构建正则前提 (api可解决不用正则)
6.2准确性（匹配预期的字符串、不匹配非预期的字符串 ） 6.3效率
 使用非捕获型分组 
独立出确定字符 
提取分支公共部分 
减少分支的数量,缩小它们的范围 
       
    
第7章正则表达式的四种操作 
先“匹配 
再验证、切分、提取、替换。 
从上面可以看出用于正则操作的方法,共有 6 个,字符串实例 4 个,正则实例 2 个 
  String#search
  String#split
  String#match
  String#replace
  RegExp#test
  RegExp#exec

test 整体匹配时需要使用 ^ 和 $ 这个相对容易理解,因为 test 是看目标字符串中是否有子串匹配正则,即有部分匹配即可。 
如果,要整体匹配,正则前后需要添加开头和结尾 

new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
查询的子字符串必须有个明确的开头和结尾，不然含有*查不到

当你想要知道字符串中是否存在某个模式（pattern）时可使用 search()，类似于正则表达式的 test() 方法。当要了解更多匹配信息时，可使用 match()（但会更慢一些），该方法类似于正则表达式的 exec() 方法。
* 如果你需要知道一个字符串是否与一个正则表达式匹配 RegExp ，可使用 test() 。
* 如果你只是需要第一个匹配结果，你也可以使用 RegExp.exec() 。
* 如果你想要获得捕获组，并且设置了全局标志，你需要用 RegExp.exec() 。
const regex1 = RegExp('foo*','g');
const str1 = 'table football, foosball';
let array1;

while ((array1 = regex1.exec(str1)) !== null) {
  console.log(`Found ${array1[0]}. Next starts at ${regex1.lastIndex}.`);
  // expected output: "Found foo. Next starts at 9."
  // expected output: "Found foo. Next starts at 19."
}
array1 = regex1.exec(str1)
 ["foo", index: 6, input: "table football, foosball", groups: undefined]
str1.match(regex1)
 ["foo", "foo"]
var str = 'For more information, see Chapter 3.4.5.1';
var re = /see (chapter \d+(\.\d)*)/i;
var found = str.match(re);
["see Chapter 3.4.5.1", "Chapter 3.4.5.1", ".1", index: 22, input: "For more information, see Chapter 3.4.5.1", groups: undefined]

正则表达式中有些字符具有特殊的含义，如果在匹配中要用到它本来的含义，需要进行转义（在其前面加一个\）。下面总结了常见的一些需要转义的特殊字符：

$ 匹配输入字符串的结尾位置。如果设置了RegExp对象的Multiline属性，则 $ 也匹配，如‘\n’或’\r’。
() 标记一个子表达式的开始和结束位置。子表达式可以获取供以后使用。要匹配这些字符。
* 匹配前面的子表达式零次或多次。
+ 匹配前面的子表达式一次或多次。
. 匹配除换行符\n之外的任何单字符。
[] 标记一个中括号表达式的开始。
? 匹配前面子表达式零次或一次，或指明一个非贪婪限定符
\ 将下一个字符标记为或特殊字符或原意字符或后向引用或八进制转义符。例如：‘n’匹配字符‘n’，而‘\n’匹配换行符。序列‘\\’匹配’\’
^ 匹配输入字符串的开始位置，除非在方括号表达式中使用，此时他表示不接受该字符集合。
{} 标记限定符表达式的开始。
| 指明两项之间的一个选择。
总述一下：* . ? + $ ^ [ ] ( ) { } | \ /


￼
