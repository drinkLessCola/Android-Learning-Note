## 01 泛型、对象和扩展

### 1.1 使用泛型创建可重复使用的类

如果想**让某个属性具有不同的数据类型**，**创建子类**并不是理想的解决方案。

- 每次添加一个新类型子类时，都需要添加该属性，即使唯一的区别在于属性的类型。
- 父类（Question）中不具备该属性（answer）并不适当。

Kotlin 提供了一种称为“**==泛型类型==**”的元素，可使单个属性在特定用例下具有不同的数据类型。

使用泛型时，可以使数据类型（例如类）指定用于属性和方法的未知占位符的数据类型。



<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/67367d9308c171da.png?hl=zh-cn" alt="67367d9308c171da.png" style="zoom: 33%;" />

系统会在实例化类时提供泛型数据类型，因此需要将该类型定义为类签名的一部分。

此占位符名称可在类中任何实际数据类型的位置使用。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/81170899b2ca0dc9.png?hl=zh-cn" alt="81170899b2ca0dc9.png" style="zoom:33%;" />

在实例化类时，系统会将泛型类型使用的数据类型作为用尖括号中的形参传递。

为泛型属性传入的值的数据类型必须与尖括号中的数据类型一致。

实例化类时传入的泛型类型也称为“**==形参==**”，但它们属于单独的形参列表，与放置在括号中的属性值是分开的。





对如下有重复部分的代码进行重构：

```kotlin
class FillInTheBlankQuestion(
    val questionText: String,
    val answer: String,
    val difficulty: String
)

class TrueOrFalseQuestion(
    val questionText: String,
    val answer: Boolean,
    val difficulty: String
)
class NumericQuestion(
    val questionText: String,
    val answer: Int,
    val difficulty: String
)
```

```kotlin
class Question<T>(
	val questionText: String,
    val answer: T,
    val difficulty: String
)

fun main() {
    val question1 = Question<String>("The Best Language: ____", "PHP", "easy")
    val question2 = Question<Boolean>("The Best Language is PHP. True or false", false, "medium")
    val question3 = Question<Int>("How many days are there between full moons?", 28, "hard")
}
```

### 1.2 使用枚举类

枚举类用于创建具有一组数量有限的可能值的类型。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/f4bddb215eb52392.png?hl=zh-cn" alt="f4bddb215eb52392.png" style="zoom:33%;" />

枚举的每个可能值都称为“**==枚举常量==**”。

- 枚举常量位于大括号内，互相以英文逗号分隔。
- 按照惯例，常量名称中的每个字母都要大写。
- 使用点运算符来引用枚举常量。

#### 使用枚举常量

```kotlin
enum class Difficulty {
    EASY, MEDIUM, HARD
}

class Question<T>(
		val questionText: String,
    val answer: T,
    val difficulty: Difficulty
)

fun main() {
    val question1 = Question<String>("The Best Language: ____", "PHP", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The Best Language is PHP. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
}
```

### 1.3 使用数据类

像 `Question` 这样只包含数据的类，没有任何用于执行操作的方法。这些类可以定义为“**==数据类==**”。

数据类的构造函数中必须至少有一个形参，并且所有构造函数形参都必须使用 `val` 或 `var` 进行标记。此外，数据类也不能是 `abstract`、`open`、`sealed` 或 `inner`。



**定义数据类的方法**：在 `class` 关键字前面添加 `data` 关键字。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/e7cd946b4ad216f4.png?hl=zh-cn" alt="e7cd946b4ad216f4.png" style="zoom:33%;" />



通过将类定义为数据类，Kotlin 编译器可以做出某些假设，并自动实现某些方法。例如：

- `println()` 函数会在后台调用 `toString()`。
- `equals()`
- `hashCode()`：在使用某些集合类型时，您会看到此方法。
- `toString()`
- [`componentN()`](https://kotlinlang.org/docs/destructuring-declarations.html#example-returning-two-values-from-a-function)：`component1()`、`component2()` 等
- `copy()`



对非数据类实例使用 `toString` 方法：输出结果仅显示类名称和对象的唯一标识符。

```kotlin
fun main() {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    println(question1.toString()) // Question@37f8bb67
}
```

对数据类实例使用 `toString` 方法：Kotlin 能够在调用 `toString()` 时显示此类的属性。

```kotlin
data class Question<T>(
    val questionText: String,
    val answer: T,
    val difficulty: Difficulty
)
fun main() {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    println(question1.toString()) // Question(questionText=Quoth the raven ___, answer=nevermore, difficulty=MEDIUM)
}
```

### 1.4 使用单例对象

在很多情况下，一个类只需要一个实例。例如：

1. 移动游戏中当前用户的玩家统计信息。
2. 与单个硬件设备互动，例如通过扬声器发送音频。
3. 用于访问远程数据源（例如 Firebase 数据库）的对象。
4. 身份验证（每次只应有一位用户登录）。

在上述情况下可能需要使用类，并且只需要对此类的一个实例进行实例化。

- 如果只有一台硬件设备，或者一次只有一位用户登录，则没有理由创建多个实例。
- 让两个对象同时访问同一硬件设备可能会导致出现一些非常奇怪且有 bug 的行为。

“单例”是指只能有一个实例的类。

Kotlin 提供了一种称为“**==对象==**”的特殊结构，可用于构建单例类。

#### 定义单例对象

<img src="01 More Kotlin fundamentals.assets/image-20240112220918612.png" alt="image-20240112220918612" style="zoom:33%;" />

- **单例对象不能包含构造函数**，**因为您无法直接创建实例**。
- 相反，**所有属性都要在大括号内定义并被赋予初始值。**



如下示例使用了一个用户状态对象，其中只需要一个实例。

```kotlin
object StudentProgress {
    var total: Int = 10
    var answered: Int = 3
}
```

#### 访问单例对象

由于一次只能存在一个 `StudentProgress` 实例，因此可以通过以下方法来访问其属性：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/1b610fd87e99fe25.png?hl=zh-cn" alt="1b610fd87e99fe25.png" style="zoom:33%;" />

```kotlin
fun main() {
    ...
    println("${StudentProgress.answered} of ${StudentProgress.total} answered.")
}
```

#### 将对象声明为伴生对象

Kotlin 中的**类和对象可以在其他类型中定义**。

可以**使用“==伴生对象==”在另一个类中定义单例对象**。

伴生对象允许您**从类内部访问其属性和方法**（如果对象的属性和方法属于相应类的话），从而让语法变得更简洁。

**伴生对象的声明方式**：在 `object` 关键字前面添加 `companion` 关键字。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/68b263904ec55f29.png?hl=zh-cn" alt="68b263904ec55f29.png" style="zoom:33%;" />

```kotlin
class Quiz {
    val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)

    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
}

fun main() {
  	// 虽然这些属性是在 StudentProgress 对象中声明的，但可以通过 Quiz 类进行访问。
    println("${Quiz.answered} of ${Quiz.total} answered.") // 3 of 10 answered.
}
```

### 1.5 使用新的属性和方法来扩展类

使用扩展属性和扩展函数可以向对象添加属性和方法，而无需直接修改对象的代码。

这是向已定义的类添加功能的绝佳方式。

#### 添加扩展属性

由于扩展属性无法存储数据，因此它们必须是 get-only 的。

<img src="01 More Kotlin fundamentals.assets/image-20240112233251867.png" alt="image-20240112233251867" style="zoom: 50%;" />

```kotlin
class Quiz {
    val question1 = Question<String>("The Best Language: ____", "PHP", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The Best Language is PHP. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
}

val Quiz.StudentProgress.progressText: String
		get() = "${answered} of ${total} answered"

fun main() {
    println(Quiz.progressText) // 3 of 10 answered.
}
```

#### 添加扩展函数

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/879ff2761e04edd9.png?hl=zh-cn" alt="879ff2761e04edd9.png" style="zoom: 50%;" />

```kotlin
class Quiz {
    val question1 = Question<String>("The Best Language: ____", "PHP", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The Best Language is PHP. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
}

val Quiz.StudentProgress.progressText: String
	get() = "${answered} of ${total} answered"

fun Quiz.StudentProgress.printProgressBar() {
    repeat(Quiz.answered) { print("▓")}
    repeat(Quiz.total - Quiz.answered) { print("▒")}
    println()
    println(Quiz.progressText)
}

fun main() {
    Quiz.printProgressBar()
  /*
  	▓▓▓▒▒▒▒▒▒▒
		3 of 10 answered.
  */
}
```

### 1.6 使用接口重写扩展函数

如果有权访问源代码，不必总是使用扩展类来添加功能。

在有些情况下，并不知道具体实现，而只知道**应该存在某种方法或属性**。

如果需要**多个类具有相同的额外属性和方法**（可能是行为方式不同），就可以使用**==接口==**定义这些属性和方法。



接口使用 `interface` 关键字定义，后跟大驼峰式命名法 (UpperCamelCase) 名称。在大括号内，您可以定义任何符合接口标准的类**==必须实现的方法签名==或 ==get-only 属性==**。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/6b04a8f50b11f2eb.png?hl=zh-cn" alt="6b04a8f50b11f2eb.png" style="zoom:33%;" />

接口是一种协定。一个类可以通过实现接口中的规定来扩展接口。

类必须实现接口中指定的所有属性和方法。

- 因此可以确保所有需要扩展接口的类都可使用完全相同的方法签名实现完全相同的方法。
- 如果以任何方式修改接口（例如添加或移除属性/方法，或者更改方法签名），编译器将要求更新扩展接口的所有类，这样可以保持代码的一致性且更易于维护。

接口允许其扩展类的行为不同。提供实现的方法取决于各个类。

声明类扩展接口：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-generics/img/78af59840c74fa08.png?hl=zh-cn" alt="78af59840c74fa08.png" style="zoom:50%;" />

```kotlin
interface ProgressPrintable {
    val progressText: String
    fun printProgressBar()
}

class Quiz: ProgressPrintable {
    val question1 = Question<String>("The Best Language: ____", "PHP", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The Best Language is PHP. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
    
    override val progressText: String
    	get() = "${answered} of ${total} answered"
    
    override fun printProgressBar() {
        repeat(Quiz.answered) { print("▓")}
        repeat(Quiz.total - Quiz.answered) { print("▒")}
        println()
        println(progressText)
    }
}

fun main() {
    // 由于 printProgressBar() 函数现在是 Quiz 类的一个方法，因此您需要先实例化 Quiz 对象，然后再调用 printProgressBar()。
    Quiz().printProgressBar()
}
```



其它接口示例：

- [手动注入依赖项。](https://developer.android.com/training/dependency-injection/manual?hl=zh-cn)创建一个定义依赖项中所有属性和方法的接口。要求该接口作为依赖项的数据类型（活动、测试用例等），以便使用实现该接口的任何类的实例。这让您可以更换底层实现。
- 模拟自动化测试。模拟类和真实类都符合同一接口标准。
- 在 [Compose Multiplatform](https://github.com/JetBrains/compose-jb) 应用中访问相同的依赖项。例如，创建一个能够为 Android 和桌面设备提供一组通用属性和方法的接口，即使每个平台的基础实现有所不同也适用。
- Compose 中的多个数据类型（例如 [`Modifier`](https://developer.android.com/reference/kotlin/androidx/compose/ui/Modifier?hl=zh-cn)）都是接口。这样一来，您无需访问或修改底层源代码即可添加新的修饰符。

### 1.7 使用作用域函数来访问类属性和方法

借助作用域函数，您可以通过简洁的方式访问类中的属性和方法，而无需重复访问变量名称。

作用域函数属于高阶函数，允许您在**不引用对象名称的情况下访问对象的属性和方法**。之所以将这些函数称为作用域函数，是因为<u>**传入的函数体**会采用**调用作用域函数时所涉及对象的作用域**。</u>

有些作用域函数允许您访问类中的属性和方法，就好像这些函数已被定义为相应类的方法一样。

#### 使用 `let()` 替换过长的对象名称

借助 `let()` 函数，您可以使**用标识符 `it` 来引用 lambda 表达式中的对象**，而无需使用对象的实际名称。

`let()` 函数是一个扩展函数，任何 Kotlin 对象都可以调用。

```kotlin
class Quiz: ProgressPrintable {
		// ...
    fun printQuiz() {
        println(question1.questionText)
        println(question1.answer)
        println(question1.difficulty)
        println()
        println(question2.questionText)
        println(question2.answer)
        println(question2.difficulty)
        println()
        println(question3.questionText)
        println(question3.answer)
        println(question3.difficulty)
        println()
    }
}
```

上述 `printQuiz()` 方法每次都会使用整个变量名称，如果变量的名称发生更改，就需要对每个声明进行更改。

下面的例子调用 `question1`、`question2` 和 `question3` 上的 `let()` 函数，将访问 `questionText`、`answer` 和 `difficulty` 属性的代码括起来。用 `it` 替换每个 lambda 表达式中的变量名称。

```kotlin
class Quiz: ProgressPrintable {
		// ...
    fun printQuiz() {
        question1.let {
          println(it.questionText)
        	println(it.answer)
        	println(it.difficulty)
        }
        println()
        question2.let {
            println(it.questionText)
            println(it.answer)
            println(it.difficulty)
        }
        println()
        question3.let {
            println(it.questionText)
            println(it.answer)
            println(it.difficulty)
        }
        println()
    }
}
```

#### 使用 `apply()` 在没有变量的情况下调用对象方法

作用域函数有一项非常棒的功能，那就是**即使尚未将某个对象分配到变量，您也可以对此对象调用作用域函数**。

例如，`apply()` 函数是一个扩展函数，可通过点表示法调用对象。`apply()` 函数还会返回对相应对象的引用，以便将其存储在变量中。

```kotlin
fun main() {
    Quiz().apply {
        printQuiz()
    }
}
```

- `apply()` 函数会返回 `Quiz` 类的实例，无需变量即可对 `Quiz` 实例调用方法。





```kotlin
enum class Difficulty {
    EASY, MEDIUM, HARD
}

class Question<T>(
	val questionText: String,
    val answer: T,
    val difficulty: Difficulty
)

interface ProgressPrintable {
    val progressText: String
    fun printProgressBar()
}

class Quiz: ProgressPrintable {
    val question1 = Question<String>("The Best Language: ____", "PHP", Difficulty.MEDIUM)
    val question2 = Question<Boolean>("The Best Language is PHP. True or false", false, Difficulty.EASY)
    val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)
    
    companion object StudentProgress {
        var total: Int = 10
        var answered: Int = 3
    }
    
    override val progressText: String
    	get() = "${answered} of ${total} answered"
    
    override fun printProgressBar() {
        repeat(Quiz.answered) { print("▓")}
        repeat(Quiz.total - Quiz.answered) { print("▒")}
        println()
        println(progressText)
    }
    
    fun printQuiz() {
        question1.let {
            println(it.questionText)
        	println(it.answer)
        	println(it.difficulty)
        }
        println()
        question2.let {
            println(it.questionText)
            println(it.answer)
            println(it.difficulty)
        }
        println()
        question3.let {
            println(it.questionText)
            println(it.answer)
            println(it.difficulty)
        }
        println()
    }
}

fun main() {
    val quiz = Quiz().apply {
        printQuiz()
    }
}
```

## 02 在 Kotlin 中使用集合

集合类型（有时称为数据结构）可让您以井然有序的方式存储多个值，这些值通常属于同一数据类型。

集合可以是：

- **有序列表**
- **唯一值分组**
- 一种数据类型的值到另一种数据类型的值的**映射**。

### 2.1 Array

数组是属于同一数据类型的一系列值。

- 数组包含多个值，这些值称为元素，有时也称为项。
- 数组中的元素是有序的，可以通过索引进行访问。

索引是与数组中的某个元素对应的整数。索引可以指示某个项与数组起始元素之间的距离。这称为“零索引”。

在设备的内存中，数组中的元素彼此相邻地存储在一起。

- 通过索引可以快速地访问数组元素。您可以通过索引访问数组的任何随机元素，并且<u>访问任何其他随机元素预计需要大约相同的时间</u>。因此称数组具有**随机访问特性**。
- **数组具有固定的大小**。这意味着您向数组添加元素时不能超过该数组的大小。如果尝试访问超过某个数组大小的索引，就会引发异常。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/bb77ec7506ac1a26.png?hl=zh-cn" alt="bb77ec7506ac1a26.png" style="zoom:50%;" />

#### **声明数组**：`arrayOf()`

`arrayOf()` 函数将**数组元素**作为形参，并返回**类型与传入的形参相符的数组**。

`arrayOf()` 的参数数量不定。如果向 `arrayOf()` 传入两个参数，生成的数组将包含两个元素（索引为 0 和 1）。如果您传入三个实参，生成的数组将包含 3 个元素，索引为 0 到 2。

![69e283b32d35f799.png](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/69e283b32d35f799.png?hl=zh-cn)



```kotlin
fun main() {
    val rockPlanets = arrayOf<String>("Mercury", "Venus", "Earth", "Mars")
    val gasPlanets = arrayOf("Jupiter", "Saturn", "Uranus", "Neptune")
    
    val solarSystem = rockPlanets + gasPlanets
}
```

- Kotlin 使用类型推断，因此在调用 `arrayOf()` 时可以省略类型名称。
- 数组可以相加。对 `rockPlanets` 和 `gasPlanets` 使用加号 (`+`) 运算符，其结果是一个新数组，其中包含 `rockPlanets` 数组的所有元素以及 `gasPlanets` 数组的元素。

#### 访问数组中的元素

可以通过索引访问数组的元素。这称为下标语法。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/1f8398eaee30c7b0.png?hl=zh-cn" alt="1f8398eaee30c7b0.png" style="zoom: 33%;" />

```kotlin
println(solarSystem[0])
println(solarSystem[1])
println(solarSystem[2])
println(solarSystem[3])
println(solarSystem[4])
println(solarSystem[5])
println(solarSystem[6])
println(solarSystem[7])
```

#### 更新特定索引处的元素

还可以按索引设置数组元素的值。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/9469e321ed79c074.png?hl=zh-cn" alt="9469e321ed79c074.png" style="zoom:33%;" />

```kotlin
fun main() {
    val rockPlanets = arrayOf<String>("Mercury", "Venus", "Earth", "Mars")
    val gasPlanets = arrayOf("Jupiter", "Saturn", "Uranus", "Neptune")
    val solarSystem = rockPlanets + gasPlanets
    
    solarSystem[3] = "Little Earth"
    println(solarSystem[3]) // Little Earth
}
```

越界访问数组外的索引时，会抛出 `ArrayIndexOutOfBounds` 异常。如果希望增加数组的大小，需要创建一个新数组。

```kotlin
fun main() {
	// ...
	solarSystem[8] = "Pluto"
  //Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 8 out of bounds for length 8
}
```

### 2.2 Lists

列表是**==有序且可调整大小的集合==**，通常作为可调整大小的数组实现。

- 当数组达到容量上限时，如果您尝试插入新元素，需要将该数组复制到一个新的较大数组。
- 使用列表，您还可以在特定索引处（介于其他元素之间）插入新元素。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/a4970d42cd1d2b66.png?hl=zh-cn" alt="a4970d42cd1d2b66.png" style="zoom:33%;" />

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/a678d6a41e6afd46.png?hl=zh-cn" alt="a678d6a41e6afd46.png" style="zoom: 33%;" />

在大多数情况下，无论列表中包含多少个元素，向列表中添加任何元素所需的时间都是相同的。

如果添加新元素会使数组超出其定义的大小，那么数组元素将会移动，以便为新元素腾出空间。

列表会为您执行所有这些操作，但实际上它只是一个在需要时换成新数组的数组。



#### `List` 和 `MutableList`

Kotlin 中的集合类型会实现一个或多个接口。接口提供了一组供类实现的标准属性和方法。

实现 `List` 接口的类可为 `List` 接口的所有属性和方法提供实现。`MutableList` 也是如此。

- `List` 是一个接口，用于定义与**只读有序项集合**相关的属性和方法。
- `MutableList` 通过定义**修改列表的方法**（例如添加和移除元素）来扩展 `List` 接口。

这些接口仅指定 `List` 和/或 `MutableList` 的属性和方法。每个属性和方法的实现方式均由扩展这些接口的类决定。上述基于数组的实现将是您最常使用（如果不是始终使用）的实现，但 Kotlin 允许其他类扩展 `List` 和 `MutableList`。



#### `listOf()` 函数

与 `arrayOf()` 类似，`listOf()` 函数将相关项作为形参，但返回 `List`，而不是数组。

`List` 具有 `size` 属性，用于获取列表中的元素数量。

```kotlin
fun main() {
    val solarSystem = listOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
		println(solarSystem.size) // 8
}
```

#### 访问列表中的元素

从 `List` 访问特定索引处的元素：

- 下标语法。
- `get()` 方法接受 `Int` 作为参数，并在该索引处返回相应元素。

与 `Array` 一样，`ArrayList` 也为零索引。

```kotlin
fun main() {
    val solarSystem = listOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
    println(solarSystem[2]) // "Earth"
    println(solarSystem.get(3))	// "Mars"
}
```

除了按索引获取元素之外，还可以使用 `indexOf()` 方法**搜索特定元素的索引**。

- `indexOf()` 方法在列表中搜索指定元素（作为实参传入），并返回该**元素在第一次出现时的索引**。
- 如果该元素未出现在列表中，则返回 `-1`。

```kotlin
fun main() {
    val solarSystem = listOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
    println(solarSystem.indexOf("Earth")) // 2
    println(solarSystem.indexOf("Pluto")) // -1
}
```

#### 使用 `for` 循环遍历列表元素

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/f11277e6af4459bb.png?hl=zh-cn" alt="f11277e6af4459bb.png" style="zoom: 50%;" />

`in` 关键字之前的变量未使用 `val` 或 `var` 进行声明，它将假定为 get-only。

- 可以随意为其命名。
- 此变量将用作与集合中的当前元素对应的临时变量，可在大括号内访问。

```kotlin
fun main() {
    val solarSystem = listOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
    for (planet in solarSystem) {
        println(planet)
    }
}
/*
  Mercury
  Venus
  Earth
  Mars
  Jupiter
  Saturn
  Uranus
  Neptune
*/
```

#### 向列表中添加元素

只有实现 `MutableList` 接口的类具有在集合中添加、移除和更新元素的功能。

要创建**可以向其中添加元素或移除元素的列表**时需要调用 `mutableListOf()` 函数而不是 `listOf()`。

`add()` 函数有两个版本：

- 第一个 `add()` 只有一个参数，会将符合列表元素类型的新元素添加到列表末尾。
- 第二个 `add()` 有两个参数。第一个参数对应于应该插入新元素的索引。第二个参数是要添加到列表中的元素。

```kotlin
fun main() {
    val solarSystem = mutableListOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
    solarSystem.add("Pluto")
    solarSystem.add(3, "Theia")
}
```

#### 更新特定索引处的元素

可以使用下标语法更新现有元素

```kotlin
fun main() {
    val solarSystem = mutableListOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
    solarSystem.add("Pluto")
    solarSystem.add(3, "Theia")
    solarSystem[3] = "Future Moon"
    println(solarSystem[3]) // "Future Moon"
		println(solarSystem[9]) // "Pluto"
}
```

#### 从列表中移除元素

您可以通过两种方法移除元素：

- 将该元素传递到 `remove()` 方法。
- 使用 `removeAt()` 按索引移除该元素。

```kotlin
fun main() {
    val solarSystem = mutableListOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
    solarSystem.add("Pluto")
    solarSystem.add(3, "Theia")
    solarSystem[3] = "Future Moon"

    solarSystem.removeAt(9)
    solarSystem.remove("Future Moon")
}
```

检查列表中是否存在某个元素：

1. `List` 提供 `contains()` 方法，该方法返回一个 `Boolean` 表示列表中是否存在某个元素。

   ```kotlin
   println(solarSystem.contains("Pluto")) // false
   ```

2. 使用 `in` 运算符。

   ```kotlin
   println("Future Moon" in solarSystem) // false
   ```

   

### 2.3 Sets

Set 是指**没有特定顺序**且**不允许出现重复值**的集合。

集合的实现依靠 hashCode。

hashCode 是由任何 Kotlin 类的 `hashCode()` 方法生成的 `Int`。可以将其视为 Kotlin 对象的半唯一标识符。

- 如果对该对象稍作更改，例如向 `String` 中添加一个字符，则会产生截然不同的哈希值。
- 虽然两个对象可以使用相同的 hashCode（称为哈希冲突），但 `hashCode()` 函数可在某种程度上确保唯一性，大多数情况下，两个不同的值各自具有唯一的 hashCode。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/84842b78e78f2f58.png?hl=zh-cn" alt="84842b78e78f2f58.png" style="zoom: 33%;" />

**Set 使用 hashCode 作为数组索引。**

- 由于存在大约 40 亿个不同的哈希代码，因此 `Set` 不仅是一个巨型数组，而且是一个列表数组。
- 外层数组（左侧蓝色数字）分别对应一个可能的 hashCode 范围（也称为桶 bucket）。
- 每个内部列表（右侧绿色）表示集中的各个项。由于哈希冲突相对罕见，即使潜在索引有限，<u>每个数组索引处的内部列表也分别只有一两个项</u>，除非添加了数万个或数十万个元素。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/a44f3d05140a19bd.png" alt="a44f3d05140a19bd.png" style="zoom:33%;" />

集合具有两个重要属性：

1. 查找速度更快。与列表相比，**可以更快地查找集合中的特定元素**，尤其是对于大型集合。
   - `List` 的 `indexOf()` 要求从头开始检查每个元素，直到找到匹配项，但平均而言，检查某个元素是否在集中所用的时间相同，无论它是第一个元素还是第十万个元素。
2. 占内存空间更大。**对于相同数量的数据，集合占用的内存往往比列表多**，因为所需的数组索引通常比集中的数据多。

> 与普遍的观点相反，检查集中是否含有某个元素所用的时间是不固定的，实际上取决于集中的数据量。不过，由于哈希冲突通常很少见，因此需要检查的元素数量仍然比搜索列表中的项要小几个数量级。



集合的优势在于**确保唯一性**，借助集合可以快速找到是否存在某个元素。

与 `List` 和 `MutableList` 一样，集合既有 `Set` 也有 `MutableSet`。

`MutableSet` 会实现 `Set`，因此任何实现 `MutableSet` 的类都需要同时实现这两者。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/691f995fde47f1ff.png?hl=zh-cn" alt="691f995fde47f1ff.png" style="zoom:33%;" />

#### 使用 `MutableSet`

使用 `mutableSetOf()` 创建一个 `Set`，会返回 `MutableSet`，其默认实现为 `LinkedHashSet()`。

- 检查集合的大小：可以使用 `size` 属性输出该集的大小。
- 向集合中添加元素：`MutableSet` 具有 `add()` 方法。它只需要为所添加的元素使用一个参数。**==集中的元素未必是有序的，因此没有索引==**。
- 检查某个元素是否在集合中：
  - `contains()` 方法接受一个参数，并返回一个布尔值表示该集中是否包含指定的元素。
  - 使用 `in` 运算符检查某个元素是否在集合中。
  - `"Pluto" in solarSystem` 相当于 `solarSystem.contains("Pluto")`。
- 从集合中移除某个元素：`remove()`。
  - 集是无序的集合。您无法按索引从集中移除某个值，因为集没有索引。

```kotlin
fun main() {
    val solarSystem = mutableSetOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
		println(solarSystem.size) // 8
    
  	solarSystem.add("Pluto")
		println(solarSystem.size) // 9
    println(solarSystem.contains("Pluto")) // true
    
    solarSystem.add("Pluto") // 重复添加元素不会更改集合的大小
		println(solarSystem.size) // 9
    
    solarSystem.remove("Pluto")
    println(solarSystem.contains("Pluto")) // false
}
```

### 2.4 Map collection

`Map` 是由键和值组成的集合。之所以称之为映射，是因为**唯一键**会映射到其他值。

键及其附带的值通常称为 `key-value pair`（键值对）。

**映射的键具有唯一性，但映射的值不具有唯一性。**两个不同的键可以映射到同一个值。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/8571494fb4a106b6.png?hl=zh-cn" alt="8571494fb4a106b6.png" style="zoom: 33%;" />



**通过相应的键访问映射的值通常比在大型列表中（例如使用 `indexOf()`）搜索值更快**。



声明映射：使用 `mapOf()` 或 `mutableMapOf()` 函数。

映射需要两个泛型类型（以英文逗号隔开），一个用于键，另一个用于值。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/affc23a0e1f2b223.png?hl=zh-cn" alt="affc23a0e1f2b223.png" style="zoom: 33%;" />

如果映射具有初始值，则还可以使用类型推断。

- 要使用初始值填充映射，每个键值对都由以下部分组成：首先是键，后跟 `to` 运算符，而后是值。
- 每个键值对均以英文逗号隔开。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-collections/img/2ed99c3391c74ec4.png?hl=zh-cn" alt="2ed99c3391c74ec4.png" style="zoom:33%;" />

#### 使用 `MutableMap`

使用 `mutableMapOf()` 创建一个映射。

```kotlin
val solarSystem = mutableMapOf(
    "Mercury" to 0,
    "Venus" to 0,
    "Earth" to 1,
    "Mars" to 2,
    "Jupiter" to 79,
    "Saturn" to 82,
    "Uranus" to 27,
    "Neptune" to 14
)
```

**检查 Map 的大小**：可以使用 `size` 属性输出映射的大小。

```kotlin
println(solarSystem.size)
```

**向映射中新增一个键值对**：可以使用下标语法。

```kotlin
solarSystem["Pluto"] = 5
```

**访问某个键对应的值**：

- 下标语法。
- 使用 `get()` 方法访问值。
- 无论您使用下标语法还是调用 `get()`，您传入的键都可能不存在于映射中。**如果没有键值对，它将返回 null。**

```kotlin
println(solarSystem["Pluto"]) // 5
println(solarSystem.get("Theia")) // null
```

**从映射中移除指定键的键值对**：`remove()`。

```kotlin
solarSystem.remove("Pluto")
println(solarSystem.size) // 8
```

**修改已存在键的值：**

- 下标语法。
- `put()` 方法。

```kotlin
solarSystem["Jupiter"] = 78
println(solarSystem["Jupiter"]) // 78	
```

### 2.5 总结

- 数组存储的是同一类型的有序数据，且具有固定大小。
- 数组用于实现很多其他集合类型。
- 列表是可调整大小的有序集合。
- Set 是无序集合，不能包含重复项。
- Map 的工作方式与 Set 类似，用于存储指定类型的键/值对。

## 03 将常用高阶函数与集合结合使用

高阶函数即接受其他函数作为参数和/或返回函数的函数，例如 `repeat()`。

高阶函数与集合密切相关，因为它们让您用较少的代码即可完成常见任务（例如排序或过滤）。

```kotlin
class Cookie(
    val name: String,
    val softBaked: Boolean,
    val hasFilling: Boolean,
    val price: Double
)

val cookies = listOf(
    Cookie(
        name = "Chocolate Chip",
        softBaked = false,
        hasFilling = false,
        price = 1.69
    ),
    Cookie(
        name = "Banana Walnut",
        softBaked = true,
        hasFilling = false,
        price = 1.49
    ),
    Cookie(
        name = "Vanilla Creme",
        softBaked = false,
        hasFilling = true,
        price = 1.59
    ),
    Cookie(
        name = "Chocolate Peanut Butter",
        softBaked = false,
        hasFilling = true,
        price = 1.49
    ),
    Cookie(
        name = "Snickerdoodle",
        softBaked = true,
        hasFilling = false,
        price = 1.39
    ),
    Cookie(
        name = "Blueberry Tart",
        softBaked = true,
        hasFilling = true,
        price = 1.79
    ),
    Cookie(
        name = "Sugar and Sprinkles",
        softBaked = false,
        hasFilling = false,
        price = 1.39
    )
)
fun main() {

}
```

### 3.1 `forEach()` 和包含 lambda 的字符串模板

#### 使用 `forEach()` 循环遍历列表

`forEach()` 函数会针对集合中的每个项分别执行一次**作为形参传递的函数**。

方法签名如下所示：

- `T` 对应于集合包含的任何数据类型。
- 由于 lambda 接受单个形参，因此您可以省略名称，并使用 `it` 来引用此形参。

```kotlin
forEach(action: (T) -> Unit)
```

使用 `forEach()` 函数输出 `cookies` 列表中的项。

- 使用尾随 lambda 语法对 `cookies` 列表调用 `forEach()`。**由于尾随 lambda 是唯一实参，因此在调用函数时可以省略括号。**

```kotlin
fun main() {
	cookies.forEach {
        println("Menu item: $it")
    }
}
/*
	Menu item: Cookie@4769b07b
  Menu item: Cookie@6f539caf
  Menu item: Cookie@79fc0f2f
  Menu item: Cookie@50040f0c
  Menu item: Cookie@2dda6444
  Menu item: Cookie@5e9f23b4
  Menu item: Cookie@4783da3f
*/
```

#### 在字符串中嵌入表达式

将美元符号 (`$`) 与变量名称结合使用**可以将变量名称插入字符串**。

不过，当与点运算符 (`.`) 结合以访问属性时，这种方法就无法实现预期效果了。

```kotlin
cookies.forEach {
    println("Menu item: $it.name")
}
/*
	Menu item: Cookie@4769b07b.name
  Menu item: Cookie@6f539caf.name
  Menu item: Cookie@79fc0f2f.name
  Menu item: Cookie@50040f0c.name
  Menu item: Cookie@2dda6444.name
  Menu item: Cookie@5e9f23b4.name
  Menu item: Cookie@4783da3f.name
*/
```

如需访问属性并将其嵌入字符串，您需要使用表达式。用大括号将表达式括住，使其成为字符串模板的一部分。

- lambda 表达式中可以访问属性、执行数学运算、调用函数等。
- 并且系统会将 lambda 的返回值插入字符串。

![2c008744cee548cc.png](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-higher-order-functions/img/2c008744cee548cc.png?hl=zh-cn)

```kotlin
fun main() {
	cookies.forEach {
        println("Menu item: ${it.name}")
    }
}
/*
	Menu item: Chocolate Chip
  Menu item: Banana Walnut
  Menu item: Vanilla Creme
  Menu item: Chocolate Peanut Butter
  Menu item: Snickerdoodle
  Menu item: Blueberry Tart
  Menu item: Sugar and Sprinkles
*/
```

### 3.2 `map()`

借助 `map()` 函数，您可以将一个集合转换为元素数量相同的新集合。

- 例如，`map()` 可将 `List<Cookie>` 转换为仅包含饼干 `name` 的 `List<String>`。

```kotlin
fun main() {
    val fullMenu = cookies.map {
        "${it.name} - $${it.price}"
    }
    println("Full menus:")
    fullMenu.forEach {
        println(it)
    }
}
/*
	Full menus:
  Chocolate Chip - $1.69
  Banana Walnut - $1.49
  Vanilla Creme - $1.59
  Chocolate Peanut Butter - $1.49
  Snickerdoodle - $1.39
  Blueberry Tart - $1.79
  Sugar and Sprinkles - $1.39
*/
```

### 3.3 `filter()`

借助 `filter()` 函数可以创建集合的子集。

- `map()` 函数始终生成大小相同的集合，而 `filter()` 生成的集合的大小是等于或小于原始集合的。
- 与 `map()` 不同，`filter()` 生成的集合具有相同的数据类型，因此过滤 `List<Cookie>` 将产生另一个 `List<Cookie>`。

`filter()` 接受单个 lambda 表达式作为形参。lambda 包含一个形参代表集合中的每个项，并返回 `Boolean` 值。

对集合中每个项执行 lambda 表达式：

- 如果结果为 `true`，则表示此项包含在新集合中。
- 如果结果为 `false`，则表示此项不包含在新集合中。

```kotlin
fun main() {
    val softBakedMenu = cookies.filter {
        it.softBaked
    }
    println("Soft cookies:")
    softBakedMenu.forEach {
        println("${it.name} - $${it.price}")
    }
}
/*
  Soft cookies:
  Banana Walnut - $1.49
  Snickerdoodle - $1.39
  Blueberry Tart - $1.79
*/
```

### 3.4 `groupBy()`

`groupBy()` 函数用于**根据函数将列表转换为映射**。

- 映射中的键为：**函数的每个唯一返回值**。
- 映射中的值为：**生成相应唯一返回值的集合中的项**。

键的数据类型与传递到 `groupBy()` 的函数的返回类型相同。值的数据类型是原始列表中项的 List。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-higher-order-functions/img/54e190b34d9921c0.png?hl=zh-cn" alt="54e190b34d9921c0.png" style="zoom:50%;" />

> 值不必与列表类型相同。另一种版本的 [`groupBy()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html) 可以将值转换为其他类型。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-higher-order-functions/img/4219eacdaca33f1d.png?hl=zh-cn" alt="4219eacdaca33f1d.png" style="zoom:33%;" />

使用 `groupBy()` 根据 `softBaked` 属性对菜单内容进行分组。

- `groupBy()` 的返回类型将为 `Map<Boolean, List<Cookie>>`。
- `Map` 的结果可能为 null，因此可以使用 Elvis 运算符（`?:`）兜底一个空列表。
  - 或使用 `emptyList()` 创建一个空列表，并且更具可读性。

```kotlin
fun main() {
    val groupedMenu = cookies.groupBy { it.softBaked }
    val softBakedMenu = groupedMenu[true] ?: listOf()
    val crunchyMenu = groupedMenu[false] ?: listOf()
  
      println("Soft cookies:")
      softBakedMenu.forEach {
          println("${it.name} - $${it.price}")
      }
      println("Crunchy cookies:")
      crunchyMenu.forEach {
          println("${it.name} - $${it.price}")
      }
}
```

如果只需要将列表一分为二，可以使用 [`partition()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/partition.html) 函数来替代。

### 3.5 `fold()`

`fold()` 函数用于从集合中生成单个值。常用于计算总价，或汇总列表中的所有元素以求平均值。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-higher-order-functions/img/a9e11a1aad05cb2f.png?hl=zh-cn" alt="a9e11a1aad05cb2f.png" style="zoom: 33%;" />

`fold()` 函数具有两个形参：

- 初始值。调用函数时，系统会推断数据类型（也就是说，系统会将 `0` 的初始值推断为 `Int`）。
- 返回与初始值类型相同的值的 lambda 表达式。

此 lambda 表达式还包含两个形参：

- 第一个称为累加器。其数据类型与初始值相同。可将其视为累计总额。每次调用 lambda 表达式时，**累加器都等于上次调用 lambda 时的返回值。**
- 第二个形参的类型与集合中的每个元素相同。

使用 `fold()` 来计算所有饼干的总价格。

```kotlin
fun main() {
    val totalPrice = cookies.fold(0.0) { total, cookie ->
        total + cookie.price
    } 
    println("Total price: $${totalPrice}") // Total price: $10.83
}
```

`fold()` 在一些其他的语言中被称为 `reduce()`。

**Kotlin 还具有名称为 [`reduce()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) 的专属函数，其累加器从集合中的第一个元素开始计算，而不是从作为实参传递的初始值开始计算。**

Kotlin 集合还具有一个适用于数字类型的 [`sum()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum.html) 函数，以及一个高阶 [`sumOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum-of.html) 函数。



### 3.6 `sortedBy()`

`sort()` 函数可用于对集合元素进行排序。

不过，这不适用于 `Cookie` 对象的集合。`Cookie` 类具有多个属性，Kotlin 不知道您要按哪些属性（`name`、`price` 等）进行排序。

对于这些情况，Kotlin 集合提供了一个 `sortedBy()` 函数。

- 通过 `sortedBy()`，可以指定一个 lambda 返回**作为排序依据的属性**。例如，如果您想按 `price` 排序，lambda 会返回 `it.price`。
- **只要值的数据类型的排列顺序是自然的（字符串按字母顺序排序，数值按升序排序），相应类型的集合的排序方式就会与其一模一样。**

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-higher-order-functions/img/5fce4a067d372880.png?hl=zh-cn" alt="5fce4a067d372880.png" style="zoom:33%;" />

使用 `sortedBy()` 来按字母顺序对饼干列表进行排序。

```kotlin
fun main() {
    val alphabeticalMenu = cookies.sortedBy {
        it.name
    }
    println("Alphabetical menu:")
    alphabeticalMenu.forEach {
        println(it.name)
    }
}
/*
  Alphabetical menu:
  Banana Walnut
  Blueberry Tart
  Chocolate Chip
  Chocolate Peanut Butter
  Snickerdoodle
  Sugar and Sprinkles
  Vanilla Creme
*/
```

> 如果数据类型的排列顺序是自然的，Kotlin 集合还会包含一个 [`sort()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html) 函数。
