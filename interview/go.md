# 面试题之go篇

##  Go 有异常类型吗？

有。Go用error类型代替try...catch语句，这样可以节省资源。同时增加代码可读性：

```text
 _, err := funcDemo()
if err != nil {
    fmt.Println(err)
    return
}
```

也可以用errors.New()来定义自己的异常。errors.Error()会返回异常的字符串表示。只要实现error接口就可以定义自己的异常，

```text
 type errorString struct {
  s string
 }
 
 func (e *errorString) Error() string {
  return e.s
 }
 
 // 多一个函数当作构造函数
 func New(text string) error {
  return &errorString{text}
 }
```

## 什么是协程（Goroutine）

协程是**用户态轻量级线程**，它是**线程调度的基本单位**。通常在函数前加上go关键字就能实现并发。一个Goroutine会以一个很小的栈启动2KB或4KB，当遇到栈空间不足时，栈会**自动伸缩**， 因此可以轻易实现成千上万个goroutine同时启动。

## 如何高效地拼接字符串

拼接字符串的方式有：`+` , `fmt.Sprintf` , `strings.Builder`, `bytes.Buffer`, `strings.Join`

1 "+"

使用`+`操作符进行拼接时，会对字符串进行遍历，计算并开辟一个新的空间来存储原来的两个字符串。



2 fmt.Sprintf

由于采用了接口参数，必须要用反射获取值，因此有性能损耗。



3 strings.Builder：

用WriteString()进行拼接，内部实现是指针+切片，同时String()返回拼接后的字符串，它是直接把[]byte转换为string，从而避免变量拷贝。



4 bytes.Buffer

`bytes.Buffer`是一个一个缓冲`byte`类型的缓冲器，这个缓冲器里存放着都是`byte`，

`bytes.buffer`底层也是一个`[]byte`切片。



5 strings.join

`strings.join`也是基于`strings.builder`来实现的,并且可以自定义分隔符，在join方法内调用了b.Grow(n)方法，这个是进行初步的容量分配，而前面计算的n的长度就是我们要拼接的slice的长度，因为我们传入切片长度固定，所以提前进行容量分配可以减少内存分配，很高效。

**性能比较**：

strings.Join ≈ strings.Builder > bytes.Buffer > "+" > fmt.Sprintf

## 什么是 rune 类型

ASCII 码只需要 7 bit 就可以完整地表示，但只能表示英文字母在内的128个字符，为了表示世界上大部分的文字系统，发明了 Unicode， 它是ASCII的超集，包含世界上书写系统中存在的所有字符，并为每个代码分配一个标准编号（称为Unicode CodePoint），在 Go 语言中称之为 rune，是 int32 类型的别名。

Go 语言中，字符串的底层表示是 byte (8 bit) 序列，而非 rune (32 bit) 序列。

```go
sample := "我爱GO"
runeSamp := []rune(sample)
runeSamp[0] = '你'
fmt.Println(string(runeSamp))  // "你爱GO"
fmt.Println(len(runeSamp))  // 4
```

## Go 支持默认参数或可选参数吗？

不支持。但是可以利用结构体参数，或者...传入参数切片数组。

```text
// 这个函数可以传入任意数量的整型参数
func sum(nums ...int) {
    total := 0
    for _, num := range nums {
        total += num
    }
    fmt.Println(total)
}
```

## 如何判断 map 中是否包含某个 key ？

```go
var sample map[int]int
if _, ok := sample[10]; ok {

} else {

}
```

## defer 的执行顺序

defer执行顺序和调用顺序相反，类似于栈**后进先出**(LIFO)。

defer在return之后执行，但在函数退出之前，defer可以修改返回值。下面是一个例子：

```go
func test() int {
	i := 0
	defer func() {
		fmt.Println("defer1")
	}()
	defer func() {
		i += 1
		fmt.Println("defer2")
	}()
	return i
}

func main() {
	fmt.Println("return", test())
}
// defer2
// defer1
// return 0
```

上面这个例子中，test返回值并没有修改，这是由于Go的返回机制决定的，执行Return语句后，Go会创建一个临时变量保存返回值。如果是有名返回（也就是指明返回值`func test() (i int)`）

```go
func test() (i int) {
	i = 0
	defer func() {
		i += 1
		fmt.Println("defer2")
	}()
	return i
}

func main() {
	fmt.Println("return", test())
}
// defer2
// return 1
```

这个例子中，返回值被修改了。对于有名返回值的函数，执行 return 语句时，并不会再创建临时变量保存，因此，defer 语句修改了 i，即对返回值产生了影响。

## Go 语言 tag 的用处？

tag可以为结构体成员提供属性。常见的：

1. json序列化或反序列化时字段的名称
2. db: sqlx模块中对应的数据库字段名
3. form: gin框架中对应的前端的数据字段名
4. binding: 搭配 form 使用, 默认如果没查找到结构体中的某个字段则不报错值为空, binding为 required 代表没找到返回错误给前端

## 如何获取一个结构体的所有tag？

利用反射：

```go
import reflect
type Author struct {
	Name         int      `json:Name`
	Publications []string `json:Publication,omitempty`
}

func main() {
	t := reflect.TypeOf(Author{})
	for i := 0; i < t.NumField(); i++ {
		name := t.Field(i).Name
		s, _ := t.FieldByName(name)
		fmt.Println(name, s.Tag)
	}
}
```

上述例子中，`reflect.TypeOf`方法获取对象的类型，之后`NumField()`获取结构体成员的数量。 通过`Field(i)`获取第i个成员的名字。 再通过其`Tag` 方法获得标签。

## 如何判断 2 个字符串切片（slice) 是相等的？

`reflect.DeepEqual()` ， 但反射非常影响性能。

## 结构体打印时，`%v` 和 `%+v` 的区别

`%v`输出结构体各成员的值；

`%+v`输出结构体各成员的**名称**和**值**；

`%#v`输出结构体名称和结构体各成员的名称和值

## Go 语言中如何表示枚举值(enums)？

在常量中用iota可以表示枚举。iota从0开始。

```text
const (
	B = 1 << (10 * iota)
	KiB 
	MiB
	GiB
	TiB
	PiB
	EiB
)
```

## 空 struct{} 的用途

- 用map模拟一个set，那么就要把值置为struct{}，struct{}本身不占任何空间，可以避免任何多余的内存分配。

```text
type Set map[string]struct{}

func main() {
	set := make(Set)

	for _, item := range []string{"A", "A", "B", "C"} {
		set[item] = struct{}{}
	}
	fmt.Println(len(set)) // 3
	if _, ok := set["A"]; ok {
		fmt.Println("A exists") // A exists
	}
}
```

- 有时候给通道发送一个空结构体,channel<-struct{}{}，也是节省了空间。

```text
func main() {
	ch := make(chan struct{}, 1)
	go func() {
		<-ch
		// do something
	}()
	ch <- struct{}{}
	// ...
}
```

- 仅有方法的结构体

```text
type Lamp struct{}
```

## go里面的int和int32是同一个概念吗？

不是一个概念！千万不能混淆。go语言中的int的大小是和操作系统位数相关的，如果是32位操作系统，int类型的大小就是4字节。如果是64位操作系统，int类型的大小就是8个字节。除此之外uint也与操作系统有关。

int8占1个字节，int16占2个字节，int32占4个字节，int64占8个字节。

## init() 函数是什么时候执行的？

**简答**： 在main函数之前执行。

**详细**：init()函数是go初始化的一部分，由runtime初始化每个导入的包，初始化不是按照从上到下的导入顺序，而是按照解析的依赖关系，没有依赖的包最先初始化。

每个包首先初始化包作用域的常量和变量（常量优先于变量），然后执行包的`init()`函数。同一个包，甚至是同一个源文件可以有多个`init()`函数。`init()`函数没有入参和返回值，不能被其他函数调用，同一个包内多个`init()`函数的执行顺序不作保证。

执行顺序：import –> const –> var –>`init()`–>`main()`

一个文件可以有多个`init()`函数！

## 如何知道一个对象是分配在栈上还是堆上？

Go和C++不同，Go局部变量会进行**逃逸分析**。如果**变量离开作用域后没有被引用**，则**优先**分配到栈上，否则分配到堆上。那么如何判断是否发生了逃逸呢？

`go build -gcflags '-m -m -l' xxx.go`.

关于逃逸的可能情况：变量大小不确定，变量类型不确定，变量分配的内存超过用户栈最大值，暴露给了外部指针。

## 2 个 interface 可以比较吗 ？

Go 语言中，interface 的内部实现包含了 2 个字段，类型 `T` 和 值 `V`，interface 可以使用 `==` 或 `!=` 比较。2 个 interface 相等有以下 2 种情况

1. 两个 interface 均等于 nil（此时 V 和 T 都处于 unset 状态）
2. 类型 T 相同，且对应的值 V 相等。

看下面的例子：

```go
type Stu struct {
     Name string
}

type StuInt interface{}

func main() {
     var stu1, stu2 StuInt = &Stu{"Tom"}, &Stu{"Tom"}
     var stu3, stu4 StuInt = Stu{"Tom"}, Stu{"Tom"}
     fmt.Println(stu1 == stu2) // false
     fmt.Println(stu3 == stu4) // true
}
```

`stu1` 和 `stu2` 对应的类型是 `*Stu`，值是 Stu 结构体的地址，两个地址不同，因此结果为 false。
`stu3` 和 `stu4` 对应的类型是 `Stu`，值是 Stu 结构体，且各字段相等，因此结果为 true。

## 2 个 nil 可能不相等吗？

可能不等。interface在运行时绑定值，只有值为nil接口值才为nil，但是与指针的nil不相等。举个例子：

```text
var p *int = nil
var i interface{} = nil
if(p == i){
	fmt.Println("Equal")
}
```

两者并不相同。总结：**两个nil只有在类型相同时才相等**。

## 简述 Go 语言GC(垃圾回收)的工作原理

垃圾回收机制是Go一大特(nan)色(dian)。Go1.3采用**标记清除法**， Go1.5采用**三色标记法**，Go1.8采用**三色标记法+混合写屏障**。

***标记清除法\***

分为两个阶段：标记和清除

标记阶段：从根对象出发寻找并标记所有存活的对象。

清除阶段：遍历堆中的对象，回收未标记的对象，并加入空闲链表。

缺点是需要暂停程序STW。

***三色标记法\***：

将对象标记为白色，灰色或黑色。

白色：不确定对象（默认色）；黑色：存活对象。灰色：存活对象，子对象待处理。

标记开始时，先将所有对象加入白色集合（需要STW）。首先将根对象标记为灰色，然后将一个对象从灰色集合取出，遍历其子对象，放入灰色集合。同时将取出的对象放入黑色集合，直到灰色集合为空。最后的白色集合对象就是需要清理的对象。

这种方法有一个缺陷，如果对象的引用被用户修改了，那么之前的标记就无效了。因此Go采用了**写屏障技术**，当对象新增或者更新会将其着色为灰色。

一次完整的GC分为四个阶段：

1. 准备标记（需要STW），开启写屏障。
2. 开始标记
3. 标记结束（STW），关闭写屏障
4. 清理（并发）

基于插入写屏障和删除写屏障在结束时需要STW来重新扫描栈，带来性能瓶颈。**混合写屏障**分为以下四步：

1. GC开始时，将栈上的全部对象标记为黑色（不需要二次扫描，无需STW）；
2. GC期间，任何栈上创建的新对象均为黑色
3. 被删除引用的对象标记为灰色
4. 被添加引用的对象标记为灰色

总而言之就是确保黑色对象不能引用白色对象，这个改进直接使得GC时间从 2s降低到2us。

## 函数返回局部变量的指针是否安全？

这一点和C++不同，在Go里面返回局部变量的指针是安全的。因为Go会进行**逃逸分析**，如果发现局部变量的作用域超过该函数则会**把指针分配到堆区**，避免内存泄漏。

## 非接口的任意类型 T() 都能够调用 `*T` 的方法吗？反过来呢？

一个T类型的值可以调用*T类型声明的方法，当且仅当T是**可寻址的**。

反之：*T 可以调用T()的方法，因为指针可以解引用。

## 无缓冲的 channel 和有缓冲的 channel 的区别？

对于无缓冲区channel：

发送的数据如果没有被接收方接收，那么**发送方阻塞；**如果一直接收不到发送方的数据，**接收方阻塞**；

有缓冲的channel：

发送方在缓冲区满的时候阻塞，接收方不阻塞；接收方在缓冲区为空的时候阻塞，发送方不阻塞。

可以类比生产者与消费者问题。

## 为什么有协程泄露(Goroutine Leak)？

协程泄漏是指协程创建之后没有得到释放。主要原因有：

1. 缺少接收器，导致发送阻塞
2. 缺少发送器，导致接收阻塞
3. 死锁。多个协程由于竞争资源导致死锁。
4. 创建协程的没有回收。

## Go 可以限制运行时操作系统线程的数量吗？ 常见的goroutine操作函数有哪些？

可以，使用runtime.GOMAXPROCS(num int)可以设置线程数目。该值默认为CPU逻辑核数，如果设的太大，会引起频繁的线程切换，降低性能。

runtime.Gosched()，用于让出CPU时间片，让出当前goroutine的执行权限，调度器安排其它等待的任务运行，并在下次某个时候从该位置恢复执行。
runtime.Goexit()，调用此函数会立即使当前的goroutine的运行终止（终止协程），而其它的goroutine并不会受此影响。runtime.Goexit在终止当前goroutine前会先执行此goroutine的还未执行的defer语句。请注意千万别在主函数调用runtime.Goexit，因为会引发panic。

## new和make的区别？

- new只用于分配内存，返回一个指向地址的**指针**。它为每个新类型分配一片内存，初始化为0且返回类型*T的内存地址，它相当于&T{}
- make只可用于**slice,map,channel**的初始化,返回的是**引用**。

## 请你讲一下Go面向对象是如何实现的？

Go实现面向对象的两个关键是struct和interface。

封装：对于同一个包，对象对包内的文件可见；对不同的包，需要将对象以大写开头才是可见的。

继承：继承是编译时特征，在struct内加入所需要继承的类即可：

```text
type A struct{}
type B struct{
A
}
```

多态：多态是运行时特征，Go多态通过interface来实现。类型和接口是松耦合的，某个类型的实例可以赋给它所实现的任意接口类型的变量。

Go支持多重继承，就是在类型中嵌入所有必要的父类型。

## uint型变量值分别为 1，2，它们相减的结果是多少？

```text
	var a uint = 1
	var b uint = 2
	fmt.Println(a - b)
```

答案，结果会溢出，如果是32位系统，结果是2^32-1，如果是64位系统，结果2^64-1.

## 讲一下go有没有函数在main之前执行？怎么用？

go的init函数在main函数之前执行，它有如下特点：

```text
func init() {
	...
}
```

init函数非常特殊：

- 初始化不能采用初始化表达式初始化的变量；
- 程序运行前执行注册
- 实现sync.Once功能
- 不能被其它函数调用
- init函数没有入口参数和返回值：
- 每个包可以有多个init函数，**每个源文件也可以有多个init函数**。
- 同一个包的init执行顺序，golang没有明确定义，编程时要注意程序不要依赖这个执行顺序。
- 不同包的init函数按照包导入的依赖关系决定执行顺序。

## golang的内存管理的原理清楚吗？简述go内存管理机制。

golang内存管理基本是参考tcmalloc来进行的。go内存管理本质上是一个内存池，只不过内部做了很多优化：自动伸缩内存池大小，合理的切割内存块。

> 一些基本概念：
> 页Page：一块8K大小的内存空间。Go向操作系统申请和释放内存都是以页为单位的。
> span : 内存块，一个或多个连续的 page 组成一个 span 。如果把 page 比喻成工人， span 可看成是小队，工人被分成若干个队伍，不同的队伍干不同的活。
> sizeclass : 空间规格，每个 span 都带有一个 sizeclass ，标记着该 span 中的 page 应该如何使用。使用上面的比喻，就是 sizeclass 标志着 span 是一个什么样的队伍。
> object : 对象，用来存储一个变量数据内存空间，一个 span 在初始化时，会被切割成一堆等大的 object 。假设 object 的大小是 16B ， span 大小是 8K ，那么就会把 span 中的 page 就会被初始化 8K / 16B = 512 个 object 。所谓内存分配，就是分配一个 object 出去。

**mheap**

一开始go从操作系统索取一大块内存作为内存池，并放在一个叫mheap的内存池进行管理，mheap将一整块内存切割为不同的区域，并将一部分内存切割为合适的大小。

mheap.spans ：用来存储 page 和 span 信息，比如一个 span 的起始地址是多少，有几个 page，已使用了多大等等。

mheap.bitmap 存储着各个 span 中对象的标记信息，比如对象是否可回收等等。

mheap.arena_start : 将要分配给应用程序使用的空间。

**mcentral**

用途相同的span会以链表的形式组织在一起存放在mcentral中。这里用途用**sizeclass**来表示，就是该span存储哪种大小的对象。

找到合适的 span 后，会从中取一个 object 返回给上层使用。



**mcache**

为了提高内存并发申请效率，加入缓存层mcache。每一个mcache和处理器P对应。Go申请内存首先从P的mcache中分配，如果没有可用的span再从mcentral中获取。

## mutex有几种模式？

mutex有两种模式：**normal** 和 **starvation**

正常模式

所有goroutine按照FIFO的顺序进行锁获取，被唤醒的goroutine和新请求锁的goroutine同时进行锁获取，通常**新请求锁的goroutine更容易获取锁**(持续占有cpu)，被唤醒的goroutine则不容易获取到锁。公平性：否。

饥饿模式

所有尝试获取锁的goroutine进行等待排队，**新请求锁的goroutine不会进行锁获取**(禁用自旋)，而是加入队列尾部等待获取锁。公平性：是。

## go竞态条件了解吗？

所谓竞态竞争，就是当**两个或以上的goroutine访问相同资源时候，对资源进行读/写。**

比如`var a int = 0`，有两个协程分别对a+=1，我们发现最后a不一定为2.这就是竞态竞争。

通常我们可以用`go run -race xx.go`来进行检测。

解决方法是，对临界区资源上锁，或者使用原子操作(atomics)，原子操作的开销小于上锁。

## 如果若干个goroutine，有一个panic会怎么做？

有一个panic，那么剩余goroutine也会退出，程序退出。如果不想程序退出，那么必须通过调用 recover() 方法来捕获 panic 并恢复将要崩掉的程序。

## defer可以捕获goroutine的子goroutine吗？

不可以。它们处于不同的调度器P中。对于子goroutine，必须通过 **recover() 机制来进行恢复**，然后结合日志进行打印（或者通过channel传递error），下面是一个例子：

```text
// 心跳函数
func Ping(ctx context.Context) error {
    ... code ...
 
	go func() {
		defer func() {
			if r := recover(); r != nil {
				log.Errorc(ctx, "ping panic: %v, stack: %v", r, string(debug.Stack()))
			}
		}()
 
        ... code ...
	}()
 
    ... code ...
 
	return nil
}
```

### gRPC是什么？

基于go的**远程过程调用**。RPC 框架的目标就是让远程服务调用更加简单、透明，RPC 框架负责屏蔽底层的传输方式（TCP 或者 UDP）、序列化方式（XML/Json/ 二进制）和通信细节。服务调用者可以像调用本地接口一样调用远程的服务提供者，而不需要关心底层通信细节和调用过程。

## 微服务了解吗？

微服务是一种开发软件的架构和组织方法，其中软件由通过明确定义的 API 进行通信的小型独立服务组成。微服务架构使应用程序更易于扩展和更快地开发，从而加速创新并缩短新功能的上市时间。

## 服务发现是怎么做的？

主要有两种服务发现机制：**客户端发现**和**服务端发现**。

**客户端发现模式**：当我们使用客户端发现的时候，客户端负责决定可用服务实例的网络地址并且在集群中对请求负载均衡, 客户端访问**服务登记表**，也就是一个可用服务的数据库，然后客户端使用一种**负载均衡算法**选择一个可用的服务实例然后发起请求。

**服务端发现模式**：客户端通过**负载均衡器**向某个服务提出请求，负载均衡器查询服务注册表，并将请求转发到可用的服务实例。如同客户端发现，服务实例在服务注册表中注册或注销。

## ETCD用过吗？

**etcd**是一个**高度一致**的**分布式键值存储**，它提供了一种可靠的方式来存储需要由分布式系统或机器集群访问的数据。它可以优雅地处理网络分区期间的领导者**选举**，即使在领导者节点中也可以容忍机器故障。

etcd 是用**Go语言**编写的，它具有出色的跨平台支持，小的二进制文件和强大的社区。etcd机器之间的通信通过**Raft共识算法**处理。

## 你项目有优雅的启停吗？

所谓「优雅」启停就是在启动退出服务时要满足以下几个条件：

- **不可以关闭现有连接**（进程）
- 新的进程启动并「**接管**」旧进程
- 连接要**随时响应用户请求**，不可以出现拒绝请求的情况
- 停止的时候，必须**处理完既有连接**，并且**停止接收新的连接**。

为此我们必须引用**信号**来完成这些目的：

启动：

- 监听SIGHUP（在用户终端连接(正常或非正常)结束时发出）；
- 收到信号后将服务监听的文件描述符传递给新的子进程，此时新老进程同时接收请求；

退出：

- 监听SIGINT和SIGSTP和SIGQUIT等。
- 父进程停止接收新请求，等待旧请求完成（或超时）；
- 父进程退出。

实现：go1.8采用Http.Server内置的Shutdown方法支持优雅关机。 然后[fvbock/endless](https://link.zhihu.com/?target=http%3A//github.com/fvbock/endless)可以实现优雅重启。

### 持久化怎么做的？

所谓持久化就是将要保存的字符串写到硬盘等设备。

- 最简单的方式就是采用ioutil的WriteFile()方法将字符串写到磁盘上，这种方法面临**格式化**方面的问题。
- 更好的做法是将数据按照**固定协议**进行组织再进行读写，比如JSON，XML，Gob，csv等。
- 如果要考虑**高并发**和**高可用**，必须把数据放入到数据库中，比如MySQL，PostgreDB，MongoDB等。

## 对已经关闭的chan进行读写会怎么样？

- 读已经关闭的chan能一直读到东西，但是读到的内容根据通道内关闭前是否有元素而不同。
  - 如果chan关闭前，buffer内有元素还未读,会正确读到chan内的值，且返回的第二个bool值（是否读成功）为true。
  - 如果chan关闭前，buffer内有元素已经被读完，chan内无值，接下来所有接收的值都会非阻塞直接成功，返回 channel 元素的零值，但是第二个bool值一直为false。

写已经关闭的chan会panic。

## select的实现原理？

select源码位于`src\runtime\select.go`，最重要的`scase` 数据结构为：

```text
type scase struct {
	c    *hchan         // chan
	elem unsafe.Pointer // data element
}
```

scase.c为当前case语句所操作的channel指针，这也说明了一个case语句只能操作一个channel。

scase.elem表示缓冲区地址：

- caseRecv ： scase.elem表示读出channel的数据存放地址；
- caseSend ： scase.elem表示将要写入channel的数据存放地址；

select的主要实现位于：`select.go`函数：其主要功能如下：

1. 锁定scase语句中所有的channel
2. 按照随机顺序检测scase中的channel是否ready

2.1 如果case可读，则读取channel中数据，解锁所有的channel，然后返回(case index, true)

2.2 如果case可写，则将数据写入channel，解锁所有的channel，然后返回(case index, false)

2.3 所有case都未ready，则解锁所有的channel，然后返回（default index, false）

3. 所有case都未ready，且没有default语句

3.1 将当前协程加入到所有channel的等待队列

3.2 当将协程转入阻塞，等待被唤醒

4. 唤醒后返回channel对应的case index

4.1 如果是读操作，解锁所有的channel，然后返回(case index, true)

4.2 如果是写操作，解锁所有的channel，然后返回(case index, false)

## 说说context包的作用？你用过哪些，原理知道吗？

`context`可以用来在`goroutine`之间传递上下文信息，相同的`context`可以传递给运行在不同`goroutine`中的函数，上下文对于多个`goroutine`同时使用是安全的，`context`包定义了上下文类型，可以使用`background`、`TODO`创建一个上下文，在函数调用链之间传播`context`，也可以使用`WithDeadline`、`WithTimeout`、`WithCancel` 或 `WithValue` 创建的修改副本替换它，听起来有点绕，其实总结起就是一句话：**`context`的作用就是在不同的`goroutine`之间同步请求特定的数据、取消信号以及处理请求的截止日期**。

