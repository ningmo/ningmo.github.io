---
date: 2012-12-18T23:12:08
title: 微博短网址生成算法原理 by cubeking
categories: php
---

<strong>   <a href="http://baike.baidu.com/view/2693499.htm" target="_blank">短网址</a>（Short URL），顾名思义就是在形式上比较短的网址。通常用的是asp或者php转向，在Web 2.0的今天，不得不说，这是一个潮流。目前已经有许多类似服务，借助短网址您可以用简短的网址替代原来冗长的网址，让使用者可以更容易的分享链接。</strong>

<strong>例如：http://t.cn/SzjPjA  表示<a href="http://hi.baidu.com/cubeking/">http://hi.baidu.com/cubeking/</a></strong>

短网址服务，可能很多朋友都已经不再陌生，现在大部分微博、手机邮件提醒等地方已经有很多应用模式了，并占据了一定的市场。估计很多朋友现在也正在使用。

看过新浪的短连接服务，发现后面主要有6个字符串组成，于是第一个想到的就是原来公司写的一个游戏激活码规则，也就是下面的算法2，

26个大写字母 26小写字母，10个数字，随机生成6个然后插入数据库对应一个id，短连接跳转的时候，根据字符串查询到对应id，即可实现相应的跳转！不过2的62次方，不知道有没有重复的，小概率可以，但是对应不是很大的网站应该足够了

自从twitter推出短网址(shorturl),继之国内各大微博跟风,google公开goo.gl使用API,短网址之风愈演愈烈.不得不说这是一个新兴又一大热门web2.0服务.现整理一下,包括完整短网址网站,<a href="http://hi.baidu.com/cubeking/blog/item/5e73241e5d75769786d6b658.html" target="_blank"><strong>短网址生成原理</strong></a>,算法举例,以及优劣比较,同时还介绍几个phper个人实现的.

<strong>算法原理
</strong><strong>算法一
</strong>1)将长网址md5生成32位签名串,分为4段, 每段8个字节;
2)对这四段循环处理, 取8个字节, 将他看成16进制串与0x3fffffff(30位1)与操作, 即超过30位的忽略处理;
3)这30位分成6段, 每5位的数字作为字母表的索引取得特定字符, 依次进行获得6位字符串;
4)总的md5串可以获得4个6位串; 取里面的任意一个就可作为这个长url的短url地址;

这种算法,虽然会生成4个,但是仍然存在重复几率,下面的算法一和三,就是这种的实现.

<strong>算法二
</strong>a-zA-Z0-9 这64位取6位组合,可产生500多亿个组合数量.把数字和字符组合做一定的映射,就可以产生唯一的字符串,如第62个组合就是aaaaa9,第63个组合就是aaaaba,再利用洗牌算法，把原字符串打乱后保存，那么对应位置的组合字符串就会是无序的组合。
把长网址存入数据库,取返回的id,找出对应的字符串,例如返回ID为1，那么对应上面的字符串组合就是bbb,同理 ID为2时，字符串组合为bba,依次类推,直至到达64种组合后才会出现重复的可能，所以如果用上面的62个字符，任意取6个字符组合成字符串的话，你的数据存量达到500多亿后才会出现重复的可能。
具体参看这里彻底完善新浪微博接口和超短URL算法,算法四可以算作是此算法的一种实现,此算法一般不会重复,但是如果是统计的话,就有很大问题,特别是对域名相关的统计,就抓瞎了.

<strong>简单生成短网址方法</strong>

<strong>&lt;?php</strong>

02function <strong>base62</strong>($x)03{04    $show= '';05    while($x&gt; 0) {06        $s= $x% 62;07        if($s&gt; 35) {08            $s= chr($s+61);            09        } elseif($s&gt; 9 &amp;&amp; $s&lt;=35) {10            $s= chr($s+ 55);11        }12        $show.= $s;13        $x= floor($x/62);14    }15    return $show;    16}17  18function <strong>urlShort</strong>($url)19{20    $url= crc32($url);21    $result= sprintf("%u", $url);22    return base62($result);23}24  25echo <strong>urlShort</strong>("<a href="http://hi.baidu.com/cubeking/">http://hi.baidu.com/cubeking/</a>");<strong>?&gt;</strong>
<a href="http://hi.baidu.com/cubeking/blog/item/5e73241e5d75769786d6b658.html" target="_blank"><strong>短网址算法</strong></a><strong>枚举</strong>

代码如下:
&lt;?php
function shorturl($input) {
$base32 = array (
'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h',
'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p',
'q', 'r', 's', 't', 'u', 'v', 'w', 'x',
'y', 'z', '0', '1', '2', '3', '4', '5'
);

$hex = md5($input);
$hexLen = strlen($hex);
$subHexLen = $hexLen / 8;
$output = array();

for ($i = 0; $i &lt; $subHexLen; $i++) {
$subHex = substr ($hex, $i * 8, 8);
$int = 0x3FFFFFFF &amp; (1 * ('0x'.$subHex));
$out = '';

for ($j = 0; $j &lt; 6; $j++) {
$val = 0x0000001F &amp; $int;
$out .= $base32[$val];
$int = $int &gt;&gt; 5;
}

$output[] = $out;
}

return $output;
}
?&gt;

<strong>另一个是纯随机数算法
</strong>&lt;?php
function random($length, $pool = '')
{
$random = '';

if (empty($pool)) {
$pool    = 'abcdefghkmnpqrstuvwxyz';
$pool   .= '23456789';
}

srand ((double)microtime()*1000000);

for($i = 0; $i &lt; $length; $i++)
{
$random .= substr($pool,(rand()%(strlen ($pool))), 1);
}

return $random;
}
?&gt;

各大微博短网址(ShortUrl)的算法 ,第一种算法的C#实现
&lt;?php
// 这是c#代码,受编辑器的限制,只能用php
public static string[] ShortUrl(string url)
{
//可以自定义生成MD5加密字符传前的混合KEY
string key = "Leejor";
//要使用生成URL的字符
string[] chars = new string[]{
"a","b","c","d","e","f","g","h",
"i","j","k","l","m","n","o","p",
"q","r","s","t","u","v","w","x",
"y","z","0","1","2","3","4","5",
"6","7","8","9","A","B","C","D",
"E","F","G","H","I","J","K","L",
"M","N","O","P","Q","R","S","T",
"U","V","W","X","Y","Z"

};
//对传入网址进行MD5加密
string hex = System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(key + url, "md5");

string[] resUrl = new string[4];

for (int i = 0; i &lt; 4; i++)
{
//把加密字符按照8位一组16进制与0x3FFFFFFF进行位与运算
int hexint = 0x3FFFFFFF &amp; Convert.ToInt32("0x" + hex.Substring(i * 8, 8), 16);
string outChars = string.Empty;
for (int j = 0; j &lt; 6; j++)
{
//把得到的值与0x0000003D进行位与运算，取得字符数组chars索引
int index = 0x0000003D &amp; hexint;
//把取得的字符相加
outChars += chars[index];
//每次循环按位右移5位
hexint = hexint &gt;&gt; 5;
}
//把字符串存入对应索引的输出数组
resUrl[i] = outChars;
}

return resUrl;
}
?&gt;

高进制数字转换,使用六十二进制将十进制数字变“短”。例如数据库中第 56800235583 条信息，对应的短网址后缀可以是 ZZZZZZ 。
下面是存储示例：

&lt;?php
//id 自增序列/自增编号
//url 目标链接
//*suffix* 短网址后缀 （并不需要存储在数据库内）
+------------+-----------------------+---------+
|id          | url                   | *suffix*|
+------------+-----------------------+---------+
|123456      | http://zoeey.com/     | w7e     |
+------------+-----------------------+---------+
|123457      | http://www.zoeey.com/ | w7f     |
+------------+-----------------------+---------+
|56800235582 | http://zoeey.org/     | ZZZZZY  |
+------------+-----------------------+---------+
|56800235583 | http://www.zoeey.org/ | ZZZZZZ  |
+------------+-----------------------+---------+
?&gt;
短网址使用流程：
提交网址存储后获取其编号 如：123456
用dec2Any将编号转换为62进制，并拼接网址 如：http://go.to/w7e
用户访问到 http://go.to/w7e 时，提取短网址后缀 w7e
用any2Dec将短网址后缀转换为10进制，得到链接编号 如：123456
使用编号查询链接，并进行跳转[/list]
下面是进制转换所需要的源码：
&lt;?php
/*
* MoXie (SysTem128@GMail.Com) 2010-6-30 17:53:57
*
* Copyright &amp;copy; 2008-2010 Zoeey.Org . All rights are reserved.
* Code license: Apache License  Version 2.0
* http://www.apache.org/licenses/LICENSE-2.0.txt
*/
error_reporting(E_ALL);

/**
* 返回一字符串，十进制 number 以 radix 进制的表示。
* @param dec       需要转换的数字
* @param toRadix    输出进制。当不在转换范围内时，此参数会被设定为 2，以便及时发现。
* @return    指定输出进制的数字
*/
function dec2Any($dec, $toRadix) {
$MIN_RADIX = 2;
$MAX_RADIX = 62;
$num62 = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
if ($toRadix &lt; $MIN_RADIX || $toRadix &gt; $MAX_RADIX) {
$toRadix = 2;
}
if ($toRadix == 10) {
return $dec;
}
// -Long.MIN_VALUE 转换为 2 进制时长度为65
$buf = array();
$charPos = 64;
$isNegative = $dec &lt; 0; //(bccomp($dec, 0) &lt; 0);
if (!$isNegative) {
$dec = -$dec; // bcsub(0, $dec);
}

while (bccomp($dec, -$toRadix) &lt;= 0) {
$buf[$charPos--] = $num62[-bcmod($dec, $toRadix)];
$dec = bcdiv($dec, $toRadix);
}
$buf[$charPos] = $num62[-$dec];
if ($isNegative) {
$buf[--$charPos] = '-';
}
$_any = '';
for ($i = $charPos; $i &lt; 65; $i++) {
$_any .= $buf[$i];
}
return $_any;
}

/**
* 返回一字符串，包含 number 以 10 进制的表示。&lt;br /&gt;
* fromBase 只能在 2 和 62 之间（包括 2 和 62）。
* @param number    输入数字
* @param fromRadix    输入进制
* @return  十进制数字
*/
function any2Dec($number, $fromRadix) {
$num62 = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
$dec = 0;
$digitValue = 0;
$len = strlen($number) - 1;
for ($t = 0; $t &lt;= $len; $t++) {
$digitValue = strpos($num62, $number[$t]);
$dec = bcadd(bcmul($dec, $fromRadix), $digitValue);
}
return $dec;
}

$sol = '&lt;br /&gt;' . PHP_EOL;
echo dec2Any('56800235583', 62), $sol; // ZZZZZZ
echo any2Dec('ZZZZZZ', 62), $sol; // 56800235583
echo dec2Any('123456', 62), $sol; // w7e
echo any2Dec('w7e', 62), $sol; // 123456
?&gt;

另外,介绍一下goo.gl接口api的应用.
据说此算法是各种短网址服务中 "......（这是）互联网上最稳定、最安全、最快速的短网址服务。"(google声称)

有人做了比较，证明确实如此。

除了速度快，goo.gl还提供详细的点击统计。比如，Yahoo首页的短网址是http://goo.gl/QuXj，那么它的统计数据就在http://goo.gl/info/QuXj。加上后缀".qr"，还能得到这个网址的二维条形码，Yahoo的就是http://goo.gl/QuXj.qr。

详细信息可从这查看：http://code.google.com/apis/urlshortener/overview.html

下面是http://www.biaodianfu.com/php-google-urlshortener-api.html弄的一个简单的PHP调用API生成短网址方法，分享给大家。
在使用前，请先到这里申请API Key：http://code.google.com/apis/console/
短网址的生成需要使用POST提交数据，所以使用了curl，短网址的还原可以直接使用file_get_contents()函数。不过为了统一，也使用了curl来处理哈。
&lt;?php
// 生成
function shortenGoogleUrl($long_url){
$apiKey = 'API-KEY'; //Get API key from : http://code.google.com/apis/console/
$postData = array('longUrl' =&gt; $long_url, 'key' =&gt; $apiKey);
$jsonData = json_encode($postData);
$curlObj = curl_init();
curl_setopt($curlObj, CURLOPT_URL, 'https://www.googleapis.com/urlshortener/v1/url');
curl_setopt($curlObj, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($curlObj, CURLOPT_SSL_VERIFYPEER, 0);
curl_setopt($curlObj, CURLOPT_HEADER, 0);
curl_setopt($curlObj, CURLOPT_HTTPHEADER, array('Content-type:application/json'));
curl_setopt($curlObj, CURLOPT_POST, 1);
curl_setopt($curlObj, CURLOPT_POSTFIELDS, $jsonData);
$response = curl_exec($curlObj);
curl_close($curlObj);
$json = json_decode($response);
return $json-&gt;id;
}

//还原
function expandGoogleUrl($short_url){
$curlObj = curl_init();
curl_setopt($curlObj, CURLOPT_URL, 'https://www.googleapis.com/urlshortener/v1/url?shortUrl='.$short_url);
curl_setopt($curlObj, CURLOPT_HEADER, 0);
curl_setopt($curlObj, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($curlObj, CURLOPT_SSL_VERIFYPEER, 0);
$response = curl_exec($curlObj);
curl_close($curlObj);
$json = json_decode($response);
return $json-&gt;longUrl;
}
?&gt;

<strong> PHP SDK</strong>
<ul>
	<li>下载地址：http://code.google.com/p/libweibo/</li>
</ul>
（包含新版接口及OAuth2.0，由SAE提供维护，SAE平台已集成最新版PHPSDK）

&nbsp;
<ul>
	<li>Demo使用教程</li>
</ul>
<ol>
	<li>在open.weibo.com创建应用，得到AppKey，设置“授权设置”中的“应用回调页”地址为"http://host/callback.php"，其中host为网站域名。</li>
	<li>下载Demo,然后解压,修改config.php中的WB_AKEY为App Key，WB_SKEY为App Secret，WB_CALLBACK_URL为刚才填入的回调页地址。</li>
	<li>上传到PHP空间即可</li>
</ol>
