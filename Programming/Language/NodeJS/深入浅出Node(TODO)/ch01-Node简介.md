
- 为什么是JS
	- 作者本来想做一个基于事件驱动，非阻塞I/O的高性能Web服务器
	- JavaScript
		- 开发门槛比C低
		- 历史包袱比Lua少
		- Ruby虚拟机性能不好
- Node的特点
	- 异步I/O
	- JS单线程
	- 事件驱动
	- 跨平台(通过Libuv)
- 应用场景
	- 大前端
	- 工具类应用
	- I/O密集型
	- RPC服务：主要对OLTP(联机事务处理过程)数据库进行操作，如果是复杂计算，请用Go/Java
