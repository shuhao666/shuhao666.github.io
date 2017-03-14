###[遭遇php的in_array低性能08/28/2013](http://www.zendstudio.net/archives/php-in_array-s-low-performance/?utm_source=tool.lu)
```js
$y="1800";
$x = array();
for($j=0;$j&lt;2000;$j++){
        $x[]= "{$j}";
}
 
for($i=0;$i&lt;3000;$i++){
        if(in_array($y,$x)){
                continue;
        }
}
strace -ttt -o xxx /usr/local/php/bin/php test.php
ltrace -c /usr/local/php/bin/php test.php

in_array这种松比较，会将两个字符型数字串先转换为长整型再进行比较，却不知性能就耗在这上面了。
最简单的就是为in_array加第三个参数为true，即变为严格比较，同时还要比较类型，这样避免了PHP自作聪明的转换类型
面对大数组查询的时候，在PHP中应该尽量采用key查询而不是value查询。函数in_array的性能是不好的。
所以文中的例子代码如果改为下面的，应该会快很多：
<?php
$y="1800";
$x = array();
for($j=0;$j<2000;$j++){
$x[{$j}]= true;
}
for($i=0;$i
总之，大数组查询，用in_array函数是个糟糕的选择。应该尽量用isset函数或array_key_exists函数来替代 。in_array函数的复杂度是O(n)，而isset函数的复杂度是O(1)。 大数据都不要用in_array，要么先array_flip,isset($arr[$key]),要么strpos

$elemCount = 1000;
$repeatCount = 1000000;
 
$vArr = range(1, $elemCount);
$kArr = array_flip($vArr);
 
$start = microtime(true);
for ($i = 0; $i < $repeatCount; $i++) {
    in_array($i, $vArr);
}
$inArrTime = microtime(true) - $start;
echo "in_array:{$inArrTime}<br>";
 
$start = microtime(true);
for ($i = 0; $i < $repeatCount; $i++) {
    array_key_exists($i, $kArr);
}
$keyTime = microtime(true) - $start;
echo "array_key_exists:{$keyTime}<br>";
 
$start = microtime(true);
for ($i = 0; $i < $repeatCount; $i++) {
    isset($kArr[$i]);
}
$issetTime = microtime(true) - $start;
echo "isset:{$issetTime}<br>";

in_array:1.6679329872131
array_key_exists:0.23828101158142
isset:0.022069931030273
```
###[第三方微信登录和支付开发记录](https://blog.skyx.in/archives/348/)
微信的网页应用、移动应用、公众号的上限都是10个，所有同一个账号下的应用获取到的 union_id 是相同的，open_id 不同，所以需注意应用数量是否会超过上限。
微信登录目前只有APP登录、扫码登录和公众号登录三种登录方式，在微信浏览器内打开网页使用的是公众号登录的方式，其他浏览器只能使用扫码登录，换句话说目前移动端非微信浏览器打开的网页基本无法使用微信登录。 通过微信自带浏览器打开网页时唤起微信授权登录页面进行授权登录。


https://open.weixin.qq.com/connect/oauth2/authorize?appid=wxa77178c0a05b6498&redirect_uri=http%3A%2F%2Fh5.2144.com%2Fsite%2Fauth%3Fauthclient%3Dweixin-mp&scope=snsapi_userinfo&response_type=code

https://open.weixin.qq.com/connect/oauth2/authorize?appid=wxa77178c0a05b6498&response_type=code&redirect_uri=http%3A%2F%2Fh5.2144.com%2Fsite%2Fauth%3Fauthclient%3Dweixin-mp&scope=snsapi_userinfo

上面两个链接除了参数的顺序不同之外完全相同，但是上面那个链接可以正常显示授权页面，下面那个则不可以
###[PHP修改apk文件的comment实现](https://blog.skyx.in/archives/319/)
```js
$comment = '123测试';
 
$file = fopen('R:\1.apk', 'r+');
fseek($file, -2, SEEK_END);
fwrite($file, pack('s', mb_strlen($comment, '8bit')));
fwrite($file, $comment);
fclose($file);
 
$zip = new ZipArchive();
 
$zip->open('R:\1.apk');
var_dump($zip->getArchiveComment());
//$zip->setArchiveComment($comment);
//var_dump($zip->getArchiveComment());
$zip->close();
```
###[使用phpbrew管理php版本](https://blog.skyx.in/archives/322/)
curl -L -O https://github.com/phpbrew/phpbrew/raw/master/phpbrew
chmod +x phpbrew
sudo mv phpbrew /usr/bin/phpbrew
###[开源一个简单轻量的高性能PHP路由实现](https://blog.skyx.in/archives/303/)
```js
Github:https://github.com/takashiki/cdo
$do = new \Mis\Cdo();
 
$do->get('/', function () {
    echo 'hello world';
});
 
$do->post('/', function () {
    $name = isset($_POST['name']) ? $_POST['name'] : 'world';
    echo "hello {$name}";
});
 
$do->any('/(\d+)', function ($id) {
    echo $id;
});
 
/**
 * When using named subpattern, order of parameters is not matter.
 * eg. /book/2
 */
$do->any('/(?P<type>\w+)/(?P<page>\d+)', function ($page, $type) {
    echo $type.'<br>'.$page;
});
 
$do->run();
```
###[使用db2md生成表结构](https://blog.skyx.in/archives/275/)
https://github.com/index0h/node-db2md
```js
这个简直是神器，写数据库文档再也不用头疼了。

首先要安装node和npm，这就不多说了。然后使用npm安装db2md：
npm install db2md -g
安装完成后创建配置文件db2md.json，示例如下：

{
    "user": "root",
    "pass": "123456",
    "database": "test",
    "tables": ".*"
}
配置完成后即可以开始导出：


db2md -o tables.md
```
###[极简图床、sm.ms图床curl上传图片](https://blog.skyx.in/archives/164/)
```js
$data = base64_encode(file_get_contents('test.jpg'));
 
$ch = curl_init('http://yotuku.cn/upload?name=');                                                                      
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");                                                                     
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);                                                                  
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);                                                                      
curl_setopt($ch, CURLOPT_HTTPHEADER, array(                                                                          
    'Content-Type: text/plain',                                                                                
    'Content-Length: ' . strlen($data))                                                                       
);                                                                                                                   
 
$result = curl_exec($ch);
echo $result;
$url = 'https://sm.ms/index.php?act=upload';
$image = curl_file_create(realpath('test.jpg'), 'image/jpg', 'test.jpg');
 
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, ['smfile' => $image]);
$data = curl_exec($ch);
curl_close($ch);
echo $data;
 
```
###[linux服务器上mysql无法远程连接的可能原因](https://blog.skyx.in/archives/152/)
```js
grant all privileges on *.* to root@"%" identified by "password" with grant option;
 
flush privileges;
第一个代表数据库名；第二个代表表名。root：授予用户账号。“%”：表示授权的用户IP，%代表任意的IP地址都能访问。“password”：分配账号对应的密码。第二行命令是刷新权限信息
如果telnet不通，我们先用netstat查看3306端口是否已监听所有ip地址的请求：


netstat -an | grep 3306
如果输出为


tcp        0      0 127.0.0.1:3306            0.0.0.0:*               LISTEN
则说明只监听了本地连接。解决方法：修改/etc/mysql/my.cnf文件。打开文件，找到下面内容：


# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address  = 127.0.0.1
把上面bind-address = 127.0.0.1这一行注释掉或者把127.0.0.1换成合适的IP。
重新启动mysql再用netstat检测是否为：


tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN
如果这样之后还是telnet不通，那基本就是防火墙的问题了，查看iptables的rules文件里是否包含


-A INPUT -p tcp -m tcp --dport 3306 -j ACCEPT
如果没有该规则的话加入该规则后重启iptables就可以了。
```

###[PHP解析\x22之类的十六进制字符串代码](https://blog.skyx.in/archives/192/)
function parse_hex($string) {
    return preg_replace_callback(
        "(\\\\x([0-9a-f]{2}))i",
        function($a) {return chr(hexdec($a[1]));},
        $string
    );
}

###[开源一个简单的短网址程序Ourls](https://blog.skyx.in/archives/183/)
在线演示地址：http://skyx.in/。

github地址：https://github.com/takashiki/Ourls
###[php读写crx文件](https://blog.skyx.in/archives/264/)
###[PHP Warning: Module 'modulename' already loaded in Unknown on line 0 产生原因及解决方法](https://blog.skyx.in/archives/288/)
该扩展在编译 PHP 时已经 enable 了，但是在 php.ini 中又写了动态调用该扩展的 so 文件。

这时候我们可以查看一下 phpinfo ：
 
php -i | grep 'modulename'
php -i | grep 'php.ini'
然后去对应的 php.ini 文件中去掉该扩展即可
###[PHPStorm中使用php-cs-fixer进行自动代码格式化](https://blog.skyx.in/archives/207/)
composer global require fabpot/php-cs-fixer
jsonp接口xss防范 
只要在header里输出类型设置为javascript即可：

header('Content-type: text/javascript;charset=utf-8');
git config --global core.autocrlf false Windows下git commit时设置不自动将LF转换为CRLF

*nix系统的换行符为LF(\n)，而Windows的换行符为CRLF(\r\n)，所以在Windows上的默认配置的Git会在git pull时将LF换行符换为CRLF，而git push时会再将换行符换回去。然而，当文件中含有中文时Git的这个功能会出现问题，pull时能正常转换，push时却无法正常执行，这时就会出现文件比对时整个文件内容都改变了，但肉眼却无法看出。

解决方法很简单，直接执行以下命令进行全局配置就可以了：

 
git config --global core.autocrlf false
###[Fork过来的项目拉取源项目的更新](https://blog.skyx.in/archives/276/)
git remote add upstream https://github.com/lj2007331/lnmp.git
在每次 Pull Request 前做如下操作，即可实现和上游版本库的同步。

 
git remote update upstream
git rebase upstream/{branch name}
注意的是在rebase操作之前，一定要将checkout到{branch name}所指定的branch
git Push代码
###[PHP源码阅读，PHP设计模式-胖胖的空间](http://www.phppan.com/2014/02/php-var-compare/?utm_source=tool.lu)
###[git发布脚本](http://stackoverflow.com/questions/279169/deploy-a-project-using-git-push?utm_source=tool.lu)
###[An object oriented PHP driver for FFMpeg binary](https://github.com/PHP-FFMpeg/PHP-FFMpeg?utm_source=tool.lu)
$ composer require php-ffmpeg/php-ffmpeg
```js
$ffmpeg = FFMpeg\FFMpeg::create();
$video = $ffmpeg->open('video.mpg');
$video
    ->filters()
    ->resize(new FFMpeg\Coordinate\Dimension(320, 240))
    ->synchronize();
$video
    ->frame(FFMpeg\Coordinate\TimeCode::fromSeconds(10))
    ->save('frame.jpg');
$video
    ->save(new FFMpeg\Format\Video\X264(), 'export-x264.mp4')
    ->save(new FFMpeg\Format\Video\WMV(), 'export-wmv.wmv')
    ->save(new FFMpeg\Format\Video\WebM(), 'export-webm.webm');
```
###[lxml 解析 html 以爬取 豆瓣电影主页本周口碑榜](http://blog.csdn.net/tanzuozhev/article/details/50442243)
```js
import lxml.html
str_url = 'http://movie.douban.com/'
request = urllib.request.Request(str_url)
html_text = urllib.request.urlopen(request).read()
root = lxml.html.fromstring(html_text)
# 获取本页面所有项目名称
movies_list = [a.text for a in  root.cssselect('div.billboard-bd tr td a')]
print(movies_list)
# 获取所有电影超链接
movies_href = [a.get('href') for a in  root.cssselect('div.billboard-bd tr td a')]
print(movies_href)
```


###[51job和智联招聘的自动刷新简历脚本](http://www.woowen.com/php/2014/09/20/51job%E5%92%8C%E6%99%BA%E8%81%94%E6%8B%9B%E8%81%98%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0%E7%AE%80%E5%8E%86%E8%84%9A%E6%9C%AC/)
```js
dl("php_curl.dll");

$ifupdate = false;

$rand = mt_rand(1, 5);

if($rand == 5)
$ifupdate = true;

if($ifupdate)
{
//智联招聘
$ch = curl_init();

curl_setopt($ch, CURLOPT_URL, "...填写刷新简历ajax链接...");

curl_setopt($ch,CURLOPT_COOKIE,"JSpUserInfo=...填写cookie参数...");

$result = curl_exec($ch);

curl_close($ch);

//前程无忧
$ch = curl_init();

curl_setopt($ch, CURLOPT_URL, "...填写刷新简历ajax链接...");

curl_setopt($ch,CURLOPT_COOKIE,"51job=...填写cookie参数...");

$result = curl_exec($ch);

curl_close($ch);

}else
{
echo 'rand not hit';
}

在CURLOPT_URL参数中写上你刷新简历时访问的ajax url 的地址.
在CURLOPT_COOKIE中写上你浏览器cookie中对应参数的value.
然后在linux中使用将脚本1小时一次的执行起来就可以了.
```

###[PHP内核](http://www.imsiren.com/archives/tag/php%e5%86%85%e6%a0%b8)
###[Nginx PHP 使用 limit_req,limit_conn 限制并发，外加白名单](http://www.imsiren.com/archives/1230)
nginx/conf/limit/whiteip.conf 里面是你要忽略限制的白名单IP地址
###[跨浏览器下PHP下载文件名中的中文乱码问题](http://www.cnblogs.com/jiji262/archive/2012/09/21/2697205.html?utm_source=tool.lu)
```js
$ua = $_SERVER["HTTP_USER_AGENT"];

$filename = "中文 文件名.txt";
$encoded_filename = urlencode($filename);
$encoded_filename = str_replace("+", "%20", $encoded_filename);

header('Content-Type: application/octet-stream');

if (preg_match("/MSIE/", $ua)) {
    header('Content-Disposition: attachment; filename="' . $encoded_filename . '"');
} else if (preg_match("/Firefox/", $ua)) {
    header('Content-Disposition: attachment; filename*="utf8\'\'' . $filename . '"');
} else {
    header('Content-Disposition: attachment; filename="' . $filename . '"');
}

print 'ABC';
```
###[JavaScript获取服务器时间](http://amals.org/?id=216)
```js
var xhr = null;
if(window.XMLHttpRequest){
   xhr = new window.XMLHttpRequest();
}else{
 // ie
   xhr = new ActiveObject("Microsoft")
}
// 通过get的方式请求当前文件
xhr.open("get","/");
xhr.send(null);
// 监听请求状态变化
xhr.onreadystatechange = function(){
   var time = null,curDate = null;
   if(xhr.readyState===2){
       // 获取响应头里的时间戳
       time = xhr.getResponseHeader("Date");
       console.log(xhr.getAllResponseHeaders())
       curDate = new Date(time);
       console.log("服务器时间："+curDate.getFullYear()+"-"+(curDate.getMonth()+1)+"-"+curDate.getDate()+" "+curDate.getHours()+":"+curDate.getMinutes()+":"+curDate.getSeconds());
   }
}
```
###[Laravel会成为最成功的PHP框架](http://www.hoehub.com/PHP/141.html?utm_source=tool.lu)
模块化和可扩展性 中间件 各种集成 事件处理 对象关系化映射（ORM）


###[PHP 实现 短URL](http://www.imsiren.com/archives/459)
```js
function base62($x){
    $show='';
    while($x>0){
        $s=$x%62;
        if($s>35){
            $s=chr($s+61);
        }elseif($s>9&&$s<=35){
            $s=chr($s+55);
        }
        $show.=$s;
        $x=floor($x/62);
    }
            return $show;
}
function urlShort($url){
    $url=crc32($url);
    $res=sprintf('%u',$url);
    return base62($res);
}
echo urlShort("http://www.imsiren.com");
```
###[redis 应用场景](http://www.imsiren.com/archives/982)
将redis的List用作队列，这个很轻量级，不用引入别的队列服务器，缺点是可能会丢失数据，因为其持久化方案是redis通用的aof或者rdb方式

2.将排好序的实体id放到LIST中，然后以prefix 实体id为key，用hashtable存储具体的实体信息

3.用ZSET存储排名和带有权重信息的一些实体id，缺点是内存占用太厉害了。

4.用hashtable做一些映射，例如username=>user_id等

5.set可以支持一些逻辑操作，但是排序的时间复杂度不佳，所以我选择了用list

6.set用来做唯一性验证，如果验证某个用户是否已经对某篇文章进行了赞的操作

7.使用redis用来优化内存hash-max-zipmap-entries等参数减少内存使用量

8.排序好的id也可以用string的getRange和setRange命令来实现顺序访问

用LIST不好的是其顺序已经确定，其删除操作耗时O(n)，顺序查找并删除，而且不支持union inter等操作，这些操作可以模拟and 和or这两种关系操作。
###[php strtotime是否有bug](https://segmentfault.com/q/1010000002454116?utm_source=tool.lu)
```js
$beginMon=strtotime("-1 week Monday");
$endMon=strtotime("-1 week Tuesday")-1;
echo date("Y-m-d H:i:s", $beginMon);
echo('<br/>');
echo date("Y-m-d H:i:s", $endMon);
echo("<br />");

//上面是获取本周一的开始与结束时间戳
//结果如下：
//2015-01-05 00:00:00（错误）
//2014-12-29 23:59:59（正确）

$beginSun=strtotime("+0 week Sunday");
$endSun=strtotime("+1 week Monday")-1;
echo date("Y-m-d H:i:s", $beginSun)."<br />";
echo date("Y-m-d H:i:s",$endSun)."<br />";

//上面获取的是本周末的开始与结束时间戳
//结果如下：
//2015-01-04 00:00:00
//2015-01-11 23:59:59(错误)

//以上案例都是在今天测试(非周末 因为周末)
http://php.net/manual/en/datetime.formats.relative.php 
每星期的七天，'sunday' | 'monday' | 'tuesday' | 'wednesday' | 'thursday' | 'friday' | 'saturday' | 'sun' | 'mon' | 'tue' | 'wed' | 'thu' | 'fri' | 'sat' | 'sun' 应该是以当前时间往后推 7 * 24 小时来考量的。本例中，如果你想特指本周的某个星期一，你可以使用 strtotime("-1 week Monday this week"); ，其它的天，类似地，指定是在哪个自然周内（本例即 this week 指定为本自然周），不要让系统自己按自己默认的逻辑处理即可。
```

###[表分区](http://www.jianshu.com/p/52843a98acda)
```js
ALTER TABLE users MODIFY COLUMN `id` bigint(20) unsigned NOT NULL;
ALTER TABLE users DROP PRIMARY KEY;
ALTER TABLE users ADD INDEX `id`(`id`);
ALTER TABLE users PARTITION BY HASH(id) PARTITIONS 64;
分区表限制：
单表最多支持1024个分区
MySQL5.1只能对数据表的整型列进行分区，或者数据列可以通过分区函数转化成整型列;
MySQL5.5的RANGE LIST类型可以直接使用列进行分区
如果分区字段中有主键或唯一索引的列，那么所有的主键列和唯一索引列都必须包含进来
分区表无法使用外键约束
分区必须使用相同的Engine
对于MyISAM分区表，不能在使用LOAD INDEX INTO CACHE操作
对于MyISAM分区表，使用时会打开更多的文件描述符（单个分区是一个独立的文件）
t1#p#p0.ibd t1#p#p1.ibd
mysql> show table status from vhall like 'webinar_user_regs'\G
*************************** 1. row ***************************
           Name: webinar_user_regs
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 728334
 Avg_row_length: 174
    Data_length: 126992384
Max_data_length: 0
   Index_length: 210354176
      Data_free: 231735296
 Auto_increment: NULL
    Create_time: 2017-03-13 12:27:36
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_unicode_ci
       Checksum: NULL
 Create_options: partitioned
        Comment:
1 row in set (0.02 sec)
无论创建何种类型的分区，如果表中存在主键或唯一索引时，分区列必须是唯一索引的一个组成部分
在MySQL5.6中，可以使用清空一个分区数据：alter table operation_log truncate partition2014-01;
清空该分区表所有分区数据：alter table operation_log truncate partition all;

http://blog.csdn.net/tjcyjd/article/details/11194489 

ALTER TABLE users ADD PARTITION PARTITIONS 8;  
            将分区总数扩展到8个。
	    
CREATE TABLE ti2 (id INT, amount DECIMAL(7,2), tr_date DATE)  
    ENGINE=myisam  
    PARTITION BY HASH( MONTH(tr_date) )  
    PARTITIONS 6;    
默认分区限制分区字段必须是主键（PRIMARY KEY)的一部分，为了去除此
限制：
[方法1] 使用ID 
[方法2] 将原有PK去掉生成新PK
alter table results drop PRIMARY KEY;   
```
###[phptrace 是一个追踪（trace）PHP执行流程的工具](http://chuansong.me/n/1031743)
Github：https://github.com/Qihoo360/phptrace
```js
$ ./phptrace -p 3130 -s 3130 为php-fpm的进程ID
phptrace 0.1 demo, published by infra webcore team
process id = 3130
script_filename = /home/renyongquan/opt/nginx//webapp/block.php
[0x7f27b9a99dc8]  sleep /home/renyongquan/opt/nginx/webapp/block.php:6
[0x7f27b9a99d08]  say /home/renyongquan/opt/nginx/webapp/block.php:3
[0x7f27b9a99c50]  run /home/renyongquan/opt/nginx/webapp/block.php:10
-p 参数指定进程pid， -s表示我们需要获取stack信息； -p参数是必需的，并且只能是PHP相关进程（php,php-cli,php-fpm）的pid， 这很好理解，因为我们获取的是PHP的调用信息。-s 如果省略，则phptrace不会打印调用栈，而是实时获取PHP函数执行流程，即phptrace 的第二个功能，也是其主要功能。现在我们仍然回到stack上来。
基础架构快报 
程序输出的第一行，是版本信息，第二行显示了其进程PID，第三行是当前执行的PHP脚本，从第四行开始就是调用栈信息，从打印的 信息可以看出，最外层run函数调用了say函数，最终调用了sleep函数。
```
###[温故而知新之PHP手册](http://www.woowen.com/php/2015/01/21/%E6%B8%A9%E6%95%85%E8%80%8C%E7%9F%A5%E6%96%B0%E4%B9%8BPHP%E6%89%8B%E5%86%8C(1)/?utm_source=tool.lu)
```js
不要使用AND 和 OR 尽量使用 && 和 || 来替代,因为 && 和 || 的优先级比AND 和 OR要高,连 = 的优先级都比AND 和 OR要高.

 //这个还是要记录下,虽然以前就知道.
    $c = $a or $b 跟 ($c = $a) or $b 同义.
    $c = $a || $b 跟 $c = ($a or $b) 同义.
    不要对each() 中需要遍历的数组赋值 例如
    $a = array('a','v','c');
    while(list($b,$c) = each($a)){          
        $e = $a;
        echo $b;
        echo $c;
    }
    //数组在赋值的时候会重置指针,上面代码会无限循环
传递参数使用sub.x 的时候,PHP会自动转成subx因此应该使用$GET['sub_x']来获取值    
   const BIT_5 = 1 << 5;  //PHP5.6之后可以这么做
    define('BIT_5', 1 << 5);//一直可以
    //define还可以这么写
    for ($i = 0; $i < 32; ++$i) {
        define('BIT_' . $i, 1 << $i);
    }
    //const不能这么定义
    if(1){
        const XX = 'xx';
    }
    //define可以
    if(1){
        define('XX','xx');
    }  
var_dump("10" == "1e1"); // 10 == 10 -> true
    var_dump(100 == "1e2"); // 100 == 100 -> true   
    数组比较,数组中的单元如果具有相同的键名和值则比较时相等
    $a = array("apple", "banana");
    $b = array(1 => "banana", "0" => "apple");

    var_dump($a == $b); // bool(true)
    var_dump($a === $b); // bool(false)
    $a = 1;
$b = 1.25;
debug_zval_dump($a);
debug_zval_dump($b);
$array = [0];
foreach ($array as &$val) {
    var_dump($val);
    $array[1] = 1;
}
PHP7之前，当数组通过 foreach 迭代时，数组指针会移动。现在开始，不再如此
$array = [0, 1, 2];
foreach ($array as &$val) {
    var_dump(current($array)); 

    // PHP7
    // return int(0)

    // PHP5
    // return int(1),int(2),bool(false)
}

$a = '9d9';
for ($i = 0; $i < 10; $i++) {
    $a++;
}
var_dump($a);

//echo 18
当运行到9e0的时候 ,科学计数法转换成了9,那么后面++就成了数字了..

$a = floor((0.1+0.7) * 10);
//返回的结果并不是8,而是7
$a = round((0.1+0.7) * 10);
//返回的结果 = 8
$a = 9 - 5.1;
$b = 3.9;
var_dump(round($a, 2) == round($b, 2));

如果需要判断是否为空,可以使用Isset这种语句,而不要使用Isnull函数.或者使用===Null方法.速度跟Isset差不多,含义跟Isnull一致
键值为数字时  使用 + : 会返回前一个数组的value
使用 array_merge : 返回合并之后的一个数组,且重置键值
键值为字符时 使用 + : 返回数组1的value
使用 array_merge : 返回数组2的value
键值重置 使用 + : 键值不会重置
使用 array_merge : 数字键值会从0开始重置,且返回的合并数组的键值排序是按照先数组1,再数组2的次序来的

当Errorreporting 设置 Notice之上的时候,Arraymerge函数第一个参数为Null也不会报错,但是确实不会返回任何数据了.

个人开发本地/测试环境还是把Errorreporting设置成EALL吧.
__FUNCTION__和METHOD的不同在于前者只会返回函数名称,后者会连类名一起返回
PHP7中在switch语句中不能出现多个default,而php5可以,并且会执行最后一个 新增方法随机数字random_int
ASP的元素标签被移除,不能再使用<% <%=以及<script language="php”> .新增方法随机字节random_bytes
新增intdiv方法,取参数1 除以参数2 然后取整
http://www.woowen.com/php/2015/12/07/PHP7%20%E6%96%B0%E7%89%B9%E6%80%A7,%E6%94%B9%E5%8F%98%E5%8F%98%E5%8C%96/ 

SELECT * FROM `module_images`  WHERE pid = 'xx' and appid = 'xx' and parent in (416,415,419,421,414) GROUP BY parent order by FIELD(parent,416,415,419,421,414)
前面的In里面的顺序可以随便改变,但是后面的需要按照顺序书写. 关键就是这个Order By FIELD.另外不要忘记Group By 不然是会出错的.

arrayshift 在使用arrayshift弹出一个非常大的数组的第一个元素的时候,执行效率会很低.
Array_Pop()的复杂度为O(1)
Array_Shift()的复杂度为O(N)
当你执行一个非常大的数组的时候会随着数组庞大而降低效率.因此当你给一个非常大的数组执行弹出首元素操作的时候可以使用,Arrayreverse() 和 Arraypop()结合的方式.
较频繁地作为查询条件的字段
唯一性太差的字段不适合建立索引
更新太频繁地字段不适合创建索引
不会出现在where条件中的字段不该建立索引


新增两个CSPRNG函数：random_int和random_bytes /dev/urandom 会被作为最后一个可使用的工具
session_start([
    'cache_limiter' => 'private',
    'read_and_close' => true,
]);
覆盖写在 php.ini 中的 session 配置项。
有了preg_replace_callback_array就可以让每个不同的表达式对应不同的回调函数。
$subject = 'Aaaaaa Bbb';
 
echo preg_replace_callback_array(
    [
        '~[a]+~i' => function ($match) {
            return str_repeat('1', strlen($match[0]));
        },
        '~[b]+~i' => function ($match) {
            return str_repeat('2', strlen($match[0]));
        }
    ],
    $subject
);
使用 intdiv() 计算整数除法
生成器支持 return
$gen = (function() {
    yield 1;
    yield 2;
 
    return 3;
})();
 
foreach ($gen as $val) {
    echo $val, PHP_EOL;
}
 
echo $gen->getReturn(), PHP_EOL;
class A {private $x = 1;}
 
// Pre PHP 7 code
$getXCB = function() {return $this->x;};
$getX = $getXCB->bindTo(new A, A::class); // intermediate closure
echo $getX();
 
// PHP 7+ code
$getX = function() {return $this->x;};
echo $getX->call(new A);

// converts all objects into __PHP_Incomplete_Class object
$data = unserialize($foo, ["allowed_classes" => false]);
 
// converts all objects into __PHP_Incomplete_Class object except those of MyClass and MyClass2
$data = unserialize($foo, ["allowed_classes" => ["MyClass", "MyClass2"]);
//emoji微笑
echo "\u{1F603}";
echo [1, 2, 3][0];
echo 'string'[0];
$result = isset($var) ? $var : 'none';
 
$result = $var ?? 'none';  现在可以支持大于 2GB 的文件上传。
```
###[PHP 单例模式和工厂模式](http://www.woowen.com/php/2014/10/22/php%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F%E5%92%8C%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F/)
这个类有且只有一个实例存在,这样可以方式被多个实例化,造成多个操作.或者操作同时进行,你可以将单例用在数据库类上,这样在同一时间只会有一个数据库实例存在,也就是只会有一个数据库连接的存在,这样避免的过多的数据库连接,和不必要的系统资源的浪费.
```js
//单例模式
class myClass{
    static $__staticvar; //静态成员变量
    private $_str; //私有的变量
    private function __construct(){
        $this->_str = '单例模式';
    }
    private function __clone(){} //重载clone方法
    //由于单例不能被其他类所实例化,也就是不能使用$test = new myclass();
    public static function getObject(){
    //判断静态成员中是否有该对象如果没有就重新实例化.有就直接返回
    if(!(self::$__staticvar instanceof self))
    {
        self::$__staticvar = new self();
    }
        return self::$__staticvar;
    }
    //单例的一个方法
    public function getStr(){
        return $this->_str;
    }
}

$dl = myClass::getObject();
print_r($dl->getStr());
工厂模式,就是你定义一个接口,在接口中写下哪些方法可能会被用到.然后将每个特定的类起调用这个接口,并且重写里面的方法,在通过一个工厂类来根据判断而申明不同的类.工厂类必须返回一个对象,一般的命名规则为factory的静态方法.
//工厂模式
public static function factory($var){
    switch($var){
        case 'xx':
        $test = new classname();
        break;
        case 'xxx':
        $test = new classname1();
        break;
    }
return $test;
}

```
###[208.130.29.30-35这个IP段换成CIDR格式](http://mp.weixin.qq.com/s?__biz=MzAwMTEwNzEyOQ%3D%3D&mid=2650009231&idx=1&sn=6845ceb44d7ea6bf6464c3e5d4e0b75c&chksm=82d9fb59b5ae724f13346d784adb54b65378f94cf7f8c08000aecc93681f7e496c1c37b59697&scene=0&utm_source=tool.lu#wechat_redirect)
```js
# 确定起始和结尾IP，无论多复杂都可以转换
startip = '208.130.29.30'
endip = '208.130.29.35'
cidrs = netaddr.iprange_to_cidrs(startip, endip)
for k, v in enumerate(cidrs):
    iplist = v
    print iplist
输出：
208.130.29.30/31
208.130.29.32/30

反过来，CIDR也能直接转成IP地址段：

from netaddr import *

ip = IPNetwork('192.0.2.16/29')
ip_list = list(ip)
print(ip_list)
```
###[php缺点](https://www.toptal.com/php/10-most-common-mistakes-php-programmers-make?utm_source=tool.lu)

```js
$arr = array(1, 2, 3, 4);
foreach ($arr as &$value) {
    $value = $value * 2;
}
// $arr is now array(2, 4, 6, 8)
unset($value);

```
###[Python 技巧总结](http://litaotao.github.io/python-materials?utm_source=tool.lu)
###[字符型图片验证码识别完整过程及Python实现](http://www.cnblogs.com/beer/p/5672678.html?utm_source=tool.lu)
###[python词云 wordcloud 入门](http://blog.csdn.net/tanzuozhev/article/details/50789226?utm_source=tool.lu)
```js
官网: https://amueller.github.io/word_cloud/ 
github: https://github.com/amueller/word_cloud 
wget  https://github.com/amueller/word_cloud/archive/master.zip
unzip master.zip
rm master.zip
cd word_cloud-master
pip install -r requirements.txt
python setup.py install
from os import path
from scipy.misc import imread
import matplotlib.pyplot as plt

from wordcloud import WordCloud, STOPWORDS, ImageColorGenerator

# 获取当前文件路径
# __file__ 为当前文件, 在ide中运行此行会报错,可改为
# d = path.dirname('.')
d = path.dirname(__file__)

# 读取文本 alice.txt 在包文件的example目录下
#内容为
"""
Project Gutenberg's Alice's Adventures in Wonderland, by Lewis Carroll

This eBook is for the use of anyone anywhere at no cost and with
almost no restrictions whatsoever.  You may copy it, give it away or
re-use it under the terms of the Project Gutenberg License included
with this eBook or online at www.gutenberg.org
"""
text = open(path.join(d, 'alice.txt')).read()

# read the mask / color image
# taken from http://jirkavinse.deviantart.com/art/quot-Real-Life-quot-Alice-282261010
# 设置背景图片
alice_coloring = imread(path.join(d, "alice_color.png"))

wc = WordCloud(background_color="white", #背景颜色max_words=2000,# 词云显示的最大词数
mask=alice_coloring,#设置背景图片
stopwords=STOPWORDS.add("said"),
max_font_size=40, #字体最大值
random_state=42)
# 生成词云, 可以用generate输入全部文本(中文不好分词),也可以我们计算好词频后使用generate_from_frequencies函数
wc.generate(text)
# wc.generate_from_frequencies(txt_freq)
# txt_freq例子为[('词a', 100),('词b', 90),('词c', 80)]
# 从背景图片生成颜色值
image_colors = ImageColorGenerator(alice_coloring)

# 以下代码显示图片
plt.imshow(wc)
plt.axis("off")
# 绘制词云
plt.figure()
# recolor wordcloud and show
# we could also give color_func=image_colors directly in the constructor
plt.imshow(wc.recolor(color_func=image_colors))
plt.axis("off")
# 绘制背景图片为颜色的图片
plt.figure()
plt.imshow(alice_coloring, cmap=plt.cm.gray)
plt.axis("off")
plt.show()
# 保存图片
wc.to_file(path.join(d, "名称.png"))
```
###[python2,3对比](http://python-future.org/compatible_idioms.html?utm_source=tool.lu)
###[python脚本集合](https://github.com/realpython/python-scripts?utm_source=tool.lu)
###[Python列表对象实现原理](https://foofish.net/python-list-implements.html?utm_source=tool.lu)
###[Php Imagick常用知识](http://www.woowen.com/php/2014/08/10/PHP%20Imagick%20%E8%B5%84%E6%96%99%E6%B1%87%E6%80%BB/)
html->pdf->imgick->pic

htmltopdf 有第三方公司提供接口http://pdfcrowd.com/

###[数据库设计需要注意的地方](http://www.woowen.com/mysql/2014/10/15/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%AE%BE%E8%AE%A1%20%E6%B3%A8%E6%84%8F%E8%A6%81%E7%82%B9/)


###[多维数组根据键值排序](http://www.woowen.com/php/2014/10/28/php%E5%A4%9A%E7%BB%B4%E6%95%B0%E7%BB%84%E6%8E%92%E5%BA%8F,%E5%AD%97%E7%AC%A6%E7%9B%B8%E4%BC%BC%E5%BA%A6%E5%8C%B9%E9%85%8D%E5%BA%A6/)
```js
/**
     * @param $array 需要排序的数组
     * @param $keyName 数组键值名称
     * @param int $order
     * @return array
     * @desc 多维数组根据键值排序,排序之后重置键值
     * @明心 php 获取2个字符之间的相似度 similar_text()
     */
    public static function array_sort($array, $keyName, $order=SORT_ASC)
    {
        $new_array = array();
        $sortable_array = array();
        if (count($array) > 0) {
            foreach ($array as $k => $v) {
                if (is_array($v)) {
                    foreach ($v as $k2 => $v2) {
                        if ($k2 == $keyName) {
                            $sortable_array[$k] = $v2;
                        }
                    }
                } else {
                    $sortable_array[$k] = $v;
                }
            }

            switch ($order) {
                case SORT_ASC:
                    asort($sortable_array);
                    break;
                case SORT_DESC:
                    arsort($sortable_array);
                    break;
            }
            foreach ($sortable_array as $k => $v) {
                $new_array[$k] = $array[$k];
            }
        }
        //重置混乱键值
        return array_values($new_array);
    }
```

###[PHP二维码类库(phpqrcode.php)详解及应用](http://www.hoehub.com/PHP/176.html?utm_source=tool.lu)
###[php文章索引](http://tool.lu/article/tag/php?page=2)
###[说说 PHP 的 die 和 exit](http://0x1.im/blog/php/php-exit-die.html?utm_source=tool.lu)
###[max/min 函数（PHP）的一个小 BUG](http://0x1.im/blog/php/bug-of-php-function-max.html?utm_source=tool.lu)
>>> max(0, ceil(-0.5))
=> 0
>>> $a = ceil(-0.5)
=> -0.0
>>> max($a, 0)
=> -0.0
###[php的curl调用远程接口](https://my.oschina.net/u/222608/blog/808708?utm_source=tool.lu)
改了curl一个参数CURLOPT_CONNECTTIMEOUT_MS
strace -o output.txt -f -T -tt -e trace=all php test.php
###[Differences between array_replace and array_merge in PHP](http://stackoverflow.com/questions/34367511/differences-between-array-replace-and-array-merge-in-php/34367698?utm_source=tool.lu)
```js
$a = array('a' => 'hello', 'b' => 'world');
$b = array('a' => 'person', 'b' => 'thing', 'c'=>'other', '15'=>'x');

print_r(array_merge($a, $b));
/*Array
(
    [a] => person
    [b] => thing
    [c] => other
    [0] => x
)*/

print_r(array_replace($a, $b));
/*Array
(
    [a] => person
    [b] => thing
    [c] => other
    [15] => x
)*/
$a = array('0'=>'a', '1'=>'c');
$b = array('0'=>'b');

print_r(array_merge($a, $b));
/*Array
(
  [0] => a
  [1] => c
  [2] => b
)*/

print_r(array_replace($a, $b));
/*Array
(
  [0] => b
  [1] => c
)*/
```
###[当cpu飙升时，找出php中可能有问题的代码行](http://www.bo56.com/%E5%BD%93cpu%E9%A3%99%E5%8D%87%E6%97%B6%EF%BC%8C%E6%89%BE%E5%87%BAphp%E4%B8%AD%E5%8F%AF%E8%83%BD%E6%9C%89%E9%97%AE%E9%A2%98%E7%9A%84%E4%BB%A3%E7%A0%81%E8%A1%8C/?spm=0.0.0.0.mlzm0G&utm_source=tool.lu)
###[php命令行](http://www.phpsh.org/?utm_source=tool.lu)
###[JavaScript + PHP(正则表达式)](http://amals.org/?id=1&utm_source=tool.lu)
```js
var pattern = /\w[-\w.+]*@([A-Za-z0-9][-A-Za-z0-9]+\.)+[A-Za-z]{2,14}/,
	str = '';
        console.log(pattern.test(str));
	
 $str = '';
        $isMatched = preg_match('/\w[-\w.+]*@([A-Za-z0-9][-A-Za-z0-9]+\.)+[A-Za-z]{2,14}/', $str, $matches);
        var_dump($isMatched, $matches);
匹配双字节字符(包含汉字)var pattern = /[^\x00-\xff]/,
匹配时间(时:分:秒):var pattern = /([01]?\d|2[0-3]):[0-5]?\d:[0-5]?\d/,
匹配身份证: var pattern = /\d{15}|\d{17}[0-9Xx]/,
```
###[php 实现同一个账号同时只能一个人登录](http://blog.51yip.com/php/1698.html?utm_source=tool.lu)
实现原理
1,用户在电脑A登录,session信息存放在redis当中，并将session_id存到mysql数据库中。
2,同一用户在电脑B登录，验证完用户名和密码后，将该用户信息从数据库读出，取得用户在电脑A登录的session_id，然后在到redis中验证session是否过期。
3,如果过期，不用openfire推送提示信息。如果没有过期，php利用openfire推送消息后，在将redis中用户在电脑A中登录的session删除掉，删除后，在将用户在电脑B登录的个人信息放到session中，并将电脑B登录的session_id更新到数据库中，在这里一定要先发送推送，然后在清空session，不然用户在电脑A收不到xmpp发过来的消息。
https://phpbestpractices.org/?utm_source=tool.lu  https://phptrends.com/?utm_source=tool.lu
###[Markdown Parser in PHP](http://parsedown.org/?utm_source=tool.lu)
###[php安全](http://phpsecurity.readthedocs.io/en/latest/index.html?utm_source=tool.lu)
###[APP后端开发系列：登陆系统设计中的注意问题](https://helei112g.github.io/2016/07/12/1-APP%E5%90%8E%E7%AB%AF%E5%BC%80%E5%8F%91%E7%B3%BB%E5%88%97%EF%BC%9A%E7%99%BB%E9%99%86%E7%B3%BB%E7%BB%9F%E8%AE%BE%E8%AE%A1%E4%B8%AD%E7%9A%84%E6%B3%A8%E6%84%8F%E9%97%AE%E9%A2%98/)
```js
验证通过后，把用户uid+username+salt等md5后，作为token返回到客户端。
对token加入时间戳，过期后客户端重新提交username + pwd验证后再发一个token到客户端
服务端生成一个token后下发到客户端，客户端按照约定的规则加密后请求服务端
参考oauth2.0，用户提交username + pwd后，服务端返回以下信息
{
    "access_token":"2YotnFZFEjr1zCsicMWpAA",
    "expires_in":3600,
    "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA"
}
access_token 是用来进行访问的接口的，expires_in 是他的过期时间，到达过期时间后，需要用 refresh_token 来请求服务端刷新 access_token。

这里几个重点是：refresh_token 仅能使用一次，使用一次后，将被废弃。另外这个 access_token 只在 expires_in 有效期内有效。

注意： 这里的 expires_in 仅返回秒数就好了。别返回时间戳。因为各个平台计算s的时间戳，不一致，这样子做更方便处理。
token常用的一种生成方式：

该用户的uid，如：8888
该用户的口令，如： 123123
用户对应的salt，如：abcd
过期时间戳，如：1468293948
把上面几部分拼接起来：888:123123:abcd:1468293948

token = md5(‘888:123123:abcd:1468293948’);
```