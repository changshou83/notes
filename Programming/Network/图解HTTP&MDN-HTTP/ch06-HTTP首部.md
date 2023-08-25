- 特定于连接的首部不得与HTTP/2一起使用，例如：`Connection`，`Keep-Alive`，`Trailer`，`TE`，`Proxy-Connection`，`Transfer-Encoding`，`Upgrade`

## 通用首部

| 字段名            | 说明                                                             |
| ----------------- | ---------------------------------------------------------------- |
| Cache-Control     | 控制缓存                                                         |
| Connection        | 决定当前的事务完成后，是否会关闭网络连接                         |
| Date              | 报文的创建日期                                                   |
| Upgrade           | 升级为其他协议                                                   |
| Trailer           | 允许发送方在分块发送的消息后面添加额外的元信息                   |
| Transfer-Encoding | 报文主体的编码方式                                               |
| Via               | 代理服务器的相关信息                                             |
| Keep-Alive        | 允许消息发送者暗示连接的状态，还可以用来设置超时时长和最大请求数(`Connection:keep-alive`时有效) |

## 请求首部

| 字段名              | 说明                                                                                                                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Accept              | 用户代理可处理的媒体类型                                                                                                       |
| Accept-Charset      | 优先字符集                                                                                                                     |
| Accept-Encoding     | 优先编码                                                                                                                       |
| Accept-Language     | 优先语言                                                                                                                       |
| Authorization       | 认证信息                                                                                                                       |
| Expect              | 期待服务器的特定行为                                                                                                           |
| From                | 用户的电子邮箱地址                                                                                                             |
| Host                | 用户资源所在服务器                                                                                                             |
| If-Match            | 比较实体标记(ETag)                                                                                                             |
| If-None-Match       | 比较实体标记(与If-Match相反)                                                                                                   |
| If-Modified-Since   | 比较资源的更新时间                                                                                                             |
| If-Unmidified-Since | 比较资源的更新时间(与Last-Modified-Since相反)                                                                                  |
| Max-Forwards        | 用于限制 [`TRACE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/TRACE) 方法可经过的服务器（通常指代理服务器）数目 |
| Proxy-Authorization | 包含了用户代理提供给代理服务器的用于身份验证的凭证                                                                             |
| Range               | 告知服务器返回文件的哪一部分                                                                                                   |
| Referer             | 包含了当前请求页面的来源页面的地址                                                                                             |
| User-Agent          | 让网络协议的对端来识别发起请求的用户代理软件的应用类型、操作系统、软件开发商以及版本号                                         |
| TE                  | 指定用户代理希望使用的传输编码类型                                                                                             |
| Cookie                    | 包含Set-Cookie的值                                                                                                                               |

## 响应首部

| 字段名             | 说明                                             |
| ------------------ | ------------------------------------------------ |
| Accept-Range       | 是否接受范围请求                                 |
| Age                | 包含对象在缓存代理中存贮的时长，以秒为单位       |
| ETag               | 资源的特定版本的标识符                           |
| Location           | 指定的是需要将页面重新定向至的地址               |
| Proxy-Authenticate | 指定代理服务器获取权限需采用的身份验证方式       |
| Retry-After        | 表示用户代理需要等待多长时间之后才能继续发送请求 |
| Server             | 包含了处理请求的源头服务器所用到的软件相关信息   |
| Vary               | 代理服务器的缓存的管理信息                       |
| WWW-Authenticate   | 服务端对客户端要求的身份验证方式                 |
| Set-Cookie         | 由服务器向用户代理发送Cookie                     |

## 实体首部

| 字段名           | 说明                   |
| ---------------- | ---------------------- |
| Allow            | 资源支持的HTTP方法     |
| Content-Encoding | 实体主体使用的编码方式 |
| Content-Language | 实体主体的自然语言     |
| Content-Length   | 实体主体的大小         |
| Contnet-Location | 资源对应的URI          |
| Contnet-Range    | 实体主体的位置范围     |
| Contnet-Type     | 实体主体的媒体类型     |
| Expires          | 实体主体的过期日期     |
| Last-Modified    | 实体主体的最后修改日期 |
