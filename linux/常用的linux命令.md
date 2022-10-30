## 网络相关
### 查看socket buffer
#### 查看是否阻塞

```bash
netstat -antup | awk '{if($2>100||$3>100){print $0}}'
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0    176 172.16.55.9:36000       1.116.221.166:53104     ESTABLISHED 414664/sshd: root@p

```
 注: `Recv-Q` 是接收队列，如果持续有堆积，可能是高负载，应用处理不过来，也可能是程序的 bug，卡住了，导致没有从 buffer 中取数据，可以看看对应 pid 的 stack 卡在哪里了(`cat /proc/$PID/stack`)。
#### 查看是否有udp buffer满导致丢包

```bash
# 使用 netstat 查看统计 
netstat -s | grep "buffer errors"
    0 receive buffer errors
    0 send buffer errors


# 也可以用 nstat 查看计数器 
nstat -az | grep -E 'UdpRcvbufErrors|UdpSndbufErrors'
UdpRcvbufErrors                 0                  0.0
UdpSndbufErrors                 0                  0.0


```
#### 查看当前 UDP buffer 的情况

```bash
ss -nump
State      Recv-Q Send-Q Local Address:Port               Peer Address:Port
ESTAB      0      0      172.16.55.9:40390              169.254.128.26:19090               users:(("monitor-agent",pid=8032,fd=10))
         skmem:(r0,rb12582912,t0,tb12582912,f0,w0,o0,bl0,d134958)


```
注:  rb12582912 表示 UDP 接收缓冲区大小是 12582912 字节，tb12582912 表示 UDP 发送缓存区大小是 12582912 字节。
Recv-Q 和 Send-Q 分别表示当前接收和发送缓冲区中的数据包字节数。
#### 查看是否有tcp buffer满导致丢包

```bash
nstat -az | grep TcpExtTCPRcvQDrop
TcpExtTCPRcvQDrop               0                  0.0

```
注：对于 TCP，发送 buffer 慢不会导致丢包，只是会让程序发送数据包时卡住，等待缓冲区有足够空间释放出来，而接收 buffer 满了会导致丢包。
#### 查看当前 TCP buffer 的情况

```bash
ss -ntmp
State      Recv-Q Send-Q Local Address:Port               Peer Address:Port
ESTAB      0      0      172.16.55.9:33816              169.254.0.71:80                  users:(("loglistener",pid=109998,fd=11))
         skmem:(r0,rb12582912,t0,tb12582912,f4096,w0,o0,bl0,d0)
         
```
### 查看监听队列
```bash
ss -lnt
State       Recv-Q Send-Q                                 Local Address:Port                                                Peer Address:Port
LISTEN      0      32768                                    172.16.55.9:31689                                                          *:*

```
注：`Recv-Q` 表示 accept queue 中的连接数，如果满了(`Recv-Q`的值比`Send-Q`大1)，要么是并发太大，或负载太高，程序处理不过来；要么是程序 bug，卡住了，导致没有从 accept queue 中取连接，可以看看对应 pid 的 stack 卡在哪里了(`cat /proc/$PID/stack`)。

### 查看网络计算器
```bash
nstat -az | grep TcpExtListen
TcpExtListenOverflows           0                  0.0
TcpExtListenDrops               0                  0.0

```
注：如果有 overflow，意味着 accept queue 有满过，可以查看监听队列看是否有现场。

### 查看conntrack
```bash
conntrack -S
cpu=0           found=668033 invalid=96456 ignore=358556029 insert=0 insert_failed=118 drop=118 early_drop=0 error=103 search_restart=19338898
cpu=1           found=221995 invalid=55168 ignore=246435954 insert=0 insert_failed=59 drop=59 early_drop=0 error=42 search_restart=15630361

```
注：若有 `insert_failed`，表示存在 conntrack 插入失败，会导致丢包。

### 查看链接数
```bash
# ss命令查看
ss -s
Total: 542 (kernel 4541)
TCP:   246 (estab 36, closed 105, orphaned 0, synrecv 0, timewait 88/0), ports 832

Transport Total     IP        IPv6
*         4541      -         -
RAW       2         1         1
UDP       9         6         3
TCP       141       113       28
INET      152       120       32
FRAG      0         0         0

# 脚本统计当前各种状态的 TCP 连接数
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
ESTABLISHED 37
SYN_SENT 1
TIME_WAIT 94


# 直接手动统计 /proc
cat /proc/net/tcp* | wc -l

```

### 网络带宽测试命令
```bash
## TCP协议测试
# 服务端执行
iperf3 -s

# 客户端执行
iperf3 -c <serverIP>

## UDP协议测试
# 服务端执行
iperf -s -u -i 1

# 客户端执行
iperf -c <serverIP> -u -b 10m
```

## 排查资源占用
### 文件占用
```
# 看某个文件在被哪些进程读写
lsof <文件名>

# 看某个进程打开了哪些文件
lsof -p <pid>


```

### 磁盘空间占用
```bash
# 读取当前目录的文件大小
du --max-depth=1 -h ./

# df命令查看
df -h

```

### inode占用
```bash
# df命令
df -i 
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
devtmpfs        215609   1395  214214    1% /dev
tmpfs           220382      7  220375    1% /dev/shm

# tune2fs 命令
tune2fs -l /dev/vda1 | grep -i inode
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super large_file huge_file uninit_bg dir_nlink extra_isize
Inode count:              3276800
Free inodes:              2945228
Inodes per group:         8192
Inode blocks per group:   512
First inode:              11
Inode size:               256
Journal inode:            8
First orphan inode:       525095
Journal backup:           inode blocks


```









