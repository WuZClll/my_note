## 二. Redis的安装与配置

### 2.1 Redis的安装

#### 2.1.2 安装前的准备工作

##### 1) 安装 gcc

由于 Redis 是由 C/C++语言编写的，而从官网下载的 Redis 安装包是需要编译后才可安装 的，所以对其进行编译就必须要使用相关编译器。对于 C/C++语言的编译器，使用最多的是 gcc 与 gcc-c++，而这两款编译器在 CentOS7 中是没有安装的，所以首先要安装这两款编译器。 GCC，GNU Compiler Collection，GNU 编译器集合。

**安装 gcc： **`yum -y install gcc gcc-c++`

##### 2) 下载Redis

redis 的官网为： http://redis.io。点击下面的链接可以直接进行**下载**

[点击打开资源](C:\Users\^\Documents\aaa\Redis7动力节点\资源\资源)

##### 3) 上传到Linux

将下载好的压缩包**上传**到 Linux 的/opt/tools 目录中。（这个路径是随便创的）

#### 2.1.3 Redis 安装

##### 1) 解压Redis

- 将 Redis **解压到**/opt/apps 目录中。（这个路径是随便创的）
  `tar -zxvf redis-7.0.3.tar.gz -C /opt/apps`
- 进入到/opt/apps 目录中再将 Redis 解压包目录更名为 redis（不更名也无所谓）。
  `mv redis-7.0.3/ redis`

##### 2) 编译

编译过程是根据 Makefile 文件进行的，而 Redis 解压包中已经存在该文件了。所以可以 **直接进行编译**了

- 进入到解压目录中，然后执行编译命令 `make`。
  当看到如下提示时，表示编译成功
  <img src=".\MDImg\二\2.1eg.png" alt="img" style="zoom:67%;" />

##### 3) 安装

在 Linux 中对于编译过的安装包执行 `make install` 进行安装。
共安装了三个组件：redis 服务器、客户端与一个性能测试工具 benchmark。

##### 4) 查看bin目录

安装完成后，打开/usr/local/bin 目录，可以看到出现了很多的文件。
通过 `echo $PATH` 可以看到，/usr/local/bin 目录是存在于该系统变量中的，这样这些命令就可以在任意目录中执行了。

#### 2.1.4 Redis启动与停止

##### ####1) 前台启动

在任意目录执行 `redis-server `命令即可启动 Redis。这种启动方式会占用当前命令行窗口。
执行`ps aux | grep redis`可以查看到当前的 Redis 进程，默认端口号为 6379。
通过 `Ctrl + C` 命令可以停止 Redis

##### 2) 命令式后台启动

- 使用 `nohub` 命令，最后再添加一个`&`符，可以使要启动的程序在后台以守护进程方式运行。
  `nohup redis-server $`
  <img src=".\MDImg\二\2.1.4.2eg.png" alt="img" style="zoom:67%;" />
- 好处
  进程启动后不会占用一个会话窗口，且其还会在当前目录，即运行启动 命令的当前目录中创建一个 `nohup.out 文件`用于记录 Redis 的操作日志。

##### 3) Redis的停止

通过` redis-cli shutdown `命令可以停止 Redis。

##### 4) 配置式后台启动

- 使用 `nohup` 命令可以使 Redis 后台启动，
- 但每次都要键入` nohup` 与`&`符，比较麻烦。可以通过修改 Linux 中 Redis 的核心配置文件 `redis.conf` 达到后台启动的目的。
  `redis.conf` 文件在 Redis 的安装目录根下
  修改`redis.conf` 文件内容，将 `daemonize` 属性值由` no `改为` yes`，使 Redis 进程以守护进程方式运行。
- 修改后再启动 Redis，就无需再键入 nohup 与&符了，但必须要指定启动所使用的 Redis 配置文件。这是为什么呢？ 使用` nohup redis-server &`命令启动 Redis 时，启动项中已经设置好了 Redis 各个参数的 默认值，Redis 会按照这些设置的参数进行启动。但这些参数是可以在配置文件中进行修改 的，修改后，需要在启动命令中指定要加载的配置文件，这样，配置文件中的参数值将覆盖 原默认值。 Redis 已经给我们提供好了配置文件模板，是 Redis 安装目录的根目录下的 redis.conf 文 件。由于刚刚对 redis.conf 配置文件做了修改，所以在开启 Redis 时需要显示指出要加载的 配置文件。配置文件应紧跟在 redis-server 的后面

### 2.2 连接前的配置

#### 2.2.1 绑定客户端ip

Redis 可以通过修改配置文件来限定可以访问自己的客户端 IP。
<img src=".\MDImg\二\2.2.1eg.png" alt="img" style="zoom:67%;" />
只允许当前主机访问当前的 Redis，其它主机均不可访问。所以，如果不 想限定访问的客户端，只需要将该行注释掉即可。
<img src=".\MDImg\二\2.2.1-1eg.png" alt="img" style="zoom:67%;" />

#### 2.2.2 关闭保护模式

默认保护模式是开启的。其只允许本机的客户端访问，即只允许自己访问自己。但生产 中应该关闭(no)，以确保其它客户端可以连接 Redis。
<img src=".\MDImg\二\2.2.2-1eg.png" alt="img" style="zoom:67%;" />

#### 2.2.3 设置访问密码

为 Redis 设置访问密码，可以对要读/写 Redis 的用户进行身份验证。没有密码的用户可 以登录 Redis，但无法访问。

##### 2.2.3.1 密码设置

访问密码的设置位置在 `redis.conf` 配置文件中。默认是被注释掉的，没有密码 
<img src=".\MDImg\二\2.2.3-1eg.png" alt="img" style="zoom:67%;" />
没有通过密码登录的用户，无法读/写 Redis。

##### 2.2.3.2 使用密码

对于密码的使用，有两种方式：登录时未使用密码，则访问时先输入密码；登录时直接 使用密码登录，访问时无需再输入密码。

1. 登陆时未使用密码`auth 111`
2. 登陆时使用密码`redis-cli -a 111`
3. 退出时使用密码`redis-cli -a 111 shutdown`

> 注意：为了方便后面的学习，我们这里就不设置访问密码了，直接将其注释掉即可。

#### 2.2.4 禁止/重命名命令

后面要学习两个非常危险的命令：flushal 与 flushdb。它们都是用于直接删除整个 Redis 数据库的。若让用户可以随便使用它们，可能会危及数据安全。Redis 可以通过修改配置文 件来禁止使用这些命令，或重命名这些命令。以下配置，禁用了 flushall 与 flushdb 命令。 当然，在学习过程中暂时不禁用它们
<img src=".\MDImg\二\2.2.3.3-1eg.png" alt="img" style="zoom:67%;" />

#### 2.2.5 启动Redis

当然，若要使客户端能够连接 Redis，则必须开启服务端的 Redis。
`redis-server redis.conf`

### 2.3 客户端的分类

#### 2.3.1 命令行客户端

Redis 提供了基本的命令行客户端。打开命令行客户端的命令为 `redis-cli`。

- `-h`：指定要连接的 Redis 服务器的 IP。 
- `-p`：指定要连接的 Redis 的端口号。
- `-a`：指定要连接的 Redis 的密码

<img src=".\MDImg\二\2.3.1-1eg.png" alt="img" style="zoom:67%;" />

若连接的是本机 Redis，且端口号没有改变，保持默认的 6379，则`-h` 与`-p` 选项可以省略不写。

#### 2.3.2 图形界面客户端

##### 1)  Redis Desktop Manager

Redis 的图形界面客户端很多，其中较出名的是 Redis Desktop Manager 的客户端。不过，该软件原来是免费软件，从 0.8.8 版本后变为了商业化收费软件。

官网为：https://resp.app/（原来是 http://redisdesktop.com）。
[点击打开本地资源](C:\Users\^\Documents\aaa\Redis7动力节点\资源\资源)

#####  2) RedisPlus

RedisPlus 是为 Redis 可视化管理开发的一款开源免费的桌面客户端软件，支持 Windows 、

Linux、Mac 三大系统平台，RedisPlus 提供更加高效、方便、快捷的使用体验，有着更加现

代化的用户界面风格。

RedisPlus 的官网地址为 https://gitee.com/MaxBill/RedisPlus。

#### 2.3.2 Java 代码客户端

所谓 Java 代码客户端就是一套操作 Redis 的 API，其作用就像 JDBC 一样，所以 Java 代码客户端其实就是一个或多个 Jar 包，提供了对 Redis 的操作接口。

对 Redis 操作的 API 很多，例如 jdbc-redis、jredis 等，但最常用也是最有名的是 Jedis。

### 2.4 Redis 配置文件详解

Redis 的核心配置文件 redis.conf 在安装根目录下，默认包含 2000 多行。这些内容根据功能被划分为了很多部分。下面将一些重要部分进行介绍。

#### 2.4.1 基础说明

<img src=".\MDImg\二\2.4.1-1eg.png" alt="img" style="zoom:67%;" />这部分主要是给出一些说明，包含三部分意思：

- 第 1-6 行用于说明，如果要启动 Redis，需要指出配置文件的路径。
- 第 8-16 行用于说明当前配置文件中可以使用的的容量单位及意义。
- 第 18 行用于说明这些容量单位没有大小写之分

#### 2.4.2  includes

<img src=".\MDImg\二\2.4.2-1eg.png" alt="img" style="zoom:67%;" />

指定要在<mark>当前配置文件中包含的配置文件</mark>。这样做的目的主要是便于配置信息管理：可以将不同场景的配置都进行单独定义，然后在当前核心配置文件中根据不同场景选择包含进不同的配置文件。

**在`redis.conf`文件最后一行添加`include 自定义配置文件的位置`来将自定义配置文件里的配置覆盖掉`redis.conf`的配置**

#### 2.4.3 modules

<img src=".\MDImg\二\2.4.3-1eg.png" alt="img" style="zoom:67%;" />
Redis 配置文件中可以通过加载不同的<mark>第三方模块</mark>，来**增强、扩展 Redis 的功能。**

#### 2.4.4 network

<img src=".\MDImg\二\2.4.4-1eg.png" alt="img" style="zoom:67%;" />

Network 配置模块是比较重要的部分，主要进行网络相关的配置。其中较重要的有：

##### 1) bind

<img src=".\MDImg\二\2.4.4.1)-1eg.png" alt="img" style="zoom:67%;" /><mark>指定可以访问当前 Redis 服务的客户端 IP</mark>，**默认只允许本地访问**，即当前 Redis 自己访问自己。**为了使所有其它客户端都可访问，一般要将其注释掉。**

##### 2) protected-mode

<img src=".\MDImg\二\2.4.4.2)-1eg.png" alt="img" style="zoom:67%;" />默认保护模式是开启的。其只允许本机的客户端访问，即只允许自己访问自己。但<mark>生产中应该关闭，以确保其它客户端可以连接 Redis。</mark>

##### 3) port

<img src=".\MDImg\二\2.4.4.3)-1eg.png" alt="img" style="zoom:67%;" />
Redis 监听的连接<mark>端口号</mark>，默认 6379。 

##### 4) tcp-backlog

<img src=".\MDImg\二\2.4.4.4)-1eg.png" alt="img" style="zoom:67%;" />

tcp-backlog 是一个 TCP 连接的队列，其主要用于解决高并发场景下客户端**慢连接(连接速度慢)**问题。这里设置的值就是这个队列的长度。该队列与 TCP 连接的三次握手有关。不同的 Linux 内核，backlog 队列中存放的元素（客户端连接）类型是不同的。

- **Linux 内核 2.2 版本之前**，该队列中存放的是已完成了第一次握手的所有客户端连接
  其中就包含已完成三次握手的客户端连接。当然，此时的 backlog 队列中的连接也具有两种状态：

  1. 未完成三次握手的连接状态为 SYN_RECEIVED
  2. 已完成三次握手的连接状态为 ESTABLISHED

  只有 ESTABLISHED 状态的连接才会被 Redis 处理。

- **Linux 内核 2.2 版本之后 **TCP 系统中维护了两个队列：SYN_RECEIVED 队列与 ESTABLISHED队列。SYN_RECEIVED 队列中存放的是未完成三次握手的连接，ESTABLISHED 队列中存放的是已完成三次握手的连接。
  此时的 backlog 就是 ESTABLISHED 队列。

三次握手(补充)
<img src=".\MDImg\二\2.4.4.4)-6eg.png" alt="img" style="zoom:100%;" />

查看 Linux 内核版本：
<img src=".\MDImg\二\2.4.4.4)-2eg.png" alt="img" style="zoom:100%;" />

TCP 中的 backlog 队列的长度在 Linux 中由内核参数 `somaxconn `来决定。所以，<mark>在 Redis中该队列的长度由 Redis 配置文件设置与 somaxconn 来共同决定：取它们中的最小值。</mark>

查看当前 Linux 内核中 somaxconn 的值:
<img src=".\MDImg\二\2.4.4.4)-3eg.png" alt="img" style="zoom:67%;" />

**生产环境下**（特别是高并发场景下），**backlog 的值最好要大**一些，否则可能会影响系统性能。
修改/etc/sysctl.conf 文件`vim /etc/sysctl.conf`，在文件最后添加如下内容：`net.core.somaxconn=2048`
<img src=".\MDImg\二\2.4.4.4)-4eg.png" alt="img" style="zoom:67%;" />
修改过后可以重启虚拟机，也可以通过执行如下命令来使新的修改生效。`sysctl -p`(在不重启虚拟机的情况下重新加载配置文件)
<img src=".\MDImg\二\2.4.4.4)-5eg.png" alt="img" style="zoom:67%;" />

##### 5) timeout

<img src=".\MDImg\二\2.4.4.5)-1eg.png" alt="img" style="zoom:67%;" />空闲超时。当客户端与 Redis 间的<mark>空闲时间超过该时长后，连接自动断开</mark>。单位秒。**默认值为 0，表示永远不超时**

> eg：如果设置超时三分钟，则表示服务端与客户端在3分钟内没有数据通信， 连接会自动断开

##### 6) tcp-keepalive

<img src=".\MDImg\二\2.4.4.6)-1eg.png" alt="img" style="zoom:67%;" />
该配置执行的前提是`timeout 0`永不超时

该配置主要用于设置 Redis<mark> 检测与其连接的所有客户端的存活性时间间隔，单位秒</mark>。一般是在空闲超时 timeout 设置为 0 时进行配置。

> 解释：
> `tcp-keepout 300`：当`timeout 0`永不断开时，每300秒检测一次客户端，当发现客户端不在，在300秒后第二次检测客户端还不在，就断开连接

#### 2.4.5 general

##### 1) daemonize

<img src=".\MDImg\二\2.4.5.1)-1eg.png" alt="img" style="zoom:67%;" />该配置可以控制 Redis 启动是否采用<mark>守护进程方式</mark>，即是否是后台启动。yes 是采用**后台启动**。

##### 2) pidfile

<img src=".\MDImg\二\2.4.5.2)-1eg.png" alt="img" style="zoom:67%;" />该配置用于指定 Redis **运行时 pid 写入的文件**，无论 Redis 是否采用守护进程方式启动，pid 都会写入到该配置的文件。

注意，**如果没有配置 pid 文件**，不同的启动方式，pid 文件的产生效果是不同的：

- 采用守护进程方式启动即后台启动（daemonize 为 yes）：pid 文件为/var/run/redis.pid。 
- 采用前台启动（daemonize 为 no）：不生产 pid 文件

##### 3) loglevel

<img src=".\MDImg\二\2.4.5.3)-1eg.png" alt="img" style="zoom:67%;" /><mark>配置日志的级别</mark>。Redis 中共有四个级别，由低到高依次是：

- debug：可以获取到很多的信息，一般在开发和测试时使用。
- verbose：可以获取到很多不太有用的信息，但不像 debug 级别那么多。
- notice：可以获取到在生产中想获取到的适当多的信息，默认级别。
- warning：只记录非常重要/关键的信息

##### 4) logfile

<img src=".\MDImg\二\2.4.5.4)-1eg.png" alt="img" style="zoom:67%;" /><mark>指定日志文件。</mark>

- 如果设置为空串，且是后台运行，则强制将日志记录到标准输出设备（显示器）。
- 如果使用的是守护进程启动方式，设置为空串，则意味着会将日志发送到设备/dev/null（空设备）即不显示。 

##### 5) darabases

<img src=".\MDImg\二\2.4.5.5)-1eg.png" alt="img" style="zoom:67%;" />
<mark>设置数据库的数量</mark>。默认数据库是 0 号数据库。可以使用 `select <dbid>`在每个连接的基础上选择一个不同的数据库，其中 dbid 是介于 0 和'databases'-1'之间的数字。

#### 2.4.6 security

<img src=".\MDImg\二\2.4.6.1)-1eg.png" alt="img" style="zoom:67%;" />
**用户设置 ACL 权限、Redis 访问密码相关配置**。该模块中最常用的就是 `requirepass `属性。
<img src=".\MDImg\二\2.4.6.1)-2eg.png" alt="img" style="zoom:67%;" />
<mark> `requirepass `设置客户端访问密码</mark>。注释掉后则没有密码。

#### 2.4.7 clients

<img src=".\MDImg\二\2.4.7-1eg.png" alt="img" style="zoom:67%;" />该模块用于设置与客户端相关的属性，其中**仅包含一个属性 `maxclients`。**

- maxclients 用于设置 Redis <mark>可并发处理的客户端连接数量</mark>，默认值为 10000。如果达到了该最大连接数，则会拒绝再来的新连接，并返回一个异常信息：已达到最大连接数。
- 注意，该值不能超过 Linux 系统支持的可打开的文件描述符最大数量阈值。查看该阈值的方式如下。修改该值，可以通过修改`/etc/secutiry/limits.conf 文件`（自己去改）。
  <img src=".\MDImg\二\2.4.7-2eg.png" alt="img" style="zoom:67%;" />

#### 2.4.8 memory management 内存管理

<img src=".\MDImg\二\2.4.8-1eg.png" alt="img" style="zoom:67%;" />
该配置可以控制最大可用内存及相关内容移除问题。

##### 1) maxmermory

<img src=".\MDImg\二\2.4.8.1)-1eg.png" alt="img" style="zoom:67%;" />将内存使用限制设置为指定的字节数。**当达到内存限制时**，Redis 将**根据选择的逐出策略** maxmemory-policy 尝试**删除**符合条件的 **key。**
如果不能按照逐出策略移除 key，则会给写操作命令返回 `error`，但对于只读的命令是没有影响的

##### 2) maxmamory-policy

 maxmermory的**逐出策略**
<img src=".\MDImg\二\2.4.8.2)-1eg.png" alt="img" style="zoom:67%;" />该属性用于设置，当达到 maxmemory 时，Redis 将如何选择要移除的内容。当然，如果没有符合相应策略的内容要删除，则在执行写入命令时会给出 errors 的响应。Redis 中共支持 8 种移除策略：

- volatile-lru：使用近似** LRU** 算法移除，仅适用于**设置了过期时间的 key。 **
- allkeys-lru：使用近似** LRU** 算法移除，可适用于**所有类型的 key。 **
- volatile-lfu：使用近似 **LFU** 算法移除，仅适用于**设置了过期时间的 key。 **
- allkeys-lfu：使用近似 **LFU** 算法移除，可适用于**所有类型的 key。 **
- volatile-random：**随机**移除一个 key，仅适用于**设置了过期时间的 key。** 
- allkeys-random：**随机**移除一个 key，可适用于**所有类型的 key。 **
- volatile-ttl：移除**距离过期时间最近的 key。** 
- noeviction：不移除任何内容，**只**是在写操作时**返回一个错误**，默认值。

##### 3) maxmemory-samples

<img src=".\MDImg\二\2.4.8.3)-1eg.png" alt="img" style="zoom:67%;" />该属性用于指定挑选要删除的 key 的样本数量。样本的选择采用的是 LRU 算法，其不能修改。但从样本中再选择要移除的 key，则采用的是 maxmamory-policy 指定的策略。

> `maxmemory-samples 5`：先选择5个key的样本，在使用指定的逐出策略在这五个样本中移除
> maxmemory-samples   3：很快但不准确，10：非常接近真正的LRU算法但要耗费更多的CPU，我们采用折中的5

##### 4) maxmemory-eviction-tenacity

<img src=".\MDImg\二\2.4.8.4)-1eg.png" alt="img" style="zoom:67%;" /><mark>设置移除容忍度(找到需要移除的样本后，能容忍它存在的度)</mark>
数值越小表示容忍度越低，需要移除的数据移除延迟越小；
数值越大表示容忍度越高，需要移除的数据移除延迟越大。

maxmemory-eviction-tenacity 0最小延迟，10是默认值，100已经不再考虑移除问题了

#### 2.4.9 threaded I/O

<img src=".\MDImg\二\2.4.9-1eg.png" alt="img" style="zoom:67%;" />
该配置模块用于配置 Redis 对多线程 IO 模型的支持

##### 1) io-threads

<img src=".\MDImg\二\2.4.9.1)-1eg.png" alt="img" style="zoom:67%;" />该属性用于指定要启用多线程 IO 模型时，<mark>要使用的线程数量。</mark>

查看当前系统中包含的 CPU 数量`lscpu`：
<img src=".\MDImg\二\2.4.9.1)-2eg.png" alt="img" style="zoom:67%;" />

##### 2) io-threads-do-reads

<img src=".\MDImg\二\2.4.9.2)-2eg.png" alt="img" style="zoom:67%;" />
该属性用于**启用**多线程 IO 模型中的多线程**处理读请求的能力**。
一般来说这个东西保持默认no就行了

## 三. Redis 命令

> - 3.1-3.8 +3.15基础 一定要掌握
> - 3.9-3.10 高级 底层原理 面使用
> - 3.11-3.14 了解就行 以后工作遇到能想到就行

Redis 根据命令所操作对象的不同，可以分为三大类：对 Redis 进行基础性操作的命令，对 Key 的操作命令，对 Value 的操作命令。

### 3.1 Redis 基本命令

首先通过` redis-cli` 命令进入到 Redis 命令行客户端，然后再运行下面的命令。

#### 3.1.1 `ping`心跳命令 

键入 ping 命令，会看到 PONG 响应，则说明该客户端与 Redis 的连接是正常的。该命令亦称为心跳命令。
<img src=".\MDImg\三\3.1.1-1eg.png" alt="img" style="zoom:67%;" />

#### 3.1.2 `set`,`get`读写键值命令

`set key value` 会将指定 key-value 写入到 DB。`get key` 则会读取指定 key 的 value 值。关于更多 set 与 get 命令格式，后面会详细学习。
 <img src=".\MDImg\三\3.1.2-1eg.png" alt="img" style="zoom:67%;" />

#### 2.1.3  `select`数据库切换

Redis 默认有 16 个数据库。这个在 Redis Desktop Manager（RDM）图形客户端中可以直观地看到。
<img src=".\MDImg\三\3.1.3-1eg.png" alt="img" style="zoom:67%;" />
默认使用的是 0 号 DB，可以通过 `select db` 索引来切换 DB。

例如，如`select 3`会切换到DB3，并会将 age-23 写入到 DB3 中。
<img src=".\MDImg\三\3.1.3-2eg.png" alt="img" style="zoom:67%;" />
并且这个结果在 RDM 中是可以直观地看到的。
<img src=".\MDImg\三\3.1.3-3eg.png" alt="img" style="zoom:67%;" />

#### 3.1.4 `dbsize`查看 key 的数量 

`dbsize` 命令可以查看当前数据库中 key 的数量。
<img src=".\MDImg\三\3.1.4-1eg.png" alt="img" style="zoom:67%;" />
从以上查看情况看，DB0 中有 2 个 key，DB1 中没有 key，DB3 中有 1 个 key。

#### 3.1.5  `flushdb`删除当前库中数据

`flushdb` 命令仅仅删除的是当前数据库中的数据，不影响其它库。
<img src=".\MDImg\三\3.1.5-1eg.png" alt="img" style="zoom:67%;" />

#### 3.1.6 `flushall`删除所有库中数据命令 

`flushall` 命令可以删除所有库中的所有数据。所以该命令的使用一定要慎重。
<img src=".\MDImg\三\3.1.6-1eg.png" alt="img" style="zoom:67%;" />

#### 3.1.7 `exit` 或 `quit` 退出客户端命令

使用 `exit` 或 `quit` 命令均可退出 Redis 命令行客户端。
<img src=".\MDImg\三\3.1.7-1eg.png" alt="img" style="zoom:67%;" />

### 3.2 Key 操作命令

Redis 中存储的数据整体是一个 Map，其 key 为 String 类型，而 value 则可以是 String、Hash 表、List、Set 等类型

#### 3.2.1 `keys` 检索键

- 格式：`KEYS pattern`
- 功能：查找所有符合给定模式 pattern 的 key，**pattern 为正则表达式**。
- 说明：KEYS 的速度非常快，<mark>但在一个大的数据库中使用它可能会**阻塞**当前服务器的服务</mark>。所以生产环境中**一般不使用**该命令，而使用 scan 命令代替。

#### 3.2.2 `exists` 是否存在 key

- 格式：`EXISTS key`
- 功能：检查给定 key**是否存在**。
- 说明：若 key 存在，返回 1 ，否则返回 0 。

#### 3.2.3 `del` 删除key

- 格式：`DEL key [key ...]`
- 功能：删除给定的一个或多个 key 。不存在的 key 会被忽略。
- 说明：返回被删除 key 的数量。
- eg:`del key1 key2`

#### 3.2.4 `rename` 修改 key 名

- 格式：`RENAME key newkey`
- 功能：将 key 改名为 newkey。 
- 说明：
  - 当 key 和 newkey 相同，或者 key 不存在时，返回一个错误。
  - 当 newkey 已经存在时， RENAME 命令将覆盖旧值。改名成功时提示 OK ，失败时候返回一个错误。

#### 3.2.5 `move` 移动键所在库

- 格式：`MOVE key db`
- 功能：将当前数据库的 key 移动到给定的数据库 db 当中。
- 说明：如果当前数据库(源数据库)和给定数据库(目标数据库)有相同名字的给定 key ，或者 key 不存在于当前数据库，那么 MOVE 没有任何效果。移动成功返回 1 ，失败则返回 0 。

#### 3.2.6 `type` 检查键所对应值的类型

- 格式：`TYPE key`
- 功能：返回 key 所储存的**值的类型**。
- 说明：返回值有以下六种
  - none (key 不存在) 
  - string (字符串) 
  - list (列表) 
  - set (集合) 
  - zset (有序集) 
  - hash (哈希表)

#### 3.2.7 `expire` 与 `pexpire` 设置key生存时间

- 格式：`EXPIRE key seconds`
- 功能：为给定 key 设置生存时间。当 key 过期时(生存时间为 0)，它会被自动删除。
- 注意：
  - `expire `的时间单位为**秒**，`pexpire` 的时间单位为**毫秒**。
  - 在 Redis 中，带有生存时间的 key被称为“易失的”(volatile)。 
- 说明：生存时间设置成功返回 1。若 key 不存在时返回 0 。rename 操作不会改变 key的生存时间。

#### 3.2.8 `ttl` 与 `pttl` 返回 key 的生存时间

- 格式：`TTL key`
- 功能：TTL, time to live，**返回给定 key 的剩余生存时间。**
- 说明：其返回值存在三种可能：
  - 当 key 不存在时，返回 -2 。 
  - 当 key 存在但没有设置剩余生存时间时，返回 -1 。 
  - 否则，返回 key 的剩余生存时间。ttl 命令返回的时间单位为秒，而 pttl 命令返回的时间单位为毫秒。

#### 3.2.9 `persist` 持久化

+ 格式：`PERSIST key`
+ 功能：**去除给定 key 的生存时间**，将这个 key 从“易失的”转换成“持久的”。 
+ 说明：当生存时间移除成功时，返回 1；若 key 不存在或 key 没有设置生存时间，则返回 0。

#### 3.2.10 `randomkey` 返回随机key

- 格式：`RANDOMKEY`
- 功能：从当前数据库中随机返回(不删除)一个 key。 
- 说明：当数据库不为空时，返回一个 key。当数据库为空时，返回 nil。

`3.2.11 scan`

+ 格式：
  `SCAN cursor [MATCH pattern] [COUNT count] [TYPE type]`
  scan 开始游标 [match 匹配模式]  [count 返回个数] [type 返回value类型]

+ 功能：用于**迭代数据库中的数据库键**。
  其各个选项的意义为：

  - cursor：本次迭代**开始**的游标。
  - pattern ：本次迭代要**匹配**的 key 的**模式**。
  - count ：本次迭代要从数据集里返回**多少元素**，默认值为 10 。 
  - type：本次迭代**要返回的** value 的**类型**，默认为所有类型。

  `SCAN` 命令是一个基于游标 cursor 的迭代器：
  `SCAN` 命令每次被调用之后，都会向用户返回返回一个包含两个元素的数组， 
  第一个元素是用于进行下一次迭代的新游标,
  第二个元素则是一个数组， 这个数组中包含了所有被迭代的元素。用户在下次迭代时需要使用这个新游标作为 `SCAN` 命令的游标参数，以此来延续之前的迭代过程。
  当`SCAN` 命令的游标参数被设置为 0 时，服务器将开始一次新的迭代。如果新游标返回 0 表示迭代已结束。

  + 说明：使用间断的、负数、超出范围或者其他非正常的游标来执行增量式迭代不会造成服务器崩溃。
    当**数据量很大时**，**`count` 的数量的指定可能会不起作用**，Redis 会自动调整每次的遍历数目。由于 scan 命令每次执行都只会返回少量元素，所以该命令可以用于生产环境，而不会出现像 `KEYS` 命令带来的服务器阻塞问题。
    增量式迭代命令所使用的算法只保证在**数据集的大小有界的情况下迭代才会停止**，换句话说，如果被迭代数据集的大小不断地增长的话，增量式迭代命令可能永远也无法完成一次完整迭代。即当一个数据集不断地变大时，想要访问这个数据集中的所有元素就需要做越来越多的工作， **能否结束一个迭代取决于用户执行迭代的速度是否比数据集增长的速度更快**。

+ 相关命令：另外还有 3 个 scan 命令用于对三种类型的 value 进行遍历。

  - `hscan`：属于 Hash 型 Value 操作命令集合，用于遍历当前 db 中指定 Hash 表的所有 field-value 对。即数据库键值对中值是hash型的时候遍历这个hash型的值
  - `sscan`：属于 Set 型 Value 操作命令集合，用于遍历当前 db 中指定 set 集合的所有元素
  - `zscan`：属于 ZSet 型 Value 操作命令集合，用于遍历当前 db 中指定有序集合的所有元素（数值与元素值）

### 3.3 String 型 `Value` 操作命令

Redis 存储数据的 Value 可以是一个 String 类型数据。String 类型的 Value 是 Redis 中最基本，最常见的类型。String 类型的 Value 中可以存放任意数据，包括数值型，甚至是二进制的图片、音频、视频、序列化对象等。一个 String 类型的 Value 最大是 512M 大小。

#### 3.3.1 `set`

- 格式：`SET key value [EX seconds | PX milliseconds] [NX|XX]`
- 功能：`SET `除了可以**直接将 key 的值设为 value **外，还可以指定一些参数。
  - `EX seconds`：为当前 key 设置**过期时间**，单位**秒**。等价于` SETEX `命令。
  - `PX milliseconds`：为当前 key 设置**过期时间**，单位**毫秒**。等价于 `PSETEX` 命令。
  - `NX`：指定的 **key 不存在才会设置成功**，**用于添加**指定的 key。等价于` SETNX` 命令。
  - `XX`：指定的**key 必须存在才会设置成功**，**用于更新**指定 key 的 value。 
- 说明：如果 value 字符串中带有空格，则该字符串需要使用**双引号或单引号**引起来，否则会认为 set 命令的参数数量不正确，报错。

#### 3.3.2 `setex` 与 `psetex`设置值和生存时间

- 格式：`SETEX/PSETEX key seconds value`
- eg: `setex name 3 zhangsan`
- 功能：`set expire`，**其不仅为 key 指定了 value，还为其设置了生存时间**。`setex` 的单位为**秒**，`psetex` 的单位为**毫秒**。 
- 说明：如果 **key 已经存在**， 则**覆写旧值**。该命令类似于以下两个命令，不同之处是，SETEX 是一个原子性操作，关联值和设置生存时间两个动作会在同一时间内完成，该命令在 Redis 用作缓存时，非常实用。
  `SET key value`
  `EXPIRE key seconds # 设置生存时间`

#### 3.3.3 `setnx` 不存在时

- 格式：`SETNX key value`
- 功能：SET if Not exists，**将 key 的值设为 value ，当且仅当 key 不存在。**
  若给定的 **key已经存在**，则 SETNX **不做任何动作**。成功，返回 1，否则，返回 0。
- 说明：该命令等价于 `set key value nx`

#### 3.3.4 `getset`设置值的同时返回旧值

- 格式：`GETSET key value`
- 功能：**将给定 key 的值设为 value** ，并**返回 **key 的**旧值**。
-  说明：当 key 存在但不是字符串类型时，返回一个错误；当 key 不存在时，返回 nil 。

#### 3.3.5 `mset `与 `msetnx`多个键值对

- 格式：`MSET/MSETNX key value [key value ...]`

- 功能：同时设置一个或多个 key-value 对。

- 说明：
  如果**某个给定 key 已经存在**，

  - **`MSET` **会用新值***覆盖*原来的旧值**，
  - **`MSETNX`** 命令：它只会在所有给定**key *都不存在*的情况下进行设置操作**。

  `MSET/MSETNX` 是一个原子性(atomic)操作，**所有给定 key 都会在同一时间内被设置**，某些给定 key 被更新而另一些给定 key 没有改变的情况不可能发生。该命令永不失败。

#### 3.3.6 `mget`获取多个

- 格式：`MGET key [key ...]`
- 功能：**返回所有(一个或多个)给定 key 的值。**
- 说明：如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 。因此，该命令永不失败。

#### 3.3.7 `append`追加到值的末尾

- 格式：`APPEND key value`
- 功能：如果 key 已经存在并且是一个字符串， APPEND 命令将 value **追加到** key 原来的**值的末尾**。如果 key 不存在， APPEND 就简单地将给定 key 设为 value ，就像执行 SET key value 一样。
- 说明：追加 value 之后， key 中字符串的长度。

#### 3.3.8 `incr` 与 `decr` value数字自增自减1

- 格式：`INCR key 或 DECR key`
- 功能：
  **increment，自动递增。将 key 中存储的数字值增一。
  decrement，自动递减。将 key 中存储的数字值减一。**
- 说明：
  如果**key 不存在**，那么 key 的值会先被**初始化为 0**，然后**再执行增一/减一**操作。
  如果**值不能表示为数字**，那么**返回一个错误**提示。如果执行正确，则返回增一/减一后的值。

#### 3.3.9 `incrby` 与 `decrby`value数字整数自增自减n

- 格式：`INCRBY key increment 或 DECRBY key decrement`
- eg: `incrby age 3`：age的值一次加三
- 功能：**将 key 中存储的数字值增加/减少指定的数值，这个数值只能是整数，可以是负数，但不能是小数**。
- 说明：如果 key 不存在，那么 key 的值会先被初始化为 0，然后再执行增/减操作。如果值不能表示为数字，那么返回一个错误提示。如果执行正确，则返回增/减后的值。

#### 3.3.10` incrbyfloat`数字浮点数自增自减n

- 格式：`INCRBYFLOAT key increment`
- 功能：**为 key 中所储存的值加上浮点数增量 increment 。 **
- 说明：与之前的说明相同。没有 decrbyfloat 命令，但 **increment 为负数**可以**实现减**操作效果。

#### 3.3.11 `strlen` 返回value的长度

- 格式：`STRLEN key`
- 功能：返回 key 所储存的字符串值的长度。
- 说明：当 key 储存的不是字符串值时，返回一个错误；当 key 不存在时，返回 0 。

#### 3.3.12 `getrange`截取字符串

- 格式：`GETRANGE key start end`
  <img src=".\MDImg\三\3.3.13-1eg.png" alt="img" style="zoom:67%;" />
- 功能：返回 key 中字符串值的子字符串，字符串的**截取**范围由 start 和 end 两个偏移量决定，**包括 start 和 end 在内。**
- 说明：**end 必须要比 start 大**。支持**负数偏移量，表示从字符串最后开始计数**，-1 表示最后一个字符，-2 表示倒数第二个，以此类推。

#### 3.3.13 `setrange`从值的索引处开始替换值

- 格式：`SETRANGE key offset value`
- 功能：用 value 参数替换给定 key 所储存的字符串值 str，从偏移量 offset 开始。
- 说明：当 offset 值大于 str 长度时，中间使用零字节`\x00` 填充，即 0000 0000 字节填充；对于不存在的 key 当作空串处理。

#### 3.3.14 位操作命令

名称中包含 BIT 的命令，都是对二进制位的操作命令，例如，setbit、getbit、bitcount、

bittop、bitfield，这些命令不常用。

#### 3.3.15 典型应用场景

Value 为 String 类型的应用场景很多，这里仅举这种典型应用场景的例子： 

##### （1） 数据缓存

Redis **作为数据缓存层**，MySQL 作为数据存储层。应用服务器首先从 Redis 中获取数据，**如果缓存层中没有，则从 MySQL 中获取后先存入缓存层再返回给应用服务器。**

##### （2） 计数器

在 Redis 中写入一个 value 为数值型的 key 作为平台计数器、视频播放计数器等。每个有效客户端访问一次，或视频每播放一次，都是直接修改 Redis 中的计数器，然后再以异步方式持久化到其它数据源中，例如持久化到 MySQL。 

##### （3） 共享 Session

<img src=".\MDImg\三\3.3.15-1eg.png" alt="img" style="zoom:67%;" />

对于一个分布式应用系统，如果将类似用户登录信息这样的 Session 数据保存在提供登录服务的服务器中，那么如果用户再次提交像收藏、支付等请求时可能会出现问题：在提供收藏、支付等服务的服务器中并没有该用户的 Session 数据，从而导致该用户需要重新登录。对于用户来说，这是不能接受的。
此时，可以将系统中所有用户的 Session 数据全部保存到 Redis 中，用户在提交新的请求后，系统先从 Redis 中查找相应的 Session 数据，如果存在，则再进行相关操作，否则跳转到登录页面。这样就不会引发“重新登录”问题。

##### （4） 限速器

- 现在很多平台为了防止 DoS（Denial of Service，拒绝服务）攻击，**一般都会限制一个 IP不能在一秒内访问超过 n 次**。而 **Redis 可以可以结合 key 的过期时间与 incr 命令来完成限速功能，充当限速器。**
- 注意，其**无法防止 DDoS**（Distributed Denial of Service，分布式拒绝服务）**攻击。**

```java
// 客户端每提交一次请求，都会执行下面的代码
// 等价于 set 192.168.192.55 1 ex 60 nx
// 指定新 ip 作为 key 的缓存过期时间为 60 秒
Boolean isExists = redis.set(ip, 1, “EX 60”, “NX”);
//set失败会返回null
if(isExists != null || redis.incr(ip) <= 5) {
 // 通过
} else {
    // 限流
}
```

### 3.4 Hash 型 Value 操作命令

Redis 存储数据的 Value 可以是一个 Hash 类型。Hash 类型也称为 Hash 表、字典等。
Hash 表就是一个映射表 Map，也是由键-值对构成，为了与整体的 key 进行区分，这里的键称为 field，值称为 value。注意，Redis 的 Hash 表中的 field-value 对均为 String 类型。

#### 3.4.1 `hset` 

- 格式：`HSET key field value`
- 功能：将哈希表 **key 中的域 field 的值设为 value**
- 说明：如果 key 不存在，一个新的哈希表被创建并进行 HSET 操作。**如果域 **field **已经存在**于哈希表中，**旧值将被覆盖**。如果 field 是哈希表中的一个新建域，并且值设置成功，返回 1 。如果哈希表中域 field 已经存在且旧值已被新值覆盖，返回 0 。

#### 3.4.2 `hget` 

- 格式：`HGET key field`
- 功能：**返回**哈希表 **key 中给定域 field 的值。**
- 说明：当给定域不存在或是给定 key 不存在时，返回 nil 。

#### 3.4.3 `hmset`设置多个

- 格式：`HMSET key field value [field value ...]`
- 功能：**同时将多个 field-value (域-值)对设置到哈希表 key 中。**
-  说明：此命令会覆盖哈希表中已存在的域。如果 key 不存在，一个空哈希表被创建并执行 HMSET 操作。如果命令执行成功，返回 OK 。当 key 不是哈希表(hash)类型时，返回一个错误。

#### 3.4.4 `hmget`返回多个

- 格式：`HMGET key field [field ...]`
- 功能：**按照给出顺序返回哈希表 key 中一个或多个域的值。**
- 说明：如果给定的域不存在于哈希表，那么返回一个 nil 值。因为不存在的 key 被当作一个空哈希表来处理，所以对一个不存在的 key 进行 HMGET 操作将返回一个只带有 nil 值的表。

#### 3.4.5 `hgetall`返回所有(不建议)

- 格式：`HGETALL key`
- 功能：**返回哈希表 key 中所有的域和值。**
- 说明：在返回值里，紧跟每个域名(field name)之后是域的值(value)，所以返回值的长度是哈希表大小的两倍。若 key 不存在，返回空列表。若 key 中包含大量元素，则该命令可能会阻塞 Redis 服务。所以生产环境中**一般不使用该命令**，而使用 hscan 命令代替。

#### 3.4.6 `hsetnx`不存在时

- 格式：`HSETNX key field value`
- 功能：将哈希表**key 中的域 field 的值设置为 value ，当且仅当域 field 不存在。**
- 说明：若域 field 已经存在，该操作无效。如果 key 不存在，一个新哈希表被创建并执行 HSETNX 命令。

#### 3.4.7 `hdel` 删除多个

- 格式：`HDEL key field [field ...]`
- 功能：**删除哈希**表 key 中的一个或多个指定域，不存在的域将被忽略。
- 说明：返回被成功移除的域的数量，不包括被忽略的域。

#### 3.4.8 `hexits`是否存在

- 格式：`HEXISTS key field`
- 功能：**查看哈希表 key 中给定域 field 是否存在。**
- 说明：如果哈希表含有给定域，返回 1 。如果不含有给定域，或 key 不存在，返回 0 。

#### 3.4.9 `hincrby` 与 `hincrbyfloat`自增整数(浮点型)量

- 格式：`HINCRBY key field increment`
- 功能：**为哈希表 key 中的域 field 的值加上增量 increment 。*hincrby 命令只能增加整数值*，而 *hincrbyfloat 可以增加小数值*。**
- 说明：增量也可以为负数，相当于对给定域进行减法操作。如果 key 不存在，一个新的哈希表被创建并执行 HINCRBY 命令。如果域 field 不存在，那么在执行命令前，域的值被初始化为 0。对一个储存字符串值的域 field 执行 HINCRBY 命令将造成一个错误。

#### 3.4.10 `hkeys` 与 `hvals` 返回所有

- 格式：`HKEYS key` 或 `HVALS key`
- 功能：返回哈希表 key 中的所有域/值。 
- 说明：当 key 不存在时，返回一个空表。

#### 3.4.11 `hlen`获取key中field的数量

- 格式：`HLEN key`
- 功能：返回哈希表 key 中域的数量。
- 说明：当 key 不存在时，返回 0 。

#### 3.4.12 `hstrlen`获取关联值的长度

- 格式：`HSTRLEN key field`
- 功能：返回哈希表 key 中， 与给定域 field 相关联的值的字符串长度（string length）。
- 说明：如果给定的键或者域不存在， 那么命令返回 0 。

#### 3.4.13 应用场景

Hash 型 Value 非常**适合存储对象数据**。key 为对象名称，value 为描述对象属性的 Map，对对象属性的修改在 Redis 中就可直接完成。其不像 String 型 Value 存储对象，那个对象是序列化过的，例如序列化为 JSON 串，对对象属性值的修改需要先反序列化为对象后再修改，修改后再序列化为 JSON 串后写入到 Redis

### 3.5 List 型 Value 操作命令

Redis 存储数据的 Value 可以是一个 String 列表类型数据。即该列表中的每个元素均为String 类型数据。列表中的数据会**按照插入顺序进行排序**。不过，该列表的底层实际是一个**无头节点的双向链表**，所以对列表表头与表尾的操作性能较高，但**对中间元素的插入与删除**的操作**的性能相对较差**。

#### 3.5.1 `lpush/rpush`

- 格式：`LPUSH key value [value ...]`
  或 `RPUSH key value [value ...]`

- 功能：将**一个或多个值 **value **插入**到列表 key 的**表头/表尾**（表头在左表尾在右）

- 说明：
  如果有多个 value 值，

  - 对于 lpush 来说，各个 value 会按从左到右的顺序依次插入到表头；
  - 对于 rpush 来说，各个 value 会按从左到右的顺序依次插入到表尾。

  如果 key不存在，一个空列表会被创建并执行操作。当 key 存在但不是列表类型时，返回一个错误。执行成功时返回列表的长度。

#### 3.5.2 `llen`链表长度

- 格式：`LLEN key`
- 功能：返回**列表 key 的长度。**
- 说明：如果 key 不存在，则 key 被解释为一个空列表，返回 0 。如果 key 不是列表类型，返回一个错误。

#### 3.5.3` lindex`

- 格式：`LINDEX key index`
- 功能：**返回**列表 key 中，**下标为 index 的元素**。列表从 0 开始计数。
- 说明：如果 index 参数的值不在列表的区间范围内(out of range)，返回 nil 。

#### 3.5.4 `lset`

- 格式：`LSET key index value`
- 功能：将列表 key **下标为 index 的元素**的值**设置为 value** 。 
- 说明：当 index 参数超出范围，或对一个空列表(key 不存在）进行 LSET 时，返回一个错误。

#### 3.5.5 `lrange`

- 格式：`LRANGE key start stop`
- 功能：**返回**列表 key 中**指定区间[start, stop]内的元素**，即包含两个端点。 
- 说明：List 的下标从 0 开始，即以 0 表示列表的第一个元素，以 1 表示列表的第二个元素，以此类推。也可以使用负数下标，以 **-1 表示列表的最后一个元素**， -2 表示列表的倒数第二个元素，以此类推。超出范围的下标值不会引起错误。如果 start 下标比列表的最大下标 还要大，那么 LRANGE 返回一个空列表。如果 stop 下标比最大下标还要大，Redis 将 stop 的值设置为最大下标。

#### 3.5.6 `lpushx` 与 `rpushx`存在时

- 格式：`LPUSHX key value 或 RPUSHX key value`
- 功能：将值 value **插入**到列表 key 的**表头/表尾**，当且仅当 key 存在并且是一个列表。
- 说明：当 **key 不存在时，命令什么也不做**。若执行成功，则输出表的长度。

#### 3.5.7 `linsert`之前/后插入数据

- 格式：`LINSERT key BEFORE|AFTER pivot value`
- 功能：将值 value 插入到列表 key 当中，位于元素 pivot 之前或之后。
- 说明：当 pivot 元素不存在于列表中时，不执行任何操作，返回-1；当 key 不存在时，key 被视为空列表，不执行任何操作，返回 0；如果 key 不是列表类型，返回一个错误；如果命令执行成功，返回插入操作完成之后，列表的长度。

#### 3.5.8 `lpop / rpop`左/右移除n个元素

- 格式：`LPOP key [count] `
  `或 RPOP key [count]`
- 功能：**从列表 key 的表头/表尾移除 count 个元素**，并返回移除的元素。count 默认值 1 
- 说明：当 key 不存在时，返回 nil

#### 3.5.9 `blpop / brpop`阻塞式

- 格式：`BLPOP key [key ...] timeout 或 BRPOP key [key ...] timeout`
- 功能：BLPOP/BRPOP 是列表的阻塞式(blocking)弹出命令。它们是 LPOP/RPOP 命令的阻塞版本，当给定列表内没有任何元素可供弹出的时候，连接将被 BLPOP/BRPOP 命令阻塞，直到等待 timeout 超时或发现可弹出元素为止。**当给定多个 key 参数时，按参数 key的先后顺序依次检查各个列表，弹出第一个非空列表的头元素。**timeout 为阻塞时长，单位为秒，其值若为 0，则表示只要没有可弹出元素，则一直阻塞。
- 说明：假如在指定时间内没有任何元素被弹出，则返回一个 nil 和等待时长。反之，返回一个含有两个元素的列表，第一个元素是被弹出元素所属的 key ，第二个元素是被弹出元素的值。

#### 3.5.10` rpoplpush`

- 格式：`RPOPLPUSH source destination`
- 功能：命令 RPOPLPUSH 在一个原子时间内，执行以下两个动作：
  将列表 source 中的最后一个元素(尾元素)弹出，并返回给客户端。
  将 source 弹出的元素插入到列表 destination ，作为 destination 列表的的头元素。
  即**交换位置，将source最右边的元素弹出到destination最左边的元素**
  如果 source 不存在，值 nil 被返回，并且不执行其他动作。如果 source 和 destination相同，则列表中的表尾元素被移动到表头，并返回该元素，可以把这种特殊情况视作列表的旋转(rotation)操作。
-  <img src=".\MDImg\三\3.5.10-1eg.png" alt="img" style="zoom:100%;" />

#### 3.5.11 `brpoplpush`阻塞

- 格式：`BRPOPLPUSH source destination timeout`
- 功能：BRPOPLPUSH 是 RPOPLPUSH 的阻塞版本，当给定列表 source 不为空时，BRPOPLPUSH 的表现和 RPOPLPUSH 一样。当列表 source 为空时， BRPOPLPUSH 命令将阻塞连接，直到等待超时，或有另一个客户端对 source 执行 LPUSH 或 RPUSH 命令为止。timeout 为阻塞时长，单位为秒，其值若为 0，则表示只要没有可弹出元素，则一直阻塞。
- 说明：假如在指定时间内没有任何元素被弹出，则返回一个 nil 和等待时长。反之，返回一个含有两个元素的列表，第一个元素是被弹出元素的值，第二个元素是等待时长。

#### 3.5.12 `lrem`

- 格式：`LREM key count value`
- 功能：根据参数 count 的值，**移除列表中与参数 value 相等的元素**。count 的值可以是以下几种：
  - count **> 0** : 从**表头开始向表尾**搜索，移除与 value 相等的元素，数量为 count 。 
  - count **< 0** : 从**表尾开始向表头**搜索，移除与 value 相等的元素，数量为 count 的绝对值。
  - count **= 0 **: **移除表中所有**与 value 相等的值。
- 说明：返回被移除元素的数量。当 key 不存在时， LREM 命令返回 0 ，因为不存在的 key 被视作空表(empty list)。

#### 3.5.13 `ltrim`截取

- 格式：`LTRIM key start stop`
- 功能：对一个列表进行修剪(trim)，就是说，让列表**只保留指定区间内的元素**，不在指定区间之内的元素都将被删除。
- 说明：下标(index)参数 start 和 stop 都以 0 为底，也就是说，以 0 表示列表的第一个元素，以 1 表示列表的第二个元素，以此类推。也可以使用负数下标，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。当 key 不是列表类型时，返回一个错误。如果 start 下标比列表的最大下标 end ( LLEN list 减去 1 )还要大，或者 start > stop ， LTRIM 返回一个空列表，因为 LTRIM 已经将整个列表清空。如果 stop 下标比 end 下标还要大，Redis 将 stop 的值设置为 end 。

#### 3.5.14 应用场景

Value 为 List 类型的应用场景很多，主要是通过构建不同的数据结构来实现相应的业务功能。这里仅对这些数据结构的实现方式进行总结，不举具体的例子。

##### （1） 栈

通过** lpush + lpop **可以实现栈数据结构效果：先进后出。通过 lpush 从列表左侧插入数据，通过 lpop 从列表左侧取出数据。当然，通过** rpush + rpop** 也可以实现相同效果，只不过操作的是列表右侧。

##### （2） 队列

通过**lpush + rpop**可以实现队列数据结构效果：先进先出。通过 lpush 从列表左侧插入数据，通过 rpop 从列表右侧取出数据。当然，通过**rpush + lpop **也可以实现相同效果，只不过操作的方向正好相反。

##### （3） 阻塞式消息队列

通过**lpush + brpop** 可以实现阻塞式消息队列效果。作为消息生产者的客户端使用 lpush从列表左侧插入数据，作为消息消费者的多个客户端使用 brpop 阻塞式“抢占”列表尾部数据进行消费，保证了消费的负载均衡与高可用性。**brpop 的 timeout 设置为 0，表示只要没有数据可弹出，就永久阻塞**。

##### （4） 动态有限集合

通过 lpush + ltrim 可以实现有限集合。通过 lpush 从列表左侧向列表中添加数据，通过ltrim 保持集合的动态有限性。像企业的末位淘汰、学校的重点班等动态管理，都可通过这种动态有限集合来实现。当然，通过 rpush + ltrim 也可以实现相同效果，只不过操作的方向正好相反。

### 3.6 Set 型 Value 操作命令

Redis 存储数据的 Value 可以是一个 Set 集合，且集合中的每一个元素均 String 类型。Set与 List 非常相似，但不同之处是**Set 中的元素具有无序性与不可重复性**，而 List 则具有有序性与可重复性；**Set 底层是Map，而list底层是链表**

Redis 中的 Set 集合与 Java 中的 Set 集合的实现相似，其底层都是 value 为 null 的 hash表。也正因为此，才会引发无序性与不可重复性。

#### 3.6.1 `sadd`

-  格式：`SADD key member [member ...]`
-  功能：将**一个或多个** member 元素**加入**到集合 key 当中，**已经存在**于集合的 member 元素**将被忽略**。
-  说明：假如 key 不存在，则创建一个只包含 member 元素作成员的集合。当 key 不是集合类型时，返回一个错误。

#### 3.6.2 `smembers`

-  格式：`SMEMBERS key`
-  功能：**返回集合 key 中的所有成员。**
-  说明：不存在的 key 被视为空集合。若 key 中包含大量元素，则该命令可能会阻塞 Redis服务。所以生产环境中一般不使用该命令，而使用 sscan 命令代替。

#### 3.6.3 `scard`

- 格式：`SCARD key`
- 功能：返回 **Set 集合的长度**
- 说明：当 key 不存在时，返回 0 。

#### 3.6.`4 sismember`

- 格式：`SISMEMBER key member`
- 功能：**判断 member 元素是否是集合 key 的成员。**
- 说明：如果 member 元素是集合的成员，返回 1 。如果 member 元素不是集合的成员，或 key 不存在，返回 0 。

#### 3.6.5 `smove`

- 格式：`SMOVE source destination member`
- 功能：将 member 元素**从 source 集合移动到 destination 集合。**
- 说明：如果 source 集合不存在或不包含指定的 member 元素，则 SMOVE 命令不执行任何操作，仅返回 0 。否则， member 元素从 source 集合中被移除，并添加到destination 集合中去，返回 1。当 destination 集合已经包含 member 元素时， SMOVE命令只是简单地将 source 集合中的 member 元素删除。当 source 或 destination 不是集合类型时，返回一个错误。

#### 3.6.6` srem`

- 格式：`SREM key member [member ...]`
- 功能：**移除集合 key 中的一个或多个 member 元素**，不存在的 member 元素会被忽略，且返回成功移除的元素个数。
- 说明：当 key 不是集合类型，返回一个错误。

#### 3.6.7 `srandmember`

- 格式：`SRANDMEMBER key [count]`
- 功能：返回**集合中的 count 个随机元素**。count 默认值为 1。 
- 说明：**若 count 为<mark>正数</mark>，且小于集合长度，那么返回一个包含 count 个元素的数组，数组中的元素<mark>各不相同</mark>**。如果 count 大于等于集合长度，那么返回整个集合。**如果count 为<mark>负数</mark>，那么返回一个包含 count 绝对值个元素的数组，但数组中的元素<mark>可能会出现重复</mark>**。

#### 3.6.8 `spop`

- 格式：`SPOP key [count]`
- 功能：**移除并返回集合中的 count 个随机元素**。count **必须为正数**，且认值为 1。
- 说明：如果 count 大于等于集合长度，那么移除并返回整个集合。

#### 3.6.9 `sdiff` / `sdiffstore` 差集

- 格式：`SDIFF key [key ...] 或 SDIFFSTORE destination key [key ...]`
- 功能：返回第一个集合与其它集合之间的**差集**。差集，difference。 (差集指第一个集合减去其他集合)
- 不同：**sdiff 临时用，sdiffstore 可以存起来**
- 说明：这两个命令的不同之处在于，**sdiffstore 不仅能够显示差集，还能将差集存储到指定的集合 destination 中**。如果 destination 集合已经存在，则将其覆盖。不存在的 key 被视为空集。
-  <img src=".\MDImg\三\3.6.11-1eg.png" alt="img" style="zoom:100%;" />

#### 3.6.10 `sinter` / `sinterstore` 交集

- 格式：`SINTER key [key ...] 或 SINTERSTORE destination key [key ...]`
- 不同：**sinter 临时用，sinterstore 存起来**
- 功能：返回多个集合间的交集。**交集**，intersection。 
- 说明：这两个命令的不同之处在于，**sinterstore 不仅能够显示交集，还能将交集存储到指定的集合 destination 中**。如果 destination 集合已经存在，则将其覆盖。不存在的 key 被视为空集。

#### 3.6.11 `sunion` / `sunionstore` 并集

- 格式：SUNION key [key ...] 或 SUNIONSTORE destination key [key ...]
- 不同：**sunion 临时用，sunionstore 存起来**
- 功能：返回多个集合间的并集。**并集**，union。 
- 说明：这两个命令的不同之处在于，**sunionstore 不仅能够显示并集，还能将并集存储到指定的集合 destination 中**。如果 destination 集合已经存在，则将其覆盖。不存在的 key 被视为空集。

#### 3.6.12 应用场景

Value 为 Set 类型的应用场景很多，这里对这些场景仅进行总结。

##### （1） 动态黑白名单<img src=".\MDImg\三\3.6.12-1eg.png" alt="img" style="zoom:100%;" />

例如某服务器中要设置用于访问控制的黑名单。如果直接将黑名单写入服务器的配置文件，那么存在的问题是，无法动态修改黑名单。此时可以将黑名单直接写入 Redis，只要有客户端来访问服务器，服务器在获取到客户端 IP后先从 Redis的黑名单中查看是否存在该 IP，如果存在，则拒绝访问，否则访问通过。

##### （2） 有限随机数

有限随机数是指返回的随机数是基于某一集合范围内的随机数，例如抽奖、随机选人。通过 spop 或 srandmember 可以实现从指定集合中随机选出元素。

##### （3） 用户画像

社交平台、电商平台等各种需要用户注册登录的平台，会根据用户提供的资料与用户使用习惯，为每个用户进行画像，即为每个用户定义很多可以反映该用户特征的标签，这些标签就可以使用 sadd 添加到该用户对应的集合中。这些标签具有无序、不重复特征。同时平台还可以使用 sinter/sinterstore 根据用户画像间的交集进行好友推荐、商品推荐、客户推荐等。

### 3.7  有序 Set 型 Value 命令

Redis 存储数据的 Value 可以是一个有序 Set，这个有序 Set 中的每个元素均 String 类型。有序 Set 与 Set 的不同之处是，有序 Set 中的每一个元素都有一个分值 score，Redis 会根据score 的值对集合进行由小到大的排序。其与 Set 集合要求相同，元素不能重复，但元素的score 可以重复。由于该类型的所有命令均是字母 z 开头，所以该 Set 也称为 ZSet。

#### 3.7.1 `zadd`

- 格式：`ZADD key score member [[score member] [score member] ...]`
- <img src=".\MDImg\三\3.7.1-1eg.png" alt="img" style="zoom:100%;" />
- 功能：将一个或多个 member 元素及其 score 值加入到有序集 key 中的适当位置。
- 说明：score 值可以是整数值或双精度浮点数。如果 key 不存在，则创建一个空的有序集并执行 ZADD 操作。当 key 存在但不是有序集类型时，返回一个错误。如果命令执行成功，则返回被成功添加的新成员的数量，不包括那些被更新的、已经存在的成员。若写入的 member 值已经存在，但 score 值不同，则新的 score 值将覆盖老 score。

#### 3.7.2` zrange`与 `zrevrange`按递增/递减的顺序返回指定区间的成员

- 格式：`ZRANGE key start stop [WITHSCORES] 或 ZREVRANGE key start stop [WITHSCORES]`
- 功能：返回有序集 key 中，指定区间内的成员。**zrange 命令会按 score 值<mark>递增</mark>排序**，**zrevrange命令会按score<mark>递减</mark>排序**。具有相同 score 值的成员按字典序/逆字典序排列。可以通过**使用 WITHSCORES 选项，来让成员和它的 score 值一并返回**。
- 说明：下标参数从 0 开始，即 0 表示有序集第一个成员，以 1 表示有序集第二个成员，以此类推。也可以使用负数下标，**-1 表示最后一个成员**，-2 表示倒数第二个成员，以此类推。超出范围的下标并不会引起错误。例如，当 start 的值比有序集的最大下标还要大，或是 start > stop 时，ZRANGE 命令只是简单地返回一个空列表。再比如 stop 参数的值比有序集的最大下标还要大，那么 Redis 将 stop 当作最大下标来处理。若 key 中指定范围内包含大量元素，则该命令可能会阻塞 Redis 服务。所以生产环境中如果要查询有序集合中的所有元素，一般不使用该命令，而使用 zscan 命令代替。

#### 3.7.3 `zrangebyscore` 与 `zrevrangebyscore`

- 格式：`ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]`
  `ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]`
- eg：`zrangebyscore cities -inf +inf withscores`：从正无穷到负无穷的都显示出来
  <img src=".\MDImg\三\3.7.3-1eg.png" alt="img" style="zoom:100%;" />
- 功能：返回有序集 key 中，**所有 score 值介于 min 和 max 之间(包括等于 min 或max )的成员**。有序集成员按 score 值递增/递减次序排列。具有相同 score 值的成员按字典序/逆字典序排列。
  可选的 `LIMIT`参数指定返回结果的数量及区间(就像 SQL 中的SELECT LIMIT offset, count )，注意当 offset 很大时，定位 offset 的操作可能需要遍历整个有序集，此过程效率可能会较低。**可选的 WITHSCORES 参数**决定结果集是单单返回有序集的成员，还是**将有序集成员及其 score 值一起返回。**
- 说明：min 和 max 的取值是正负无穷大的。**默认情况下**，**区间的取值使用闭区间** (小于等于或大于等于)，也**可以通过给参数前增加左括号“(”**来使用可选的**开区间** (小于或大于)。

#### 3.7.4 `zcard`返回集合长度

- 格式：`ZCARD key`
- 功能：返回**集合的长度**
- 说明：当 key 不存在时，返回 0 。

#### 3.7.5 zcount

- 格式：`ZCOUNT key min max`
- 功能：**返回**有序集 key 中，score 值在 min 和 max **之间**(默认包括 score 值等于 min 或 max )的成员的**数量**。

#### 3.7.6 zscore

- 格式：`ZSCORE key member`
- 功能：返回有序集 key 中，成员 member 的 score 值。
- 说明：如果 member 元素不是有序集 key 的成员，或 key 不存在，返回 nil 。

#### 3.7.7 `zincrby`增加 score 值

- 格式：`ZINCRBY key increment member`
- 功能：为有序集 key 的成员 member 的 **score 值加上增量 increment **。increment 值可以是整数值或双精度浮点数。
- 说明：可以通过传递一个负数值 increment ，让 score 减去相应的值。当 key 不存在，或 member 不是 key 的成员时， ZINCRBY key increment member 等同于 ZADD key increment member 。当 key 不是有序集类型时，返回一个错误。命令执行成功，则返回 member 成员的新 score 值。

#### 3.7.8 `zrank` 与 `zrevrank`返回排名

- 格式：`ZRANK key member 或 ZREVRANK key member`
- 功能：**返回**有序集 key 中成员 member 的**排名**。**zrank 命令会按 score 值递增排序 **，**zrevrank 命令会按 score 递减排序。**
- 说明：score 值最小的成员排名为 0 。如果 member 不是有序集 key 的成员，返回 nil 。

#### 3.7.9 `zrem`移除一个或多个

- 格式：`ZREM key member [member ...]`
- 功能：**移除有序集 key 中的一个或多个成员**，不存在的成员将被忽略。
- 说明：当 key 存在但不是有序集类型时，返回一个错误。执行成功，则返回被成功移除的成员的数量，不包括被忽略的成员。

#### 3.7.10 `zremrangebyrank`移除指定排名区间

- 格式：`ZREMRANGEBYRANK key start stop`
- 功能：**移除**有序集 key 中，**指定排名(rank)区间**内的所有成员。
- 说明：排名区间分别以下标参数 start 和 stop 指出，包含 start 和 stop 在内。**排名区间参数从 0 开始，即 0 表示排名第一的成员， 1 表示排名第二的成员**，以此类推。也可以使用负数表示，-1 表示最后一个成员，-2 表示倒数第二个成员，以此类推。命令执行成功，则返回被移除成员的数量。

#### 3.7.11 `zremrangebyscore`移除指定score区间

- 格式：`ZREMRANGEBYSCORE key min max`
- 功能：**移除**有序集 key 中，所有 **score 值介于 min 和 max 之间**(包括等于 min 或max )的成员。
- 说明：命令执行成功，则返回被移除成员的数量。

#### 3.7.12 `zrangebylex`按字母(字典)序返回成员

- 格式：`ZRANGEBYLEX key min max [LIMIT offset count]`
- 注：**字母序必须用"[" 或 "(" **
- 功能：该命令**仅适用于集合中所有成员都具有相同分值的情况**。当有序集合的所有成员都具有相同的分值时，有序集合的元素会根据成员的字典序（lexicographical ordering）来进行排序。即这个命令返回给定集合中元素值介于 min 和 max 之间的成员。如果有序集合里面的成员带有不同的分值， 那么命令的执行结果与 zrange key 效果相同。 
- 说明：合法的 min 和 max 参数必须包含左小括号“(”或左中括号“[”，其中左小括号“(”表示开区间， 而左中括号“[”则表示闭区间。**min 或 max 也可使用特殊字符“+”和“-”**，分别表示正无穷大与负无穷大。

#### 3.7.13 `zlexcount`按字母序返回数量

- 格式：`ZLEXCOUNT key min max`
- 功能：该命令**仅适用于集合中所有成员都具有相同分值的情况**。该命令返回该集合中元素值本身（而非 score 值）介于 min 和 max 范围内的元素数量。

#### 3.7.14 `zremrangebylex`移除字母范围的成员

- 格式：`ZREMRANGEBYLEX key min max`
- 功能：该命令**仅适用于集合中所有成员都具有相同分值的情况**。该命令会**移除**该集合中**元素值本身介于** min 和 max 范围内的所有**元素**

#### 3.7.15 应用场景

有序 Set 最为典型的应用场景就是**排行榜**，例如音乐、视频平台中根据播放量进行排序的排行榜；电商平台根据用户评价或销售量进行排序的排行榜等。将播放量作为 score，将作品 id 作为 member，将用户评价积分或销售量作为 score，将商家 id 作为 member。使用zincrby 增加排序 score，使用 zrevrange 获取 Top 前几名，使用 zrevrank 查询当前排名，使用zscore 查询当前排序 score 等。

### 3.8 benchmark 测试工具

#### 3.8.1 简介

在Redis安装完毕后会自动安装一个redis-benchmark测试工具，其是一个压力测试工具，用于测试 Redis 的性能。
<img src=".\MDImg\三\3.8.1-1eg.png" alt="img" style="zoom:100%;" />
通过 redis-benchmark –help 命令可以查看到其用法：
<img src=".\MDImg\三\3.8.1-2eg.png" alt="img" style="zoom:100%;" />
其选项 options 非常多，下面通过例子来学习常用的 options 的用法。

#### 3.8.2 测试 1

##### （1） 命令解析

<img src=".\MDImg\三\3.8.2-1eg.png" alt="img" style="zoom:100%;" />
以上命令中选项的意义：

- -h：指定要测试的 Redis 的 IP，若为本机，则可省略
- -p：指定要测试的 Redis 的 port，若为 6379，则可省略
-  -c：指定模拟有客户端的数量，默认值为 50
- -n：指定这些客户端发出的请求的总量，默认值为 100000
- -d：指定测试 get/set 命令时其操作的 value 的数据长度，单位字节，默认值为 3。在测试其它命令时该指定没有用处。

以上命令的意义是，使用 100 个客户端连接该 Redis，这些客户端总共会发起 100000个请求，set/get 的 value 为 8 字节数据。

##### （2） 测试结果分析

该命令会逐个测试所有 Redis 命令，每个命令都会给出一份测试报告，每个测试报告由四部分构成：

A、测试环境报告

首先就是测试环境：
<img src=".\MDImg\三\3.8.2-2eg.png" alt="img" style="zoom:100%;" />

B、 延迟百分比分布

这是按照百分比进行的统计报告：每完成一次剩余测试量的 50%就给出一个统计数据。
<img src=".\MDImg\三\3.8.2-3eg.png" alt="img" style="zoom:100%;" />

C、 延迟的累积分布

这是按照时间间隔统计的报告：基本是每 0.1 毫秒统计一次。
<img src=".\MDImg\三\3.8.2-4eg.png" alt="img" style="zoom:100%;" />

D、总述报告

这是总述性报告。
<img src=".\MDImg\三\3.8.2-5eg.png" alt="img" style="zoom:100%;" />

#### 3.8.3 测试 2

<img src=".\MDImg\三\3.8.3-1eg.png" alt="img" style="zoom:100%;" />
以上命令中选项的意义：

- -t：指定要测试的命令，多个命令使用逗号分隔，不能有空格
- -q：指定**仅给出总述性报告**

### 3.9 简单动态字符串 SDS

#### 3.9.1 SDS 简介

- 无论是 Redis 的 Key 还是 Value，其基础数据类型都是字符串。例如，Hash 型 Value 的field 与 value 的类型、List 型、Set 型、ZSet 型 Value 的元素的类型等都是字符串。虽然 Redis是使用标准 C 语言开发的，但并没有直接使用 C 语言中传统的字符串表示，而是自定义了一种字符串。这种字符串本身的结构比较简单，但功能却非常强大，称为简单动态字符串，Simple Dynamic String，简称 SDS。

- 注意，Redis 中的所有字符串并不都是 SDS，**也会出现 C 字符串。C 字符串只会出现在字符串“字面常量”中，并且该字符串不可能发生变更。**

- 需要注意的是，虽然Redis中使用SDS结构来实现字符串类型和列表类型，但是在底层实现上，Redis仍然使用C字符串来存储数据。因此，Redis中的SDS结构只是一种抽象的数据结构，用于方便地管理字符串和列表等数据类型。

- eg：`redisLog(REDIS_WARNNING, “sdfsfsafsafds”);`如果对redis作二次开发，这个sdfsfsafsafds就属于字面常量C字符串

- > 存的是SDS查的是C字符串

   <img src=".\MDImg\三\3.9.1-1eg.png" alt="img" style="zoom:100%;" />
  
- Redis中使用SDS结构的数据类型主要包括：

  1. 字符串类型（string）：Redis中的字符串类型就是使用SDS结构实现的，可以存储任意长度的字符串。
  2. 列表类型（list）：Redis中的列表类型也是使用SDS结构实现的，每个列表元素都是一个SDS结构。
  3. 集合类型（set）：Redis中的集合类型也是使用SDS结构实现的，每个集合元素都是一个SDS结构。
  4. 有序集合类型（sorted set）：Redis中的有序集合类型也是使用SDS结构实现的，每个有序集合元素都是一个SDS结构。

  而其他的数据类型，如哈希表类型（hash）和位图类型（bitmap）等则不是使用SDS结构实现的。

#### 3.9.2 SDS 结构

SDS 不同于 C 字符串。C 字符串本身是一个以双引号括起来，**以空字符`’\0’`结尾的字符序列**。但 SDS 是一个结构体，定义在 Redis 安装目录下的 src/sds.h 中：
```java
struct sdshdr {
    // 字节数组，用于保存字符串
	char buf[];
	// buf[]中已使用字节数量，称为 SDS 的长度
	int len;
	// buf[]中尚未使用的字节数量
	int free;
}
```

例如执行 `SET country “China”`命令时，键 country 与值”China”都是 SDS 类型的，只不过一个是 SDS 的变量，一个是 SDS 的字面常量。”China”在内存中的结构如下：
<img src=".\MDImg\三\3.9.2-1eg.png" alt="img" style="zoom:100%;" />通过以上结构可以看出，SDS 的 buf 值实际是一个 C 字符串，包含空字符’\0’共占 6 个字节。但 SDS 的 len 是不包含空字符’\0’的。
<img src=".\MDImg\三\3.9.2-2eg.png" alt="img" style="zoom:100%;" />
该结构与前面不同的是，这里有 3 字节未使用空间。

#### 3.9.3 SDS 的优势

C 字符串使用 `Len+1 `长度的字符数组来表示实际长度为 Len 的字符串，字符数组最后以空字符’`\0`’结尾，表示字符串结束。这种结构简单，但不能满足 Redis 对字符串功能性、安全性及高效性等的要求。

---

1. 防止”字符串长度获取”性能瓶颈

   - 对于 **C 字符串，若要获取其长度，则必须要通过遍历**整个字符串才可获取到的。对于超长字符串的遍历，会成为系统的性能瓶颈。

   - 但，由于 SDS 结构体中**直接就存放着字符串的长度**数据，所以对于获取字符串长度需要消耗的系统性能，与字符串本身长度是无关的，不会成为 Redis 的性能瓶颈。

2. 保障二进制安全

   - C 字符串中只能包含符合某种编码格式的字符，例如 ASCII、UTF-8 等，并且除了字符串末尾外，其它位置是不能包含空字符`\0`的，否则该字符串就会被程序误解为提前结束。而在图片、音频、视频、压缩文件、office 文件等二进制数据中以空字符’\0’作为分隔符的情况是很常见的。故而在 C 字符串中是不能保存像图片、音频、视频、压缩文件、office 文件等二进制数据的。
   - 但 SDS 不是以空字符’\0’作为字符串结束标志的，其是通过 len 属性来判断字符串是否结束的。所以，对于程序处理 SDS 中的字符串数据，无需对数据做任何限制、过滤、假设，只需读取即可。数据写入的是什么，读到的就是什么。

3. 减少内存再分配次数

   - SDS 采用了**空间预分配策略**与**惰性空间释放策略**来避免内存再分配问题。
   - 空间预分配策略是指，每次 SDS 进行空间扩展时，程序不但为其分配所需的空间，还会
     为其分配额外的未使用空间，以减少内存再分配次数。而额外分配的未使用空间大小取决于
     空间扩展后 SDS 的 len 属性值。
     -  如果 len 属性值小于 1M，那么分配的未使用空间 free 的大小与 len 属性值相同。
     - 如果 len 属性值大于等于 1M ，那么分配的使用空间 free 的大小固定是 1M。
   - SDS 对于空间释放采用的是惰性空间释放策略。该策略是指，SDS 字符串长度如果缩短，
     那么多出的未使用空间将暂时不释放，而是增加到 free 中。以使后期扩展 SDS 时减少内存
     再分配次数。
   - 如果要释放 SDS 的未使用空间，则可通过 `sdsRemoveFreeSpace()`函数来释放。

4. 兼容 C 函数

   - Redis 中提供了很多的 SDS 的 API，以方便用户对 Redis 进行二次开发。为了能够兼容 C
     函数，SDS 的底层数组 buf[]中的字符串仍以空字符`\0`结尾。
   - 现在要比较的双方，一个是 SDS，一个是 C 字符串，此时可以通过 C 语言函数
     `strcmp(sds_str->buf，c_str)`

#### 3.9.4 常用的 SDS 操作函数

下表列出了一些常用的 SDS 操作函数及其功能描述。
| 函数 | 功能描述 |
| ---- | -------- |
|sdsnew() |使用指定的 C 字符串创建一个 SDS|
|sdsempty() |创建一个不包含任何字符串数据的 SDS|
|sdsdup()| 创建一个指定 SDS 的副本|
|sdsfree()| 释放指定的 SDS|
|sdsclear()| 清空指定 SDS 的字符串内容|
|sdslen() |获取指定 SDS 的已使用空间 len 值|
|sdsavail()| 获取指定 SDS 的未使用空间 free 值|
|sdsMakeRoomFor()| 使指定的 SDS 的 free 空间增加指定的大小|
|sdsRemoveFreeSpace() |释放指定 SDS 的 free 空间|
|sdscat()| 将指定的 C 字符串拼接到指定 SDS 字符串末尾|
|sdscatsds()| 将指定的 SDS 的字符串拼接到另一个指定 SDS 字符串末尾|
|sdscpy()| 将指定的 C 字符串复制到指定的 SDS 中，覆盖原 SDS 字符串内容|
|sdsgrouzero() |扩展 SDS 字符串到指定长度。这个扩展是使用空字符’\0’填充|
|sdsrange()| 截取指定 SDS 中指定范围内的字符串|
|sdstrim()| 在指定 SDS 中删除所有指定 C 字符串中出现的所有字符|
|sdsemp()| 对比两个给定的 SDS 字符串是否相同|
|sdstolow() |将指定 SDS 字符串中的所有字母变为小写|
|sdstoupper() |将指定 SDS 字符串中的所有字母变为大写|

### 3.10 集合的底层实现原理

Redis 中对于 Set 类型的底层实现，直接采用了 hashTable。但对于 Hash、ZSet、List 集合的底层实现进行了特殊的设计，使其保证了 Redis 的高性能。

#### 3.10.1 两种实现的选择

- 对于Hash与ZSet集合，其底层的实现实际有两种：压缩列表zipList/listPack，与跳跃列表skipList。

- 这两种实现对于用户来说是透明的，但用户写入不同的数据，系统会自动使用不同的实现。

- **只有同时满足以配置文件 redis.conf 中相关集合元素数量阈值与元素大小阈值两个条件，使用的就是压缩列表 zipList，只要有一个条件不满足使用的就是跳跃列表 skipList。**例如，对于ZSet 集合中这两个条件如下：

  - 集合元素个数小于 redis.conf 中 zset-max-ziplist-entries 属性的值，其默认值为 128

  - 每个集合元素大小都小于 redis.conf 中 zset-max-ziplist-value 属性的值，其默认值为 64
    字节

    > `config get zset-*-ziplist-*` 
    > `config get hash-*-ziplist-*`查看当前配置文件中的阈值

#### 3.10.2 zipList

在 Redis 7.0 中，已经将 zipList 全部替换为了 listPack，但为了兼容性，在配置中也保留了 zipList 的相关属性

<img src=".\MDImg\三\3.10.2eg.png" alt="img" style="zoom:100%;" />

1. 什么是 zipList
   zipList，通常称为**压缩列表**，是一个经过**特殊编码**的用于存储**字符串或整数**的双向链表。
   其底层数据结构由三部分构成：head、entries 与 end。这三部分在内存上是连续存放的。

2. head
   head 又由三部分构成(一共十个字节)：

   - zlbytes：占 4 个字节，用于存放 zipList 列表整体数据结构所占的**字节数**，包括 zlbytes
     本身的**长度**。
   - zltail：占 4 个字节，用于存放 zipList 中最后一个 entry 在整个数据结构中的**偏移量**（字
     节）。该数据的存在可以快速定位列表的尾 entry 位置，以方便操作。
   - zllen：占 2 字节，用于存放**列表包含的 entry 个数**。由于其只有 16 位，所以 zipList 最多
     可以含有的 entry 个数为$2^{16}-1=65535$个。

3. entries
   entries 是真正的列表，由**很多的列表元素** entry 构成。由于不同的元素类型、数值的不同，从而导致每个 entry 的长度不同。

   ---
   每个 entry 由三部分构成：

   - prevlength：该部分用于记录上一个 entry 的长度，以实现**逆序遍历**。默认长度为 1 字节，
     只要上一个 entry 的长度<254 字节，prevlength 就占 1 字节，否则其会自动扩展为 3 字节长度。
   - encoding：该部分用于标志后面的 data 的具体类型。如果 data 为整数类型，encoding固定长度为 1 字节。如果 data 为字符串类型，则 encoding 长度可能会是 1 字节、2 字
     节或 5 字节。data 字符串不同的长度，对应着不同的 encoding 长度。
   - data：真正存储的数据。数据类型只能是整数类型或字符串类型。不同的数据占用的字
     节长度不同。

4. end
   end 只包含一部分，称为 zlend。占 1 个字节，值固定为 255，即二进制位为全 1，**表示一个 zipList 列表的结束**。

#### 3.10.3 listPack

- 对于 ziplist，实现复杂，为了逆序遍历，每个 entry 中包含前一个 entry 的长度，这样会导致在 ziplist 中间修改或者插入 entry 时需要进行级联更新。在高并发的写操作场景下会极度降低 Redis 的性能。为了实现更紧凑、更快的解析，更简单的实现，重写实现了 ziplist，并命名为 listPack。
- 在 Redis 7.0 中，已经将 zipList 全部替换为了listPack，但为了兼容性，在配置中也保留了 zipList 的相关属性

<img src=".\MDImg\三\3.10.3eg.png" alt="img" style="zoom:100%;" />

1. 什么是 listPack
   - listPack 也是一个经过特殊编码的用于存储字符串或整数的双向链表。其底层数据结构也由三部分构成：head、entries 与 end，且这三部分在内存上也是连续存放的。
   - listPack与zipList的重大区别在head与每个entry的结构上，表示列表结束的end与zipList
     的 zlend 是相同的，占一个字节，且 8 位全为 1。
2. head
   head 由两部分构成：
   - totalBytes：占 4 个字节，用于存放 listPack 列表整体数据结构所占的字节数，包括
     totalBytes 本身的长度。
   - elemNum：占 2 字节，用于存放列表包含的 entry 个数。其意义与 zipList 中 zllen 的相同。
     与 zipList 的 head 相比，没有了记录最后一个 entry 偏移量的 zltail。
3. entries
   - entries 也是 listPack 中真正的列表，由很多的列表元素 entry 构成。由于不同的元素类
     型、数值的不同，从而导致每个 entry 的长度不同。但与 zipList 的 entry 结构相比，listPack的 entry 结构发生了较大变化。
   - 其中最大的变化就是没有了记录前一个 entry 长度的 prevlength，而增加了记录当前
     entry 长度的 element-total-len。而这个改变仍然可以实现逆序遍历，但却避免了由于在列表中间修改或插入 entry 时引发的级联更新。
   - 每个 entry 仍由三部分构成：
     - encoding：该部分用于标志后面的 data 的具体类型。如果 data 为整数类型，encoding长度可能会是 1、2、3、4、5 或 9 字节。不同的字节长度，其标识位不同。如果 data为字符串类型，则 encoding 长度可能会是 1、2 或 5 字节。data 字符串不同的长度，对应着不同的 encoding 长度。
     - data：真正存储的数据。数据类型只能是整数类型或字符串类型。不同的数据占用的字节长度不同。
     - element-total-len：该部分用于记录当前 entry 的长度，用于实现逆序遍历。由于其特殊的记录方式，使其本身占有的字节数据可能会是 1、2、3、4 或 5 字节。

#### 3.10.4 skipList

1. 什么是 skipList
   skipList，跳跃列表，简称跳表，是一种**随机化**的数据结构，基于**并联**的链表，实现简单，查找效率较高。简单来说跳表也是链表的一种，只不过它在链表的基础上增加了跳跃功能。也正是这个跳跃功能，使得在查找元素时，能够**提供较高的效率**。

2.  skipList 原理
   假设有一个带头尾结点的有序链表。
   <img src=".\MDImg\三\3.10.4-1eg.png" alt="img" style="zoom:100%;" />

   - 在该链表中，如果要查找某个数据，需要从头开始逐个进行比较，直到找到包含数据的那个节点，或者找到第一个比给定数据大的节点，或者找到最后尾结点，后两种都属于没有找到的情况。同样，当我们要插入新数据的时候，也要经历同样的查找过程，从而确定插入位置。
   - 为了提升查找效率，在**偶数结点上增加一个指**针，让其指向下一个偶数结点。

   <img src=".\MDImg\三\3.10.4-2eg.png" alt="img" style="zoom:100%;" />

   - 这样所有偶数结点就连成了一个新的链表（简称高层链表），当然，高层链表包含的节点个数只是原来链表的一半。此时再想查找某个数据时，先沿着高层链表进行查找。当遇到第一个比待查数据大的节点时，立即从该大节点的前一个节点回到原链表中进行查找。例如，若想插入一个数据 20，则先在（8，19，31，42）的链表中查找，找到第一个比 20 大的节点 31，然后再在高层链表中找到 31 节点的前一个节点 19，然后再在原链表中获取到其下一
     个节点值为 23。比 20 大，则将 20 插入到 19 节点与 23 节点之间。若插入的是 25，比节点
     23 大，则插入到 23 节点与 31 节点之间。
   - 该方式明显可以减少比较次数，提高查找效率。如果链表元素较多，为了进一步提升查找效率，可以将原链表构建为三层链表，或再高层级链表。

   <img src=".\MDImg\三\3.10.4-3eg.png" alt="img" style="zoom:100%;" />

   - 层级越高，查找效率就会越高。

3. 存在的问题

   - 这种对链表分层级的方式从原理上看确实提升了查找效率，但在实际操作时就出现了问题：由于固定序号的元素拥有固定层级，所以列表元素出现**增加或删除**的情况下，会**导致列表整体元素层级大调整**，但这样势必会**大大降低系统性能。**
   - 例如，对于划分两级的链表，可以规定奇数结点为高层级链表，偶数结点为低层级链表。
     对于划分三级的链表，可以按照节点序号与 3 取模结果进行划分。但如果插入了新的节点，
     或删除的原来的某些节点，那么定会按照原来的层级划分规则进行重新层级划分，那么势必
     会大大降低系统性能

4. 算法优化
   为了避免前面的问题，skipList 采用了**随机分配层级**方式。即在确定了总层级后，**每添加一个新的元素时会自动为其随机分配一个层级**。这种随机性就解决了节点序号与层级间的固定关系问题。
   <img src=".\MDImg\三\3.10.4-4eg.png" alt="img" style="zoom:100%;" />

   - 上图演示了列表在生成过程中为每个元素随机分配层级的过程。从这个 skiplist 的创建和插入过程可以看出，每一个节点的层级数都是随机分配的，而且新插入一个节点不会影响到其它节点的层级数。只需要修改插入节点前后的指针，而不需对很多节点都进行调整。这就降低了插入操作的复杂度。
   - skipList 指的就是除了最下面第 1 层链表之外，它会产生若干层稀疏的链表，这些链表里面的指针跳过了一些节点，并且越高层级的链表跳过的节点越多。在查找数据的时先在高
     层级链表中进行查找，然后逐层降低，最终可能会降到第 1 层链表来精确地确定数据位置。
     在这个过程中由于跳过了一些节点，从而加快了查找速度。

#### 3.10.5 quickList

<img src=".\MDImg\三\3.10.5-1eg.png" alt="img" style="zoom:100%;" />

（1） 什么是 quickList

- quickList，快速列表，quickList 本身是一个双向无循环链表，它的每一个节点都是一个zipList。从Redis3.2版本开始，对于List的底层实现，使用quickList替代了zipList 和 linkedList。
- zipList 与 linkedList 都存在有明显不足，而 quickList 则对它们进行了改进：吸取了 zipList 
  和 linkedList 的优点，避开了它们的不足。
- quickList 本质上是 zipList 和 linkedList 的混合体。其将 linkedList 按段切分，每一段使用 zipList 来紧凑存储若干真正的数据元素，多个 zipList 之间使用双向指针串接起来。当然，对于每个 zipList 中最多可存放多大容量的数据元素，在配置文件中通过 list-max-ziplist-size属性可以指定。

为了更深入的理解 quickList 的工作原理，通过对检索、插入、删除等操作的实现分析来加深理解。

---

（2） 检索操作

对于 List 元素的检索，都是以其索引 index 为依据的。quickList 由一个个的 zipList 构成，每个 zipList 的 **zllen 中记录**的就是**当前 zipList 中包含的 entry 的个数**，即包含的真正数据元素的个数。根据要检索元素的 index，从 quickList 的头节点开始，**逐个对 zipList 的 zllen 做 sum求和，直到找到第一个求和后 sum 大于 index 的 zipList，那么要检索的这个元素就在这个zipList 中**。

---

（3） 插入操作

由于 zipList 是有大小限制的，所以在 quickList 中插入一个元素在逻辑上相对就比较复杂一些。假设要插入的元素的大小为 insertBytes，而查找到的插入位置所在的 zipList 当前的大小为 zlBytes，那么具体可分为下面几种情况：

1. 情况一：当 insertBytes + zlBytes <= list-max-ziplist-size(插入的值的大小+当前这个节点的大小<这个ziplist的最大容量) 时，**直接插入**到 zipList 中相应位置即可
2. 情况二：当 insertBytes + zlBytes > list-max-ziplist-size，且插入的位置**位于该 zipList 的首部位置**，此时需要查看该 zipList 的前一个 zipList 的大小 prev_zlBytes。
   - 若 insertBytes + prev_zlBytes<= list-max-ziplist-size 时，直接将元素**插入到前一个zipList 的尾部位置**即可
   - 若 insertBytes + prev_zlBytes> list-max-ziplist-size 时，**直接将元素自己构建为一个新的 zipList，并连入 quickList 中**
3. 情况三：当 insertBytes + zlBytes > list-max-ziplist-size，且插入的位置位于该 zipList 的**尾部位置**，此时需要查看该 zipList 的后一个 zipList 的大小 next_zlBytes。
   - 若 insertBytes + next_zlBytes<= list-max-ziplist-size 时，直接将元素插入到后一个zipList 的头部位置即可
   - 若 insertBytes + next_zlBytes> list-max-ziplist-size 时，直接将元素自己构建为一个新的 zipList，并连入 quickList 中
4. 情况四：当 insertBytes + zlBytes > list-max-ziplist-size，且插入的位置位于该 zipList 的**中间位置**，则**将当前 zipList 分割为两个 zipList 连接入 quickList 中**，然后将元素**插入到分割后的前面 zipList 的尾部位置**

---

（4） 删除操作
对于删除操作，只需要注意一点，在相应的 zipList 中删除元素后，该 zipList 中是否还有元素。如果没有其它元素了，则将该 zipList 删除，将其前后两个 zipList 相连接。

#### 3.10.6 key 与 value 中元素的数量

前面讲述的 Redis 的各种特殊数据结构的设计，不仅极大提升了 Redis 的性能，并且还使得 Redis 可以支持的 key 的数量、集合 value 中可以支持的元素数量可以非常庞大。

- Redis 最多可以处理 $2^{32}$个 key（约 42 亿），并且在实践中经过测试，每个 Redis 实例至少可以处理 2.5 亿个 key。
- 每个 Hash、List、Set、ZSet 集合都可以包含 $2^{32}$
  个元素。

### 3.11 BitMap 操作命令

#### 3.11.1 BitMap 简介

BitMap 是 Redis 2.2.0 版本中引入的一种新的数据类型。该数据类型本质上就是一个仅**包含 0 和 1 的二进制字符串**。而其所有相关命令都是对这个字符串二进制位的操作。用于描述该字符串的属性有三个：key、offset、bitValue。

- key：BitMap 是 Redis 的 key-value 中的一种 Value 的数据类型，所以该 Value 一定有其对
  应的 key。
- offset：每个 BitMap 数据都是一个字符串，字符串中的每个字符都有其对应的索引，该索引从 0 开始计数。该索引就称为每个字符在该 BitMap 中的偏移量 offset。这个 offset的值的范围是$[0,2^{32}-1]$，即该 offset 的最大值为 4G-1，即 4294967295，42 亿多。
- bitValue：每个 BitMap 数据中都是一个仅包含 0 和 1 的二进制字符串，每个 offset 位上的字符就称为该位的值 bitValue。bitValue 的值非 0 即 1。

#### 3.11.2 setbit

- 格式：`SETBIT key offset value`
- 功能：为给定 key 的BitMap 数据的 offset 位置设置值为 value。其返回值为修改前该 offset位置的 bitValue
- 说明：对于原 BitMap 字符串中不存在的 offset 进行赋值，字符串会**自动伸展**以确保它可以将 value 保存在指定的 offset 上。当字符串值进行伸展时，空白位置以 0 填充。当然，设置的 value 只能是 0 或 1。不过需要注意的是，对使用较大 offset 的 SETBIT 操作来说，**内存分配过程可能造成 Redis 服务器被阻塞。**

#### 3.11.3 getbit

- 格式：`GETBIT key offset`
- 功能：对 key 所储存的 BitMap 字符串值，获取指定 offset 偏移量上的位值 bitValue。
- 说明：当 offset 比字符串值的长度大，或者 key 不存在时，返回 0 。

#### 3.11.4 bitcount

- 格式：`BITCOUNT key [start] [end]`
- 功能：统计给定字符串中被设置为 1 的 bit 位的数量。一般情况下，统计的范围是给定的整个 BitMap 字符串。但也可以通过指定额外的 start 或 end 参数，实现仅对指定字节范围内字符串进行统计，包括 start 和 end 在内。

  > 注意，这里的 start 与 end 的单位是字节，不是 bit，并且从 0 开始计数。
- 说明：start 和 end 参数都可以使用负数值： -1 表示最后一个字节， -2 表示倒数第二个字节，以此类推。另外，对于不存在的 key 被当成是空字符串来处理，因此对一个不存在的 key 进行 BITCOUNT 操作，结果为 0 。

#### 3.11.5 bitpos

- 格式：`BITPOS key bit [start] [end]`
- 功能：返回 key 指定的 BitMap 中**第一个值为指定值 bit(非 0 即 1) 的二进制位的位置**。pos，即 position，位置。在默认情况下， 命令将检测整个 BitMap，但用户也可以通过可选的 start 参数和 end 参数指定要检测的范围。
- 说明：start 与 end 的意义与 bitcount 命令中的相同。

#### 3.11.6 bitop

- 格式：`BITOP operation destkey key [key …]`
- 功能：对一个或多个 BitMap 字符串 key 进行**二进制位操作**，并将结果保存到 destkey 上。
- operation 可以是 AND 、 OR 、 NOT 、 XOR 这四种操作中的任意一种：
  - `BITOP AND destkey key [key ...] `：对一个或多个 BitMap 执行按位**与**操作，并将结果保存到 destkey 。
  - `BITOP OR destkey key [key ...]` ：对一个或多个 BitMap 执行按位**或**操作，并将结果保存到 destkey 。
  - `BITOP XOR destkey key [key ...] `：对一个或多个 BitMap 执行按位**异或**操作，并将结果保存到 destkey 。
  - `BITOP NOT destkey key` ：对给定 BitMap 执行按位**非**操作，并将结果保存到 destkey 。
- 说明：
  -  除了 NOT 操作之外，其他操作都可以接受一个或多个 BitMap 作为输入。
  - 除了 NOT 操作外，其他对一个 BitMap 的操作其实就是一个复制。
  - 如果参与运算的多个 BitMap 长度不同，较短的 BitMap 会以 0 作为补充位与较长BitMap 运算，且运算结果长度与较长 BitMap 的相同。

#### 3.11.7 应用场景

- 由于 offset 的取值范围很大，所以其一般应用于**大数据量**的二值性统计。例如平台活跃用户统计（二值：访问或未访问）、支持率统计（二值：支持或不支持）、员工考勤统计（二值：上班或未上班）、图像二值化（二值：黑或白）等。
- 不过，对于数据量较小的二值性统计并不适合 BitMap，可能使用 Set 更为合适。当然，具体多少数据量适合使用 Set，超过多少数据量适合使用 BitMap，这需要根据具体场景进行具体分析。

---

例如，一个平台要统计日活跃用户数量。

- 如果使用 Set 来统计，只需上线一个用户，就将其用户 ID 写入 Set 集合即可，最后只需统计出 Set 集合中的元素个数即可完成统计。即 **Set 集合占用内存的大小与上线用户数量成正比**。假设用户 ID 为 m 位 bit 位，当前活跃用户数量为 n，则该 Set 集合的大小最少应该是m\*n 字节。
- 如果使用 BitMap 来统计，则需要先定义出一个 BitMap，其占有的 bit 位至少为注册用户数量。只需上线一个用户，就立即使其中一个 bit 位置 1，最后只需统计出 BitMap 中 1 的个数即可完成统计。即 **BitMap 占用内存的大小与注册用户数量成正比**，与上线用户数量无关。假设平台具有注册用户数量为 N，则 BitMap 的长度至少为 N 个 bit 位，即 N/8 字节。

何时使用 BitMap 更合适？令 m\*n 字节 = N/8 字节，即 n = N/8/m = N/(8\*m) 时，使用Set 集合与使用 BitMap 所占内存大小相同。以淘宝为例，其用户 ID 长度为 11 位(m)，其注册用户数量为 8 亿(N)，当活跃用户数量为 8 亿/(8\*11) = 0.09 亿 = 9\*106= 900 万，使用 Set与 BitMap 占用的内存是相等的。但淘宝的日均活跃用户数量为 8 千万，所以淘宝使用 BitMap更合适。

### 3.12 HyperLogLog 操作命令

#### 3.12.1 HyperLogLog 简介

- HyperLogLog 是 Redis 2.8.9 版本中引入的一种新的数据类型，其意义是 hyperlog log，超级日志记录。该**数据类型可以简单理解为一个 set 集合**，集合**元素为字符串**。但实际上HyperLogLog 是一种**基数计数概率算法**，通过该算法可以利用极小的内存完成独立总数(去重)的统计。其所有相关命令都是对这个“set 集合”的操作。
- HyperLogLog 算法是由法国人 Philippe Flajolet 博士研究出来的，Redis的作者 Antirez 为了纪念 Philippe Flajolet 博士对组合数学和基数计算算法分析的研究，在设计 HyperLogLog 命令的时候使用了 Philippe Flajolet姓名的英文首字母 PF 作为前缀。遗憾的是 Philippe Flajolet 博士于 2011年 3 月 22 日因病在巴黎辞世。
- HyperLogLog 算法是一个纯数学算法，我们这里不做研究。

#### 3.12.2 pfadd

- 格式：`PFADD key element [element …]`
- 功能：将任意数量的元素添加到指定的 HyperLogLog 集合里面。如果内部存储被修改
  了返回 1，否则返回 0。

#### 3.12.3 pfcount

- 格式：`PFCOUNT key [key …]`
- 功能：
  - 该命令作用于单个 key 时，返回给定 key 的 HyperLogLog 集合的近似基数；
  - 该命令作用于多个 key 时，返回所有给定 key 的 HyperLogLog 集合的并集的近似基数；
  - 如果key 不存在，则返回0。

#### 3.12.4 pfmerge

- 格式：`PFMERGE destkey sourcekey [sourcekey …]`
- 功能：将多个 HyperLogLog 集合**合并**为一个 HyperLogLog 集合，并存储到 destkey 中，
  合并后的 HyperLogLog 的基数接近于所有 sourcekey 的 HyperLogLog 集合的并集。

#### 3.12.5 应用场景

HyperLogLog 可对数据量超级庞大的日志数据做不精确的去重计数统计。当然，这个不精确的度在 Redis 官方给出的误差是 0.81%。这个误差对于大多数超大数据量场景是被允许的。对于平台上每个页面每天的 UV 数据，非常适合使用 HyperLogLog 进行记录。

### 3.13 Geospatial 操作命令

#### 3.13.1 Geospatial 简介

Geospatial，地理空间。
Redis 在 3.2 版本中引入了 Geospatial 这种新的数据类型。该类型本质上仍是一种集合，
只不过集合元素比较特殊，是一种**由三部分构成**的数据结构，这种数据结构称为空间元素：

- 经度：longitude。有效经度为[-180，180]。正的表示东经，负的表示西经。
- 纬度：latitude。有效纬度为[-85.05112878，85.05112878]。正的表示北纬，负的表示南纬。
- 位置名称：为该经纬度所标注的位置所命名的名称，也称为该 Geospatial 集合的空间元
  素名称。

通过该类型可以设置、查询某地理位置的经纬度，查询某范围内的空间元素，计算两空
间元素间的距离等。

#### 3.13.2 geoadd

- 格式：`GEOADD key longitude latitude member [longitude latitude member …]`
- 功能：将一到多个空间元素**添加**到指定的空间集合中。
- 说明：当用户尝试输入一个超出范围的经度或者纬度时，该命令会返回一个错误。

#### 3.13.3 geopos 查看元素的位置

- 格式：`GEOPOS key member [member …]`
  - 功能：从指定的地理空间中**返回指定元素的位置**，即经纬度。
  - 说明：因为 该命令接受可变数量元素作为输入，所以即使用户只给定了一个元素，命令也会返回数组。

#### 3.13.4 geodist 返回距离

- 格式：`GEODIST key member1 member2 [unit]`
  - 功能：**返回两个给定位置之间的距离**。其中 `unit `必须是以下单位中的一种：
  - `m` ：米，默认
  - `km` ：千米
  - `mi `：英里
  - `ft`：英尺
  - 说明：如果两个位置之间的其中一个不存在， 那么命令返回空值。另外，在计算距离
  时会假设地球为完美的球形， 在极限情况下， 这一假设**最大会造成 0.5% 的误差**。

#### 3.13.5 geohash 编码为字符串

- 格式：`GEOHASH key member [member …]`
- 功能：返回一个或多个位置元素的 Geohash 值。
- 说明：GeoHash 是一种地址编码方法。他能够把二维的空间经纬度数据**编码成一个字符**
  **串**。该值主要**用于底层应用或者调试**， 实际中的作用并不大。

#### 3.13.6 georadius 返回指定经纬度为中心半径内的元素

- 格式：`GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] 
  [WITHHASH] [ASC|DESC] [COUNT count]`
  <img src=".\MDImg\三\3.13.6-2eg.png" alt="img" style="zoom:67%;" />

- 功能：以给定的经纬度为中心，返回指定地理空间中包含的所有位置元素中，与中心距离不超过**给定半径的元素**。返回时还可携带额外的信息：

  <img src=".\MDImg\三\3.13.6-1eg.png" alt="img" style="zoom:67%;" />

  - `WITHDIST `：在返回位置元素的同时，将**位置元素与中心之间的距离**也一并返回。距离的单位和用户给定的范围单位保持一致。
  - `WITHCOORD `：将位置元素的**经维度也一并返回**。
  - `WITHHASH`：将位置元素的 Geohash 也一并返回，不过这个 hash 以整数形式表示

  命令默认返回未排序的位置元素。 通过以下两个参数，用户可以指定被返回位置元素
  的排序方式：

  - `ASC` ：根据中心的位置，按照从近到远的方式返回位置元素。
  - `DESC `：根据中心的位置，按照从远到近的方式返回位置元素。

-  说明：在默认情况下， 该命令会返回所有匹配的位置元素。虽然用户可以使
  用 `COUNT <count>` 选项去获取前 N 个匹配元素，但因为命令在内部可能会需要对所有
  被匹配的元素进行处理，所以在对一个非常大的区域进行搜索时，即使使用 COUNT 选项去获取少量元素，该命令的执行速度也可能会非常慢。

#### 3.13.7 georadiusbymember返回指定位置元素为中心附近的位置元素

-  格式：`GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] 
  [WITHHASH] [ASC|DESC] [COUNT count]`
- 功能：这个命令和 `GEORADIUS` 命令一样，都可以找出位于指定范围内的元素，但该命令的中心点是由位置元素形式给定的，而不是像 GEORADIUS 那样，使用输入的经纬度来指定中心点。
- 说明：返回结果中也是包含中心点位置元素的

#### 3.13.8 应用场景

Geospatial 的意义是地理位置，所以其主要应用地理位置相关的计算。例如，微信发现中的“1附近”功能，添加朋友中“雷达加朋友”功能；QQ 动态中的“附近”功能；钉钉中的“签到”功能等。

### 3.14 发布/订阅命令

#### 3.13.1 消息系统

<img src=".\MDImg\三\3.14.1-1eg.png" alt="img" style="zoom:67%;" />

- 发布/订阅，即 pub/sub，是一种消息通信模式：发布者也称为消息生产者，生产和发送消息到存储系统；订阅者也称为消息消费者，从存储系统接收和消费消息。这个存储系统可以是文件系统 FS、消息中间件 MQ、数据管理系统 DBMS，也可以是 Redis。整个消息发布者、订阅者与存储系统称为消息系统。
- 消息系统中的订阅者订阅了某类消息后，只要存储系统中存在该类消息，其就可不断的接收并消费这些消息。当存储系统中没有该消息后，订阅者的接收、消费阻塞。而当发布者将消息写入到存储系统后，会立即唤醒订阅者。当存储系统放满时，不同的发布者具有不同的处理方式：有的会阻塞发布者的发布，等待可用的存储空间；有的则会将多余的消息丢失。
- 当然，不同的消息系统消息的发布/订阅方式也是不同的。例如 RocketMQ、Kafka 等消息中间件构成的消息系统中，发布/订阅的消息都是以主题 Topic 分类的。而 Redis 构成的消息系统中，发布/订阅的消息都是以频道 Channel 分类的。

#### 3.14.2 subscribe 订阅频道 阻塞

- 格式：`SUBSCRIBE channel [channel …]`
- eg:` subscribe news sports`
- 功能：Redis 客户端通过一个 subscribe 命令可以同时订阅任意数量的频道。在输出了订阅了主题后，命令处于阻塞状态，等待相关频道的消息。

#### 3.14.3 psubscribe 订阅频道下的子频道 通配符`*`匹配

- 格式：`PSUBSCRIBE pattern [pattern …]`
- eg:`psubscribe news.* sports.*`
- 功能：订阅一个或多个符合给定模式的频道。
- 说明：这里的模式==只能使用通配符== `*`。例如，`it*` 可以匹配所有以 it 开头的频道，像 it.news、it.blog、it.tweets 等；`news.*`可以匹配所有以` news.`开头的频道，像 news.global.today、news.it 等。

#### 3.14.4 publish 客户端发布频道消息

- 格式：`PUBLISH channel message`
- eg:
  - `publish sports.football "Chinese football broke out of Asia"`
  - `publish sports "Chinese football broke out of Asia"`
- 功能：Redis 客户端通过一条 publish 命令可以发布一个频道的消息。返回值为接收到该消息的订阅者数量。

#### 3.14.5 unsubscribe 客户端退订频道

- 格式：`UNSUBSCRIBE [channel [channel …]]`
- eg:` unsubscribe news`
- 功能：Redis 客户端退订指定的频道。
- 说明：如果没有频道被指定，也就是一个无参数的 UNSUBSCRIBE 命令被执行，那么客户端使用 SUBSCRIBE 命令订阅的所有频道都会被退订。在这种情况下，命令会返回一个信息，告知客户端所有被退订的频道。

#### 3.14.6 punsubscribe 退订频道下的子频道

- 格式：`PUNSUBSCRIBE [pattern [pattern …]]`
- 功能：退订一个或多个符合给定模式的频道。
- 说明：这里的模式只能使用通配符 *。如果没有频道被指定，其效果与 SUBSCRIBE 命令相同，客户端将退订所有订阅的频道。

#### 3.14.7 pubsub 查看订阅与发布系统状态的内省命令集

- 格式：`PUBSUB <subcommand> [argument [argument …]]`
- 功能：PUBSUB 是一个查看订阅与发布系统状态的**内省命令集**，它由数个不同格式的子命令组成，下面分别介绍这些子命令的用法。
  - `pubsub channels`:列出当前所有的**活跃频道**
    - 格式：`PUBSUB CHANNELS [pattern]`
    - 功能：列出当前所有的活跃频道。活跃频道指的是那些至少有一个订阅者的频道。
    - 说明：pattern 参数是可选的。如果不给出 pattern 参数，将会列出订阅/发布系统中的所有活跃频道。如果给出 pattern 参数，那么只列出和给定模式 pattern 相匹配的那些活跃频道。pattern 中只能使用通配符*。
  - `pubsub numsub`:给定**频道**的**订阅者数量**
    - 格式：`PUBSUB NUMSUB [channel-1 … channel-N]`
    - 功能：返回给定频道的订阅者数量。不给定任何频道则返回一个空列表。
  - `pubsub numpat`：**频道模式**的数量总和
    - 格式：`PUBSUB NUMPAT`
    - 功能：查询当前 Redis 所有客户端订阅的所有**频道模式**的数量总和

### 3.15 Redis 事务

Redis 的事务的本质是**一组命令的批处理**。这组命令在执行过程中会被顺序地、一次性全部执行完毕，只要没有出现语法错误，这组命令在执行期间是不会被中断。

#### 3.15.1 Redis 事务特性

Redis 的事务**仅保证了数据的一致性**，不具有像 DBMS 一样的 ACID 特性。

- 这组命令中的某些命令的执行失败不会影响其它命令的执行，不会引发回滚。即不具备原子性。
- 这组命令通过**乐观锁机制**实现了简单的隔离性。没有复杂的隔离级别。
- 这组命令的执行结果是被写入到内存的，是否持久取决于 Redis 的持久化策略，与事务无关

#### 3.15.2 Redis 事务实现

三个命令
Redis 事务通过三个命令进行控制。

- `muti`：开启事务

- `exec`：执行事务(执行事务和取消事务是互斥的)

- `discard`：取消事务

开始事务后写的代码不会执行，在执行exec后才会一起执行，discard会取消执行，exec和discard都会结束事务

- 如果在执行事务过程中出现语法错误会回滚
- 如果语法没有错误但出现执行异常(字符型+1之类的)不会回滚，正常执行，该报错的报错

#### 3.15.3 Redis 事务隔离机制

-  为什么需要隔离机制
  在并发场景下可能会出现多个客户端对同一个数据进行修改的情况。

  - 例如：有两个客户端 C 左与 C 右，C 左需要申请 40 个资源，C 右需要申请 30 个资源。
    它们首先查看了当前拥有的资源数量，即 resources 的值。它们查看到的都是 50，都感觉资源数量可以满足自己的需求，于是修改资源数量，以占有资源。但结果却是资源出现了“超卖”情况。
  - 为了解决这种情况，Redis 事务通过**乐观锁机制**实现了多线程下的执行隔离。

-  隔离的实现
  Redis 通过` watch `命令再配合事务实现了多线程下的执行隔离。

  eg:

  - 两个客户端执行的时间顺序为：<img src=".\MDImg\三\3.15.3-1eg.png" alt="img" style="zoom:67%;" />
  - <img src=".\MDImg\三\3.15.3-2eg.png" alt="img" style="zoom:67%;" />

  1) 当某一客户端对 key 执行了 watch 后，系统就会为该 key 添加一个 version 乐观锁，并初始化 version。例如初值为 1.0。
  2) 此后客户端 C 左将对该 key 的修改语句写入到了事务命令队列中，虽未执行，但其将该key 的 value 值与 version 进行了读取并保存到了当前客户端缓存。此时读取并保存的是
  version 的初值 1.0。
  3) 此后客户端 C 右对该 key 的值进行了修改，这个修改不仅修改了 key 的 value 本身，同时也增加了 version 的值，例如使其 version 变为了 2.0，并将该 version 记录到了该 key
  信息中。
  4) 此后客户端 C 左执行 exec，开始执行事务中的命令。不过，其在执行到对该 key 进行修改的命令时，该命令首先对当前客户端缓存中保存的 version 值与当前 key 信息中的
  version 值。如果缓存 version 小于 key 的 version，则说明客户端缓存的 key 的 value 已经过时，该写操作如果执行可能会破坏数据的一致性。所以该写操作不执行。

## 四. Redis持久化

Redis 是一个内存数据库，所以其运行效率非常高。但也存在一个问题：内存中的数据是不持久的，若主机宕机或 Redis 关机重启，则内存中的数据全部丢失。当然，这是不允许的。Redis 具有持久化功能，其会按照设置以**快照**或**操作日志**的形式将数据持久化到磁盘。
根据持久化使用技术的不同，Redis 的持久化分为两种：RDB 与 AOF。

### 1. 持久化基本原理

 <img src=".\MDImg\四\1-1eg.png" alt="img" style="zoom:67%;" />

Redis 持久化也称为钝化，是指将内存中数据库的状态描述信息保存到磁盘中。只不过是不同的持久化技术，对数据的状态描述信息是不同的，生成的持久化文件也是不同的。但它们的作用都是相同的：避免数据意外丢失。

通过手动方式，或自动定时方式，或自动条件触发方式，将内存中数据库的状态描述信息写入到指定的持久化文件中。当系统重新启动时，自动加载持久化文件，并根据文件中数据库状态描述信息将数据恢复到内存中，这个数据恢复过程也称为激活。这个钝化与激活的过程就是 Redis 持久化的基本原理。

不过从以上分析可知，对于 Redis 单机状态下，无论是手动方式，还是定时方式或条件触发方式，都存在数据丢失问题：在尚未手动/自动保存时发生了 Redis 宕机状况，那么从上次保存到宕机期间产生的数据就会丢失。不同的持久化方式，其数据的丢失率也是不同的。

 <img src=".\MDImg\四\1-2eg.png" alt="img" style="zoom:67%;" />

需要注意的是，RDB 是默认持久化方式，但 Redis 允许 RDB 与 AOF 两种持久化技术同时开启，此时系统会使用 AOF 方式做持久化，即 AOF 持久化技术的优先级要更高。同样的道理，两种技术同时开启状态下，系统启动时若两种持久化文件同时存在，则优先加载 AOF持久化文件。

### 2. RDB持久化

RDB，Redis DataBase，是指将内存中**某一时刻的数据快照全量**写入到指定的 rdb 文件的持久化技术。RDB 持久化**默认是开启**的。当 Redis 启动时会自动读取 RDB 快照文件，将数据从硬盘载入到内存，以恢复 Redis 关机前的数据库状态。

#### 2.1 持久化的执行

RDB 持久化的执行有三种方式：手动 save 命令、手动 bgsave 命令，与自动条件触发。

1. 手动save命令
   `save`
   通过在 `redis-cli` 客户端中执行 `save `命令可立即进行一次持久化保存。`save` 命令在执行期间**会阻塞**` redis-server` 进程，直至持久化过程完毕。而在 `redis-server` 进程阻塞期间，Redis不能处理任何读写请求，无法对外提供服务。

2. 手动bgsave命令
   `bgsave`

   通过在` redis-cli `客户端中执行` bgsave `命令可立即进行一次持久化保存。不同于 `save` 命令的是，正如该命令的名称一样，background save，后台运行 save。`bgsave `命令会使服务器进程 `redis-server` **生成一个子进程**，由该子进程负责完成保存过程。在子进程进行保存过程中，**不会阻塞** `redis-server` 进程对客户端读写请求的处理。

3. 自动条件触发
   自动条件触发的本质仍是` bgsave `命令的执行。只不过是用户通过在配置文件中做相应的设置后，Redis 会根据设置信息自动调用 `bgsave `命令执行。具体配置方式，后面会详解。

4. 查看持久化时间
   `lastsave`
   通过 `lastsave` 命令可以查看最近一次执行持久化的时间，其返回的是一个 Unix 时间戳。

#### 2.2 RDB 优化配置

RDB 相关的配置在` redis.conf `文件的` SNAPSHOTTING `部分。

1. `save`
   该配置用于设置快照的自动保存触发条件，即 save point，保存点。该触发条件是在指
   定时间段内发生了指定次数的写操作。除非另有规定，默认情况下持久化条件为 save 3600 1 300 100 60 10000。其等价于以下三条：

   - `save 3600 1` # 在 3600 秒(1 小时)内发生 1 次写操作
   - `save 300 100 `# 在 300 秒(5 分钟)内发生 100 次写操作
   - `save 60 10000` # 在 60 秒(1 分钟)内发生 1 万次写操作

   如果不启用 RDB 持久化，只需设置 save 的参数为空串即可：`save “”`。

2.  `stop-writes-on-bgsave-error yes`
   默认情况下，如果 RDB 快照已启用（至少一个保存点），且最近的 bgsave **命令失败，Redis将停止接受写入**。这样设置是为了让用户意识到数据没有正确地保存到磁盘上，否则很可能没有人会注意到，并会发生一些灾难。当然，如果 bgsave 命令后来可以正常工作了，Redis将自动允许再次写入。

3. ` rdbcompression yes`
   当进行持久化时启用 LZF **压缩字符串对象**。虽然压缩 RDB 文件会消耗系统资源，降低性能，但可大幅降低文件的大小，方便保存到磁盘，加速主从集群中从节点的数据同步。

4. ` rdbchecksum yes`
   从 RDB5 开始，RDB 文件的 CRC64 校验和就被放置在了文件末尾。这使**格式更能抵抗 RDB文件的损坏**，但在保存和加载 RDB 文件时，**性能会受到影响**（约 10%），因此可以设置为 no禁用校验和以获得最大性能。在禁用校验和的情况下创建的 RDB 文件的校验和为零，这将告诉加载代码跳过校验检查。默认为 yes，开启了校验功能。

5. ` sanitize-dump-payload no`
   该配置用于设置在加载 RDB 文件或进行持久化时是否开启对 zipList、listPack 等数据的**全面安全检测**。该检测可以**降低命令处理时发生系统崩溃的可能**。其可设置的值有三种选择：

   - no：不检测
   - yes：总是检测
   - clients：只有当客户端连接时检测。排除了加载 RDB 文件与进行持久化时的检测。

   默认值本应该是 clients，但其会影响 Redis 集群的工作，所以默认值为 no，不检测。

6. `1dbfilename`
   指定 RDB 文件的默认名称，默认为 dump.rdb。

7.  `rdb-del-sync-files`
   **主从复制时，是否删除**用于同步的**从机上的 RDB 文件**。默认是 no，不删除。不过需要注意，只有当从机的 RDB 和 AOF 持久化功能都未开启时才生效。

8. `dir ./`
   指定 RDB 与 AOF 文件的**生成目录**。默认为 Redis 安装根目录。

#### 2.3 RDB 文件结构

RDB 持久化文件 dump.rdb 整体上有五部分构成：

<img src=".\MDImg\四\2.3-1eg.png" alt="img" style="zoom:67%;" />

1. SOF
   SOF 是一个常量，一个字符串 REDIS，仅包含这五个字符，其长度为 5。用于标识 RDB文件的开始，以便在加载 RDB 文件时可以迅速判断出文件是否是 RDB 文件。
2. rdb_version
   这是一个整数，长度为 4 字节，表示 RDB 文件的版本号。
3. EOF
   EOF 是一个常量，占 1 个字节，用于标识 RDB 数据的结束，校验和的开始。
4. check_sum
   校验和 check_sum 用于判断 RDB 文件中的内容是否出现数据异常。其采用的是 CRC 校验算法。
   CRC 校验算法：
   在持久化时，先将 SOF、rdb_version 及内存数据库中的数据快照这三者的二进制数据拼接起来，形成一个二进制数（假设称为数 a），然后再使用这个 a 除以校验和 check_sum，此时可获取到一个余数 b，然后再将这个 b 拼接到 a 的后面，形成 databases。
   在加载时，需要先使用 check_sum 对 RDB 文件进行数据损坏验证。
   验证过程：只需将RDB 文件中除 EOF 与 check_sum 外的数据除以 check_sum。只要除得的余数不是 0，就说明文件发生损坏。当然，如果余数是 0，也不能肯定文件没有损坏。
   这种验证算法，是数据损坏校验，而不是数据没有损坏的校验。
5. databases
   <img src=".\MDImg\四\2.3-2eg.png" alt="img" style="zoom:67%;" />
   databases 部分是 RDB 文件中最重要的数据部分，其可以包含任意多个非空数据库。而每个 database 又是由三部分构成：
   - SODB：是一个常量，占 1 个字节，用于标识一个数据库的开始。
   - db_number：数据库编号。
   - key_value_pairs：当前数据库中的键值对数据。
     <img src=".\MDImg\四\2.3-3eg.png" alt="img" style="zoom:100%;" />
     每个 key_value_pairs 又由很多个用于描述键值对的数据构成。
     - VALUE_TYPE：是一个常量，占 1 个字节，用于标识该键值对中 value 的类型。
     - EXPIRETIME_UNIT：是一个常量，占 1 个字节，用于标识过期时间的单位是秒还是毫秒。
     - time：当前 key-value 的过期时间。

#### 2.4 RDB 持久化过程

 <img src=".\MDImg\四\2.4-1eg.png" alt="img" style="zoom:70%;" />

对于 Redis 默认的 RDB 持久化，在进行 bgsave 持久化时，redis-server 进程会 fork 出一个 bgsave 子进程，由**该子进程以异步方式负责完成持久化**。而在持久化过程中，redis-server进程不会阻塞，其会继续接收并处理用户的读写请求。 
bgsave 子进程的详细工作原理如下：
由于子进程可以继承父进程的所有资源，且父进程不能拒绝子进程的继承权。所以，bgsave 子进程有权读取到 redis-server 进程写入到内存中的用户数据，使得将内存数据持久化到 dump.rdb 成为可能。
bgsave 子进程在持久化时首先会将内存中的全量数据 copy 到磁盘中的一个 RDB 临时文件，copy 结束后，再将该文件 rename 为 dump.rdb，替换掉原来的同名文件。

<img src=".\MDImg\四\2.4-2eg.png" alt="img" style="zoom:100%;" />
不过，在进行持久化过程中，如果 redis-server 进程接收到了用户写请求，则系统会将内存中发生数据修改的物理块 copy 出一个副本。等内存中的全量数据 copy 结束后，会再将副本中的数据 copy 到 RDB 临时文件。这个副本的生成是由于 Linux 系统的写时复制技术（Copy-On-Write）实现的。

> 写时复制技术是 Linux 系统的一种进程管理技术。
> 原本在 Unix 系统中，当一个主进程通过 fork()系统调用创建子进程后，内核进程会复制主进程的整个内存空间中的数据，然后分配给子进程。这种方式存在的问题有以下几点：
>
> - 这个过程非常耗时
> - 这个过程降低了系统性能
> - 如果主进程修改了其内存数据，子进程副本中的数据是没有修改的。即出现了数据冗余，而冗余数据最大的问题是数据一致性无法保证。
>
> 现代的 Linux 则采用了更为有效的方式：写时复制。子进程会继承父进程的所有资源，其中就包括主进程的内存空间。即子进程与父进程共享内存。只要内存被共享，那么该内存就是只读的（写保护的）。而写时复制则是在任何一方需要写入数据到共享内存时都会出现异常，此时内核进程就会将需要写入的数据 copy 出一个副本写入到另外一块非共享内存区域。

### 3. AOF 持久化

AOF，Append Only File，是指 Redis 将每一次的写操作都**以日志的形式记录**到一个 AOF文件中的持久化技术。当需要恢复内存数据时，将这些写操作重新执行一次，便会恢复到之前的内存数据状态。

==它不会修改只会追加==

#### 3.1 AOF 基础配置

1. AOF 的开启
   默认情况下 AOF 持久化是没有开启的，通过修改配置文件中的 `appendonly `属性为 yes可以开启。

   ```shell
   127.0.0.1:6379> config get appendonly# 获取配置文件的appendonly属性
   1) "appendonly"
   2) "no"
   127.0.0.1:6379> config set appendonly yes# 将内存中的appendonly属性修改为yes
   OK
   127.0.0.1:6379> config get appendonly
   1) "appendonly"
   2) "yes"
   127.0.0.1:6379> config rewrite# 将配置文件重新写入
   OK
   ```

2. 文件名配置
   可以通过`appendfilename`属性指定
   Redis 7 在这里发生了重大变化。原来只有一个 appendonly.aof 文件，现在具有了三类多个文件：

   - 基本文件：可以是 RDF 格式也可以是 AOF 格式。其存放的内容是由 RDB 转为 AOF 当时内存的快照数据。该文件可以有多个。
   - 增量文件：以操作日志形式记录转为 AOF 后的写入操作。该文件可以有多个。
   - 清单文件：用于维护 AOF 文件的创建顺序，保障激活时的应用顺序。该文件只有一个。

3. 混合式持久化开启
    对于基本文件可以是 RDF 格式也可以是 AOF 格式。通过 aof-use-rdb-preamble 属性可以选择。其默认值为 yes，即默认 AOF 持久化的基本文件为 rdb 格式文件，也就是默认采用混合式持久化。

4. AOF 文件目录配置
    为了方便管理，可以专门为 AOF 持久化文件指定存放目录。目录名由 `appenddirname`属性指定，存放在 redis.conf 配置文件的 dir 属性指定的目录，默认为 Redis 安装目录。

#### 3.2 AOF 文件格式

AOF 文件包含三类文件：基本文件、增量文件与清单文件。其中基本文件一般为 rdb 格式，在前面已经研究过了。下面就来看一下增量文件与清单文件的内容格式。

1. Redis 协议
   增量文件扩展名为.aof，采用 AOF 格式。AOF 格式其实就是 Redis 通讯协议格式，AOF持久化文件的本质就是基于 Redis 通讯协议的文本，将命令以纯文本的方式写入到文件中。
   Redis 协议规定，Redis 文本是以行来划分，每行以\r\n 行结束。每一行都有一个消息头，以表示消息类型。消息头由六种不同的符号表示，其意义如下：

   - (+) 表示一个正确的状态信息
   - (-) 表示一个错误信息
   - (*) 表示消息体总共有多少行，不包括当前行
   - ($) 表示下一行消息数据的长度，不包括换行符长度\r\n
   - (空) 表示一个消息数据
   - (:) 表示返回一个数值

2. 查看 AOF 文件
     打开 appendonly.aof.1.incr.aof 文件，可以看到如下格式内容。
      <img src=".\MDImg\四\3.2-1eg.png" alt="img" style="zoom:100%;" />

     以上内容中框起来的是三条命令。一条数据库切换命令 SELECT 0，两条 set 命令。它们的意义如下：
     ```shell
     *2 -- 表示当前命令包含 2 个参数
     $6 -- 表示第 1 个参数包含 6 个字符
     SELECT -- 第 1 个参数
     $1 -- 表示第 2 个参数包含 1 个字符
     0 -- 第 2 个参数
     *3 --表示当前命令包含 3 个参数
     $3 -- 表示第 1 个参数包含 3 个字符
     set -- 第 1 个参数
     $3 -- 表示第 2 个参数包含 3 个字符
     k11 -- 第 2 个参数
     $3 -- 表示第 3 个参数包含 2 个字符
     v11 -- 第 3 个参数
     *3
     ```

3. 清单文件
     打开清单文件 `appendonly.aof.manifest`，查看其内容如下：
      <img src=".\MDImg\四\3.2-2eg.png" alt="img" style="zoom:100%;" />

     该文件首先会按照 seq 序号列举出所有基本文件，基本文件 type 类型为 b，然后再按照seq 序号再列举出所有增量文件，增量文件 type 类型为 i。
     对于 Redis 启动时的数据恢复，也会按照该文件由上到下依次加载它们中的数据。

#### 3.3 Rewrite 机制

随着使用时间的推移，AOF 文件会越来越大。为了防止 AOF 文件由于太大而占用大量的磁盘空间，降低性能，Redis 引入了 Rewrite 机制来对 AOF 文件进行压缩。

1. 何为 rewrite 
   所谓 Rewrite 其实就是**对 AOF 文件进行重写整理**。当 Rewrite 开启后，主进程 `redis-server` **创建出一个子进程** bgrewriteaof，由该**子进程完成 rewrite 过程**。其首先对现有 aof 文件进行 rewrite 计算，将计算结果写入到一个临时文件，写入完毕后，再 rename 该临时文件为原 aof 文件名，覆盖原有文件。

2. rewrite 计算
   rewrite 计算也称为 rewrite 策略。rewrite 计算遵循以下策略：
   - 读操作命令不写入文件
   - 无效命令不写入文件
   - 过期数据不写入文件
   - 多条命令合并写入文件

3. 开启 rewrite
   Rewrite 过程的执行有两种方式。
   一种是通过 `bgrewriteaof` 命令**手动开启**，一种是通过设置条件**自动开启**。

   - 手动开启方式：
      `bgrewriteaof`命令会使主进程 redis-server 创建出一个子进程 bgrewriteaof，由该子进程完成 rewrite过程。而在 rewrite 期间，redis-server 仍是可以对外提供读写服务的。

   - 自动开启rewrite
     手动方式需要人办干预，所以一般采用自动方式。由于 Rewrite 过程是一个计算过程，需要消耗大量系统资源，会降低系统性能。所以，Rewrite 过程并不是随时随地任意开启的，
     而是通过设置一些条件，当满足条件后才会启动，以降低对性能的影响。
     下面是配置文件中对于 Rewrite 自动启动条件的设置。

     - auto-aof-rewrite-percentage：开启 rewrite 的增大比例，默认 100%。指定为 0，表示禁用自动 rewrite。
       `auto-aof-rewrite-percentage 100`
     - auto-aof-rewrite-min-size：开启 rewrite 的 AOF 文件最小值，默认 64M。该值的设置主要是为了防止小 AOF 文件被 rewrite，从而导致性能下降。
       `auto-aof-rewrite-min-size 64mb`

     自动重写 AOF 文件。当 AOF 日志文件大小增长到指定的百分比时，Redis 主进程redis-server 会 fork 出一个子进程 bgrewriteaof 来完成 rewrite 过程。
     其工作原理如下：Redis 会记住最新 rewrite 后的 AOF 文件大小作为基本大小，如果从主机启动后就没有发生过重写，则基本大小就使用启动时 AOF 的大小。
     如果当前 AOF 文件大于基本大小的配置文件中指定的百分比阈值，且当前 AOF 文件大于配置文件中指定的最小阈值，则会触发 rewrite。

#### 3.4 AOF 优化配置

1. `appendfsync` 什么时候调用fsync()追加数据到aof文件
   当客户端提交写操作命令后，该命令就会写入到 aof_buf 中，而 aof_buf 中的数据持久化到磁盘 AOF 文件的过程称为数据同步。
   何时将 aof_buf 中的数据同步到 AOF 文件？采用不同的数据同步策略，同时的时机是不同的，有三种策略：
   - `always`：**写操作**命令写入 aof_buf 后会**立即调用** fsync()系统函数，将其追加到 AOF 文件。
   该策略**效率较低，但相对比较安全**，不会丢失太多数据。最多就是刚刚执行过的写操作在尚未同步时出现宕机或重启，将这一操作丢失。
   - `no`：写操作命令写入 aof_buf 后什么也不做，**不会调用 fsync()函数**。而将 aof_buf 中的数据同步磁盘的操作**由操作系统负责。**Linux 系统默认同步周期为 30 秒。**效率较高**。
   - `everysec`：默认策略。**写操作**命令写入 aof_buf **后**并不直接调用 fsync()，而是**每秒调用一次** fsync()系统函数来完成同步。该策略兼顾到了性能与安全，是一种**折中方案**。
2. ` no-appendfsync-on-rewrite` 是否不调用fsync()追加数据到aof文件，一般写操作高并发时设置为yes
   - 该属性用于指定，当 AOF fsync 策略设置为 `always `或 `everysec`，且主进程创建了子进程正在执行 `bgsave` 或 `bgrewriteaof` 时，
     主进程**是否不调用 fsync()来做数据同步**。设置为 `no`，双重否定**即肯定**，主进程**会调用 fsync()做同步**。而 **`yes `则不会调用 fsync()做数据同步。**
   - 如果设置`no`**调用 fsync()**，在需要同步的数据量非常大时，会**阻塞主进程**对外提供服务，即会存在延迟问题。
     如果设置`yes`**不调用 fsync()，则` AOF fsync `策略相当于设置为了 `no`**，将 aof_buf 中的数据同步磁盘的操作由操作系统负责，可能会存在 30 秒数据丢失的风险。
3. `aof-rewrite-incremental-fsync`将缓存文件写入临时文件的单次最大数量
   当 bgrewriteaof 在执行过程也是先将 rewrite 计算的结果写入到了 aof_rewrite_buf 缓存中，然后**当缓存中数据达到一定量后就会调用 fsync()进行刷盘操作，即数据同步**，将数据写入到临时文件。该属性用于**控制 fsync()每次刷盘的数据量最大不超过 4MB。这样可以避免由于单次刷盘量过大而引发长时间阻塞。**
4. ` aof-load-truncated`
   在进行 AOF 持久化过程中可能会**出现系统突然宕机的情况**，此时写入到 AOF 文件中的最后一条数据可能会不完整。当主机启动后，Redis 在 **AOF 文件不完整的情况下(仅适用于最后数据不完整，中间数据不完整是无效的)是否可以启动**，取决于属性 aof-load-truncated 的设置。其值为：
   - `yes`：AOF 文件**最后不完整的数据**直接从 AOF 文件中**截断删除**，不影响 Redis 的**启动**。**中间数据不完整**，**就不启动了**
   - `no`：AOF 文件最后**不完整的数据不可以被截断删除**，Redis **无法启动**。可以在重新启动服务器前，使用`/usr/local/bin/redis-check-aof`文件修复文件(使用`redis-check-aof 要修复的文件名`)
5. `aof-timestamp-enabeld`
   该属性设置为` yes` 则会开启在 AOF 文件中增加时间戳的显示功能，可方便按照时间对数据进行恢复。但该方式可能会与 AOF 解析器不兼容，所以默认值为 `no`，不开启。

#### 3.5 AOF 持久化过程

<img src=".\MDImg\四\3.5-1eg.png" alt="img" style="zoom:100%;" />

AOF 详细的持久化过程如下：
1) Redis 接收到的写操作命令并不是直接追加到磁盘的 AOF 文件的，而是将每一条写命令按照 redis 通讯协议格式暂时添加到 AOF 缓冲区 aof_buf。
2) 根据设置的数据同步策略，当同步条件满足时，再将缓冲区中的数据一次性写入磁盘的AOF 文件，以减少磁盘 IO 次数，提高性能。
3) 当磁盘的 AOF 文件大小达到了 rewrite 条件时，redis-server 主进程会 fork 出一个子进程bgrewriteaof，由该子进程完成 rewrite 过程。
4) 子进程 bgrewriteaof 首先对该磁盘 AOF 文件进行 rewrite 计算，将计算结果写入到一个临时文件，全部写入完毕后，再 rename 该临时文件为磁盘文件的原名称，覆盖原文件。
5) 如果在 rewrite 过程中又有写操作命令追加，那么这些数据会暂时写入 aof_rewrite_buf缓冲区。等将全部 rewrite 计算结果写入临时文件后，会先将 aof_rewrite_buf 缓冲区中的数据写入临时文件，然后再 rename 为磁盘文件的原名称，覆盖原文件。

### 4. RDB 与 AOF 对比

| 文件 | 优势                                  | 不足                                                         |
| ---- | :------------------------------------ | :----------------------------------------------------------- |
| RDB  | - RDB文件较小<br />- 数据恢复较快     | - 数据安全性较差(一次性写数据太大)<br/>- 写时复制会降低性能<br/>- RDB 文件可读性较差 |
| AOF  | - 数据安全性高<br/>- AOF 文件可读性强 | - AOF 文件较大(本质上是一个日志)<br/>- 写操作会影响性能<br/>- 数据恢复较慢 |

### 5. 持久化技术选型

- 官方推荐使用 RDB 与 AOF 混合式持久化。
- 若对数据安全性要求不高，则推荐使用纯 RDB 持久化方式。
- 不推荐使用纯 AOF 持久化方式。
- **若 Redis 仅用于缓存，则无需使用任何持久化技术。**

## 五. Redis 主从集群

为了避免 Redis 的单点故障问题，我们可以搭建一个 Redis 集群，将数据备份到集群中的其它节点上。若一个 Redis 节点宕机，则由集群中的其它节点顶上。

### 1. 主从集群搭建

Redis 的主从集群是一个“一主多从”的读写分离集群。集群中的 Master 节点负责处理客户端的读写请求，而 Slave 节点仅能处理客户端的读请求。只所以要将集群搭建为读写分离模式，主要原因是，对于数据库集群，写操作压力一般都较小，压力大多数来自于读操作请求。所以，只有一个节点负责处理写操作请求即可。

#### 1.1 伪集群搭建与配置

在采用单线程 IO 模型时，为了提高处理器的利用率，一般会在一个主机中安装多台 Redis，构建一个 Redis 主从伪集群。当然，搭建伪集群的另一个场景是，在学习 Redis，而学习用主机内存不足以创建多个虚拟机。
下面要搭建的读写分离伪集群包含一个 Master 与两个 Slave。它们的端口号分别是：6380、6381、6382。

`slaveof 127.0.0.1 6380`：我要服务于6380，6380是我的主

`slaveof no one`：取消从机身份，恢复主机

**只有主可以写，主和从可以读**

1. 复制 redis.conf
   在 redis 安装目录中 `mkdir 一个目录`，名称随意。这里命名为 cluster。然后将 redis.conf文件复制到 cluster 目录中。该文件后面会被其它配置文件包含，所以该文件中需要设置每个 Redis 节点相同的公共的属性。
   `cp redis.conf cluster`

2. 修改 redis.conf
   在 redis.conf 中做如下几项修改：

   - `masterauth <master-password>`当前奴隶机访问主宰机时的访问密码`requirepass <password>`主机的密码
     因为我们要搭建主从集群，且每个主机都有可能会是 Master，所以**最好不要设置密码验证属性**`requirepass`。如果真需要设置，一定要每个主机的密码都设置为相同的。此时每个配置文件中都要设置两个完全相同的属性：`requirepass` 与 `masterauth`。其中` requirepass `用于指定当前主机的访问密码，而 `masterauth `用于指定当前 slave 访问 master 时向 master 提交的访问密码，用于让 master 验证自己身份是否合法。
   - `repl-disable-tcp-nodelay no`来了数据是否延迟，攒一些数据后在发送
     该属性用于设置**是否禁用 TCP 特性 tcp-nodelay**。
     设置为 `yes` 则禁用 tcp-nodelay，延迟，来了数据攒一会儿在发送，此时master 与 slave 间的通信会产生延迟，但使用的 TCP 包数量会较少，占用的网络带宽会较小。
     相反，**如果设置为 `no`，意味着不禁用tcp-nodelay，不延迟，来了数据直接发送，则网络延迟会变小，但使用的 TCP 包数量会较多，相应占用的网络带宽会大。**
     - tcp-nodelay：为了充分复用网络带宽，TCP 总是希望发送尽可能大的数据块。为了达到该目的，TCP 中使用了一个名为 Nagle 的算法。
     - Nagle 算法的工作原理是，网络在接收到要发送的数据后，并不直接发送，而是等待着数据量足够大（由 TCP 网络特性决定）时再一次性发送出去。这样，网络上传输的有效数据比例就得到了大大提升，无效数据传递量极大减少，于是就节省了网络带宽，缓解了网络压力。
     - tcp-nodelay 则是 TCP 协议中 Nagle 算法的开头。

3. 新建 redis6380.conf
   新建一个 redis 配置文件 redis6380.conf，该配置文件中的 Redis 端口号为 6380。

   `cd redis`

   `mkdir cluster`

   `cd cluster`
   `touch redis6380.conf`

   ```shell
   ### redis6380.conf 文件内容 ###
   include redis.conf
   pidfile "/var/run/redis_6380.pid"
   port 6380
   dbfilename "dump6380.rdb" # RDB 文件的默认名称
   appendfilename "appendonly6380.aof" # AOF持久化文件保存位置
   # logfile logs/access6380.log# 日志
   replica-priority 90 # 选举master的优先级,数字越小优先级越高，但0表示不具备master的能力
   ```

4. 再复制出两个 conf 文件
   再使用 redis6380.conf 复制出两个 conf 文件：redis6381.conf 与 redis6382.conf。然后修改其中的内容(将6380改为对应的值)

5.  启动三台 Redis
   分别使用redis6380.conf、redis6381.conf与redis6382.conf 三个配置文件启动三台Redis。

   ```shell
   redis-server redis6380.conf
   redis-server redis6381.conf
   redis-server redis6382.conf
   ```

6.  设置主从关系
   再打开三个会话框，分别使用客户端连接三台 Redis。然后通过 
   `redis-cli -p 6381`进入指定端口的客户端
   `slaveof` 命令，==指定 6380的 Redis 为 Master。==
   `slaveof 127.0.0.1 6380`：我要服务于6380，6380是我的主

7. 查看状态信息
   通过` info replication` 命令可查看当前连接的 Redis 的状态信息。

   ```shell
   127.0.0.1:6380> info replication# 查看状态
   # Replication
   role:master
   connected_slaves:0
   master_failover_state:no-failover
   master_replid:a7b8b22d25c4747b2fb6c9c3a582191f9bf8b1cd
   master_replid2:0000000000000000000000000000000000000000
   master_repl_offset:0
   second_repl_offset:-1
   repl_backlog_active:0
   repl_backlog_size:1048576
   repl_backlog_first_byte_offset:0
   repl_backlog_histlen:0
   ```

#### 1.2 分级管理

若 Redis 主从集群中的 Slave 较多时，它们的数据同步过程会对 Master 形成较大的性能压力。此时可以对这些 Slave 进行分级管理。

 <img src=".\MDImg\五\1.2-1eg.png" alt="img" style="zoom:100%;" />

设置方式很简单，只需要让低级别 Slave 指定其 slaveof 的主机为其上一级 Slave 即可。
不过，上一级 Slave 的状态仍为 Slave，只不过，其是更上一级的 Slave。
例如，指定 6382 主机为 6381 主机的 Slave，而 6381 主机仍为真正的 Master 的 Slave。

如果master挂了，一级slave仍然没有写的权限，这就有了容灾冷处理

#### 1.3 容灾冷处理

在 Master/Slave 的 Redis 集群中，若 Master 出现宕机怎么办呢？
有两种处理方式：

- 一种是通过**手工角色调整**，使 Slave 晋升为 Master 的冷处理；
- 一种是使用**哨兵模式**，实现 Redis集群的高可用 HA，即热处理。

冷处理
无论 Master 是否宕机，Slave 都可通过 `slaveof no one `**将自己由 Slave 晋升为 Master**。如果其原本就有下一级的 Slave，那么，其就直接变为了这些 Slave 的真正的 Master 了。而原来的 Master 也会失去这个原来的 Slave。
如果Master宕机，Slave 通过 `slaveof no one `**将自己由 Slave 晋升为 Master**，原来的master恢复运行状态后，原master变为现任master的小弟

### 2. 主从复制原理

#### 2.1 主从复制过程

当一个 Redis 节点（slave 节点）接收到类似` slaveof 127.0.0.1 6380 `的指令后直至其可以从 master 持续复制数据，大体经历了如下几个过程：

[点击打开原图](.\MDImg\五\2.1-1eg.png) <img src=".\MDImg\五\2.1-1eg.png" alt="img" style="zoom:100%;" />

1. 保存 master 地址
   当 slave 接收到 slaveof 指令后，slave 会立即将新的 master 的地址保存下来。
2. 建立连接
   slave 中维护着一个定时任务，该定时任务会尝试着与该 master 建立 socket 连接。如果连接无法建立，则其会不断定时重试，直到连接成功或接收到 slaveof no one 指令。
3. slave 发送 ping 命令
   连接建立成功后，slave 会发送 ping 命令进行首次通信。如果 slave 没有收到 master 的回复，则 slave 会主动断开连接，下次的定时任务会重新尝试连接。
4. 对 slave 身份验证
   如果 master 收到了 slave 的 ping 命令，并不会立即对其进行回复，而是会先进行身份验证。如果验证失败，则会发送消息拒绝连接；如果验证成功，则向 slave 发送连接成功响应。
5. master 持久化
   首次通信成功后，slave 会向 master 发送数据同步请求。当 master 接收到请求后，会 fork出一个子进程，让子进程以异步方式立即进行持久化。
6.  数据发送
   持久化完毕后 master 会再 fork 出一个子进程，让该子进程以异步方式将数据发送给slave。slave 会将接收到的数据不断写入到本地的持久化文件中。
   在 slave 数据同步过程中，master 的主进程仍在不断地接受着客户端的写操作，且不仅将新的数据写入到了 master 内存，同时也写入到了同步缓存。当 master 的持久化文件中的数据发送完毕后，master 会再将同步缓存中新的数据发送给 slave，由 slave 将其写入到本地持久化文件中。数据同步完成。
7. slave 恢复内存数据
   当 slave 与 master 的数据同步完成后，slave 就会读取本地的持久化文件，将其恢复到本地内存，然后就可以对外提供读服务了。
8. 持续增量复制
   在 slave 对外提供服务过程中，master 会持续不断的将新的数据以增量方式发送给 slave，以保证主从数据的一致性。

#### 2.2 数据同步演变过程

##### 2.2.1 psync 同步

Redis 2.8 版本之前，首次通信成功后，slave 会向 master 发送 sync 数据同步请求。然后master 就会将其所有数据全部发送给 slave，由 slave 保存到其本地的持久化文件中。这个过程称为全量复制。
但这里存在一个问题：在全量复制过程中可能会出现由于网络抖动而导致复制过程中断。当网络恢复后，slave 与 master 重新连接成功，此时 slave 会重新发送 sync 请求，然后会从头开始全量复制。
由于全量复制过程非常耗时，所以期间出现网络抖动的概率很高。而中断后的从头开始不仅需要消耗大量的系统资源、网络带宽，而且可能会出现长时间无法完成全量复制的情况。

##### 2.2.2 psync 同步

Redis 2.8 版本之后，全量复制采用了 psync（Partial Sync，不完全同步）同步策略。当全量复制过程出现由于网络抖动而导致复制过程中断时，当重新连接成功后，复制过程可以“**断点续传**”。即从断开位置开始继续复制，而不用从头再来。这就大大提升了性能。
为了实现 psync，整个系统做了三个大的变化：

1. 复制偏移量
   系统为每个要传送数据进行了编号，该编号从 0 开始，每个字节一个编号。该编号称为复制偏移量。参与复制的主从节点都会维护该复制偏移量。
   master 每发送过一个字节数据后就会进行累计。统计信息通过 `info replication` 的master_repl_offset 可查看到。同时，slave 会定时向 master 上报其自身已完成的复制偏移量给 master，所以 master 也会保存 slave 的复制偏移量 offset。
   slave在接收到master的数据后，也会累计接收到的偏移量。统计信息通过info replication的 slave_repl_offset 可查看到。
2. 主节点复制 ID
   当 master 启动后就会动态生成一个长度为 40 位的 16 进制字符串作为当前 master 的复制 ID，该 ID 是在进行数据同步时 slave 识别 master 使用的。通过 info replication 的master_replid 属性可查看到该 ID。
3. 复制积压缓冲区
   当 master 有连接的 slave 时，在 master 中就会创建并维护一个队列 backlog，默认大小为 1MB，该队列称为复制积压缓冲区。master 接收到了写操作数据不仅会写入到 master 主存，写入到 master 中为每个 slave 配置的发送缓存，而且还会写入到复制积压缓冲区。其作用就是用于保存最近操作的数据，以备“断点续传”时做数据补偿，防止数据丢失。
4. psync 同步过程
