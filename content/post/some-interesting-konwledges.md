---
title: "一些有意思的知识点整理"
date: 2020-03-12T16:02:41+08:00
Description: ""
Tags: []
Categories: []

---

### 一致性哈希算法

背景：有n个服务器做缓存，负载均衡的做法。

答：hash，对用户的序列（如IP）取n模，分发到对应的服务器上；

问：此时有一台宕机，或是业务需要新增了服务器，此时大多数用户的请求都会转到其他服务器，这样会发生大量访问错误，有没有办法。

思考：还是用hash，但需要像一个保证在增加和删除哈希模值时不会发生变化的情况，或发生了变化但可以纠正。

答：比如宕机一台，剩余n-1，首先还是模n，若分发的服务器不是宕机的服务器则不需要变动；若是，再对n-1取模，分发到任意一台完好的服务器中。若宕机m台，依照该思路每次减一求模直到n-m。
（但是对增加新的服务器，这种方式不是很好）

有没有更好的办法：一致性哈希，基本思路就是将服务器和用户请求散列到一个相同的区间上，使用相同的hash算法，因为hash的特殊作用可以把它想象成一个首尾相接的环。将散列后的用户顺时针查找第一个遇到的服务器作为对应的服务器，这样当环上增加或减少某个服务器节点，影响的将是有限个用户，并可以找到新的对应分配的服务器。

保证一致性哈希算法的平衡性，需要使用虚拟节点技术。

建议阅读[博客](https://blog.csdn.net/v_july_v/article/details/6879101)。

### BitMap算法

减少数据存放的占用内存大小的方式。考虑一个4字节int数值，也就是32bit，当有10000000个int会占用

4*10000000/1024/1024=38MB

若一个字节，占8bit，每个bit位用来标识下标数字是否存在，共需要

10000000/8/1024/1024=1.2MB

BitMap适合处理大量数据的加载、排序（鸽巢）和查询，缺点是无法对存在重复的数据进行排序和查找。做到这个需要建立一个十进制到二进制bit位的映射。

假设需要排序或者查找的总数N=10000000，那么我们需要申请的内存空间为 int a[N/32 + 1]。其中a[0]在内存中占32位：

```
a[0] ------> 0 - 31

a[1] ------> 32 - 63

a[2] ------> 64 - 95

a[3] ------> 96 - 127

...
```

需要以下转换：

1. 求十进制数 0-N 对应的在数组 a 中的下标。index_loc = N / 32即可，index_loc即为n对应的数组下标。例如n = 76，则loc = 76 / 32 = 2，因此76在a[2]中；
2. 求十进制数 0-N 对应的bit位。bit_loc = N % 32即可，例如 n = 76，bit_loc = 76 % 32 = 12；
3. 利用移位 0-31 使得对应的 32bit 位为 1 。

建议阅读[博客](https://www.cnblogs.com/chanshuyi/p/5287825.html)。

一般见到不重复的大量数字，都可以用位图。

### 布隆过滤器

用于反向检测新来的字段是否已经在现有的集合中，若多个哈希值中有一个在过滤器数组中的值为0，则一定不存在；若都为一，只能说可能存在，有一定的误判率。

为了防止过滤器内部都为1的情况，一般长度要远大于集合中的个数，因此就非常适合上面的BitMap的思想，采用一个bit数组作为布隆过滤器的实际载体。

布隆过滤器支持isexist和add操作，不支持delete，解决这个方法可以使用引用计数，但就不能采用bit操作的形式了，应该存储数值。

刚才说到布隆过滤器越长，误报率越低。哈希函数的个数也和这个有关，个数越多，过滤器置1的速度越快，效率也会变低。但是个数太少，误报率也会变高。因此对于哈希函数的个数k和布隆过滤器长度的选择m，有以下公式，其中n为插入元素个数，p为误报率：

$$m = -\frac{nlnp}{(ln2)^2}$$

$$k = \frac{m}{n}ln2$$

建议阅读[博客](https://juejin.im/post/5de1e37c5188256e8e43adfc)

利用布隆过滤器减少磁盘 IO 或者网络请求，因为一旦一个值必定不存在的话，我们可以不用进行后续昂贵的查询请求。其他常用情景有：
1. 网页爬虫对URL的去重，避免爬取相同的URL地址；
2. 反垃圾邮件，从数十亿个垃圾邮件列表中判断某邮箱是否垃圾邮箱（同理，垃圾短信）；
3. 缓存穿透，将已存在的缓存放到布隆中，当黑客访问不存在的缓存时迅速返回避免缓存及DB挂掉。

Redis 因其支持 setbit 和 getbit 操作，且纯内存性能高等特点，因此天然就可以作为布隆过滤器来使用。但是布隆过滤器的不当使用极易产生大 Value，增加 Redis 阻塞风险，因此生成环境中建议对体积庞大的布隆过滤器进行拆分。

拆分的形式方法多种多样，但是本质是不要将 Hash(Key) 之后的请求分散在多个节点的多个小 bitmap 上，而是应该拆分成多个小 bitmap 之后，对一个 Key 的所有哈希函数都落在这一个小 bitmap 上。

### 缓存穿透、缓存击穿、缓存雪崩

[博客](https://blog.csdn.net/zeb_perfect/article/details/54135506)

### Trie树

Trie树也叫字典树、前缀树，是一种多叉树，如图

![GZdDqC](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/GZdDqC.jpg)

1. 根节点不包含字符，是空字符；
2. 根节点到某一个节点，路径连接起来为该节点的字符串；
3. 每个节点所有的子节点互不相同；
4. 每个节点会有一个标志位标识该处是否构成一个单词。

显然插入和查询效率很高，都是O(m)，m为要查询或插入的字符串长度，并且是按照字典排序的。但是会占用比较多的空间，如果按照小写英文字符存放，就是一颗26叉树，可以这样定义：

```c++
struct trie_node
{
    bool isKey;   // 标记该节点是否代表一个关键字
    trie_node *children[26]; // 各个子节点 
};
```

Trie树应用：

1. 字符串检索，非常适合数据量很大，但大部分都是重复的情况；
2. 词频统计，可以修改节点结构，添加一个int变量记录该节点代表的单词个数，代替isKey；
3. 对大量字符串按照字典排序：遍历一次所有关键字，将它们全部插入trie树，树的每个结点的所有儿子很显然地按照字母表排序，然后**先序遍历**输出Trie树中所有关键字即可；
4. 前缀匹配，如需要查找以ab开头的所有字符串，构造一个Trie树，并遍历该路径上面的关键字即可。

### map-reduce

分布式计算，基本上就是map-reduce过程，首先可以根据数据值或者把数据hash(md5)后的值，将数据按照范围划分到不同的机子，最好可以让数据划分后可以一次读入内存，这样不同的机子负责处理各种的数值范围，实际上就是map。得到结果后，各个机子只需拿出各自的出现次数最多的前N个数据，然后汇总，选出所有的数据中出现次数最多的前N个数据，这实际上就是reduce过程。

适用于数据量大，但按照种类划分后每个种类都可以装入内存。

### 可执行文件到进程

ELF文件：[传送门](https://terenceli.github.io/%E6%8A%80%E6%9C%AF/2014/11/02/elf)

ELF文件加载过程：[传送门](https://www.ibm.com/developerworks/cn/linux/l-excutff/index.html)

装在一个ELF文件并执行：[传送门](https://www.cnblogs.com/feng9exe/p/6100059.html)
1. 首先bash调用fork()系统调用创建一个新的进程，然后新的进程调用execve()系统调用执行指定的ELF文件。 bash进程继续返回等待新进程执行结束，然后重新等待用户输入命令。
2. 调用execve()系统调用之后，再调用内核的入口sys_execve()。陷入内核的方式是0x80中断。sys_execve()进行一些参数的检查复制之后，调用do_execve()。 因为可执行文件不止ELF一种，还有java程序和以“#!”开始的脚本程序等， 所以do_execve()会首先检查被执行文件，读取前128个字节，特别是开头4个字节的魔数，用以判断可执行文件的格式。 如果是解释型语言的脚本，前两个字节“#!"就构成了魔数，系统一旦判断到这两个字节，就对后面的字符串进行解析，以确定程序解释器的路径。
3. 当do_execve()读取了这128个字节的文件头部之后，然后调用search_binary_handle()去搜索和匹配合适的可执行文件装载处理过程。Linux中所有被支持的可执行文件格式都有相应的装载处理过程，search_binary_handle()会通过判断文件头部的魔数确定文件的格式，并且调用相应的装载处理过程。如ELF用load_elf_binary()，a.out用load_aout_binary()，脚本用load_script()。其中ELF装载过程的主要步骤是:
    a. 检查ELF可执行文件格式的有效性，比如魔数、程序头表中段(Segment)的数量。
    b. 寻找动态链接的”.interp”段(该段保存可执行文件所需要的动态链接器的路径)，设置动态链接器路径。
    c. 根据ELF可执行文件的程序头表的描述，对ELF文件进行映射，比如代码、数据、只读数据。
    d. 初始化ELF进程环境，比如进程启动时EDX寄存器的地址应该是DT_FINI的地址(结束代码地址)。
    e. 将系统调用的返回地址修改成ELF可执行文件的入口点，这个入口点取决于程序的链接方式，对于静态链接的ELF可执行文件，这个程序入口就是ELF文件的文件头中e_enEry所指的地址；对于动态链接的ELF可执行文件，程序入口点是动态链接器。
4. 当ELF被load_elf_binary()装载完成后，函数返回至do_execve()在返回至sys_execve()。在load_elf_binary()中（第5步）系统调用的返回地址已经被改成ELF程序的入口地址了。 所以当sys_execve()系统调用从内核态返回到用户态时，EIP寄存器直接跳转到了ELF程序的入口地址，于是新的程序开始执行，ELF可执行文件装载完成。

### fork

在UNIX中，fork是进程创建另一个进程的唯一方法。只有第一个进程也就是被称作"init"的进程需要"手工创建"。所有其他进程都是用fork这个系统调用创建的。fork系统调用只是复制了父进程的数据和堆栈，并在这两个进程之间共享文本区。fork系统调用采用比较聪明的方式—"写时拷贝（copy-on-write）"技术，使得fork结束后并不立刻复制父进程的内容，而是到了真正使用的时候才复制，这样使效率大大提高。fork函数创建了一个子进程后，子进程会调用exec族函数执行另外一个程序。

### GET和POST区别
1. GET是用来获取信息的、无副作用的，是幂等的（即同样的请求执行一次和执行多次效果是一样的），可以被缓存；
2. POST是用来修改服务器上的数据的，有副作用，非幂等，不可缓存；
3. GET参数跟在地址后面用&分割（自己约定写法也是可以的），POST参数在request的body里面，相对来说POST较为安全，因为参数不会保存在服务器日志和浏览器历史中。注意，这不代表GET不能往body中传参、POST不能往URL后面传参，但都需要服务器支持；
4. 编码类型不同，GET只允许application/x-www-form-urlencoded编码，POST主要有以下四种编码方式：application/x-www-form-urlencoded、multipart/form-data、application/json、text/xml；
5. 所谓GET有长度限制：服务器是因为处理长URL要消耗比较多的资源，为了性能和安全（防止恶意构造长URL来攻击）考虑，会给URL长度加限制。但这并不是HTTP协议的规定，大多是浏览器和框架的行为；
6. 所谓POST会产生两次TCP，即将header和body分两次请求，先发送header得到100后再发送body得到200，这是浏览器或框架的行为，而且chrome和firefox只会发送一次。

[传送门](https://blog.fundebug.com/2019/02/22/compare-http-method-get-and-post/)

### HTTP1.0/1.1/2.0

#### 1.0和1.1区别
1. 长连接：HTTP 1.0需要使用keep-alive参数来告知服务器端要建立一个长连接，而HTTP1.1默认支持长连接。HTTP是基于TCP/IP协议的，创建一个TCP连接是需要经过三次握手的（无状态），有一定的开销，如果每次通讯都要重新建立连接的话，对性能有影响。因此最好能维持一个长连接，可以用个长连接来发多个请求；
2. 节约带宽：HTTP 1.1支持只发送header信息(不带任何body信息)，如果服务器认为客户端有权限请求服务器，则返回100，否则返回401。客户端如果接受到100，才开始把请求body发送到服务器。这样当服务器返回401的时候，客户端就可以不用发送请求body了，节约了带宽。另外HTTP还支持传送内容的一部分。这样当客户端已经有一部分的资源后，只需要跟服务器请求另外的部分资源即可。这是支持文件断点续传的基础。

#### 1.1和2.0区别

1. 多路复用：HTTP2.0使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1大了好几个数量级。当然HTTP1.1也可以多建立几个TCP连接，来支持处理更多并发的请求，但是创建TCP连接本身也是有开销的。TCP连接有一个预热和保护的过程，先检查数据是否传送成功，一旦成功过，则慢慢加大传输速度。因此对应瞬时并发的连接，服务器的响应就会变慢。所以最好能使用一个建立好的连接，并且这个连接可以支持瞬时并发的请求；
2. 数据压缩：HTTP1.1不支持header数据的压缩，HTTP2.0使用HPACK算法对header的数据进行压缩，这样数据体积小了，在网络上传输就会更快；
3. 服务器推送：意思是说，当我们对支持HTTP2.0的web server请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到客户端，免得客户端再次创建连接发送请求到服务器端获取。这种方式非常合适加载静态资源。服务器端推送的这些资源其实存在客户端的某处地方，客户端直接从本地加载这些资源就可以了，不用走网络，速度自然是快很多的。

### HTTP/HTTPS

1. HTTPS协议需要到CA申请证书，一般免费证书很少，需要交费。
2. HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。
3. HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
4. HTTPS可以有效的防止运营商劫持，解决了防劫持的一个大问题。

### TLS/SSL与非对称加密

1. 对称加密（如AES）计算量小、速度快；非对称加密（如RSA）计算量大、速度慢，只适合对少量数据进行加密。因此实际开发中对数据量大的情况常用RSA+AES方式，对称加密数据，然后将非对称加密AES的密钥；
2. 非对称加密计算量大的原因是密钥长度一般较长，通常在1024位或2048位。如此大的公钥私钥能保证安全性的原因是依赖大数难以分解，公钥私钥的生成依赖以下四个数学知识：互质关系、欧拉函数、欧拉定理、模反元素，加密原理：[传送门](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)；
3. 如何保证公钥不被篡改：将公钥放在数字证书中。只要证书是可信的，公钥就是可信的。

因此经过SSL/TLS层协议的基本过程如下：
1. client向server索要并验证公钥的正确性；
2. 双方协商计算并生成“对话密钥”，这个过程采用非对称加密；
3. 双方采用对话密钥进行加密通信，这个过程采用对称加密。
 
### 进程、线程调度开销的差异

1. 线程是调度的基本单位，进城是资源分配的基本单位。fork一个新的进程，实际上产生了一个线程参与任务的调度。进程通过pthread_create创建更多的线程；
2. 不管进程还是线程，linux中都用`struct task_struct`来描述。体现不同进程的区别在于结构体内的一个指针字段`struct mm_struct *mm`，这个指针指向实际的内存资源。同一个进程下的不同线程指向的资源是相同的，不同进程下的线程指向的资源不同。从这点来看，linux淡化了进程和线程的区别；
3. 为什么说进程的任务调度开销要比线程小？实际上可以这样区分：线程调度指的是使用相同资源的`struct task_struct`之间的调度，进程调度：使用不同资源的`struct task_struct`之间的调度。任务调度的开销主要有二：一是CPU执行任务调度的开销，主要是切换上下文；二是任务调度后产生的CPU的Cache/TLB（改进虚拟地址到物理地址转换速度的缓存）不命中，导致缺页中断的开销。对第一个二者没有区别，所以开销主要在第二个上面：既然线程调度是切换使用相同资源的`struct task_struct`，那这样Cache/TLB的命中就会相对高很多；相反使用不同资源的`struct task_struct`做切换，那原有的Cache就可能不适用了，从而引发更多的缺页中断。

### BTree/B+Tree

1. B+Tree和BTree基本相同，除了
2. 非叶子结点的子树指针个数和关键字个数相同，BTree+1；
3. 非叶子结点的子树指针P[i]指向关键字值属于[K[i], K[i+1])的子树，而BTree是开区间；
4. B+Tree的所有叶子节点有一条链指针；
5. B+Tree所有data都在叶子节点，其他节点只有索引。BTree每个节点都有data域。这样同样大小的磁盘页B+Tree能容纳更多节点；
6. B+Tree的范围查询方便，可先找到下限，通过叶子节点的链表顺序查找到上限即可。BTree只能先找下限，再通过中序遍历找到上限。

既然MyISAM和InnoDB是MySQL的两代引擎，肯定会有一个提升，而InnoDB是最新一代，那么它到底优在哪里？

试想，MyISAM和InnoDB都是以B+树为基础实现的，相对于B树的不同其实前面已经讲过，即数据域和结点分离；

而MyISAM更是索引和文件分离，B+树的叶子结点的数据域存放的是文件内容的地址，主索引和辅助索引的B+树都是如此，那么如果我改变了一个地址，是不是所有的索引树都得改变，正如前面我们讲的在磁盘上频繁的读写操作是效率很低的，而这块又不适用局部原理，因为逻辑上相邻的结点，物理上不一定相邻，那么这样就会造成效率上的降低；

于是乎，InnoDB就产生了，它让除了主索引以外的辅助索引的叶子结点的数据域都保存主键，先通过辅助索引找到主键，然后通过主键找到叶子结点的所有数据，听起来貌似很麻烦，遍历了两棵树，但是，这样如果有了修改的话，改变的只是主索引，其它辅助缩印都不用动，而且，数据库中的树的每一个结点的key可不是咱们给的那么少，试想如果一个结点有1024个key，那么高度为2的B+树都有1024*1024个key，所以一般树的高度都很低，所以，遍历树的消耗几乎忽略不计！

### 惊群效应

惊群简单来说就是多个进程或者线程在等待同一个事件，当事件发生时，所有线程和进程都会被内核唤醒。唤醒后通常只有一个进程获得了该事件并进行处理，其他进程发现获取事件失败后又继续进入了等待状态，在一定程度上降低了系统性能。

linux2.6内核后accept已经不会产生惊群效应，即当多个进程/线程都阻塞在对同一个 socket 的 accept 调用上时，当有一个新的连接到来，内核只会唤醒一个进程，其他进程保持休眠，压根就不会被唤醒。

epoll 上还是存在惊群问题。即，如果多个进程/线程阻塞在监听同一个 listening socket fd 的 epoll_wait 上，当有一个新的连接到来时，所有的进程都会被唤醒。

Nginx 的 accept_mutex,并不是解决 accept 惊群问题，而是解决 epoll_wait 惊群问题。说Nginx 解决了 epoll_wait 惊群问题，也是不对的，它只是控制是否将监听套接字加入到epoll 中。监听套接字只在一个子进程的 epoll 中，当新的连接来到时，其他子进程当然不会惊醒了。

Linux 4.5解决了这一问题，使用EPOLLEXCLUSIVE标记

[传送门](https://www.cnblogs.com/Anker/p/7071849.html)

### EPOLLONESHOT事件

使用场合：

      一个线程在读取完某个socket上的数据后开始处理这些数据，而数据的处理过程中该socket又有新数据可读，此时另外一个线程被唤醒来读取这些新的数据。

      于是，就出现了两个线程同时操作一个socket的局面。可以使用epoll的EPOLLONESHOT事件实现一个socket连接在任一时刻都被一个线程处理。

作用：

      对于注册了EPOLLONESHOT事件的文件描述符，操作系统最多出发其上注册的一个可读，可写或异常事件，且只能触发一次。
      
使用：

      注册了EPOLLONESHOT事件的socket一旦被某个线程处理完毕，该线程就应该立即重置这个socket上的EPOLLONESHOT事件，以确保这个socket下一次可读时，其EPOLLIN事件能被触发，进而让其他工作线程有机会继续处理这个sockt。

效果：

      尽管一个socket在不同事件可能被不同的线程处理，但同一时刻肯定只有一个线程在为它服务，这就保证了连接的完整性，从而避免了很多可能的竞态条件。

[一篇非常好的文章](https://www.lagou.com/lgeduarticle/23901.html)
