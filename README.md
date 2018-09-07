# js-基础点汇总
## 变量作用域问题
```
var a = 10;//全局变量
function aaa(){
  console.log(a);// 输出的值为10
}
function bbb(){
  var a = 20;//bbb函数的局部变量
  aaa();
}
bbb();
 ```
代码分析：当bbb函数执行的时候，程序走到bbb函数位置运行，此时bbb函数的局部变量a=20；在bbb函数位置调用执行aaa函数，并不是把aaa函数拿到bbb函数内部去执行，而是程序调到aaa函数的位置去运行，此时，aaa函数内部并没有局部变量a，所以aaa函数输出就是全局变量a=10。aaa函数并不能访问bbb函数的局部变量。
```
var a = 10;
function aaa(){
  console.log(a);
}
function bbb(){
  console.log(a);
  var a = 20;
}
aaa();//输出10
bbb();//输出undifined
```
代码分析：
代码在编译时，bbb函数会被编译成：` function bbb(){var a; console.log(a); a = 20;} `，所以在执行bbb函数的时候，因为bbb的局部变量没有赋值，所以输出的是undifined。
```
function aaa(){
  var a = b = 10; //变量a是aaa函数的局部变量，它的值是10；变量b为全部变量，它的值是10；
}
```
### 字符串 & 正则
1、 写一个字符串转成驼峰的方法。例如：border-bottom-color -> borderBottomColor
方法一：字符串方法
```
var str = 'border-bottom-color';
function change(str){
  var arr = str.split('-'); //以 ‘-’ 分割字符串成数组[border, bottom,color]
  //循环，将数组出了首个之外的项的首字母转成大写
  for(var i=1; i<arr.length; i++){
    arr[i] = arr[i].charAt(0).toUpperCase() + arr[i].substring(1);
  }
  return arr.join('');// 将数组用空字符转成字符串
}
change(str);
```
方法二：正则表达式法
```
var str = 'border-bottom-color';
function change(str){
  var reg = /-(\w)/g; //正则中用（）括起来的正则的子项。
  //主要是使用字符串的replace方法的第二个参数--回调函数
  return str.replace(reg, function($0, $1){ // 回调函数的第一个参数$0代表使用正则匹配到的整个字符串，第二个参数$1代表的是正则的第一个子项
    return $1.toUpperCase();// 将匹配到的第一个子项转成大写。
  })
}
change(str);
```
2、 蟾照字符串中出现最多的字符和个数。例如：sdkjssscfsssfd -> 字符最多的是s，出现了6次
方法一：字符串方法
```
var str = 'sdkjssscfsssfd';
function findStr(str){
  var obj = {};//利用对象键的唯一性 obj = {s:[], d:[], k:[]...}
  var num = 0;
  var iStr = '';
  for(var i=0; i<str.length;i++){
    if(!obj[str[i]]){
      obj[str[i]] = []; //当字符串中的某个字符在对象中找不到时，就在对象中以该字符串为键，空数组作为值，创建一条属性。
    }
    obj[str[i]].push(str[i]); //将与对象键相同的字符添加到键对应值的数组中。
  }
  //循环该对象，将最长的属性值的长度赋值给num
  for(var item in obj){
    if(num < obj[item].length){
      num = obj[item].length;
      iStr = obj[item][0]; // 将长度最长的该数组中的第一个值赋值给iStr。其实数组中每一项都是相同的。
    }
  }
  return '字符最多的是' + iStr + '，出现了' + num + '次';
}
findStr(str);
```
方法二：正则方法
```
var str = 'sdkjssscfsssfd';
function findStr(str){
  var reg = /(\w)\1+/g;//  \1代表前面子项的赋值
  var num = 0;
  var iStr = '';
  var arr = str.split('');
  arr.sort();
  str = arr.join(''); //str = 'cddffjksssssss'
  str.replace(reg, function($0, $1){
    if(num < $0.length){
      num = $0.length;
      iStr = $1;
    }
  })
  return '字符最多的是' + iStr + '，出现了' + num + '次';
}
findStr(str);
```
3、如何给字符串加千分符。 例如： 3562123761 -> 3,562,123,761
方法一：字符串方法
```
var str = '3562123761';
function toCon(str){
  var ipos = str.length % 3; // 0 1 2
  var arr = [];
  var res = '';
  if(ipos){ //当余数存在的时候，截取前余数位放入数组中
    arr.push(str.substring(0, ipos));
  }
  str = str.substring(ipos);// -> 562123761
  for(var i=0; i<str.length; i=i+3){
    arr.push(str.substring(i,i+3));//循环，每三位截取一次，将截取的字符串放入数组。
  }
  res = arr.join(',');//将数组中的各项用‘,’拼接
  return res;
}
toCon(str);
```
```
var str = '3562123761';
function toCon(str){
  var ipos = str.length % 3; // 0 1 2
  var arr = [];
  var res = '';
  var iNow = 0; //计数，每三位截取一次
  var temp = '';
  if(ipos){ //当余数存在的时候，截取前余数位放入数组中
    arr.push(str.substring(0, ipos));
  }
  str = str.substring(ipos);// -> 562123761
  for(var i=0; i<str.length; i++){
    iNow++;
    temp += str[i];
    if(iNow == 3 && temp){ //当iNow=3时，将暂存在temp中拼接好的三位字符串放入数组
      arr.push(temp);
      temp = '';//将暂存变量置空，方便下一次存储
      iNow = 0; //当前计数器也重置，下次循环重新计数
    }
  }
  res = arr.join(',');//将数组中的各项用‘,’拼接
  return res;
}
toCon(str);
```
方法二：正则法
前向声明： (?=) `var str = 'abacad'; var reg = /a(?=b)/g` 匹配a后面紧挨着b的那个a。结果：str中ab中的a会被匹配到，ac 和ad 中的a不会被匹配到。
反前向声明： (?!)`var str = 'abacad'; var reg = /a(?！b)/g` 匹配a后面紧挨着不是b的那个a。结果：str中匹配到的是ac和ad中的a，ab中的a不会被匹配到。

  `/(?=(\d{3})+$)/g `这个正则的意思是：前向声明 匹配紧挨着 3个数字 ，这三个数字可以出现多次，并且从字符串尾部开始匹配。因为前向声明前面什么都没有，所以在匹配字符串的时候匹配到的也是三个数字与三个数字中间什么都没有的部分 。
 ` /(?=(?!\b)(\d{3})+$)/g`  这个正则的意思是，前向声明中要排除前向是独立部分（‘空格，开始，结束’），所以加了反前向声明(?!\b)

```
var str = '3562123761';
function toCon(str){
  var reg = /(?=(?!\b)(\d{3})+$)/g; 
  return str.replace(reg, ',');//将匹配到的什么都没有的部分替换成‘,’
}
toCon(str);
```
### 限制条件不全代码
1、a b 两个变量，不用第三个变量来切换两个变量的值
```
var a = 3, b = 5;
function exchange(a, b){
  a = a + b; //  a = 8 b = 5
  b = a - b; // b = 3 a = 8
  a = a - b; // a = 5 b = 3
  console.log(a, b);
}
exchange(a, b);
```
```
var a = 'hello';
var b = 'world';
function exchange(a ,b){
  var a = a + b; // a = 'helloworld' b = 'world'
  b = a.substr(0, a.length-b.length); // b = 'hello' a = 'helloworld' 
  console.log('b', b);
  a = a.substring(b.length);
  console.log(a +'-------'+ b);
}
exchange(a, b);
```
2、有一个数n=5，不用循环，怎么返回[1,2,3,4,5]这样的数组
```
var n = 5;
var arr = [];
function toArr(n){
  arr.unshift(n);
  n--;
  if(n){
  //使用递归函数调用函数本身的时候，一定要注意 定义的变量arr一定要是全局变量，不然，每次递归调用函数toArr时，都会重新格式化arr =[];
    toArr(n); 
  }
  return arr;
}
toArr(n);
```
```
var n = 5;
function toArr(n){
  var arr = [];
  //这里使用的是匿名自持行函数
  return (function() {
    arr.unshift(n);
    n--;
    if(n){
      arguments.callee();//递归调用匿名函数。
    }
    return arr;
  })();
}
toArr(n);
```
`arguments`是js的一个内置对象，当调用函数时，就会实例化出该对象，而`arguments.callee()`是一个指向正在执行的函数的指针，这样就可以通过`arguments.callee()`来调用函数，尤其是在编写递归函数的时候。但是在严格模式下，`arguments.call()`将会失效，可以使用同名函数达到相同的效果。

3、返回100以内的素数
```
function prime(){
  var  arr = [];
  for(var i=1; i<100; i++){
    for(var j=2; j<100; j++){
      if(i%j === 0){
        break;
      }
    }
    if(i==j&&i!=1){
      arr.push(i);
    }
  }
  return arr;
}
prime();
```











