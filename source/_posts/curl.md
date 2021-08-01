---
title: curl 调用 openstack api
date: 2021-07-30 03:32:36
author: dFaruiy
tags: 
 - linux
 - curl

top: true
toc: true
categories: curl
keywords: curl
---

# *CURL*

## 1.简介
`curl`是常用的命令行工具，用来请求web服务器，功能非常强大，命令行参数多大10几种。
> 本文介绍他的主要参数命令；作为日常参考，方便查阅; [curl cookbook](https://catonmat.net/cookbooks/curl); 初学者可以查看[curl 初学者教程](https://www.ruanyifeng.com/blog/2011/09/curl.html)

## 2.使用

- 不带任何参数是，curl就是发出GET请求

    ```shell
    $ curl https://www.example.com
    # 上面的命令向www.example.com 发出GET请求，服务器返回的内容会在命令行输出
    ```

- `-A`
    
    `-A`参数指定客户端的用户端的用户代理标头，即User-Agent，curl的默认用户代理字符串是`curl/[version]`
    
    ```shell
    $ curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://google.com
    # 上面命令将 User-Agent 改成 Chrome 浏览器。
    $ curl -A '' https://www.google.com
    # 上面命令会移除User-Agent标头
    # 也可以通过 -H 参数直接指定标头，更改User-Agent
    $ curl -H 'User-Agent: php/1.0' https://google.com
    ```

- `-b`

    `-b`参数用来向服务器发送`Cookie`

    ```shell
    $ curl -b 'foo-bar' https://google.com
    # 上面命令会生成一个标头Cookie: foo=bar，向服务器发送一个名为foo、值为bar的 Cookie。
    ```

- `-c`
    
    `-c` 参数将服务器设置的 `Cookie` 写入一个文件。
    ```shell
    $ curl -c cookies.txt https://www.google.com
    # 上面命令将服务器的 HTTP 回应所设置 Cookie 写入文本文件cookies.txt
    ```

- `-d`

    `-d`参数用于发送POST请求的数据体
    ```shell
    $ curl -d'login=emma＆password=123'-X POST https://google.com/login
    # 或者
    $ curl -d 'login=emma' -d 'password=123' -X POST  https://google.com/login
    $ curl -d '@data.txt' https://google.com/login
    # 上面命令读取data.txt文件的内容，作为数据体向服务器发送。
    ```
    >使用`-d`参数以后，`HTTP` 请求会自动加上标头`Content-Type : application/x-www-form-urlencoded`。并且会自动将请求转为 `POST` 方法，因此可以省略`-X POST`。

- `--data-urlencode`

    `--data-urlencode`参数等同于`-d`，发送 `POST` 请求的数据体，区别在于会自动将发送的数据进行 `URL` 编码。
    ```shell
    $ curl --data-urlencode 'comment=hello world' https://google.com/login
    # 上面代码中，发送的数据hello world之间有一个空格，需要进行 URL 编码。
    ```

- `-e`

    -e参数用来设置 HTTP 的标头Referer，表示请求的来源。
    ```shell
    $ curl -e 'https://google.com?q=example' https://www.example.com
    # 上面命令将Referer标头设为https://google.com?q=example。

    # -H参数可以通过直接添加标头Referer，达到同样效果。
    $ curl -H 'Referer: https://google.com?q=example' https://www.example.com
    ```

- `-F`

    -F参数用来向服务器上传二进制文件。

    ```shell
    $ curl -F 'file=@photo.png' https://google.com/profile
    # 上面命令会给 HTTP 请求加上标头Content-Type: multipart/form-data，然后将文件photo.png作为file字段上传。

    -F参数可以指定 MIME 类型。


    $ curl -F 'file=@photo.png;type=image/png' https://google.com/profile
    # 上面命令指定 MIME 类型为image/png，否则 curl 会把 MIME 类型设为application/octet-stream。

    -F参数也可以指定文件名。


    $ curl -F 'file=@photo.png;filename=me.png' https://google.com/profile
    # 上面命令中，原始文件名为photo.png，但是服务器接收到的文件名为me.png。
    ```shell
- `-G`
    -G参数用来构造 URL 的查询字符串。

    ```shell
    $ curl -G -d 'q=kitties' -d 'count=20' https://google.com/search
    # 上面命令会发出一个 GET 请求，实际请求的 URL 为https://google.com/search?q=kitties&count=20。如果省略--G，会发出一个 POST 请求。

    如果数据需要 URL 编码，可以结合--data--urlencode参数。


    $ curl -G --data-urlencode 'comment=hello world' https://www.example.com
    ```
- `-H`

    -H参数添加 HTTP 请求的标头。

    ```shell
    $ curl -H 'Accept-Language: en-US' https://google.com
    # 上面命令添加 HTTP 标头Accept-Language: en-US。


    $ curl -H 'Accept-Language: en-US' -H 'Secret-Message: xyzzy' https://google.com
    # 上面命令添加两个 HTTP 标头。


    $ curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login
    # 上面命令添加 HTTP 请求的标头是Content-Type: application/json，然后用-d参数发送 JSON 数据。
    ```
- `-i`

    -i参数打印出服务器回应的 HTTP 标头。

    ```shell
    $ curl -i https://www.example.com
    # 上面命令收到服务器回应后，先输出服务器回应的标头，然后空一行，再输出网页的源码。
    ```
- `-I`

    -I参数向服务器发出 HEAD 请求，然会将服务器返回的 HTTP 标头打印出来。

    ```shell
    $ curl -I https://www.example.com
    # 上面命令输出服务器对 HEAD 请求的回应。

    --head参数等同于-I。

    $ curl --head https://www.example.com

    ```
- `-k`

    -k参数指定跳过 SSL 检测。

    ```shell
    $ curl -k https://www.example.com
    上面命令不会检查服务器的 SSL 证书是否正确。
    ```

-  `-L`
    -L参数会让 HTTP 请求跟随服务器的重定向。curl 默认不跟随重定向。

    ```shell
    $ curl -L -d 'tweet=hi' https://api.twitter.com/tweet
    ```

- `--limit-rate`

    --limit-rate用来限制 HTTP 请求和回应的带宽，模拟慢网速的环境。

    ```shell
    $ curl --limit-rate 200k https://google.com
    # 上面命令将带宽限制在每秒 200K 字节。
    ```

- `-o`

    -o参数将服务器的回应保存成文件，等同于wget命令。

    ```shell
    $ curl -o example.html https://www.example.com
    # 上面命令将www.example.com保存成example.html。
    ```

- `-O`
    -O参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。

    ```shell
    $ curl -O https://www.example.com/foo/bar.html
    # 上面命令将服务器回应保存成文件，文件名为bar.html。
    ```

- `-s`

    -s参数将不输出错误和进度信息。

    ```shell
    $ curl -s https://www.example.com
    # 上面命令一旦发生错误，不会显示错误信息。不发生错误的话，会正常显示运行结果。

    如果想让 curl 不产生任何输出，可以使用下面的命令。


    $ curl -s -o /dev/null https://google.com
    ```

- `-S`

    -S参数指定只输出错误信息，通常与-s一起使用。

    ```shell
    $ curl -s -o /dev/null https://google.com
    # 上面命令没有任何输出，除非发生错误。
    ```

- `-u`

    -u参数用来设置服务器认证的用户名和密码。

    ```shell
    $ curl -u 'bob:12345' https://google.com/login
    # 上面命令设置用户名为bob，密码为12345，然后将其转为 HTTP 标头Authorization: Basic Ym9iOjEyMzQ1。

    curl 能够识别 URL 里面的用户名和密码。


    $ curl https://bob:12345@google.com/login
    # 上面命令能够识别 URL 里面的用户名和密码，将其转为上个例子里面的 HTTP 标头。


    $ curl -u 'bob' https://google.com/login
    # 上面命令只设置了用户名，执行后，curl 会提示用户输入密码。
    ```

- `-v`

    -v参数输出通信的整个过程，用于调试。

    ```shell
    $ curl -v https://www.example.com

    --trace参数也可以用于调试，还会输出原始的二进制数据。


    $ curl --trace - https://www.example.com
    ```
- `-x`

    -x参数指定 HTTP 请求的代理。

    ```shell
    $ curl -x socks5://james:cats@myproxy.com:8080 https://www.example.com
    # 上面命令指定 HTTP 请求通过myproxy.com:8080的 socks5 代理发出。

    如果没有指定代理协议，默认为 HTTP。


    $ curl -x james:cats@myproxy.com:8080 https://www.example.com
    # 上面命令中，请求的代理使用 HTTP 协议。
    ```

- `-X`

    -X参数指定 HTTP 请求的方法。

    ```shell
    $ curl -X POST https://www.example.com
    # 上面命令对https://www.example.com发出 POST 请求。
    ```
---
[参考](https://www.ruanyifeng.com/blog/2019/09/curl-reference.html)

# *OPENSTACK API*

## 1.API接口

[**OpenSatckAPI**](https://docs.openstack.org/api-quick-start/#)

## 2.获取token

- `openstack cli`

    ```shell
    $ source openrc
    $ openstack token issue
    ```

- `curl`

    ```shell
    [stack@openstack ~]$ curl -i -X POST http://10.196.33.10/identity/v3/auth/tokens -H "Content-Type: application/json" -d '{"auth": {"identity": {"methods": ["password"],"password": {"user": {"name": "admin", "domain": {"name": "default"},"password": "openstack"}}}}}'
    HTTP/1.1 201 CREATED
    Date: Sun, 01 Aug 2021 03:24:53 GMT
    Server: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips mod_wsgi/3.4 Python/2.7.5
    Content-Type: application/json
    Content-Length: 312
    X-Subject-Token:    gAAAAABhBhQFZ8P6dDC2j1oTN0I0D9qjM57MM43zgLS7g8_rzqgqaQXSrgtmFiRSjsegI_qBjFmoS3UKayhTs3gWUr-nl1DU6PyilCp    NHqXWPwU12dgtKLhWWzGLThssnMaCjcglzG2T4olLg1WNVYztjYfQM_HErg
    Vary: X-Auth-Token
    x-openstack-request-id: req-acc25d01-a2a5-44fd-9ea8-4ac8bf82c51f
    Connection: close

    {"token": {"issued_at": "2021-08-01T03:24:53.000000Z", "audit_ids": ["6yiziGokQJijxOfR7SfJ0A"],     "methods": ["password"], "expires_at": "2021-08-01T04:24:53.000000Z", "user": {"password_expires_at":   null, "domain": {"id": "default", "name": "Default"}, "id": "d3336c1c8b434b5285ae7dd2afc8c9ac",   "name": "admin"}}}[stack@openstack ~]$
    ```

    ```json
    # Request
    {
        "auth": {
            "identity": {
                "methods": [
                    "password"
                ],
                "password": {
                    "user": {
                        "name": "admin",
                        "domain": {
                            "name": "Default"
                        },
                        "password": "openstack"
                    }
                }
            }
        }
    }
    ```

## 3.获取nova列表

```json
[stack@openstack ~]$ curl  -X GET http://10.196.33.10/compute/v2.1/servers -H "Content-Type: application/json" -H 'X-Auth-Token: gAAAAABhBhUbqBPYJY8OObNKQLP93g7U5WZDFzCBEtrGB32b521KsYnZSre-rUVg645bWW6GNLd-G-05v7e5e4xSHMoX_Kf5sUlu5DbSSLZlWAlNGMsT01C3r0Lawbk21sN8CyyDti32GQ_pGHZb7lUGhBgHS0-EVw'|python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    15  100    15    0     0    647      0 --:--:-- --:--:-- --:--:--   681
{
    "servers": []
}

# 由于没有创建vm，所以返回为空，

# 使用flavor举例，获取flavor列表
[stack@openstack ~]$ curl  -X GET http://10.196.33.10/compute/v2.1/flavors -H "Content-Type: application/json" -H 'X-Auth-Token: gAAAAABhBhUbqBPYJY8OObNKQLP93g7U5WZDFzCBEtrGB32b521KsYnZSre-rUVg645bWW6GNLd-G-05v7e5e4xSHMoX_Kf5sUlu5DbSSLZlWAlNGMsT01C3r0Lawbk21sN8CyyDti32GQ_pGHZb7lUGhBgHS0-EVw'|python -m json.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2241  100  2241    0     0  69713      0 --:--:-- --:--:-- --:--:-- 72290
{
    "flavors": [
        {
            "id": "1",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/1",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/1",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.tiny"
        },
        {
            "id": "2",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/2",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/2",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.small"
        },
        {
            "id": "3",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/3",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/3",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.medium"
        },
        {
            "id": "4",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/4",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/4",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.large"
        },
        {
            "id": "42",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/42",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/42",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.nano"
        },
        {
            "id": "5",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/5",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/5",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.xlarge"
        },
        {
            "id": "84",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/84",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/84",
                    "rel": "bookmark"
                }
            ],
            "name": "m1.micro"
        },
        {
            "id": "c1",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/c1",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/c1",
                    "rel": "bookmark"
                }
            ],
            "name": "cirros256"
        },
        {
            "id": "d1",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/d1",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/d1",
                    "rel": "bookmark"
                }
            ],
            "name": "ds512M"
        },
        {
            "id": "d2",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/d2",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/d2",
                    "rel": "bookmark"
                }
            ],
            "name": "ds1G"
        },
        {
            "id": "d3",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/d3",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/d3",
                    "rel": "bookmark"
                }
            ],
            "name": "ds2G"
        },
        {
            "id": "d4",
            "links": [
                {
                    "href": "http://10.196.33.10/compute/v2.1/flavors/d4",
                    "rel": "self"
                },
                {
                    "href": "http://10.196.33.10/compute/flavors/d4",
                    "rel": "bookmark"
                }
            ],
            "name": "ds4G"
        }
    ]
}
```
>如上述两个例子，结合openstack api 实际使用了curl命令，增加对curl命令的了解
