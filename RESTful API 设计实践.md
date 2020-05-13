# RESTful API 设计实践

工作以来写了很多HTTP接口，阅读了一些RESTful API相关文章，在此做一个总结，指导后续工作。

## 一、URL设计

### 1.1 见名知意

| **Resource** | **POST** | **GET** | **PUT** | **DELETE** |
| :------------ | :-------- | :------- | :------- | :---------- |
| /customers | Create a new customer | Retrieve all customers | Bulk update of customers | Remove all customers |
| /customers/1 | Error | Retrieve the details for customer 1 | Update the details of customer 1 if it exists | Remove customer 1 |
| /customers/1/orders | Create a new order for customer 1 | Retrieve all orders for customer 1  | Bulk update of orders for customer 1 | Remove all orders for customer 1 |

当整个应用坚持这种 API 设计风格时，将使接口变的简单、可读。
HTTP 常见方法，对应 CRUD 操作。

- GET：读取（Read）
- POST：新建（Create）
- PUT：更新（Update）
- PATCH：更新（Update），通常是部分更新
- DELETE：删除（Delete）

每个 HTTP 方法对应不同的动作，给 URL 施加不同的动作，改变其状态。 
方法 `GET`, `PUT`, `DELETE` 应该设计成**幂等**的。

URL 命名应全部采取小写。

URL 应该是一个名词"**复数**"，不能表示成名词复数时应尽可能少的使用**动词**，例如：`
GET /add?operand1=99&operand2=1` 表示2个数相加，`add` 为动词。

URL 复杂度应尽量避免超过 `collection/item/collection` 此种形式，例如：
`/customers/1/orders/99/products` 不推荐；
`/customers/1/products?orders=99` 推荐。


### 1.2 API 根入口

API 根入口应尽可能保持简单，例如：

```
api.example.com/*
example.com/api/*
```

### 1.3 URL 版本信息

API 应保持向后兼容，应该在引入新版本 API 的同时确保旧版本 API 仍然可用。所以应该为其提供版本支持。

目前比较常见的两种版本号形式：

在 URL 中嵌入版本编号 `api.example.com/v1/*`
这种做法是版本号直观、易于调试；另一种做法是，将版本号放在 HTTP Header 头中：
通过媒体类型来指定版本信息 `Accept: application/vnd.example.com.v1+json`。



## 二、状态码
### 2.1 状态码必须精确
客户端的每一次请求，服务器都必须给出回应。回应包括 HTTP 状态码和数据两部分。

HTTP 状态码就是一个三位数，分成五个类别。

- `1xx`：相关信息
- `2xx`：操作成功
- `3xx`：重定向
- `4xx`：客户端错误
- `5xx`：服务器错误

<!--不需要1xx状态码，下面介绍其他四类状态码的精确含义。--> 



### 2.2 2xx 状态码

`200`状态码表示操作成功，但是不同的方法可以返回更精确的状态码。

- GET: 200 OK
- POST: 201 Created
- PUT: 200 OK
- PATCH: 200 OK
- DELETE: 204 No Content


HTTP 响应实体部分直接就是数据，**不做多余的包装**。

`POST`返回`201`状态码，表示生成了新的资源，创建成功后可以返回生成资源的数据，也可以返回一个响应实体为空的 HTTP Response。

`DELETE`返回`204`状态码，表示资源已经不存在；`202 Accepted`状态码表示服务器已经收到请求，但还未进行处理，会在未来再处理，通常用于异步操作。

`202 Accepted` 该状态码表示服务器已经接受到了来自客户端的请求，但还未开始处理。常用短信发送、邮件通知、模板消息推送等这类很耗时需要队列支持的场景中；返回该状态码时，响应实体必须为空。

### 2.3 3xx 状态码

API 用不到301状态码（永久重定向）和302状态码（暂时重定向，307也是这个含义），因为它们可以由应用级别返回，浏览器会直接跳转，API 级别可以不考虑这两种情况。

API 用到的3xx状态码，主要是303 See Other，表示参考另一个 URL。它与302和307的含义一样，也是"暂时重定向"，区别在于302和307用于GET请求，而303用于POST、PUT和DELETE请求。收到303以后，浏览器不会自动跳转，而会让用户自己决定下一步怎么办。

### 2.4 4xx 状态码

`4xx` 状态码表示客户端错误，主要有下面几种。
`400 Bad Request`：服务器不理解客户端的请求，未做任何处理。
`401 Unauthorized`：用户未提供身份验证凭据，或者没有通过身份验证。
`403 Forbidden`：用户通过了身份验证，但是不具有访问资源所需的权限。
`404 Not Found`：所请求的资源不存在，或不可用。
`405 Method Not Allowed`：用户已经通过身份验证，但是所用的 HTTP 方法不在他的权限之内。
`410 Gone`：所请求的资源已从这个地址转移，不再可用。
`415 Unsupported Media Type`：客户端要求的返回格式不支持。比如，API 只能返回 JSON 格式，但是客户端要求返回 XML 格式。
`422 Unprocessable Entity`：客户端上传的附件无法处理，导致请求失败。
`429 Too Many Requests`：客户端的请求次数超过限额。

### 2.5 5xx 状态码

5xx状态码表示服务端错误。一般来说，API 不会向用户透露服务器的详细信息，所以只要两个状态码就够了。

`500 Internal Server Error`：客户端请求有效，服务器处理时发生了意外。
`503 Service Unavailable`：服务器无法处理请求，一般用于网站维护状态。



## 三、HTTP Response

### 3.1 不要返回纯本文
API 返回的数据应该是一个 JSON 对象，因为这样才能返回标准的结构化数据。所以，服务器回应的 HTTP 头的 `Content-Type` 属性要设为 `application/json`。

客户端请求时，也要明确告诉服务器，可以接受 `JSON` 格式，即请求的 HTTP 头的 `ACCEPT` 属性也要设成 `application/json`，例如：
```
GET /orders/2 HTTP/1.1 
Accept: application/json
```

### 3.2 返回错误信息
将错误信息直接放入响应实体中，并携带正确的 HTTP 状态码，建议错误信息格式如下：
```json
{
    "error": "您查找的资源不存在",
    "detail": {
        "project": [
        	"This field may not be null."
    	],
    	"name": [
        	"This field may not be null."
    	]
    }
}
```

### 3.3 发生错误时，不要返回 200 状态码
有一种不恰当的做法是，即使发生错误，也返回200状态码，把错误信息放在数据体里面，就像下面这样。

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
  "status": "failure",
  "data": {
    "error": "Expected at least two items in list."
  }
}
```
上面代码中，解析数据体以后，才能得知操作失败。

这张做法实际上取消了状态码，这是完全不可取的。正确的做法是，状态码反映发生的错误，具体的错误信息放在数据体里面返回。下面是一个例子。

```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": "Invalid payoad.",
  "detail": {
     "surname": "This field is required."
  }
}

```


## 参考文献

[HTTP概述, by MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview)

[理解RESTful架构--阮一峰](http://www.ruanyifeng.com/blog/2011/09/restful.html)

[RESTful API 最佳实践--阮一峰](http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)

[API design, by MicroSoft Azure](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design)

[RESTful API Design: 13 Best Practices to Make Your Users Happy, by Florimond Manca](https://florimond.dev/blog/articles/2018/08/restful-api-design-13-best-practices-to-make-your-users-happy/)

[RESTful API 设计规范, by godruoyi](https://godruoyi.com/posts/the-resetful-api-design-specification)

[List of HTTP status codes, by wiki](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

