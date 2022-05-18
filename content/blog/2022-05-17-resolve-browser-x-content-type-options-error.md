---
categories:
- 立占 | Site
- 石马 | Code
date: "2022-05-17T23:40:10+08:00"
lastmod: "2022-05-18T11:10:10+08:00"
tags:
- Hugo
- Gitlab
- HTTP
- Vercel
featured_image: /images/http-error.png
title: 解决浏览器X-Content-Type-Options报错
url: /blog/2022/05/resolve-browser-x-content-type-options-error.html
description: 今天无意用Edge的开发人员调试工具，就出现了错误：Response should include 'x-content-type-options' header.
toc: true
---
![HTTP-ERROR RESPONSE](/images/http-error.png)

今天无意用Edge的开发人员调试工具，就出现了如上图的<mark>错误</mark>：`Response should include 'x-content-type-options' header.`

<!--more-->

### **错误提示：**

+---------------------------+------------------------------------------------------------------+
| 请求                        | FURTHER READING                                                  |
+===========================+==================================================================+
| https://zhu8.net/project/ | Learn why this is important and how to fix it on [webhint.io][1] |
+---------------------------+------------------------------------------------------------------+

研究了一下，发现是HTTP响应头的问题，需要从我的托管网站Vercel着手。
> The ***\*X-Content-Type-Options\**** response ***\*HTTP\**** header is a marker used by the server to indicate that the <abbr title="Multipurpose Internet Mail Extensions">***\*MIME\****</abbr> types advertised in the ***\*Content-Type\**** headers should be followed and not be changed. The header allows you to avoid ***\*MIME type\**** sniffing by saying that the MIME types are deliberately configured.
>
> <cite>— via ***\*MDN\**** [^2]</cite>

### **解决方法：**

在你的Github或者Gitlab的库首页，增加文件：`vercel.json`[^4]

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers" : [
        {
          "key" : "X-Content-Type-Options",
          "value" : "nosniff"
        },
        {
          "key" : "X-Frame-Options",
          "value" : "DENY"
        },
        {
          "key" : "X-XSS-Protection",
          "value" : "1; mode=block"
        },
        {
          "key" : "Referrer-Policy",
          "value" : "strict-origin"
        },
        {
          "key" : "Permissions-Policy",
          "value" : "geolocation=(self), microphone=()"
        }
      ]
    }
  ]
}
```

然后，继续按照我[上一篇文字][6]的方法，去更新和发布就好啦！

### **最终成果：**

最后，得到如下的响应头，可以看到，已经有了`X-Content-Type-Options: nosniff`：

{{< highlight http >}}
access-control-allow-origin: *
age: 8412
cache-control: public, max-age=0, must-revalidate
content-disposition: inline; filename="index.html"
content-encoding: br
content-type: text/html; charset=utf-8
date: Wed, 18 May 2022 03:11:07 GMT
etag: W/"149726f18dff76e3a59bc59ce92b94217c5cc93ea57e6269faf4a0ede9709c75"
permissions-policy: geolocation=(self), microphone=()
referrer-policy: strict-origin
server: Vercel
strict-transport-security: max-age=63072000
x-content-type-options: nosniff
x-frame-options: DENY
x-vercel-cache: HIT
x-vercel-id: hnd1:hnd1::857cn-1652843467303-5523938ae2c8
x-xss-protection: 1; mode=block
{{< /highlight >}}

[1]: https://webhint.io/docs/user-guide/hints/hint-x-content-type-options/	"Use `X-Content-Type-Options` header | webhint documentation"
[^2]: [X-Content-Type-Options - HTTP | MDN][3] - Mozilla Developer Network

[3]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options	"MDN Web Docs"

[^4]: [Setting up Security Headers with Vercel][5] - Dave Blakey

[5]: https://blakey.co/blog/setting-up-security-headers-with-vercel	"Setting up Security Headers with Vercel | Dave Blakey"
[6]: https://zhu8.net/blog/2022/04/make-a-whole-cloud-site-with-hugo.html	"Hugo搭建完全云托管个人网站"