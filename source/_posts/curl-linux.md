---
title: Linux下curl的用法及shell处理json数据介绍
date: 2017-03-13 14:03:01
tags: linux
categories: linux
---
请求的url为什么要进行编码
------------------------

url(Uniform Resource Locator)统一资源定位符，即我们通常说的网址，完整的URL由这几个部分构成：

<center> scheme://host:port/path?query#fragment </center>

其中，

scheme:通信协议，常用的http,ftp,maito,https等；

host:主机，服务器(计算机)域名系统 (DNS) 主机名或 IP 地址；

port:端口号，整数，可选，省略时使用方案的默认端口，如http的默认端口为80,https的默认端口是443；

path:路径，由零或多个"/"符号隔开的字符串，一般用来表示主机上的一个目录或文件地址；

query:查询，可选，用于给动态网页（如使用CGI、ISAPI、PHP/JSP/ASP/ASP.NET等技术制作的网页）传递参数，可有多个参数，用"&"符号隔开，每个参数的名和值用"="符号隔开；

fragment:信息片断，字符串，用于指定网络资源中的片断。例如一个网页中有多个名词解释，可使用fragment直接定位到某一名词解释。(也称为锚点.)这个标志点不是统一资源标志符的一部分，而是让用户浏览器在获得了文件后来导航用的，因此它实际上不被送到服务器。

网络标准[RFC 1738](http://www.ietf.org/rfc/rfc1738.txt)对url的编码进行的规定
>只有字母和数字[0-9a-zA-Z]、一些特殊符号"$-_.+!*'(),"[不包括双引号]、以及某些保留字，才可以不经过编码直接用于URL

除了上边的这些字符，在url中出现时，都要经过编码,RFC 1738没有规定具体的编码方法，而是交给应用程序（浏览器）自己决定，具体的编码方式可以查看这篇[文章](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)和[这篇](https://www.zhihu.com/question/19673368)，例如双引号可以用\来转义，汉字可以用其他编码方式来表示

shell下有个挺好用的工具curl，可以进行服务器之间的通信，但是如果请求的参数中有{}或者[]，例如请求参数是json格式，如http://abc.com/query?a=b&c=d&\{\"name\":\"1\",\"age\":3\}，否则curl会进行多次的请求，因为在curl中，可以用{}来指定多个url，如http://site.{one,two,three}.com，这将请求三次，分别为one,two,three,用[]可以用来指定一个范围，如ftp://ftp.numericals.com/file[1-100].txt

curl详细介绍
-----------

curl是一个向服务器或者从服务器下载数据的传输工具，它支持HTTP, HTTPS, FTP, FTPS, SCP, SFTP, TFTP, DICT, TELNET, LDAP or FILE等协议，这个命令设计为不需要用户的干预来执行。curl提供了非常多有用的功能，比如代理支持，用户认证，HTTP POST ,FTP Upload,断点续传等等,curl所有传输相关功能都基于libcurl。

详细用法

> * **直接请求，返回源代码**    
    curl http://wwww.baidu.com
> * **保存网页内容**，-o：将文件保存为命令行中指定的文件名的文件中,-O：使用URL中默认的文件名保存文件到本地    
    curl -o [文件名] www.sina.com
> * **自动跳转,有的网址是自动跳转的**。使用`-L`参数，curl就会跳转到新的网址。    
    curl -L www.sina.com
> * **显示头信息**    `-i`参数可以显示http response的头信息，连同网页代码一起。`-I`参数则是只显示http response的头信息。
    curl -i www.sina.com
> * **显示通信过程**, `-v`参数可以显示一次http通信的整个过程，包括端口连接和http request头信息。--trace可以显示更详细内容    
    curl -v www.sina.com    
    curl --trace output.txt www.sina.com    
    curl --trace-ascii output.txt www.sina.com
> * **发送表单信息**,发送表单信息有GET和POST两种方法。GET方法相对简单，只要把数据附在网址后面就行。    
    curl example.com/form.cgi?data=xxx    
    POST方法必须把数据和网址分开，curl就要用到--data参数.    
    curl -X POST --data "data=xxx" example.com/form.cgi    
    如果你的数据没有经过表单编码，还可以让curl为你编码，参数是`--data-urlencode`。    
    curl -X POST--data-urlencode "date=April 1" example.com/form.cgi  
> * **HTTP动词**    curl默认的HTTP动词是GET，使用`-X`参数可以支持其他动词。
   curl -X POST www.example.com
   curl -X DELETE www.example.com
> * **文件上传**  假定文件上传的表单是下面这样：
   
``` html
   		<form method="POST" enctype='multipart/form-data' action="upload.cgi">
　　　　	<input type=file name=upload>
　　　　	<input type=submit name=press value="OK">
　　    </form>
```
> * 则上传命令是curl --form upload=@localfilename --form press=OK [URL]        
> * **cookie**  使用`--cookie`参数，可以让curl发送cookie。    
    curl --cookie "name=xxx" www.example.com    
> * **增加头信息** 有时需要在http request之中，自行增加一个头信息。`--header`参数就可以起到这个作用    
    curl --header "Content-Type:application/json" http://example.com    
> * **HTTP认证** 有些网域需要HTTP认证，这时curl需要用到`--user`参数。    
    curl --user name:password example.com
> * **断点续传** 通过使用-C选项可对大文件使用断点续传功能    
    \#当文件在下载完成之前结束该进程    
    $ curl -O http://www.gnu.org/software/gettext/manual/gettext.html    
    ##############             20.1%    
    # 通过添加-C选项继续对该文件进行下载，已经下载过的文件不会被重新下载    
    curl -C - -O http://www.gnu.org/software/gettext/manual/gettext.html    
    ###############            21.1%    
> *  **网络限速** ,通过--limit-rate选项对CURL的最大网络使用进行限制		
    curl --limit-rate 1000B -O http://www.gnu.org/software/gettext/manual/gettext.html


shell处理json
-------------
通常大部分接口都是json格式的，对于大部分程序语言，都有库或者函数来实现json的序列化和反序列化，shell中也有命令可以处理json数据，如[jq](https://stedolan.github.io/jq/tutorial/)命令，需要手动安装  
> * 下载[源码](https://github.com/stedolan/jq/releases/download/jq-1.5/jq-1.5.tar.gz)，执行./configure && make && sudo make install  

测试
> * 获取数据    
    curl 'https://api.github.com/repos/stedolan/jq/commits?per_page=5' > json.data    
	结果如下:

``` json
[
  {
    "sha": "d25341478381063d1c76e81b3a52e0592a7c997f",
    "commit": {
      "author": {
        "name": "Stephen Dolan",
        "email": "mu@netsoc.tcd.ie",
        "date": "2013-06-22T16:30:59Z"
      },
      "committer": {
        "name": "Stephen Dolan",
        "email": "mu@netsoc.tcd.ie",
        "date": "2013-06-22T16:30:59Z"
      },
      "message": "Merge pull request #162 from stedolan/utf8-fixes\n\nUtf8 fixes. Closes #161",
      "tree": {
        "sha": "6ab697a8dfb5a96e124666bf6d6213822599fb40",
        "url": "https://api.github.com/repos/stedolan/jq/git/trees/6ab697a8dfb5a96e124666bf6d6213822599fb40"
      },
      "url": "https://api.github.com/repos/stedolan/jq/git/commits/d25341478381063d1c76e81b3a52e0592a7c997f",
      "comment_count": 0
    },
    "url": "https://api.github.com/repos/stedolan/jq/commits/d25341478381063d1c76e81b3a52e0592a7c997f",
    "html_url": "https://github.com/stedolan/jq/commit/d25341478381063d1c76e81b3a52e0592a7c997f",
    "comments_url": "https://api.github.com/repos/stedolan/jq/commits/d25341478381063d1c76e81b3a52e0592a7c997f/comments",
    "author": {
      "login": "stedolan",
	...
```

> * 高亮显示 用jq "." json.data
> * 获取数据某一项  jq ".[n]" json.data ,如 jq ".[1]" json.data
``` json
	{
  "sha": "125071cf005e687d4beba9d5822b1c6a72d7d14c",
  "commit": {
    "author": {
      "name": "Nicolas Williams",
      "email": "nico@cryptonector.com",
      "date": "2017-02-04T06:11:10Z"
    },
    "committer": {
      "name": "Nicolas Williams",
      "email": "nico@cryptonector.com",
      "date": "2017-02-04T06:11:10Z"
    },
    "message": "Fix handling of unsupported math functions",
    "tree": {
      "sha": "98bbc0beba737efc16c7d345b3c06c88302f5912",
      "url": "https://api.github.com/repos/stedolan/jq/git/trees/98bbc0beba737efc16c7d345b3c06c88302f5912"
    },
    "url": "https://api.github.com/repos/stedolan/jq/git/commits/125071cf005e687d4beba9d5822b1c6a72d7d14c",
    "comment_count": 0
  },
  "url": "https://api.github.com/repos/stedolan/jq/commits/125071cf005e687d4beba9d5822b1c6a72d7d14c",
  "html_url": "https://github.com/stedolan/jq/commit/125071cf005e687d4beba9d5822b1c6a72d7d14c",
  "comments_url": "https://api.github.com/repos/stedolan/jq/commits/125071cf005e687d4beba9d5822b1c6a72d7d14c/comments",
  "author": {
    "login": "nicowilliams",
    "id": 604851,
    "avatar_url": "https://avatars.githubusercontent.com/u/604851?v=3",
    "gravatar_id": "",
    "url": "https://api.github.com/users/nicowilliams",
    "html_url": "https://github.com/nicowilliams",
    "followers_url": "https://api.github.com/users/nicowilliams/followers",
    "following_url": "https://api.github.com/users/nicowilliams/following{/other_user}",
    "gists_url": "https://api.github.com/users/nicowilliams/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/nicowilliams/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/nicowilliams/subscriptions",
    "organizations_url": "https://api.github.com/users/nicowilliams/orgs",
    "repos_url": "https://api.github.com/users/nicowilliams/repos",
    "events_url": "https://api.github.com/users/nicowilliams/events{/privacy}",
    "received_events_url": "https://api.github.com/users/nicowilliams/received_events",
    "type": "User",
    "site_admin": false
  },
  "committer": {
    "login": "nicowilliams",
    "id": 604851,
    "avatar_url": "https://avatars.githubusercontent.com/u/604851?v=3",
    "gravatar_id": "",
    "url": "https://api.github.com/users/nicowilliams",
    "html_url": "https://github.com/nicowilliams",
    "followers_url": "https://api.github.com/users/nicowilliams/followers",
    "following_url": "https://api.github.com/users/nicowilliams/following{/other_user}",
    "gists_url": "https://api.github.com/users/nicowilliams/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/nicowilliams/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/nicowilliams/subscriptions",
    "organizations_url": "https://api.github.com/users/nicowilliams/orgs",
    "repos_url": "https://api.github.com/users/nicowilliams/repos",
    "events_url": "https://api.github.com/users/nicowilliams/events{/privacy}",
    "received_events_url": "https://api.github.com/users/nicowilliams/received_events",
    "type": "User",
    "site_admin": false
  },
  "parents": [
    {
      "sha": "2fb099e4cfe5a9fedd55a1ace44ae2c5ee02cb12",
      "url": "https://api.github.com/repos/stedolan/jq/commits/2fb099e4cfe5a9fedd55a1ace44ae2c5ee02cb12",
      "html_url": "https://github.com/stedolan/jq/commit/2fb099e4cfe5a9fedd55a1ace44ae2c5ee02cb12"
    }
  ]
}
```

> * 自定义格式输出某一项
    jq '.[0] | {message: .commit.message, name: .commit.committer.name}'    
``` json
	{
  		"message": "Add more missing math functions",
  		"name": "Nicolas Williams"
	}
```
这里 | 后面的内容是以前面的内容为输入的，.commit 中的 . 就是指 .[0] 中的内容。

> * 自定义格式输出多项    
	jq '.[] | {message: .commit.message, name: .commit.committer.name}'
	也就是如果要指定去数组中的某一项，需要指定一个值，否则就用一个[]标示取所有的，结果是:   


``` json
	{
	  "name": "Stephen Dolan",
	  "message": "Merge pull request #162 from stedolan/utf8-fixes\n\nUtf8 fixes. Closes #161"
	}
	{
	  "name": "Stephen Dolan",
	  "message": "Reject all overlong UTF8 sequences."
	}
	{
	  "name": "Stephen Dolan",
	  "message": "Fix various UTF8 parsing bugs.\n\nIn particular, parse bad UTF8 by replacing the broken bits with U+FFFD\nand resychronise correctly after broken sequences."
	}
	{
	  "name": "Stephen Dolan",
	  "message": "Fix example in manual for `floor`. See #155."
	}
	{
	  "name": "Nicolas Williams",
	  "message": "Document floor"
	}
```

可以看到上边输出不是一个标准的json，修改如下

> * 以数组形式自定义输出多项    
    jq '[.[] | {message: .commit.message, name: .commit.committer.name}]'

```json
	[
	  {
	    "name": "Stephen Dolan",
	    "message": "Merge pull request #162 from stedolan/utf8-fixes\n\nUtf8 fixes. Closes #161"
	  },
	  {
	    "name": "Stephen Dolan",
	    "message": "Reject all overlong UTF8 sequences."
	  },
	  {
	    "name": "Stephen Dolan",
	    "message": "Fix various UTF8 parsing bugs.\n\nIn particular, parse bad UTF8 by replacing the broken bits with U+FFFD\nand resychronise correctly after broken sequences."
	  },
	  {
	    "name": "Stephen Dolan",
	    "message": "Fix example in manual for `floor`. See #155."
	  },
	  {
	    "name": "Nicolas Williams",
	    "message": "Document floor"
	  }
	]
```

> * 获取其他内容    
    jq '[.[] | {message: .commit.message, name: .commit.committer.name, parents: [.parents[].html_url]}]'

```json
	[
  {
    "parents": [
      "https://github.com/stedolan/jq/commit/54b9c9bdb225af5d886466d72f47eafc51acb4f7",
      "https://github.com/stedolan/jq/commit/8b1b503609c161fea4b003a7179b3fbb2dd4345a"
    ],
    "name": "Stephen Dolan",
    "message": "Merge pull request #162 from stedolan/utf8-fixes\n\nUtf8 fixes. Closes #161"
  },
  {
    "parents": [
      "https://github.com/stedolan/jq/commit/ff48bd6ec538b01d1057be8e93b94eef6914e9ef"
    ],
    "name": "Stephen Dolan",
    "message": "Reject all overlong UTF8 sequences."
  },
  {
    "parents": [
      "https://github.com/stedolan/jq/commit/54b9c9bdb225af5d886466d72f47eafc51acb4f7"
    ],
    "name": "Stephen Dolan",
    "message": "Fix various UTF8 parsing bugs.\n\nIn particular, parse bad UTF8 by replacing the broken bits with U+FFFD\nand resychronise correctly after broken sequences."
  },
  {
    "parents": [
      "https://github.com/stedolan/jq/commit/3dcdc582ea993afea3f5503a78a77675967ecdfa"
    ],
    "name": "Stephen Dolan",
    "message": "Fix example in manual for `floor`. See #155."
  },
  {
    "parents": [
      "https://github.com/stedolan/jq/commit/7c4171d414f647ab08bcd20c76a4d8ed68d9c602"
    ],
    "name": "Nicolas Williams",
    "message": "Document floor"
  }
]
```
这里用 .parents[].html_url 获取当前项的 parents 节点中的所有项的 html_url 属性的内容，然后两边加个中括号组装成数组输出。