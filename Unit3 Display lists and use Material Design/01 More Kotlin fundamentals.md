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

