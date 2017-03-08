###[女神](https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxgeticon?seq=666763777&username=@4f873dc74aadf84024d98e388ce128ca&skey=@crypt_62a633fb_209bfde195bcf54b88f99ce2646e0a1d&type=big)
###[求windows下git bash配色方案及方法](https://segmentfault.com/q/1010000008603023)
$ cd ~

$ vim .minttyrc https://github.com/mavnn/mintty-colors-solarized
###[使用第三方的守护程序启动，比如 forever 、 pm2](https://segmentfault.com/q/1010000008567492)
```js
npm install pm2 -g
2.用pm2运行你的项目入口文件

pm2 start [项目入口文件].js
3.如果需要热更新，需要加上参数--watch

pm2 start [项目入口文件].js --watch
###[求教使用python库提取pdf](https://segmentfault.com/q/1010000008611030)
使用过pypdf 对英文pdf文档处理比较简单，但是对中文的支持好像不太好
import textract
import pyPdf
import pdf2text
import pdfminer
import chardet

text = textract.process("F:ll.pdf",method = 'pdfminer')print text
import textract
import pyPdf
import pdfminer
import chardet

text = textract.process("F:ll.pdf",method = 'pdfminer')print text
```
###[有没有适配移动页面的pdf转html工具](https://segmentfault.com/q/1010000008611494)
###[javascript动态插入DOM节点详细过程如何](https://segmentfault.com/q/1010000008604339)
```js
<iframe src="A.html" width="" height=""></iframe>
<iframe src="null" name="frame" id="frame" width="" height=""></iframe>
<ul>
    <li><a href="B.html" target="frame">我是主角，点我右边跳到页B</a><b onclick="new A()">点我new A()</b></li>
    <li><a href="C.html" target="frame">我是配角，点我右边跳刀页C</a></li>
</ul>
<script type="text/javascript">
    function A() {
        if (!this.dom) {
            this.dom = this.create()
            this.bindEvent()
            this.append()
        } else {
            /* OOXX */
            this.append()
        }
    }
    A.prototype.create = function() {
        /* 创建DOM对象 append到右侧iFrame */
        return document.createElement('div');
    }
    A.prototype.append = function() {
        /* 把this.dom追加到父容器 假定是body */
        top.document.getElementById('frame').contentDocument.body.appendChild(this.dom);
    }
    A.prototype.bindEvent = function() {
        /* 绑定事件 click, alert(123)*/
        this.dom.addEventListener('click', function() {
            alert(1)
        }, false);
    }
</script>
```

###[一个表只能有一个 自增键](https://segmentfault.com/q/1010000008610794)
```js
如果你的主键已经是自增了，那么这个表不能再有自增键了。
如果你的主键是非自增的，那么可以设置一个

ALTER TABLE `weibo`
ADD COLUMN `serial`  bigint UNSIGNED NOT NULL AUTO_INCREMENT AFTER `id`,
ADD INDEX `serial` (`serial`) USING BTREE,
AUTO_INCREMENT=94381;

create table test(
   id int not null,
   noid int not null auto_increment,
   primary key(id), --如果主键设置了自增长，那么其他列就不能在设置自增长了
   key(noid) -- 如果给其他列设置自增长，那么必须为其创建一个索引，索引类型有很多，自行查资料
)engine=xxx auto_increment=10000;
```

###[内部函数调用对象的问题](https://segmentfault.com/q/1010000008603916)
```js
function Person(name){
    var name=name;
    function getName(){
        return name;
    }
    getName();
}
Person("Nicholas");
在全局作用域中，调用Person("Nicholas")对象是window,那么在Person函数的内部，
调用getName()函数的对象该是谁呢？？？

补充:
 function Person(name){
    var name=name;
    function getName(){
        alert(name);
    }
    window.getName();//window.getName is not a function 
}
Person("Nicholas");
```
###[文章、分类、标签的mysql查询问题](https://segmentfault.com/q/1010000008601655)

```js
CREATE TABLE `articles` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `title` varchar(50) NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ;

CREATE TABLE `tags` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `name` varchar(50) NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ;

CREATE TABLE `tag_links` (
    `aid` int(11) NOT NULL,
    `tid` int(11) NOT NULL,
    PRIMARY KEY (`aid`,`tid`)
);

-- insert demo data

INSERT INTO `articles` (`id`,`title`) VALUES (1,'xxx'),(2,'a'),(3,'b');
INSERT INTO `tags` (`id`,`name`) VALUES (1,'php'),(2,'java'),(3,'sql');

INSERT INTO `tag_links` (`aid`,`tid`) VALUES (1,2),(1,3);
INSERT INTO `tag_links` (`aid`,`tid`) VALUES (2,1),(2,3);

-- query statement

SELECT a.title as 'title',
(
    SELECT GROUP_CONCAT(t.name,' ') FROM `tags` t 
    LEFT JOIN `tag_links` tl 
    ON t.id=tl.tid 
    WHERE tl.aid=a.id
) as 'tags'
FROM `articles` a;

```
###[判断每个月的每一周是几号到几号](https://segmentfault.com/q/1010000008608311)

```js
print_r(date('Y-m-d', strtotime('first mon of january'))); 
for($i=1;$i<=52;$i++){
    $month = ($i < 10) ? '0'.$i : $i;
    echo '第'.$i.'周开始：';
    $monday = date('Y-m-d', strtotime('2017W'.$month));
    echo $monday;
    echo '第'.$i.'周结束：';
    $sunday = date('Y-m-d' ,strtotime($monday . '+6day'));
    echo $sunday;
    echo '<br>';
}

function get_weekinfo($month){
    $weekinfo = array();
    $end_date = date('d',strtotime($month.' +1 month -1 day'));
    for ($i=1; $i <$end_date ; $i=$i+7) { 
        $w = date('N',strtotime($month.'-'.$i));
 
        $weekinfo[] = array(date('Y-m-d',strtotime($month.'-'.$i.' -'.($w-1).' days')),date('Y-m-d',strtotime($month.'-'.$i.' +'.(7-$w).' days')));
    }
    return $weekinfo;
}
print_r(get_weekinfo('2013-11'));
 
//执行结果
Array    
(    
   [0] => Array    
       (    
           [0] => 2013-11-25    
           [1] => 2013-12-01    
       )    
   [1] => Array    
       (    
           [0] => 2013-12-02    
           [1] => 2013-12-08    
       )    
   [2] => Array    
       (    
           [0] => 2013-12-09    
           [1] => 2013-12-15    
       )    
   [3] => Array    
       (    
           [0] => 2013-12-16    
           [1] => 2013-12-22    
       )    
   [4] => Array    
       (    
           [0] => 2013-12-23    
           [1] => 2013-12-29    
       )    
)


/**
 * @param DateTime|int|string|null $date 可以是DateTime对象、时间戳、字符串形式的日期或者空值表示当前月份
 * @return array [[1,2,3,4,5], ...] 分别表示这月每一周都是几号到几号
 */
function getWeeksOfMonth($date=null){
    if (is_numeric($date)){
        $d = new DateTime();
        $d->setTimestamp($date);
        $date = $d;
    } else if (is_string($date)){
        $date = new DateTime($date);
    } else if ($date instanceof DateTime){
        // nothing to do
    } else if (!$date){
        $date = new DateTime();
    } else {
        throw new InvalidArgumentException("Invalid type of date!");
    }


    // 当前日期是在一个月里面是第几天
    //  j: 月份中的第几天，没有前导零    1 到 31
    $dateDay = (int)$date->format('j');

    // 这个月1号是星期几
    // N: 1（表示星期一）到 7（表示星期天）
    $beginWeekDay = (int)$date->sub(new DateInterval("P" . ($dateDay - 1) . "D"))->format('N');

    // 这个月最后一天是几号
    // j    月份中的第几天，没有前导零    1 到 31
    $endMonthDay = (int)($date->add(new DateInterval('P1M'))->sub(new DateInterval("P1D"))->format('j'));

    $weeks = [];
    $indexOfWeek = 0;
    $weekDay = $beginWeekDay;

    for ($day = 1; $day <= $endMonthDay; $day++){
        if (!isset($weeks[$indexOfWeek])){
            $weeks[$indexOfWeek] = [];
        }

        $weeks[$indexOfWeek][] = $day;

        $weekDay++;
        if ($weekDay > 7){
            $weekDay = $weekDay - 7;
            $indexOfWeek++;
        }
    }

    // 只要一个星期里面的第一天和最后一天？
    foreach ($weeks as &$week) {
        $week = [$week[0], end($week)];
    }

    return $weeks;
}

// 测试一下：
foreach (getWeeksOfMonth() as $week){
    echo implode(", ", $week) . "\n";
}

// 输出：（今天是2017-03-08）
// 1, 5
// 6, 12
// 13, 19
// 20, 26
// 27, 31
// 



```
###[请问那个带授权的视频怎么下载](https://segmentfault.com/q/1010000008607901)

让视频加载结束，到浏览器缓存目录中找到大小时间吻合的文件
我是 win10 chrome 中测试缓存目录是 C:\Users\当前登录用户名\AppData\Local\Google\Chrome\User Data\Default\Cache ，然后把找到吻合的文件添加上后缀就可以正常使用了
###[获取网页默认字体的字号大小?](https://segmentfault.com/q/1010000008605788)
getComputedStyle(document.getElementsByTagName("p")[0],undefined).fontSize就可以

刚刚试了用getComputedStyle(document.getElementsByTagName("p")[0],undefined).getPropertyValue("font-size")也可以,你这个更方便
###[PHP多维数组排序问题](https://segmentfault.com/q/1010000008605080)
```js
如果a相等，那么比较b，b相等再比较c，排序完在生成个新字段sort 作为标识
        $arr = [
            0 =>[
                a=>1,
                b=>3,
                c=>3
            ],
            1=>[
                a=>2,
                b=>2,
                c=>3
            ],
            2=>[
                a=>1,
                b=>2,
                c=>3
            ]
        ];
        for($i = 0;$i < count($arr); $i++){
            for($j = $i+1;$j <count($arr);$j++){
                $temp1 = $arr[$i];
                $temp2 = $arr[$j];
                if($temp1["a"] > $temp2["a"] || ($temp1["a"] == $temp2["a"] && $temp1["b"] > $temp2["b"]) || ($temp1["a"] == $temp2["a"] && $temp1["b"] == $temp2["b"] && $temp1["c"] > $temp2["c"])){
                    $arr[$i] = $temp2;
                    $arr[$j] = $temp1;
                }
            }
        }
        var_dump($arr);
	<?php

$a = $b = $c = [];

array_map(function( $value ) use ( &$a,&$b,&$c ){
      array_push($a, $value['a']);
      array_push($b, $value['b']);
      array_push($c, $value['c']);
} , $arr);

$count = $arr;

var_dump($count);
array_multisort(
    $a,SORT_ASC,
    $b,SORT_ASC,
    $c,SORT_ASC,
    $arr
);

var_dump($arr);
```
###[如何使用html5的FileApi上传大文件](https://segmentfault.com/q/1010000008603884)

https://github.com/recca0120/upload  https://famanoder.com/bokes/5867fddf4aee37201fb4d1d3 
###[如何在浏览器上启动本地的应用程序](https://segmentfault.com/q/1010000008596199)
http://geeklu.com/2011/01/start-application-from-url-talk-about-wangwang/
###[正则表达式匹配@和空格之间的字符](https://segmentfault.com/q/1010000008594625)
```js
preg_match('/@(.*) /', 'AAA@XXX YYYY', $result);
if(preg_match_all(
    '%@(\w+)%u', 
    '@张全蛋 含泪质检@三星Note7 被炸飞,听说@炸机 跟@啤酒 更配哦!', 
    $arr
)) {
    var_export($arr);
}
//输出
array (
  0 => 
  array (
    0 => '@张全蛋',
    1 => '@三星Note7',
    2 => '@炸机',
    3 => '@啤酒',
  ),
  1 => 
  array (
    0 => '张全蛋',
    1 => '三星Note7',
    2 => '炸机',
    3 => '啤酒',
  ),
)
正则表达式 %@(\w+)%u 中:
%是分隔符.
u是修饰符,表示unicode.
\w是元字符,在ASCII下等价于[A-Za-z0-9_],在unicode下表示字符(包括中文)和数字和下划线.
+是量词,表示1个或多个,等价于{1,}的写法.
()表示子模式,体现在匹配结果中的$arr[1]里.
区别于主模式,体现在匹配结果中的$arr[0]里.

另外,也可以试试下面这个正则表达式:

%@(\S+)\s%
其中:
\s   匹配空白字符,包括:空格,制表符(\t,\v),回车(\r),换行(\n),换页(\f),等价于[ \t\r\n\v\f]
\S   匹配除空白字符外的任意字符,等价于[^ \t\r\n\v\f]
另外:

preg_match:     返回模式的匹配次数,0次(不匹配)或1次,因为preg_match在第1次匹配后会停止搜索.
preg_match_all: 返回完整匹配次数,如果发生错误返回FALSE.
也就是说,如果上面的例子使用preg_match,那只能匹配到字符串中的"张全蛋".
```
###[php56-redis 安装出错](https://segmentfault.com/q/1010000008594306)

brew install php56-igbinary --build-from-source



###[python list 写进txt中的问题](https://segmentfault.com/q/1010000008604099)
```js
# -*- coding: utf-8 -*-
import sys

reload(sys)
sys.setdefaultencoding('utf-8')

from bs4 import BeautifulSoup
import requests
url = 'http://news.qq.com/'
wb_data = requests.get(url).text
soup = BeautifulSoup(wb_data,'lxml')
news_titles = soup.select('div.text > em.f14 > a.linkto')
file = open('新闻.txt', 'a')
for n in news_titles:
    title = n.get_text()

    link = n.get("href")
    b = str(title) + ' 链接: ' + link +"\n"
    file.write(str(b))

file.close()

for n in news_titles:
    title = n.get_text()

    link = n.get("href")


    b = []
    b.append(title + '链接' + link)
    
with open('/Users/sufan/Desktop/新闻.txt', 'w') as file:
    file.write(str(b))
```

###[大家有javascript检测回文](https://segmentfault.com/q/1010000008606309)
```js
function isHuiwen(text) {
  if(text.length <= ) return true;
  if(text.charAt(0) != text.chatAt(text.length - 1)) return false;
  return isHuiwen(text.substr(1, text.length - 2));
}
function isHuiwen(str){
     str = str.replace(/\W\s_/gi,'');
     return str.toLowerCase == str.split('').reverse().join('').toLowerCase();
}
return  text.split('').reverse().join('')==text;
function palindrome(str) {
    if (!str) return false; // null或undefined
    for ( var i = 0, len = str.length; i < Math.floor(len/2); i++ ) {
        if (str[i] !== str[len - 1 - i]) {
            return false;
        }
    }
    return true;
}
['1', null, '121', '111', undefined, '1a1', 'asdf', '', '我为人人人人为我'].forEach(function(v) {
    console.log(v, palindrome(v) ? '是回文' : '不是回文');
});
```
###[Python 练习实例2](http://www.runoob.com/python/python-exercise-example2.html)
```js
利润(I)低于或等于10万元时，奖金可提10%；利润高于10万元，低于20万元时，低于10万元的部分按10%提成，高于10万元的部分，可提成7.5%；20万到40万之间时，高于20万元的部分，可提成5%；40万到60万之间时高于40万元的部分，可提成3%；60万到100万之间时，高于60万元的部分，可提成1.5%，高于100万元时，超过100万元的部分按1%提成，从键盘输入当月利润I，求应发放奖金总数
i = int(raw_input('净利润:'))
arr = [1000000,600000,400000,200000,100000,0]
rat = [0.01,0.015,0.03,0.05,0.075,0.1]
r = 0
for idx in range(0,6):
    if i>arr[idx]:
        r+=(i-arr[idx])*rat[idx]
        print (i-arr[idx])*rat[idx]
        i=arr[idx]
print r
输入某年某月某日，判断这一天是这一年的第几天

year = int(raw_input('year:\n'))
month = int(raw_input('month:\n'))
day = int(raw_input('day:\n'))
 
months = (0,31,59,90,120,151,181,212,243,273,304,334)
if 0 < month <= 12:
    sum = months[month - 1]
else:
    print 'data error'
sum += day
leap = 0
if (year % 400 == 0) or ((year % 4 == 0) and (year % 100 != 0)):
    leap = 1
if (leap == 1) and (month > 2):
    sum += 1
print 'it is the %dth day.' % sum

斐波那契数列。
def fib(n):
	a,b = 1,1
	for i in range(n-1):
		a,b = b,a+b
	return a
 # 使用递归
def fib(n):
	if n==1 or n==2:
		return 1
	return fib(n-1)+fib(n-2)
def fib(n):
    if n == 1:
        return [1]
    if n == 2:
        return [1, 1]
    fibs = [1, 1]
    for i in range(2, n):
        fibs.append(fibs[-1] + fibs[-2])
    return fibs
输出 9*9 乘法口诀表
for i in range(1, 10):
    print 
    for j in range(1, i+1):
        print "%d*%d=%d" % (i, j, i*j),    
print time.strftime('%Y-%m-%d %H:%M:%S',time.localtime(time.time()))

古典问题：有一对兔子，从出生后第3个月起每个月都生一对兔子，小兔子长到第三个月后每个月又生一对兔子，假如兔子都不死，问每个月的兔子总数为多少？
兔子的规律为数列1,1,2,3,5,8,13,21....
f1 = 1
f2 = 1
for i in range(1,21):
    print '%12ld %12ld' % (f1,f2),
    if (i % 3) == 0:
        print ''
    f1 = f1 + f2
    f2 = f1 + f2
判断101-200之间有多少个素数，并输出所有素数。
程序分析：判断素数的方法：用一个数分别去除2到sqrt(这个数)，如果能被整除，则表明此数不是素数，反之是素数。    
h = 0
leap = 1
from math import sqrt
from sys import stdout
for m in range(101,201):
    k = int(sqrt(m + 1))
    for i in range(2,k + 1):
        if m % i == 0:
            leap = 0
            break
    if leap == 1:
        print '%-4d' % m
        h += 1
        if h % 10 == 0:
            print ''
    leap = 1
print 'The total is %d' % h
所谓"水仙花数"是指一个三位数，其各位数字立方和等于该数本身。例如：153是一个"水仙花数"，因为153=1的三次方＋5的三次方＋3的三次方。

for n in range(100,1000):
    i = n / 100
    j = n / 10 % 10
    k = n % 10
    if n == i ** 3 + j ** 3 + k ** 3:
        print n
一球从100米高度自由落下，每次落地后反跳回原高度的一半；再落下，求它在第10次落地时，共经过多少米？第10次反弹多高？
tour = []
height = []
 
hei = 100.0 # 起始高度
tim = 10 # 次数
 
for i in range(1, tim + 1):
    tour.append(hei)
    hei /= 2
    height.append(hei)
 
print('总高度：tour = {0}'.format(sum(tour)))
print('第10次反弹高度：height = {0}'.format(height[-1]))
有一分数序列：2/1，3/2，5/3，8/5，13/8，21/13...求出这个数列的前20项之和
a = 2.0
b = 1.0
s = 0
for n in range(1,21):
    s += a / b
    t = a
    a = a + b
    b = t
print s

a = 2.0
b = 1.0
l = []
for n in range(1,21):
    b,a = a,a + b
    l.append(a / b)
print reduce(lambda x,y: x + y,l)
求1+2!+3!+...+20!的和。
n = 0
s = 0
t = 1
for n in range(1,21):
    t *= n
    s += t
print '1! + 2! + 3! + ... + 20! = %d' % s
1 2 6 24 120 720 5040 40320 362880 3628800 39916800 479001600 6227020800 8717829
1200 1307674368000 20922789888000 355687428096000 6402373705728000 1216451004088
32000 2432902008176640000
利用递归方法求5!
def fact(j):
    sum = 0
    if j == 0:
        sum = 1
    else:
        sum = j * fact(j - 1)
    return sum

for i in range(5):
    print '%d! = %d' % (i,fact(i))

有5个人坐在一起，问第五个人多少岁？他说比第4个人大2岁。问第4个人岁数，他说比第3个人大2岁。问第三个人，又说比第2人大两岁。问第2个人，说比第一个人大两岁。最后问第一个人，他说是10岁。请问第五个人多大
def age(n):
    if n == 1: c = 10
    else: c = age(n - 1) + 2
    return c
print age(5)
x = int(raw_input("计算个十百千万数字:\n"))
a = x / 10000
b = x % 10000 / 1000
c = x % 1000 / 100
d = x % 100 / 10
e = x % 10
一个5位数，判断它是不是回文数。即12321是回文数，个位与万位相同，十位与千位相同。
求100之内的素数。
for num in range(2,101):
	# 素数大于 1
	if num > 1:
		for i in range(2,num):
			if (num % i) == 0:
				break
		else:
			print(num)
将一个数组逆序输出。
a = [9,6,5,4,1]
    N = len(a) 
    print a
    for i in range(len(a) / 2):
        a[i],a[N - i - 1] = a[N - i - 1],a[i]用第一个与最后一个交换。
    print a
MAXIMUM = lambda x,y :  (x > y) * x + (x < y) * y
MINIMUM = lambda x,y :  (x > y) * y + (x < y) * x
MAXIMUM(a,b)
求0—7所能组成的奇数个数。
sum = 4
    s = 4
    for j in range(2,9):
        print sum
        if j <= 2:
            s *= 7
        else:
            s *= 8
        sum += s
    print 'sum = %d' % sum
  有n个人围成一圈，顺序排号。从第一个人开始报数（从1到3报数），凡报到3的人退出圈子，问最后留下的是原来第几号的那位。
  nmax = 50
    n = int(raw_input('请输入总人数:'))
    num = []
    for i in range(n):
        num.append(i + 1)

    i = 0
    k = 0
    m = 0

    while m < n - 1:
        if num[i] != 0 : k += 1
        if k == 3:
            num[i] = 0
            k = 0
            m += 1
        i += 1
        if i == n : i = 0

    i = 0
    while num[i] == 0: i += 1
    print num[i]
```









###[TensorFlow 是一个用于人工智能的开源神器](http://www.tensorfly.cn/)
###[笔记1: 介绍TensorFlow](http://xinqiu.me/2017/01/20/cs20si-1/)

四舍六入五成双 的算法  小于等于4的时候舍去
大于等于6的时候进位
5的时候，看5前面是基数还是偶数  偶舍基进位 适合浮点计算中有强迫症的同学

四舍六入五凑偶 PHP_ROUND_HALF_EVEN 这个参数就就是：四舍五入六成双吧 parse_url() 函数可以解析 URL，获取顶级域名。

###&
```js
 $a=1;
function fun1($a){
$a=$a+1;echo $a;
}
function fun2(&$a){
  $a=$a+3;
}
echo $a;echo '<br>';1
fun1($a);echo '<br>';2
echo $a;echo '<br>';1
fun2($a);echo '<br>';4
echo $a;echo '<br>';
```
###[开源文档](http://dokuwiki.org/)
查看忘记密码 
内置的 web 服务器  nodejs anywhere 
php -S localhost:8000 python -m 
###[PHP 之道 ](http://laravel-china.github.io/php-the-right-way/)
```js
http://php.net/features.commandline.webserver https://github.com/phpbrew/phpbrew
http://pear.php.net/package/PHP_CodeSniffer/ https://github.com/benmatselby/sublime-phpcs
https://psr.phphub.org/  http://www.php-fig.org/psr/psr-0/ 
PHP-FIG 废弃了上一个自动加载标准： PSR-0，而采用新的自动加载标准 PSR-4。但 PSR-4 要求 PHP 5.3 以上的版本http://www.php-fig.org/psr/psr-4/ 
PHP 会在脚本运行时根据参数设置两个特殊的变量，$argc 是一个整数，表示参数个数，$argv 是一个数组变量，包含每个参数的值
curl -s https://getcomposer.org/composer.phar -o $HOME/local/bin/composer
chmod +x $HOME/local/bin/composer

Composer 会建立一个 composer.lock 文件，在你第一次执行 php composer install 时，存放下载的每个依赖包精确的版本编号。假如你要分享你的项目给其他开发者，并且 composer.lock 文件也在你分享的文件之中的话。 当他们执行 php composer.phar install 这个命令时，他们将会得到与你一样的依赖版本。 当你要更新你的依赖时请执行 php composer update。请不要在部署代码的时候使用 composer update，只能使用 composer install 命令，否则你会发现你在生产环境中用的是不同的扩展包依赖版本。
$end = clone $start;
$end->add(new DateInterval('P1M6D'));

$diff = $end->diff($start);
echo 'Difference: ' . $diff->format('%m month, %d days (total: %a days)') . "\n";
// Difference: 1 month, 6 days (total: 37 days)

if ($start < $end) {
    echo "Start is before end!\n";
}
在操作 Unicode 字符串时，请你务必使用 mb_* 函数。例如，如果你对一个 UTF-8 字符串使用 substr()，那返回的结果中有很大可能会包含一些乱码。正确的方式是使用 mb_substr()。
strpos() 和 strlen()，确实需要特别的处理。这些函数名中通常包含 mb_*：比如，mb_strpos() 和 mb_strlen()。
$pdo = new PDO('sqlite:/path/db/users.db');
$stmt = $pdo->prepare('SELECT name FROM users WHERE id = :id');
$id = filter_input(INPUT_GET, 'id', FILTER_SANITIZE_NUMBER_INT); // <-- filter your data first (see [Data Filtering](#data_filtering)), especially important for INSERT, UPDATE, etc.
$stmt->bindParam(':id', $id, PDO::PARAM_INT); // <-- Automatically sanitized for SQL by PDO
$stmt->execute();
三个最常见的的信息类型是错误（error）、通知（notice）和警告（warning）。它们有不同的严重性: E_ERROR 、E_NOTICE和 E_WARNING。error_reporting(E_ERROR | E_WARNING);
基本上你可以利用 ErrorException 类抛出「错误」来当做「异常」，这个类是继承自 Exception 类。
try
{
    $email->send();
}
catch(Fuel\Email\ValidationFailedException $e)
{
    // 验证失败
}
catch(Fuel\Email\SendingFailedException $e)
{
    // 这个驱动无法发送 email
}
finally
{
    // 无论抛出什么样的异常都会执行，并且在正常程序继续之前执行
}
display_errors = On
display_startup_errors = On
error_reporting = -1将值设为 -1 将会显示出所有的错误
log_errors = On

如果黑客将一个构造的 id 参数通过像 http://domain.com/?id=1%3BDELETE+FROM+users 这样的 URL 传入。这将会使 $_GET['id'] 变量的值被设为 1;DELETE FROM users 然后被执行从而删除所有的 user 记录
PHP 5.5 中自带了 opcode 缓存工具，叫做OPcache 当一个 PHP 文件被解释执行的时候，首先是被编译成名为 opcode 的中间代码，然后才被底层的虚拟机执行。 如果PHP文件没有被修改过，opcode 始终是一样的。这就意味着编译步骤白白浪费了 CPU 的资源。
```