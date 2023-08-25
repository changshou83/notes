
## HTTP访问控制(CORS)

- Cross-Origin Resource Sharing：跨源资源共享
- 源：文档URL的协议、主机和端口
- 使用场景
	- 跨域HTTP请求
	- Web字体
	- WebGL贴图
	- `drawImage`
	- CSS的`background-image: url();`

### 机制

- 通过浏览器发起一个到服务器托管的跨源资源的“预检”请求来检查服务器是否允许要发送的真实请求
- 浏览器限制脚本内发起的跨源HTTP请求，除非预检请求的响应报文包含了正确的CORS响应头

#### 简单请求

- 简单请求不会触发预检
- 动机：不处理表单请求
- 条件
	- 请求方法：GET/POST/HEAD
	- 请求首部：Accept/Accept-Language/Content-Language/Content-Type(`text/plain`,`multipart/form-data`,`application/x-www-form-urlencoded`)

#### 预检请求

- 要求必须首先使用`OPTIONS`方法发起一个预检请求(包含必要信息)到服务器，获知服务器是否允许该真实请求

#### 附带身份凭证的请求

- 服务器需要返回`Access-Control-Allow-Credentials: true`的响应，否则响应内容会被忽略
- 且`Access-Control-Allow-(Origin/Headers/Methods)`不可为`*`

### 相关HTTP首部

- 请求标头
	- `Origin`：源站URL
	- `Access-Control-Request-Method`：真实请求的方法
	- `Access-Control-Request-Headers`：真实请求的http头
- 响应标头
	- `Access-Control-Allow-Origin`：允许访问的源
	- `Access-Control-Expose-Headers`：除基本响应头外允许被获取的响应头
		- 基本响应头：`Cache-Control`,`Content-Language`,`Content-Type`,`Expires`,`Last-Modified`,`Pragma`
	- `Access-Control-Max-Age`：预检请求的缓存时间
	- `Access-Control-Allow-Credentials`：用于附带身份凭证的请求
	- `Access-Control-Allow-Methods`：允许使用的方法
	- `Access-Control-Allow-Headers`：允许携带的http头

## HTTPS

- 在使用HTTP进行通信的基础上使用`SSL/TLS`来加密数据包,默认端口443
- 目的：提供对网站服务器的身份认证，保证交换资料的隐私和完整性
- 信任基于预先安装在操作系统中的证书颁发机构(CA)
- 特点
	- 信息加密：交互信息无法被窃取
	- 校验机制：无法篡改通信内容，篡改了就不能正常显示
	- 身份证书：证明报文的完整性
- 缺点：需要进行SSL握手，时延高;需要购买CA证书并进行加解密，部署成本高
- 加密方式
	- 对称密钥加密：只是用一个密钥，运算速度快，但是无法做到安全的交换密钥
	- 非对称密钥加密：公钥可以发布，私钥保密，解决密钥交换问题，但速度慢
	- 混合加密：先使用非对称加密交换密钥，然后使用对称密钥加密明文内容
- 通信步骤：与[TLS](../图解TLS#TLS 1.2)基本一致。

### 验证流程

1. client发送http请求，连接到server端口
2. server将自己的信息已数字证书的形式返回给client
3. client验证证书合法性，通过后，会生成一个随机的对称密钥并用公钥加密
5. server使用私钥对加密内容进行解密得到对称密钥，然后使用这个密钥进行传输
