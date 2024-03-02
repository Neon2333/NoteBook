使用Ubuntu搭建http服务（https://www.jianshu.com/p/1540681e8cd3）

# http原理

## 1. 请求报文和响应报文格式

* 概念：http协议是客户端按照一定规则向服务器索要数据、发送数据，服务器按照一定规则回应数据。

  浏览器可以发http协议。其他工具也可以发http。

* 特点：

## 1）请求报文

![image-20240302210528151](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302210528151.png)
$$
请求报文
\begin {cases}
	请求行
		\begin{cases}
		请求方法(Method)\\
		请求路径（URL）\\
		所用协议（HTTP1.1)
		\end{cases}\\
		
	请求头(RequestHeader)\\
	空一行\\
	请求主体（可以没有）
\end {cases}
$$


| -请求方法 | -    |
| --------- | ---- |
| GET       |      |
| POST      |      |
| HEAD      |      |
| PUT       |      |
| DELETE    |      |
| TRACE     |      |
| OPTIONS   |      |

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
	响应头(ResponseHeader，key:value形式)
		\begin{cases}
			Content-Length（主体长度）\\
			Content-type
		\end{cases}\\
	空一行\\
	响应主体
\end{cases}
$$


## 1)POST实例

![image-20240302213604050](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302213604050.png)

![image-20240302214453742](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20240302214453742.png)