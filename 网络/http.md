使用Ubuntu搭建http服务（https://www.jianshu.com/p/1540681e8cd3）

# http原理

## 1. 请求报文和响应报文格式

* 概念：http协议是客户端按照一定规则向服务器索要数据、发送数据，服务器按照一定规则回应数据。

  浏览器可以发http协议。其他工具也可以发http。

* 特点：不保存状态（不对之前的请求和响应状态进行保存）。

## 1）请求报文

![image-20240302210528151](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302210528151.png)
$$
请求报文
\begin {cases}
	请求行
		\begin{cases}
		请求方法(Method)\\
		请求路径（URI）\\
		所用协议（HTTP1.1)
		\end{cases}\\
		
	请求头(RequestHeader，key:value格式)\\
	空一行\\
	请求主体（可以没有）
\end {cases}
$$

![image-20240302211951187](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302211951187.png)
$$
响应报文
\begin{cases}
	响应行
		\begin{cases}
			协议版本\\
            状态码(StatusCode)\\
            状态文字
		\end{cases}\\
	响应头(ResponseHeader，key:value格式)
		\begin{cases}
			Content-Length（主体长度）\\
			Content-type
		\end{cases}\\
	空一行\\
	响应主体
\end{cases}
$$


## 2）POST实例

![image-20240302213604050](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302213604050.png)

![image-20240302214453742](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302214453742.png)

## 3）请求方法Method

| -请求方法 | -                                                            |
| --------- | ------------------------------------------------------------ |
| 常用      |                                                              |
| GET       |                                                              |
| HEAD      | 和GET基本一致，只是不返回内容。比如想要确认一个内容是否存在，不需要返回内容本身，HEAD比GET更合适。 |
| POST      |                                                              |
| PUT       |                                                              |
| OPTIONS   | 返回服务器可用                                               |

## 4）状态码、状态文字

状态码：反映服务器响应情况。如：200 OK，404 NOT FOUND

状态文字：描述状态码，便于人理解

| -    | -                                |
| ---- | -------------------------------- |
| 2XX  | 成功                             |
| 3XX  | 重定向。需要进一步措施           |
| 4XX  | 客户端错误                       |
| 5XX  | 服务器错误。服务器无法完成请求。 |

最常用要记忆的：

* 200——成功返回

* **304——未修改Not Modified（服务器告知浏览器内容未修改，从浏览器缓存取内容就行）**

  服务器的响应头里有ETag和Last-Modified。

  客户端请求时会有If-None-Match和If-Modified-Since，发送给服务器让服务器判断请求的内容是否发生了修改。

  若修改了，服务器重新发。没修改，服务器响应304.

* 301/2——永久/临时重定向
* 404——内容不存在
* 503——服务器不可用
* 500——服务器内部错误

# C#实现简单http服务器

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading;
using System.Windows.Forms;

namespace WinHttpServer
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void btnStart_Click(object sender, EventArgs e)
        {
            HttpServer server = new HttpServer();
            server.Handler += server_Handler;
            server.Start(int.Parse(this.txtPort.Text));
        }

        private string server_Handler(string action, string args)
        {
            switch (action)
            {
                case "add":
                    return add(args);
                default:
                    return "ok";
            }
        }

        private string add(string args)
        {
            // a=1&b=2&c=3
            Regex regex = new Regex(@"a=(?<a>\d+)&b=(?<b>\d+)&c=(?<c>\d+)");
            int a = int.Parse(regex.Match(args).Groups["a"].Value);
            int b = int.Parse(regex.Match(args).Groups["b"].Value);
            int c = int.Parse(regex.Match(args).Groups["c"].Value);

            return (a + b + c).ToString();
        }
    }
}
/*
 POST /cloudquery.php HTTP/1.1
User-Agent: Post_Multipart
Host: 111.7.68.24
Accept: * / *
Pragma: no-cache
X-360-Cloud-Security-Desc: Scan Suspicious File
x-360-ver: 4
Content-Length: 666
Content-Type: multipart/form-data; boundary=----------------------------7166f2fc60a2

------------------------------7166f2fc60a2
Content-Disposition: form-data; name="m"


...Q............SO.R.Q
..#.wq.{D..Jy..VhQF]{...`H.t....h.....Kj;w.@..p/W.q...o...
..!c...2....VpR(vh..H...s1...G2a'xq..F.R....q-"2..._..@...P......_2..u....xT.%=nhv.ky.G.c..j..2iyRp.A.tl#..1............Os.....?....Ot...k.....Tu...1.Nt.......6.0..6..........
\...3......X.D.I.)i...j."..'..
..e.@...k..g.......>+.z........@.J,...*...`..8...`..f.....\..+.8..|2..@..`U.d.*,.Q.{h...PMT-lh..x}$&y.D....J..].!..XJ.`.....w.W..6jM.$.V.....4H@z.A.m.+..../.....o.d.....".#.@.]$..
./...Q.....T....e....I.s.,..) .f.uh.9...M..V0.....}....P.o..
------------------------------7166f2fc60a2--
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 01 Feb 2021 13:42:30 GMT
Content-Type: application/octet-stream
Transfer-Encoding: chunked
Connection: close
Cache-Control: no-cache
pragma: no-cache

a1

...Q.......P..E. B
...R..0...,..ve@.....7..d.;.>?1De/.w. .[..p.=[..m....'4L.4.,....y.
2.........<.R.......................................?.?.?.?.?.?.?.?.?.?.;.
0

 * 
GET /igd.xml HTTP/1.1
Cache-Control: no-cache
Connection: Close
Pragma: no-cache
Accept: text/xml, application/xml
Host: 192.168.0.1:1900
User-Agent: Microsoft-Windows/6.1 UPnP/1.0

HTTP/1.1 200 OK
Content-Type: text/xml
Connection: close
Content-Length: 2728
Server: Wireless N Router FW300R, UPnP/1.0

<?xml version="1.0"?>
<root xmlns="urn:schemas-upnp-org:device-1-0">
<specVersion>
<major>1</major>
<minor>0</minor>
</specVersion>
 */
```

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading;

namespace WinHttpServer
{
    public class HttpServer
    {
        public event Func<string, string, string> Handler = null;

        public void Start(int port)
        {
            Socket server = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
            server.Bind(new IPEndPoint(IPAddress.Any, 10000));
            server.Listen(1000);

            Thread t = new Thread(Execute);
            t.IsBackground = true;
            t.Start(server);
        }

        private void Execute(Object obj)
        {
            Socket server = obj as Socket;
            while (true)
            {
                Socket client = server.Accept();

                Thread t = new Thread(SingleExecute);
                t.IsBackground = true;
                t.Start(client);
            }
        }

        private void SingleExecute(object obj)
        {
            Socket client = obj as Socket;
            byte[] buffer = new byte[8192];
            client.ReceiveBufferSize = 8192;
            int count = client.Receive(buffer);
            if (count > 0)
            {
                string data = Encoding.UTF8.GetString(buffer, 0, count);
                if (data.StartsWith("GET "))
                {
                    Regex regex = new Regex(@"GET /(?<action>[^?]+)\?+(?<args>[^\s]{0,})");
                    string action = regex.Match(data).Groups["action"].Value;
                    string args = regex.Match(data).Groups["args"].Value;

                    string result = HandleRequest(action, args);
                    string body = create_body(result);

                    client.Send(Encoding.UTF8.GetBytes(body));
                }
                else if (data.StartsWith("POST "))
                {
                    Regex regex = new Regex(@"POST /(?<action>[^?]+)[\s\S]+\r\n\r\n(?<args>[^\s]{0,})");
                    string action = regex.Match(data).Groups["action"].Value;
                    string args = regex.Match(data).Groups["args"].Value;

                    string result = HandleRequest(action, args);
                    string body = create_body(result);

                    client.Send(Encoding.UTF8.GetBytes(body));
                }
            }

            client.Shutdown(SocketShutdown.Both);
        }

        private string HandleRequest(string action, string args)
        {
            if (Handler != null)
            {
                return Handler(action, args);
            }

            return string.Empty;
        }

        private string create_body(string data)
        {
            return string.Format(@"HTTP/1.1 200 OK
            Content-Type: text/html
            Connection: close
            Content-Length: {0}
            
            {1}", Encoding.UTF8.GetBytes(data).Length, data);
        }
    }
}

```





