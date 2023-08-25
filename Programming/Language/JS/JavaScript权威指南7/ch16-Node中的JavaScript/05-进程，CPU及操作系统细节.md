## Process 对象

```js
// 属性
process.argv               // 包含命令行参数的数组
process.arch               // CPU架构，如：x64
process.env                // 环境变量
process.execPath           // Node可执行文件的绝对路径
process.exitCode           // 程序退出时报告的整数编码
process.pid                // 当前进程ID
process.ppid               // 父进程ID
process.platform           // 操作系统，如：Linux，Darwin，Win32
process.title              // 返回当前进程标题
process.version            // Node的版本号字符串
process.versions           // Node依赖库的版本号字符串

// 方法
process.cwd()              // 返回当前工作目录
process.chdir()            // 设置当前工作目录
process.cpuUsage()         // 报告CPU使用情况
process.exit()             // 终止当前程序
process.getuid()           // 返回当前用户的Unix用户ID
process.hrtime.bigint()    // 返回 “高分辨率” 的纳秒级时间戳
process.kill()             // 向另一个进程发送信号，发送的信号可能会做其他事情而不是杀死目标进程
process.memoryUsage()      // 返回一个包含内存占用细节的对象
process.nextTick()         // 类似于setImmediate(),立即调用一个函数。可以使用queueMicrotask()代替
process.resourceUsage()    // 返回包含资源占用细节的对象
process.setuid()           // 通过ID或名字设置当前用户
process.umask()            // 设置或返回新文件的默认权限
process.uptime()           // 返回Node正常运行的时间(秒级)
```

## OS 内置模块

```js
const os = require('os')

// 属性
os.constants            // 一些预定义常量
os.EOL                  // 操作系统原生的行终止符

// 方法
os.arch()               // CPU架构
os.cpus()               // CPU核心数据
os.endianness()         // CPU原生字节序
os.freemem()            // 自由RAM数量(字节)
os.getPriority()        // 调度进程的优先级
os.homedir()            // 当前用户主目录
os.hostname()           // 计算机主机名
os.loadavg()            // 返回1，5，15分钟的平均负载
os.networkInterfaces()  // 可用网络连接的细节
os.platform()           // 操作系统,aix,darwin,freebsd,linux,openbsd,sunos,win32,相当于process.platform
os.release()            // 操作系统版本号
os.setPriority()        // 设置进程优先级
os.tmpdir()             // 默认的临时目录
os.totalmem()           // RAM总数
os.type()               // 操作系统，Linux,Darwin,Windows_NT
os.uptime()             // 系统运行时间
os.userinfo()           // 当前用户的信息
```
