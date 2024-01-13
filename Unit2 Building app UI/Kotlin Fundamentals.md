## 01. when statement

`when` 语句通过形参接受单个值。然后，系统依序评估每个条件。接着，系统执行所满足的第一个条件对应的正文。各条件和正文均以箭头 (`->`) 分隔。

与 `if/else` 语句类似，**每对条件和正文都称为 `when` 语句的一个分支**。

此外，与 `if/else` 语句类似，您还可以在 `when` 语句中添加一个 **`else` 分支作为最终条件**。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-conditionals/img/2f7c0a1e312a2581.png?hl=zh-cn" alt="展示 when 语句详解的示意图。其开头是一个 when 关键字，后跟一对圆括号，圆括号中包含一个形参代码块。接下来是一对大括号，其中包含三行情形代码。每行代码中都包含一个条件代码块，后跟一个箭头符号和一个正文代码块。图中备注称，系统会依序评估每行情形代码。" style="zoom:50%;" />

```kotlin
fun main() {
    val trafficLightColor = "Yellow"

    when (trafficLightColor) {
        "Red" -> println("Stop")
        "Yellow" -> println("Slow")
        "Green" -> println("Go")
        else -> println("Invalid traffic-light color")
    }
}
```

### 在 when 语句中编写更复杂的条件

可以使用英文逗号 (`,`)、`in` 关键字和 `is` 关键字来组成更复杂的 `when` 条件。

1. **使用英文逗号（`,`） 处理多个条件**

   ```kotlin
   fun main() {
       val x = 3
       when (x) {
           2, 3, 5, 7 -> println("x is a prime number between 1 and 10.")
           else -> println("x isn't a prime number between 1 and 10.")
       }
   }
   ```

2. **使用 `in` 关键字处理一系列条件**

   - 判断形参值是否等于范围起点到范围终点之间的任意值

   ```kotlin
   fun main() {
       val x = 3
       when (x) {
           2, 3, 5, 7 -> println("x is a prime number between 1 and 10.")
           in 1..10 -> println("x is a number between 1 and 10, but not a prime number.")
           else -> println("x isn't a prime number between 1 and 10.")
       }
   }
   ```

3. **使用 `is` 关键字检查数据类型**

   - 使用 `is` 关键字作为条件，以检查所评估值的**数据类型**。

   ```kotlin
   fun main() {
     val x: Any = 4
     when (x) {
       2, 3, 5, 7 -> println("x is a prime number between 1 and 10.")
       in 1 .. 10 -> println("x is a number between 1 and 10, but not a prime number.")
       is Int -> println("x is an integer number, but not between 1 and 10.")
       else -> println("x isn't a prime number between 1 and 10.")
     }
   }
   ```

### 使用 if/else 和 when 作为表达式

使用条件作为表达式，以便为每个条件分支返回不同的值。

条件表达式的**每个分支的最后一个代码行需要返回一个值或表达式**，并且要将条件分配给一个变量。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-conditionals/img/a6ff7ba09d3cdea3.png?hl=zh-cn" alt="一个示意图，描述了一个 if/else 表达式，其 val 关键字后跟一个名称代码块、一个等号、一个 if 关键字、圆括号（其中包含一个条件）、一对大括号（其中包含正文 1 代码块）、一个 else 关键字以及另一对大括号（其中包含一个正文代码块）。" style="zoom:50%;" />

如果正文仅包含一个返回值或表达式，您可以移除大括号，使代码更简洁。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-conditionals/img/411c2aff894a72e2.png?hl=zh-cn" alt="一个示意图，描述了一个 if/else 表达式，其 val 关键字后跟一个名称代码块、一个等号、一个 if 关键字、圆括号（其中包含一个条件）、一个表达式 1 代码块、一个 else 关键字以及一个表达式 2 代码块。" style="zoom:67%;" />

```kotlin
fun main() {
    val trafficLightColor = "Black"

    val message =
      if (trafficLightColor == "Red") "Stop"
      else if (trafficLightColor == "Yellow") "Slow"
      else if (trafficLightColor == "Green") "Go"
      else "Invalid traffic-light color"

    println(message)
}
```

```kotlin
fun main() {
    val trafficLightColor = "Amber"

    val message = when(trafficLightColor) {
        "Red" -> "Stop"
        "Yellow", "Amber" -> "Proceed with caution."
        "Green" -> "Go"
        else -> "Invalid traffic-light color"
    }
    println(message)
}
```

`when` 语句不是必须定义 `else` 分支。但在大多数情况下，`when` 表达式需要 `else` 分支，因为 `when` 表达式需要返回值。



## 02. Use nullability in Kotlin

### 使用 nullable 变量

```kotlin
fun main() {
	val favoriteActor = null
	println(favoriteActor)
}
```

可以为使用 `var` 关键字定义的变量重新赋值为相同类型的不同值。也可以将变量重赋值为 `null`。

```kotlin
fun main() {
	var favoriteActor: String = "Sandra Oh"
  favoriteActor = null
}
```

系统会显示以下错误消息：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/20fa3d758a5de502.png?hl=zh-cn" alt="内容为“Null cannot be a value of a non-null type String”的警告消息。" style="zoom: 50%;" />

在 Kotlin 中，有 nullable 类型与 non-nullable 类型之分：

- nullable 类型是指可以存储 `null` 值的变量。
- non-nullable 类型是指不能存储 `null` 值的变量。

只有当明确声明某个变量可以存储 `null` 值时，该变量才属于 nullable 类型。正如错误消息所示，`String` 数据属于 non-nullable 类型，因此您无法为该变量重新赋予 `null`。

在 Kotlin 中声明可为 null 的变量：只需在相应类型的末尾添加 `?` 运算符。

- 例如，`String?` 类型可以存储字符串或 `null`，而 `String` 类型只能存储字符串。

![此图展示了如何声明可为 null 类型的变量。这种变量以 var 关键字开头，后面依次是变量块的名称、分号、变量的类型、问号、等号和值块。类型块和问号使用“Nullable type”文字标示，表示该类型后接问号即变成可为 null 类型。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/c3bbad8de6afdbe9.png?hl=zh-cn)

```kotlin
fun main() {
    var favoriteActor: String? = "Sandra Oh"
    favoriteActor = null
}
```

### 处理 nullable 变量

如需访问 nullable 变量的方法和属性：

```kotlin
fun main() {
    var favoriteActor: String? = "Sandra Oh"
    println(favoriteActor.length)
}
```

系统会显示以下错误消息：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/5c5e60b58c31d162.png?hl=zh-cn" alt="内容为“Only safe or non-null asserted calls are allowed on a nullable receiver of type String?”的错误消息。" style="zoom:50%;" />

如果在应用运行期间尝试访问 `null` 变量的成员（称为 `null` 引用），应用会**因 `null` 变量不含任何属性或方法而崩溃**。此类崩溃称为“运行时错误”，即在代码完成编译和运行后发生的错误。

由于 Kotlin 具有 `null` 安全特性，Kotlin 编译器会对 nullable 类型强制执行 `null` 检查，以免发生此类运行时错误。

- “`Null` 检查”是指在访问变量并将其视为 non-nullable 类型之前，检查该变量是否可为 `null` 的过程。
- 如果您想将 nullable 的值用作 non-nullable 类型，则需要明确执行 `null` 检查。

### 使用 `?.` 安全调用运算符

`?.` 安全调用运算符可让您更安全地访问可为 null 的变量。Kotlin 编译器会阻止对 `null` 引用的成员访问，并返回 `null`。

```kotlin
fun main() {
    var favoriteActor: String? = "Sandra Oh"
    println(favoriteActor?.length)
  
  	favoriteActor = null
  	println(favoriteActor?.length)
}
```

### 使用 `!!` 非 null 断言运算符

也可以使用 `!!` 非 null 断言运算符来访问 nullable 变量的方法或属性。

使用 `!!` 非 null 断言运算符，即表示**断言变量的值不是 `null`**。

- 与 `?.` 安全调用运算符不同，当 nullable 变量确实为 `null` 时，使用 `!!` 非 null 断言运算符可能会导致系统抛出 **`NullPointerException` 错误**。
- 因此，只有在变量始终为 null 或设置了适当的异常处理时，才应使用该断言运算符。如果异常未得到处理，便会导致运行时错误。

```kotlin
fun main() {
    var favoriteActor: String? = "Sandra Oh"
    println(favoriteActor!!.length) // 9
  
  	favoriteActor = null
    println(favoriteActor!!.length) // NullPointerException
}
```

系统会显示 `NullPointerException` 错误，内容如下：

![内容为“Exception in thread "main" java.lang.NullPointerException”的错误消息。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/c74ab53164de0c01.png?hl=zh-cn)

### 使用 if/else 条件

您可以在 `if/else` 条件中使用 `if` 分支来执行 `null` 检查。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/326d68521327f229.png?hl=zh-cn" alt="此图展示了可为 null 的变量块，后面依次跟感叹号、等号和 null。" style="zoom:50%;" />

将 null 检查与 `if/else` 语句结合使用， `if` 条件为 `nullableVariable != null` 表达式。具有以下优点：

- `if` 分支会假定变量不为 `null`。因此，在这个语句块中可以随意访问变量的方法或属性，如同变量是 non-nullable 一般，而不必使用 `?.` 安全调用运算符或 `!!` 非 null 断言运算符。
- `else` 分支会假定变量为 `null`。因此，在这个语句块中，可以添加在变量为 `null` 时运行的语句。`else` 分支是可选的。

如果有多行代码使用可为 null 的变量，那么将 `null` 检查与 `if` 条件搭配使用会更方便。相比之下，`?.`安全调用运算符更适用于对可为 null 变量的单次引用。

### 使用 `if/else` 表达式

可以将 `null` 检查与 `if/else` 表达式结合使用，以将 nullable 变量转换为 non-nullable 变量。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/15737297f364000b.png?hl=zh-cn" alt="此图描述了 if/else 表达式，其 val 关键字后面依次跟一个“name”块、一个冒号、一个“non-null type”块、一个等号、if 关键字、一对内含条件的圆括号、一对内含“body 1”的大括号、else 关键字，以及另一对内含“body 2”块的大括号。" style="zoom:50%;" />

```kotlin
fun main() {
    val favoriteActor: String? = "Sandra Oh"

    val lengthOfName = if(favoriteActor != null) {
      favoriteActor.length
    } else {
      0
    }
  
   println("The number of characters in your favorite actor's name is $lengthOfName.")
}
```

### 使用 `?:` Elvis **运算符**

`?:` Elvis 运算符可以与 `?.` 安全调用运算符搭配使用。使得可以在 `?.` 安全调用运算符返回 `null` 时添加默认值。这与 `if/else` 表达式类似，但更为常用。

如果该变量不为 `null`，则执行 `?:` Elvis 运算符之前的表达式；如果变量为 `null`，则执行 `?:` Elvis 运算符之后的表达式。

![此图展示了 val 关键字，后面依次跟名称块、等号、可为 null 的变量块、问号、点、方法或属性块、问号、冒号以及默认值块。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-nullability/img/d450a60e3be7418d.png?hl=zh-cn)

```kotlin
fun main() {
    val favoriteActor: String? = "Sandra Oh"

    val lengthOfName = favoriteActor?.length ?: 0

    println("The number of characters in your favorite actor's name is $lengthOfName.")
}
```



## 03. Use classes and objects in Kotlin

OOP 涵盖以下四个基本概念：

- **封装**：<u>将相关属性和针对这些属性的操作方法封装在类中</u>。以手机为例，它封装了摄像头、显示屏、存储卡以及其他一些硬件和软件组件。您不必关心这些组件的内部连接方式。
- **抽象**：<u>封装的扩展，其目的是尽可能隐藏内部实现逻辑</u>。例如，如果您要使用手机拍照，只需打开相机应用，将手机对准要拍摄的场景，然后点击按钮即可。您不需要了解相机应用的构建方式或手机上相机硬件的实际运作方式。简而言之，相机应用的内部机制以及移动设备相机的拍照方式已经过抽象，可让您专心执行重要的任务。
- **继承**：<u>可通过建立父子关系来基于其他类的特性和行为构建类。</u>例如，不同的制造商生产各种运行 Android OS 的移动设备，但每种设备的界面都不同。换言之，制造商会继承 Android OS 的功能，并在这个基础上构建自己的自定义功能。
- **多态性**：Polymorphism（多态性）这个单词是希腊语词根“poly-”（意为许多）和“morphism”（意为形态）的合成词。<u>多态性是指以单一、通用的方式使用不同对象的能力</u>。例如，当您将蓝牙音箱连接到手机后，手机只需要知道目前有设备可通过蓝牙播放音频。虽然可供您选择的蓝牙音箱有很多种，但手机不必知道各个音箱的具体使用方式。

属性委托：它们提供的可重复使用的代码能让您用简洁的语法来管理属性值。

### 3.1 定义类

定义类时，您需要指定该类的所有对象都应具有的属性和方法。

以下是建议遵循的**类命名惯例**：

- 可以选择任何想要的类名称，但不要将 Kotlin [关键字](https://kotlinlang.org/docs/keyword-reference.html)用作类名称，例如 `fun` 关键字。
- 类名称采用 PascalCase 大小写形式编写，因此每个单词都以大写字母开头，且各个单词之间没有空格。以“SmartDevice”为例，每个单词的第一个字母都大写，且单词之间没有空格。

类由以下三大部分组成：

- **属性**：用于指定类实例属性的变量。
- **方法**：包含类的行为和操作的函数。
- **构造函数**：一种特殊的成员函数，用于在定义类的整个程序中创建类的实例。

![该语法是以类关键字开头，后跟名称和一对左/右大括号。大括号之间包含用于描述蓝图的类主体。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/9a07f83c06449f38.png?hl=zh-cn)

在 Kotlin 中，`Int`、`Float`、`String` 和 `Double` 等数据类型被定义为类。

在定义如以下代码段所示的变量时，将创建 `Int` 类的对象（该类使用值 `1` 进行实例化）：

```kotlin
val number: Int = 1
```

### 3.2 创建类的实例

Kotlin 运行时使用类（即蓝图）来创建该特定类型的对象。

```kotlin
fun main() {
	val smartTvDevice = SmartDevice()
}
```

### 3.3 定义类方法

类可以执行的操作在类中被定义为函数。

在类中定义函数的语法与您之前学习的语法相同。唯一的区别在于，该函数是放在类主体中。

**在类主体中定义函数时，该函数称为成员函数或==方法==，用于表示类的行为**。

```kotlin
class SmartDevice {
	fun turnOn() {
		println("Smart device is turned on.")
	}
  
  fun turnOff() {
    println("Smart device is turned off.")
  }
}
```

#### **对象**调用方法

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/fc609c15952551ce.png?hl=zh-cn" alt="fc609c15952551ce.png" style="zoom:80%;" />

```kotlin
fun main() {
		val smartTvDevice = SmartDevice()
  	smartTvDevice.turnOn()
  	smartTvDevice.turnOff()
}
```

### 3.4 定义类属性

方法用于定义类可以执行的操作，而属性用于定义类的特性或数据属性。

从根本上来讲，属性是在类主体（而非函数主体）中定义的变量。也就是说，用于定义属性和变量的语法都相同。

- 可以使用 `val` 关键字定义不可变属性，使用 `var` 关键字定义可变属性。

```kotlin
class SmartDevice {
		val name = "Android TV"
  	val category = "Entertainment"
  	var deviceStatus = "online"
	
		fun turnOn() {
        println("Smart device is turned on.")
    }

    fun turnOff() {
        println("Smart device is turned off.")
    }
}
```

```kotlin
fun main() {
		val smartTvDevice = SmartDevice()
		println("Device name is: ${smartTvDevice.name}")
		smartTvDevice.turnOn()
    smartTvDevice.turnOff()
}
```

### 3.5 属性中的 getter 和 setter 函数

属性的用途比变量更广泛。

定义**==可变属性==**的完整语法是以变量定义开头，后跟可选的 `get()` 和 `set()` 函数。语法如下图所示：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/f2cf50a63485599f.png?hl=zh-cn" alt="f2cf50a63485599f.png" style="zoom:50%;" />

如果您没有为属性定义 getter 和 setter 函数，Kotlin 编译器会在内部创建这些函数：

```kotlin
var speakerVolume = 2
		get() = field
		set(value) {
				field = value
		}
```

**==不可变属性==**的完整语法有以下两处差异：

- 以 `val` 关键字开头。
- `val` 类型的变量为只读变量，因此不含 `set()` 函数。

Kotlin 属性使用后备字段在内存中存储值。从根本上来讲，后备字段是在属性内部定义的类变量。后备字段的作用域限定为相应属性，这意味着您只能通过 `get()` 或 `set()` 属性函数访问该字段。



如果想读取 `get()` 函数中的属性值或更新 `set()` 函数中的值，您需要使用对应属性的后备字段。该字段是由 Kotlin 编译器自动生成，并通过 `field` 标识符来引用。

例如，如果您要更新 `set()` 函数中的属性值，可使用 `set()` 函数的形参（称为 `value` 形参），并将其赋给 `field` 变量，如以下代码段所示：

```kotlin
var speakerVolume = 2
		set(value) {
				field = value
		}
```

请勿使用属性名称来获取或设置值。例如，在 `set()` 函数中，如果您尝试将 `value` 形参赋给 `speakerVolume` 属性本身，代码就会进入无限循环，因为 Kotlin 运行时会尝试更新 `speakerVolume` 属性的值，而不断触发对 setter 函数的调用。



e.g. 若要确保赋给 `speakerVolume` 属性的值介于 0 到 100 之间，您可以实现 setter 函数，如以下代码段所示：

```kotlin
var speakerVolume = 2
		set(value) {
			if (value in 0 .. 100) {
				field = value
			}
		}
```

### 3.6 定义构造函数

构造函数的主要用途是**指定类对象的创建方式**。

在实例化类的对象时，系统会执行构造函数中的代码。您可以定义包含形参或不含形参的构造函数。

#### 默认构造函数

默认构造函数不含形参。定义默认构造函数的做法如以下代码段所示：

```kotlin
class SmartDevice constructor() {
	...
}
```

Kotlin 旨在简化代码，因此：

- **如果构造函数中没有任何注解或可见性修饰符**，您**可以移除 `constructor` 关键字**。
- 如果构造函数中没有任何形参，您还可以移除圆括号。

```kotlin
class SmartDevice {
	...
}
```

Kotlin 编译器会自动生成默认构造函数。

#### 定义形参化构造函数

在 `SmartDevice` 类中，`name` 和 `category` **属性不可变**。您需要<u>确保 `SmartDevice` 类的所有实例都会初始化 `name` 和 `category` 属性</u>。

```kotlin
class SmartDevice(val name: String, val category: String) {
		var deviceStatus = "online"
		
 		fun turnOn() {
        println("Smart device is turned on.")
    }

    fun turnOff() {
        println("Smart device is turned off.")
    }
}
```

该构造函数可接受形参来设置其属性，因此，为此类实例化对象的方式也会随之更改。

```kotlin
SmartDevice(name = "Android TV", category = "Entertainment")
```

如果类没有默认构造函数，而您尝试在不使用实参的情况下实例化对象，编译器就会报告错误。

Kotlin 中的构造函数主要有两类：

- **主要构造函数**：一个类只能有一个主要构造函数（在类标头中定义）。
  - 主要构造函数可以是**默认构造函数**，也可以是**形参化构造函数**。
  - **==主要构造函数没有主体，表示其中不能包含任何代码。==**
- **辅助构造函数**：<u>一个类可以有多个辅助构造函数</u>。
  - 可以定义**包含形参或不含形参的辅助构造函数**。
  - 辅助构造函数可以初始化类，具有包含初始化逻辑的主体。
  - 如果类有主要构造函数，则每个辅助构造函数都需要初始化该主要构造函数。



可以使用**主要构造函数**来初始化类标头中的属性。传递给构造函数的实参会赋给属性。

定义主要构造函数的语法是以类名称开头，后面依次跟 `constructor` 关键字和一对圆括号。圆括号中包含主要构造函数的形参。如果有多个形参，请用英文逗号分隔形参定义。定义主要构造函数的完整语法如下图所示：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/aa05214860533041.png?hl=zh-cn" alt="aa05214860533041.png" style="zoom:50%;" />

辅助构造函数包含在类的主体中，其语法包括以下三个部分：

- **辅助构造函数声明**：辅助构造函数定义以 `constructor` 关键字开头，后跟圆括号。可视情况在圆括号中包含辅助构造函数所需的形参。
- **主要构造函数初始化**：初始化以冒号开头，后面依次跟 `this` 关键字和一对圆括号。可视情况在圆括号中包含主要构造函数所需的形参。
- **辅助构造函数主体**：在主要构造函数的初始化后跟一对大括号，其中包含辅助构造函数的主体。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/2dc13ef136009e98.png?hl=zh-cn" alt="2dc13ef136009e98.png" style="zoom:50%;" />

例如，假设您想集成由智能设备提供商开发的 API。不过，该 API 会返回 `Int` 类型的状态代码来指明初始设备状态。

- 如果设备处于离线状态，该 API 会返回 `0` 值；
- 如果设备处于在线状态，则返回 `1` 值。
- 对于任何其他整数值，系统会将状态视为“未知”。

您可以在 `SmartDevice` 类中创建辅助构造函数，以将此 `statusCode` 形参转换为字符串表示形式，如以下代码段所示：

```kotlin
class SmartDevice(val name: String, val category: String) {
		var deviceStatus = "online"
		
		constructor(name: String, category: String, statusCode: Int) : this(name, category) {
				deviceStatus = when (statusCode) {
						0 -> "offline"
						1 -> "online"
						else -> "unknown"
				}
		}
}
```

### 3.7 实现类之间的关系

继承可让您基于其他类的特性和行为构建类。您可以借助这种强大机制编写可重复使用的代码，并在类之间建立关系。

在 Kotlin 中，所有类默认都是最终类，也就是说您无法扩展这些类，因此必须定义类之间的关系。

在父类中的 `class` 关键字前面添加 `open` 关键字，使其具有扩展性。

- `open` 关键字会告知编译器此类可供扩展，因此其他类现在可对其进行扩展。

```kotlin
open class SmartDevice(val name: String, val category: String) {
  	...
}
```

创建子类的语法：

在构造函数的右圆括号后面，依次跟空格、冒号、另一个空格、父类名称以及一对圆括号。如有必要，可在圆括号中包含父类构造函数所需的形参。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/1ac63b66e6b5c224.png?hl=zh-cn" alt="1ac63b66e6b5c224.png" style="zoom:50%;" />

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {
		var speakerVolume = 2
  			set(value) {
            if (value in 0..100) {
              	field = value
            }
        }
  
  	var channelNumber = 1
  			set(value) {
          	if (value in 0..200) {
              	field = value
            }
        }
  
  	fun increaseSpeakerVolume() {
      	speakerVolume++
      	println("Speaker volume increased to $speakerVolume.")
    }
  
  	fun nextChannel() {
      	channelNumber++
      	println("Channel number increased to $channelNumber.")
    }
}
```

`SmartTvDevice` 的 `constructor` 定义**没有指定属性是可变的还是不可变的**，这意味着：

- `deviceName` 和 `deviceCategory` 形参**只是 `constructor` 形参，而不是类属性**。
- **无法在类中使用这些形参，只能将其传递给父类构造函数。**

```kotlin
class SmartLightDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {
		var brightnessLevel = 0
  			set(value) {
          	if (value in 0..100) {
              	field = value
            }
        }
  
  	fun increaseBrightness() {
      	brightnessLevel++
      	println("Brightness increased to $brightnessLevel.")
    }
}
```

#### 类之间的关系

如果使用继承，您就需要在两个类之间建立 “IS-A 关系”。如果对象是继承自某个类，也会是该类的实例。

在 HAS-A 关系中，**对象可以拥有其他类的实例**，且实际上不必是该类本身的实例。这些关系的简要表示形式如以下示意图所示：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/43ebe1f550d6c614.png?hl=zh-cn" alt="HAS-A 和 IS-A 关系的简要表示形式。" style="zoom:50%;" />

#### IS-A 关系

如果在 `SmartDevice` 父类和 `SmartTvDevice` 子类之间指定 IS-A 关系，即表示 `SmartDevice` 父类可以执行的操作，`SmartTvDevice` 子类也可执行。这种关系是单向的。

```kotlin
// Smart TV IS-A smart device.
class SmartTvDevice : SmartDevice() {
}
```

#### HAS-A 关系

HAS-A 关系是指定两个类之间的关系的另一种方式。两个类之间的 HAS-A 关系也称为“组合”。

```kotlin
// The SmartHome class HAS-A smart TV device and smart light.
class SmartHome(
  val smartTvDevice: SmartTvDevice,
  val smartLightDevice: SmartLightDevice
) {
		fun turnOnTv() {
      	smartTvDevice.turnOn()
    }
  
  	fun turnOffTv() {
        smartTvDevice.turnOff()
    }
  
  	fun increaseTvVolume() {
        smartTvDevice.increaseSpeakerVolume()
    }

    fun changeTvChannelToNext() {
        smartTvDevice.nextChannel()
    }
  
  	fun turnOnLight() {
        smartLightDevice.turnOn()
    }

    fun turnOffLight() {
        smartLightDevice.turnOff()
    }
  
  	fun increaseLightBrightness() {
        smartLightDevice.increaseBrightness()
    }
  
  	fun turnOffAllDevices() {
        turnOffTv()
        turnOffLight()
    }
}
```



#### 替换子类中的父类方法

虽然所有智能设备都支持开启和关闭功能，但它们的执行方式各有不同。如需提供这种特定于设备的行为，您需要替换父类中定义的 `turnOn()` 和 `turnOff()` 方法。替换意味着要拦截操作，通常是手动控制。

替换方法时，**子类中的方法会中断父类中定义的方法的执行**，并提供其自有的执行内容。

```kotlin
class SmartDevice(val name: String, val category: String) {
		var deviceStatus = "online"
		
		open fun turnOn() {}
		open fun turnOff() {}
}
```

```kotlin
class SmartLightDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {

    var brightnessLevel = 0
        set(value) {
            if (value in 0..100) {
                field = value
            }
        }

    fun increaseBrightness() {
        brightnessLevel++
        println("Brightness increased to $brightnessLevel.")
    }

    // !
    override fun turnOn() {
      	deviceStatus = "on"
      	brightnessLevel = 2
      	println("$name turned on. The brightness level is $brightnessLevel.")
    }
      
    override fun turnOff() {
      	deviceStatus = "off"
      	brightnessLevel = 0
      	println("Smart Light turned off")
    }
}
```

`override` 关键字会告知 Kotlin 运行时去**执行子类所定义方法中包含的代码**。

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {

    var speakerVolume = 2
        set(value) {
            if (value in 0..100) {
                field = value
            }
        }

    var channelNumber = 1
        set(value) {
            if (value in 0..200) {
                field = value
            }
        }

    fun increaseSpeakerVolume() {
        speakerVolume++
        println("Speaker volume increased to $speakerVolume.")
    }

    fun nextChannel() {
        channelNumber++
        println("Channel number increased to $channelNumber.")
    }

    override fun turnOn() {
      	deviceStatus = "on"
      	println(
            "$name is turned on. Speaker volume is set to $speakerVolume and channel number is " +
                "set to $channelNumber."
        )
    }
  
    override fun turnOff() {
        deviceStatus = "off"
        println("$name turned off")
    }
}
```

```kotlin
fun main() {
		var smartDevice: SmartDevice = SmartTvDevice("Android TV", "Entertainment")
		smartDevice.turnOn()
  
  	smartDevice = SmartLightDevice("Google Light", "Utility")
    smartDevice.turnOn()
}
// Android TV is turned on. Speaker volume is set to 2 and channel number is set to 1.
// Google Light turned on. The brightness level is 2.
```

这是一个关于**多态性**的示例。代码会对 `SmartDevice` 类型的变量调用 `turnOn()` 方法，并能根据变量的实际值来执行 `turnOn()` 方法的不同实现。

#### 使用 `super` **关键字**在子类中重复使用父类代码

若要从子类调用父类中被替换的方法，您需使用 `super` 关键字。从父类调用方法与从类外部调用该方法类似。请不要在对象和方法之间使用 `.` 运算符，而是使用 `super` 关键字，后者会告知 Kotlin 编译器对父类（而不是子类）调用方法。

从父类调用方法的语法是以 `super` 关键字开头，后面依次跟 `.` 运算符、函数名称和一对圆括号。可视情况在圆括号中包含相应实参。语法如下图所示：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/18cc94fefe9851e0.png?hl=zh-cn" alt="18cc94fefe9851e0.png" style="zoom:50%;" />

```kotlin
open class SmartDevice(val name: String, val category: String) {

    var deviceStatus = "online"

    open fun turnOn() {
        deviceStatus = "on"
    }

    open fun turnOff() {
        deviceStatus = "off"
    }
}
```

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {
		// ...

    override fun turnOn() {
        super.turnOn()
        println(
            "$name is turned on. Speaker volume is set to $speakerVolume and channel number is " +
                "set to $channelNumber."
        )
    }

    override fun turnOff() {
        super.turnOff()
        println("$name turned off")
    }
}
```

```kotlin
class SmartLightDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {
		// ...

    override fun turnOn() {
        super.turnOn()
        brightnessLevel = 2
        println("$name turned on. The brightness level is $brightnessLevel.")
    }

    override fun turnOff() {
        super.turnOff()
        brightnessLevel = 0
        println("Smart Light turned off")
    }
}
```



#### 替换子类中的父类属性

```kotlin
open class SmartDevice(val name: String, val category: String) {
		var deviceStatus = "online"
		
		open val deviceType = "unknown"
}
```

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {
		override val deviceType = "Smart TV"
}
```

```kotlin
class SmartLightDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {

    override val deviceType = "Smart Light"
}
```

### 3.8 可见性修饰符

可见性修饰符在实现**==封装==**方面发挥着以下重要作用：

- 可让您在类中**隐藏自己的属性和方法**，防止在类外未经授权的访问。
- 可让您在软件包中**隐藏类和接口**，防止在软件包外未经授权的访问。



Kotlin 提供了以下四种可见性修饰符：

- `public`：默认的可见性修饰符。可让系统在任何位置访问声明。对于您想在类外部使用的属性和方法，请标记为 public。
- `private`：可让系统在相同类或源文件中访问声明。
  - 某些属性和方法可能仅在类的内部使用，而且您不一定想让其他类使用。您可以使用 `private` 可见性修饰符标记这些属性和方法，以确保其他类不会意外访问它们。

- `protected`：可让系统在子类中访问声明。对于您想在定义它们的类及其子类中使用的属性和方法，请使用 `protected` 可见性修饰符进行标记。
- `internal`：可让系统在相同模块中访问声明。internal 修饰符与 private 类似，但您可以从类的外部访问内部属性和方法，只要是在相同模块中进行访问即可。

[模块](https://developer.android.com/studio/projects?hl=zh-cn#ApplicationModules)（module）是源文件和构建设置的集合，可让您将项目划分为独立的功能单元。您的项目可以包含一个或多个模块。您可以独立构建、测试和调试每个模块。

软件包（package）就像是用来对相关类进行分组的目录或文件夹，模块则是用来为应用的源代码、资源文件和应用级设置提供容器。

一个模块（module）可以包含多个软件包（package）。



在声明类、方法或属性时，应将可见性修饰符放在声明语法之前。

#### 为属性指定可见性修饰符

为属性指定可见性修饰符的语法是以 `private`、`protected` 或 `internal` 修饰符开头，后跟定义属性的语法。语法如下图所示：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/47807a890d237744.png?hl=zh-cn" alt="47807a890d237744.png" style="zoom:80%;" />

```kotlin
open class SmartDevice(val name: String, val category: String) {
		...
		private var deviceStatus = "online"
		...
}
```

也可以将可见性修饰符设置为 setter 函数，并将修饰符放在 `set` 关键字之前：

- 如果 getter 函数的可见性修饰符与属性的可见性修饰符不匹配，编译器就会报错。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/cea29a49b7b26786.png?hl=zh-cn" alt="cea29a49b7b26786.png" style="zoom: 80%;" />

对于 `SmartDevice` 类，`deviceStatus` 属性的值应能通过类对象在类的外部进行读取。不过，只有该类及其子类可以更新或写入这个值。

```kotlin
open class SmartDevice(val name: String, val category: String) {
		...
		var deviceStatus = "online"
				protected set(value) { field = value }
  	// setter 默认实现，可以省略 set() 函数的括号与主体
  	var deviceStatus = "online"
				protected set
		...
}
```

```kotlin
class SmartHome(
		val smartTvDevice: SmartTvDevice,
		val smartLightDevice: SmartLightDevice
) {
		var deviceTurnOnCount = 0
				private set
				
		fun turnOnTv() {
      	deviceTurnOnCount++
      	smartTvDevice.turnOn()
    }
  
  	fun turnOffTv() {
      	deviceTurnOnCount--
      	smartTvDevice.turnOff()
    }
  
    ...

    fun turnOnLight() {
        deviceTurnOnCount++
        smartLightDevice.turnOn()
    }

    fun turnOffLight() {
        deviceTurnOnCount--
        smartLightDevice.turnOff()
    }

    ...
}
```

#### 为方法指定可见性修饰符

为方法指定可见性修饰符的语法是以 `private`、`protected` 或 `internal` 修饰符开头，后跟定义方法的语法：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/e0a60ddc26b841de.png?hl=zh-cn" alt="e0a60ddc26b841de.png" style="zoom: 50%;" />

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {
  	...
  	protected fun nextChannel() {
      	channelNumber++
      	println("Channel number increased to $channelNumber.")
    }
  	...
}
```

#### 为构造函数指定可见性修饰符

为构造函数指定可见性修饰符的语法与定义主要构造函数的语法类似，但存在以下两点差异：

- 修饰符是在类名称之后、`constructor` 关键字之前的位置指定。
- 为**主要构造函数**指定修饰符时，**即使函数内没有任何形参，也必须保留 `constructor` 关键字和圆括号**。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/6832575eba67f059.png?hl=zh-cn" alt="6832575eba67f059.png" style="zoom: 50%;" />

```kotlin
open class SmartDevice protected constructor (val name: String, val category: String) { ... }
```

#### 为类指定可见性修饰符

为类指定可见性修饰符的语法是以 `private`、`protected` 或 `internal` 修饰符开头，后跟定义类的语法。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/3ab4aa1c94a24a69.png?hl=zh-cn" alt="3ab4aa1c94a24a69.png" style="zoom:50%;" />

```kotlin
internal open class SmartDevice(val name: String, val category: String) { ...}
```

理想情况下，您应努力严控属性和方法的可见性，因此：

- 尽可能通过 `private` 修饰符来声明属性和方法。
- 如果无法确保它们私有，请使用 `protected` 修饰符。
- 如果无法确保它们受到保护，请使用 `internal` 修饰符。
- 如果您无法确保它们仅在内部使用，请使用 `public` 修饰符。

#### 指定适当的可见性修饰符

| **修饰符**  | **可在相同类中访问** | **可在子类中访问** | **可在相同模块中访问** | **可在模块之外访问** |
| ----------- | -------------------- | ------------------ | ---------------------- | -------------------- |
| `private`   | ✔                    | 𝗫                  | 𝗫                      | 𝗫                    |
| `protected` | ✔                    | ✔                  | 𝗫                      | 𝗫                    |
| `internal`  | ✔                    | ✔                  | ✔                      | 𝗫                    |
| `public`    | ✔                    | ✔                  | ✔                      | ✔                    |

- 在 `SmartTvDevice` 子类中，不应允许从类的外部控制 `speakerVolume` 和 `channelNumber` 属性。这些属性应仅通过 `increaseSpeakerVolume()` 和 `nextChannel()` 方法控制。
- 在 `SmartLightDevice` 子类中，应仅通过 `increaseLightBrightness()` 方法来控制 `brightnessLevel` 属性。

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {
		private var speakerVolume = 2
				set(value) {
						if (value in 0..100) {
                field = value
            }
				}
  	private var channelNumber = 1
        set(value) {
            if (value in 0..200) {
                field = value
            }
        }
}
```

```kotlin
class SmartLightDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {
    private var brightnessLevel = 0
        set(value) {
            if (value in 0..100) {
                field = value
            }
        }
}
```

### 3.9 定义属性委托

您可以在 setter 函数中，通过委托重复使用这段检查范围的代码。委托可代替使用字段、getter 和 setter 函数的方式来管理该值。

创建属性委托的语法是以变量声明开头，后面依次跟 `by` 关键字以及用于为属性处理 getter 和 setter 函数的委托对象。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/928547ad52768115.png?hl=zh-cn" alt="928547ad52768115.png" style="zoom:80%;" />

若要实现您可以委托实现的目标类，您必须熟悉**接口**。接口是实现它的类必须遵循的协议，侧重于操作的“内容”，而不是操作的“方式”。简而言之，接口可帮助您实现**抽象**。

创建接口的语法

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-classes-and-objects/img/bfe3fd1cd8c45b2a.png?hl=zh-cn" alt="bfe3fd1cd8c45b2a.png" style="zoom:50%;" />

类会实现接口。类会针对接口中声明的方法和属性提供实现详情。

如果想为 `var` 类型或 `val` 类型创建委托类，您需要实现 [`ReadWriteProperty`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-read-write-property/) 接口。

```kotlin
import Kotlin.properties.ReadWriteProperty
import Kotlin.reflect.KProperty

class RangeRegulator(
		initialValue: Int,
		private val minValue: Int,
		private val maxValue: Int
) : ReadWriteProperty<Any?, Int> {
  
		var fieldData = initialValue // 属性会充当变量的后备字段
  
		override fun getValue(thisRef: Any?, property: KProperty<*>): Int {
      	return fieldData
    }
		
		override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) {
      	if (value in minValue..maxValue) {
          	fieldData = value
        }
    }
}

```

在 `RangeRegulator` 类的主体中，替换 `getValue()` 和 `setValue()` 方法，这些方法会充当属性的 getter 和 setter 函数。

[`KProperty`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.reflect/-k-property/#kproperty) 是代表所声明属性的接口，用于访问委托属性上的元数据。

```kotlin
class SmartTvDevice(deviceName: String, deviceCategory: String) : SmartDevice(name = deviceName, category = deviceCategory) {
  	override val deviceType = "Smart TV"
  
  	private var speakerVolume by RangeRegulator(initialValue = 2, minValue = 0, maxValue = 100)
  
  	private var channelNumber by RangeRegulator(initialValue = 1, minValue = 0, maxValue = 200)
}
```

```kotlin
class SmartLightDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {

    override val deviceType = "Smart Light"

    private var brightnessLevel by RangeRegulator(initialValue = 0, minValue = 0, maxValue = 100)
}
```





```kotlin
import kotlin.properties.ReadWriteProperty
import kotlin.reflect.KProperty

open class SmartDevice(val name: String, val category: String) {

    var deviceStatus = "online"
        protected set

    open val deviceType = "unknown"

    open fun turnOn() {
        deviceStatus = "on"
    }

    open fun turnOff() {
        deviceStatus = "off"
    }
}

class SmartTvDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {

    override val deviceType = "Smart TV"

    private var speakerVolume by RangeRegulator(initialValue = 2, minValue = 0, maxValue = 100)

    private var channelNumber by RangeRegulator(initialValue = 1, minValue = 0, maxValue = 200)

    fun increaseSpeakerVolume() {
        speakerVolume++
        println("Speaker volume increased to $speakerVolume.")
    }

    fun nextChannel() {
        channelNumber++
        println("Channel number increased to $channelNumber.")
    }

    override fun turnOn() {
        super.turnOn()
        println(
            "$name is turned on. Speaker volume is set to $speakerVolume and channel number is " +
                "set to $channelNumber."
        )
    }

    override fun turnOff() {
        super.turnOff()
        println("$name turned off")
    }
}

class SmartLightDevice(deviceName: String, deviceCategory: String) :
    SmartDevice(name = deviceName, category = deviceCategory) {

    override val deviceType = "Smart Light"

    private var brightnessLevel by RangeRegulator(initialValue = 0, minValue = 0, maxValue = 100)

    fun increaseBrightness() {
        brightnessLevel++
        println("Brightness increased to $brightnessLevel.")
    }

    override fun turnOn() {
        super.turnOn()
        brightnessLevel = 2
        println("$name turned on. The brightness level is $brightnessLevel.")
    }

    override fun turnOff() {
        super.turnOff()
        brightnessLevel = 0
        println("Smart Light turned off")
    }
}

class SmartHome(
    val smartTvDevice: SmartTvDevice,
    val smartLightDevice: SmartLightDevice
) {

    var deviceTurnOnCount = 0
        private set

    fun turnOnTv() {
        deviceTurnOnCount++
        smartTvDevice.turnOn()
    }

    fun turnOffTv() {
        deviceTurnOnCount--
        smartTvDevice.turnOff()
    }

    fun increaseTvVolume() {
        smartTvDevice.increaseSpeakerVolume()
    }

    fun changeTvChannelToNext() {
        smartTvDevice.nextChannel()
    }

    fun turnOnLight() {
        deviceTurnOnCount++
        smartLightDevice.turnOn()
    }

    fun turnOffLight() {
        deviceTurnOnCount--
        smartLightDevice.turnOff()
    }

    fun increaseLightBrightness() {
        smartLightDevice.increaseBrightness()
    }

    fun turnOffAllDevices() {
        turnOffTv()
        turnOffLight()
    }
}

class RangeRegulator(
    initialValue: Int,
    private val minValue: Int,
    private val maxValue: Int
) : ReadWriteProperty<Any?, Int> {

    var fieldData = initialValue

    override fun getValue(thisRef: Any?, property: KProperty<*>): Int {
        return fieldData
    }

    override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) {
        if (value in minValue..maxValue) {
            fieldData = value
        }
    }
}

fun main() {
    var smartDevice: SmartDevice = SmartTvDevice("Android TV", "Entertainment")
    smartDevice.turnOn()

    smartDevice = SmartLightDevice("Google Light", "Utility")
    smartDevice.turnOn()
}
```

## 04. Use function types and lambda expressions in Kotlin

### 4.1 将函数存储在变量中

函数作为一级结构，也属于数据类型。

- 因此可以将函数存储在变量中、将函数传递到函数，以及从函数返回函数。
- 或在运行时更改应用中某一部分的行为，或嵌入可组合函数以构建布局。

都可通过 lambda 表达式来实现。

```kotlin
fun main() {
    val trickFunction = trick
}

fun trick() {
    println("No treats!")
}
```

上述代码试图将 `trick` 存储在 `trickFunction` 变量中，但运行会产生错误，因为 Kotlin 编译器会将 `trick` 识别为 `trick()` 函数的名称，但它想让您调用该函数，而不是将其分配给变量。

```
Function invocation 'trick()' expected
```

**如需将函数作为值引用，您需要使用函数引用运算符 (`::`)。**

```kotlin
fun main() {
    val trickFunction = ::trick
}

fun trick() {
    println("No treats!")
}
```

#### 使用 lambda 表达式重新定义函数

lambda 表达式提供了简洁的语法来定义函数，无需使用 `fun` 关键字。您**可以直接将 lambda 表达式存储在变量中，无需对其他函数进行函数引用。**

在赋值运算符 (`=`) 前面，您要添加 `val` 或 `var` 关键字，后跟变量名称，以供您在调用函数时使用。赋值运算符 (`=`) 后面是 lambda 表达式，它由一对大括号构成，而大括号中的内容则构成函数正文。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/5e25af769cc200bc.png?hl=zh-cn" alt="5e25af769cc200bc.png" style="zoom:50%;" />

借助 lambda 表达式，可以创建用于存储函数的变量，像调用函数一样调用这些变量，并将其存储在其他可以像函数一样调用的变量中。

```kotlin
fun main() {
		val trickFunction = trick
		trick()
  	trickFunction()
}

val trick = {
		println("No treats!")
}
```

### 4.2 将函数用作数据类型

要指定函数参数的类型或返回值类型，则需要使用用于表达函数类型的语法。

函数类型由一组圆括号组成，其中包含可选的参数列表、`->` 符号和返回值类型。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/5608ac5e471b424b.png?hl=zh-cn" alt="5608ac5e471b424b.png" style="zoom: 50%;" />

`trick` 变量的数据类型为 `() -> Unit`。

- 圆括号为空，因为函数没有任何参数。
- 返回值类型为 `Unit`，因为函数不返回任何内容。

```kotlin
val treat: () -> Unit = {
		println("Have a treat!")
}
```

#### 将函数用作返回值类型

函数是一种数据类型，因此您可以像使用任何其他数据类型一样使用函数。甚至可以**从其他函数返回函数**。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/f16dd6ca0c1588f5.png?hl=zh-cn" alt="f16dd6ca0c1588f5.png" style="zoom:50%;" />

```kotlin
fun trickOrTreat(isTrick: Boolean): () -> Unit {
		if (isTrick) return trick
		else return treat
}

fun main() {
  	val treatFunction = trickOrTreat(false)
  	val trickFunction = trickOrTreat(true)
  	treatFunction()
    trickFunction()
}
```

#### **将一个函数作为参数传递到另一个函数**

用作参数的函数也需要接受自己的参数。声明函数类型时，参数不会带有标签。您只需指定各个参数的数据类型（以英文逗号隔开）即可。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/8372d3b83d539fac.png?hl=zh-cn" alt="8372d3b83d539fac.png" style="zoom:50%;" />

当为接受参数的函数编写 lambda 表达式时，系统会按参数出现的先后顺序为参数命名。

- 参数名称列在左大括号后面，各名称之间以英文逗号隔开。
- 箭头 (`->`) 将参数名称与函数正文隔开。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/938d2adf25172873.png?hl=zh-cn" alt="938d2adf25172873.png" style="zoom:50%;" />

```kotlin
fun trickOrTreat(isTrick: Boolean, extraTreat: (Int) -> String): () -> Unit {
    if (isTrick) {
        return trick
    } else {
        println(extraTreat(5))
        return treat
    }
}

fun main() {
    val coins: (Int) -> String = { quantity ->
        "$quantity quarters"
    }
  
  	// 可以省略参数和箭头
  	// val cupcake: (Int) -> String = { quantity ->
    val cupcake: (Int) -> String = {
        "Have a cupcake!"
    }

    val treatFunction = trickOrTreat(false, coins)
    val trickFunction = trickOrTreat(true, cupcake)
    treatFunction()
    trickFunction()
}
```

- lambda 表达式中无法使用 `return` 关键字，函数中最后一个表达式的结果将成为返回值。
- 如果 lambda 表达式中没有使用参数，可以移除表达式的参数声明和 `->` 符号。

#### 可为 null 的函数类型

与其他数据类型一样，函数类型可声明为可为 null。在这些情况下，变量可以包含函数，也可以为 `null`。

如需将函数声明为可为 null，请用圆括号括住**函数类型**，并在右圆括号外后接 `?` 符号。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/c8a004fbdc7469d.png?hl=zh-cn" alt="c8a004fbdc7469d.png" style="zoom:50%;" />

```kotlin
fun trickOrTreat(isTrick: Boolean, extraTreat: ((Int) -> String)?): () -> Unit {
    if (isTrick) {
        return trick
    } else {
        if (extraTreat != null) {
            println(extraTreat(5))
        }
        return treat
    }
}

fun main() {
    val coins: (Int) -> String = { quantity ->
        "$quantity quarters"
    }
  
  	val cupcake: (Int) -> String = {
        "Have a cupcake!"
    }

    val treatFunction = trickOrTreat(false, null)
    val trickFunction = trickOrTreat(true, cupcake)
    treatFunction()
    trickFunction()
}
```

### 4.3 使用简写语法编写 lambda 表达式

#### 省略参数名称

可以完全省略参数名称。

**如果函数只有一个参数，而您未提供名称，Kotlin 会隐式为其分配 `it` 名称**，因此您可以省略参数名称和 `->` 符号，从而使 lambda 表达式变得更简洁。

```kotlin
val coins: (Int) -> String = { quantity ->
    "$quantity quarters"
}
// 省略参数名称
val coins: (Int) -> String = {
    "$it quarters"
}
```

#### 将 lambda 表达式直接传入函数

```kotlin
fun main() {
    val coins: (Int) -> String = {
        "$it quarters"
    }
  
    val treatFunction = trickOrTreat(false, coins)
    treatFunction()
}
// lambda 表达式直接传入函数
fun main() {
    val treatFunction = trickOrTreat(false, { "$it quarters" })
    treatFunction()
}
```

#### 使用尾随 lambda 语法

当函数类型是函数的最后一个参数时，可以将 lambda 表达式放在右圆括号后面以调用函数。

这会使代码的可读性更强，因为它将 lambda 表达式与其他参数隔开，但没有改变代码的作用。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/3ee3176d612b54.png?hl=zh-cn" alt="3ee3176d612b54.png" style="zoom: 33%;" />

```kotlin
fun main() {
    val treatFunction = trickOrTreat(false) { "$it quarters" }
    treatFunction()
}
```

用于声明界面的可组合函数接受函数作为参数，并且通常使用尾随 lambda 语法进行调用。

### 4.4 使用 repeat() 函数

如果一个函数接受另一个函数作为实参，或返回一个函数，该函数就称为高阶函数。

[`repeat()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/repeat.html) 函数就是这样一种高阶函数。`repeat()` 函数是使用函数表达 `for` 循环的简洁方式。

`repeat()` 函数具有以下函数签名：

```kotlin
repeat(times: Int, action: (Int) -> Unit)
```

- `times` 参数是操作应发生的次数。
- `action` 参数是一个函数，它接受单个 `Int` 参数并返回 `Unit` 类型的函数。`action` 函数的 `Int` 参数是到目前为止已执行的操作次数。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-function-types-and-lambda/img/519a2e0f5d02687.png?hl=zh-cn" alt="519a2e0f5d02687.png" style="zoom: 33%;" />

```kotlin
fun main() {
    val treatFunction = trickOrTreat(false) { "$it quarters" }
    val trickFunction = trickOrTreat(true, null)
    repeat(4) {
        treatFunction()
    }
    trickFunction()
}
```

