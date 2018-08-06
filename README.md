# Initial page

* 变量声明语法:
  * var 变量名字 类型 = 表达式
    * 类型”或“= 表达式”两个部分可以省略其中的一个

| 类型 | 零值 |
| :--- | :--- |
| 数值 | 0 |
| 布尔 | false |
| 字符串 | 空字符串 |
| 接口或引用类型\(slice,map,chan和函数\) | nil |

数组或结构体等聚合类型对应的零值是每个元素或字段都是对应该类型的零值

### 变量声明

可以在一个声明语句中同时声明多组变量

```go
var i, j, k int // int, int, int
var b, f, s = true, 2.3, "four" // bool, float64, string
```

#### 简短变量声明\(只能出现在函数内部\)

以“名字 := 表达式”形式声明变量

```text
anim := gif.GIF{LoopCount: nframes}
freq := rand.Float64() * 3.0
t := 0.0
```

{% hint style="info" %}
简洁和灵活的特点，简短变量声明被广泛用于大部分的局部变量的声明和初始化

var形 式的声明语句往往是用于需要显式指定变量类型地方，或者因为变量稍后会被重新赋值而初 始值无关紧要的地方
{% endhint %}

* 简短变量声明语句也可以用来声明和初始化一组变量

```text
i, j := 0,1
```

{% hint style="info" %}
请记住“:=”是一个变量声明语句，而“=‘是一个变量赋值操作
{% endhint %}

* 简短变量声明左边的变量不一定需要全部都是未声明的变量, 但是最少得要有一个变量是未声明的, 同时简短变量声明会对已经声明过的变量进行赋值行为

{% hint style="info" %}
如果在函数体代码中有未使用的变量，则无法通过编译，不过全局变量声明但不使用是可以的。

即使变量声明后为变量赋值，依旧无法通过编译，需在某处使用它
{% endhint %}

### 指针

* 如果用“var x int”声明语句声明一个x变量，那么&x表达式（取x变量的内存地址）将产生一个 指向该整数变量的指针，指针对应的数据类型是 \*int
* _p 表达式对应p指针指向的变量的值。一般_ p 表达式读取指针指向的变量的值
* 同时因为 \*p 对应一个变量，所以该表达式也可以出现在赋值语句的左边，表 示更新指针所指向的变量的值
* 任何类型的指针的零值都是nil。如果 p != nil 测试为真，那么p是指向某个有效变量

{% hint style="info" %}
在Go语言中，返回函数中局部变量的地址也是安全的
{% endhint %}

* 调用f函数时创建局 部变量v，在局部变量地址被返回之后依然有效

```go
var p = f()
func f() *int {
    v := 1
    return &v
}
```

* 每次调用f函数都将返回不同的结果：

```go
fmt.Println(f() == f()) // "false"
```

{% hint style="info" %}
上面表达式中第一个f\(\)的返回值是一个变量v的地址,第二个f\(\)的返回值是另一个变量v的地址,虽然值都为1 但是地址是不相同的, 在函数返回之后变量v都无效了,但是返回值指向的内存地址的值任然是有效的
{% endhint %}

```go
func incr(p *int) int {
    *p++ // 非常重要：只是增加p指向的变量的值，并不改变p指针！！！
    return *p
}
v := 1
incr(&v) // side effect: v is now 2
fmt.Println(incr(&v)) // "3" (and v is 3)
```

#### new函数

* 表达式new\(T\)将创建一个T类型的匿名变 量，初始化为T类型的零值，然后返回变量地址，返回的指针类型为 \*T

```go
p := new(int) // p, *int 类型, 指向匿名的 int 变量
fmt.Println(*p) // "0"
*p = 2 // 设置 int 匿名变量的值为 2
fmt.Println(*p) // "2"
```

* 每次调用new函数都是返回一个新的变量的地址，因此下面两个地址是不同的：

```text
p := new(int)
q := new(int)
fmt.Println(p == q) // "false"
```

{% hint style="info" %}
new 只是一个预定义函数而不是一个关键字, 所以可以将new重新定义为别的类型

func delta\(old, new int\) int { return new - old }
{% endhint %}

### 赋值

```go
x = 1 // 命名变量的赋值
*p = true // 通过指针间接赋值
person.name = "bob" // 结构体字段赋值
count[x] = count[x] * scale // 数组、slice或map的元素赋值
```

#### 元组赋值

* 允许同时更新多个变量的值。在赋值之前，赋值语句 右边的所有表达式将会先进行求值，然后再统一更新左边对应变量的值

```go
x, y = y, x
a[i], a[j] = a[j], a[i]
```

* 计算公约数

```go
func gcd(x, y int) int {
    for y != 0 {
        x, y = y, x%y
    }
    return x
}

```

* 计算斐波那契数列

```go
func fib(n int) int {
    x, y := 0, 1
    for i := 0; i < n; i++ {
        x, y = y, x+y
    }
    return x
}
```

* 元组赋值也可以使一系列琐碎赋值更加紧凑（译注: 特别是在for循环的初始化部分）

```go
i, j, k = 2, 3, 5
```

{% hint style="info" %}
如果表达式太复杂的话，应该尽量避免过度使用元组赋值；因为每个变量单独赋值语句的 写法可读性会更好
{% endhint %}

* 变量声明一样，我们可以用下划线空白标识符 \_ 来丢弃不需要的值

```go
_, err = io.Copy(dst, src) // 丢弃字节数
_, ok = x.(T) // 只检测类型，忽略具体值
```

{% hint style="info" %}
赋值的规则: 类型必须完全匹配，nil可以赋值给任何 指针或引用类型的变量。
{% endhint %}

### 类型

* 一个类型声明语句创建了一个新的类型名称，和现有类型具有相同的底层结构。新命名的类 型提供了一个方法，用来分隔不同概念的类型，这样即使它们底层类型相同也是不兼容的。

```go
type 类型名字 底层类型
```

* 类型声明语句一般出现在包一级，因此如果新创建的类型名字的首字符大写，则在外部包也 可以使用

{% hint style="info" %}
对于中文汉字, Unicode标志都作为小写字母处理，因此中文的命名默认不能导出；
{% endhint %}

#### 类型转换操作

* 对于每一个类型T，都有一个对应的类型转换操作T\(x\)，用于将x转为T类型（译注：如果T是 指针类型，可能会需要用小括弧包装T，比如 \(\*int\)\(0\) ）
* 只有当两个类型的底层基础类型 相同时，才允许这种转型操作，或者是两者都是指向相同底层结构的指针类型
* 这些转换只 改变类型而不会影响值本身

底层数据类型决定了内部结构和表达方式，也决定是否可以像底层类型一样对内置运算符的 支持。

* 比较运算符 == 和 &lt; 也可以用来比较一个命名类型的变量和另一个有相同类型的变量，或有 着相同底层类型的未命名类型的值之间做比较。但是如果两个值有着不同的类型，则不能直 接进行比较：

```go
var c Celsius
var f Fahrenheit
fmt.Println(c == 0) // "true"
fmt.Println(f >= 0) // "true"
fmt.Println(c == f) // compile error: type mismatch类型不同
fmt.Println(c == Celsius(f)) // "true"!将f转为Celsius类型,因为两个值都为零值,所以为true
```

{% hint style="info" %}
许多类型都会定义一个String方法，因为当使用fmt包的打印方法时，将会优先使用该类型对 应的String方法返回的结果打印
{% endhint %}

### 包和文件

* 通常一个 包所在目录路径的后缀是包的导入路径；例如包gopl.io/ch1/helloworld对应的目录路径是 $GOPATH/src/gopl.io/ch1/helloworld
* 每个包都对应一个独立的名字空间, 要在外部引用该函数，必须显式使用image.Decode或 utf16.Decode形式访问
* 包还可以让我们通过控制哪些名字是外部可见的来隐藏内部实现信息

{% hint style="info" %}
在Go语言中，一个简 单的规则是：如果一个名字是大写字母开头的，那么该名字是导出的
{% endhint %}

{% hint style="info" %}
如果导入了一个包，但是又没有使用该包将被当作一个编译错误处理
{% endhint %}

* 我们可以用 一个特殊的init初始化函数来简化初始化工作。每个文件都可以包含多个init初始化函数

```go
func init() { /* ... */ }
```

* 这样的init初始化函数除了不能被调用或引用外，其他行为和普通函数类似。在每个文件中的 init初始化函数，在程序开始执行时按照它们声明的顺序被自动调用
* 每个包在解决依赖的前提下，以导入声明的顺序初始化，每个包只会被初始化一次
* 初始化 工作是自下而上进行的，main包最后被初始化

### 作用域

{% hint style="info" %}
注意作用域与生命周期的区别
{% endhint %}

{% hint style="info" %}
当前包的其它源文件无法访问在当前源文件导入的包
{% endhint %}

```go
if f, err := os.Open(fname); err != nil { // compile error: unused: f
    return err
}
f.ReadByte() // compile error: undefined f
f.Close() // compile error: undefined f
```

* 上面代码块的问题出在变量f 的作用域没有明确, 可以修改为如下

```go
f, err := os.Open(fname)
if err != nil {
    return err
}
f.ReadByte()
f.Close()

//或者

if f, err := os.Open(fname); err != nil {
    return err
} else {
    // f and err are visible here too
    f.ReadByte()
    f.Close()
}
```

{% hint style="info" %}
但这不是Go语言推荐的做法，Go语言的习惯是在if中处理错误然后直接返回，这样可以确保 正常执行的语句不需要代码缩进
{% endhint %}

在下面代码中

```go
var cwd string
func init() {
    cwd, err := os.Getwd() // compile error: unused: cwd
    if err != nil {
        log.Fatalf("os.Getwd failed: %v", err)
    }
}
```

* 虽然cwd在外部已经声明过，但是 := 语句还是将cwd和err重新声明为新的局部变量。因为内 部声明的cwd将屏蔽外部的声明，因此上面的代码并不会正确更新包级声明的cwd变量

## 基础数据类型

{% hint style="info" %}
Go语言将数据类型分为四类：基础类型、复合类型、引用类型和接口类型
{% endhint %}

### 整型

* Go语言同时提供了有符号和无符号类型的整数运算。这里有int8、int16、int32和int64四种截 然不同大小的有符号整形数类型，分别对应8、16、32、64bit大小的有符号整形数，与此对 应的是uint8、uint16、uint32和uint64四种无符号整形数类型
* 这里还有两种一般对应特定CPU平台机器字大小的有符号和无符号整数int和uint；其中int是应 用最广泛的数值类型。这两种类型都有同样的大小，32或64bit，但是我们不能对此做任何的 假设；因为不同的编译器即使在相同的硬件平台上可能产生不同的大小
* Unicode字符rune类型是和int32等价的类型，通常用于表示一个Unicode码点。这两个名称可 以互换使用。同样byte也是uint8类型的等价类型，byte类型一般用于强调数值是一个原始的 数据而不是一个小的整数
* 最后，还有一种无符号的整数类型uintptr，没有指定具体的bit大小但是足以容纳指针。uintptr 类型只有在底层编程是才需要，特别是Go语言和C语言函数库或操作系统接口相交互的地 方。

{% hint style="info" %}
不管它们的具体大小，int、uint和uintptr是不同类型的兄弟类型。其中int和int32也是不同的类 型，即使int的大小也是32bit，在需要将int当作int32类型的地方需要一个显式的类型转换操 作，反之亦然
{% endhint %}

* 下面是Go语言中关于算术运算、逻辑运算和比较运算的二元运算符，它们按照先级递减的顺 序的排列

```text
* / % << >> & &^
+ - | ^
== != < <= > >=
&&
||
```

* 对于上表中**前两行**的运算符，例如+运算符还有一个与赋值相结合的对应运算符+=，可以用于 简化赋值语句。

{% hint style="info" %}
算术运算符+、-、 \* 和 / 可以适用与于整数、浮点数和复数，但是取模运算符%仅用于整数 间的运算
{% endhint %}

{% hint style="info" %}
在Go语言中，%取模运算 符的符号和被取模数的符号总是一致的，因此 -5%3 和 -5%-3 结果都是-2
{% endhint %}

* 两个相同的整数类型可以使用下面的二元比较运算符进行比较；比较表达式的结果是布尔类型

```text
== equal to
!= not equal to
< less than
<= less than or equal to
> greater than
>= greater than or equal to
```

{% hint style="info" %}
事实上，布尔型、数字类型和字符串等基本类型都是可比较的，也就是说两个相同类型的值 可以用==和!=进行比较。此外，整数、浮点数和字符串可以根据比较结果排序。许多其它类 型的值可能是不可比较的，因此也就可能是不可排序的。对于我们遇到的每种类型，我们需 要保证规则的一致性
{% endhint %}

* 对于整数，+x是0+x的简写，-x则是0-x的简写；对于浮点数和复数，+x就是x，-x则是x 的负数
* Go语言还提供了以下的bit位操作运算符，前四个不区分是否有符号

```text
& 位运算 AND
| 位运算 OR
^ 位运算 XOR        //二元运算时是按位异或, 用作一元运算时是按位取反
&^ 位清空 (AND NOT)
<< 左移
>> 右移
```

* 当使用fmt包打印一个数值时，我们可以用%d、%o或%x参数控制输出的进制格式，就像下面的例子

```go
o := 0666
fmt.Printf("%d %[1]o %#[1]o\n", o) // "438 666 0666"
x := int64(0xdeadbeef)
fmt.Printf("%d %[1]x %#[1]x %#[1]X\n", x)
// Output:
// 3735928559 deadbeef 0xdeadbeef 0XDEADBEEF
```

{% hint style="info" %}
请注意fmt的两个使用技巧。通常Printf格式化字符串包含多个%参数时将会包含对应相同数量 的额外操作数，但是%之后的 \[1\] 副词告诉Printf函数再次使用第一个操作数。第二，%后 的 \# 副词告诉Printf在用%o、%x或%X输出时生成0、0x或0X前缀
{% endhint %}

* 字符使用 %c 参数打印，或者是用 %q 参数打印带单引号的字符

```go
ascii := 'a'
unicode := '国'
newline := '\n'
fmt.Printf("%d %[1]c %[1]q\n", ascii) // "97 a 'a'"
fmt.Printf("%d %[1]c %[1]q\n", unicode) // "22269 国 '国'"
fmt.Printf("%d %[1]q\n", newline) // "10 '\n'"
```

### 浮点数

* Go语言提供了两种精度的浮点数，float32和float64。它们的算术规范由IEEE754浮点数国际 标准定义，该浮点数规范被所有现代的CPU支持

{% hint style="info" %}
待补充
{% endhint %}

### 布尔型

* 一个布尔类型的值只有两种：true和false
* 布尔值可以和&&（AND）和\|\|（OR）操作符结合，并且可能会有短路行为：如果运算符左边 值已经可以确定整个布尔表达式的值，那么运算符右边的值将不在被求值，因此下面的表达 式总是安全的：

```text
s != "" && s[0] == 'x'
```

{% hint style="info" %}
&& 的优先级比 \|\| 高
{% endhint %}

```text
if 'a' <= c && c <= 'z' ||
    'A' <= c && c <= 'Z' ||
    '0' <= c && c <= '9' {
    // ...ASCII letter or digit...
}
```

* 布尔值并不会隐式转换为数字值0或1，反之亦然。必须使用一个显式的if语句辅助转换：

```go
// btoi returns 1 if b is true and 0 if false.
//布尔型到数字的转换
func btoi(b bool) int {
    if b {
        return 1
    }
    return 0
}

// itob reports whether i is non-zero.
//数字到布尔型的转换
func itob(i int) bool { return i != 0}
```

### 字符串

* 一个字符串是一个不可改变的字节序列
* 子字符串操作s\[i:j\]基于原始的s字符串的第i个字节开始到第j个字节（并不包含j本身）生成一 个新字符串
* 其中+操作符将两个字符串链接构造一个新字符串
* 字符串可以用==和&lt;进行比较；比较通过逐个字节比较完成的，因此比较的结果是字符串自然 编码的顺序
* 因为字符串是不可修改的，因此尝试修改字符串内部数据的操作也是被禁止的：

```text
s[0] = 'L' // compile error: cannot assign to s[0]
```



