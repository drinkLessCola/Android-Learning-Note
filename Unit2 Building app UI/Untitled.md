### 01 Compose 中的状态

- 应用中的状态是指可以随时间变化的任何值。这个定义非常宽泛，包含应用中从数据库到变量等的所有元素。
- 数据库是结构化信息的有组织集合，就像计算机中的文件一样。

#### 组合

如果想让界面在应用运行时或用户与应用互动时发生变化，需要依赖名为“重组（recomposition）”的进程来更新应用的组合。

- 组合(Composition)：对 Compose 在执行可组合项时所构建界面的描述。Compose 应用调用可组合函数，以将数据转换为界面。
- 重组(Recomposittion)：**如果发生状态更改，Compose 会使用新状态重新执行受影响的可组合函数**，从而创建更新后的界面。Compose 会为您安排重组。

当 Compose 在 initial composition 期间首次运行您的可组合项时，它会跟踪被调用的可组合项。重组 Recomposition 是指 Compose 重新执行可能因数据更改而更改的可组合项，然后更新组合以反映所有更改。

组合只能通过 initial composition 生成且只能通过重组进行更新。重组是修改组合的唯一方式。为此，Compose 需要知道要跟踪的状态，以便在收到更新时能够安排重组。

在 Compose 中，可以使用 **[`State`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/State?hl=zh-cn) 和 [`MutableState`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState?hl=zh-cn) 类型**让应用中的**状态可被 Compose 观察或跟踪**。

- `State` 类型不可变，因此您只能读取其中的值。
- [`MutableState`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState?hl=zh-cn) 类型是可变的。您可以使用 [`mutableStateOf()`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary?hl=zh-cn#mutableStateOf(kotlin.Any,androidx.compose.runtime.SnapshotMutationPolicy)) 函数来创建可观察的 `MutableState`。它接受初始值作为封装在 `State` 对象中的形参，这样便可使其 `value` 变为可观察。

`mutableStateOf()` 函数返回的值：

- 会保持状态。
- 可变，因此该值可以更改。
- 可观察，因此 Compose 会观察值的所有更改并触发重组以更新界面。

```kotlin
@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
   var amountInput = mutableStateOf("0")
   TextField(
       value = amountInput.value,
       onValueChange = { amountInput.value = it },
       modifier = modifier
   )
}
```

Compose 会跟踪每个读取状态 `value` 属性的可组合项，并在其 `value` 更改时触发重组。

但此时在文本框中输入文本，文本框显示的数值不会更改：

- 用户在文本框中输入文本后，系统会调用 `onValueChange` 回调，并使用新值更新 `amountInput` 变量。
- Compose 跟踪 `amountInput` 状态，因此当其值更改时，系统会安排重组并再次执行 `EditNumberField()` 可组合函数。
- 在该可组合函数中，`amountInput` 变量会重置为初始 `0` 值。因此，文本框会显示 `0` 值。

需要在重组后保留 `amountInput` 变量的值，以免每次 `EditNumberField()` 函数重组时它都重置为 `0` 值。

#### 使用 remember 函数保存状态

可组合方法可能会因重组而被系统多次调用。如果不保存，可组合项就会在重组期间重置状态。

可组合函数可以使用 **[`remember`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary?hl=zh-cn#remember(kotlin.Function0)) 跨重组存储对象**。初始组合期间，`remember` 函数计算的值会存储在组合中，而存储的值会在重组期间返回。`remember` 和 `mutableStateOf` 函数通常在可组合函数中一起使用，以使状态及其更新正确反映在界面中。

```kotlin
import androidx.compose.runtime.remember
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue

@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
   var amountInput by remember { mutableStateOf("") }
   TextField(
       value = amountInput,
       onValueChange = { amountInput = it },
       modifier = modifier
   )
}
```

- 空字符串是 `amountInput` 变量的初始默认值。
- `by` 是 [Kotlin 属性委托](https://kotlinlang.org/docs/delegated-properties.html)。`amountInput` 属性的**默认 getter 和 setter 函数**分别**委托给 `remember` 类的 getter 和 setter 函数。**

通过添加委托的 getter 和 setter 导入内容，您可以读取和设置 `amountInput`，而**无需引用 `MutableState` 的 `value` 属性**。



- 在初始组合期间，`TextField` 中的 `value` 会设置为初始值，该值为一个空字符串。
- 当用户在文本字段中输入文本时，系统会调用 `onValueChange` lambda 回调，执行 lambda，并将 `amountInput.value` 设置为在文本字段中输入的更新后的值。
- `amountInput` 是 Compose 跟踪的可变状态，系统会安排重组。`EditNumberField()` 可组合函数会重组。由于使用的是 `remember` `{ },` 因此相应更改在重组后会保持不变。
- 文本字段的 `value` 已设置为记住的 `amountInput` 值。文本字段会重组（使用新值在屏幕上重新绘制）。

#### 状态和重组（recomposition）的实际运用

```kotlin
TextField(
    value = amountInput,
    onValueChange = { amountInput = it },
    modifier = modifier,
    label = { Text(stringResource(id = R.string.bill_amount)) },
    singleLine = true,
    keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number)
    // 用于配置屏幕上显示的键盘，以便输入数字、电子邮件地址、网址和密码等内容。
)
```

- `label = { Text(...) }` 添加标签
- `singleLine = true` 设置输入框为单行
- `keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number)` 键盘类型设置为数字。
  - `keyboardOptions` 可配置屏幕上显示的键盘，以便输入数字、电子邮件地址、网址和密码等内容。

#### 显示小费金额

使用 [`NumberFormat`](https://developer.android.com/reference/java/text/NumberFormat?hl=zh-cn) 将小费的格式显示为货币：`NumberFormat.getCurrencyInstance().format(tip)`

```kotlin
private fun calculateTip(amount: Double, tipPercent: Double = 15.0): String {
    val tip = tipPercent / 100 * amount
    return NumberFormat.getCurrencyInstance().format(tip)
}
```

将文本框中输入的文本转换为数值：

```kotlin
val amount = amountInput.toDoubleOrNull() ?: 0.0
```

- `toDoubleOrNull()` 函数是一个预定义的 Kotlin 函数，该函数会将字符串解析为 `Double` 数字并返回结果，而如果字符串不是有效的数字表示法，该函数会返回 `null`。



要在父 Composable 中使用子 Composable 中的状态，需要进行状态提升。状态提升后，子 Composable 将变为无状态。

- 无状态 Composable 指不存储自身状态的可组合项。它会显示作为输入实参提供给它的任何状态。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-using-state/img/4d8b69d49a90683a.png?hl=zh-cn" alt="4d8b69d49a90683a.png" style="zoom:50%;" />

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-using-state/img/38bd92a2346a910b.png?hl=zh-cn" alt="38bd92a2346a910b.png" style="zoom:50%;" />

#### 状态提升（state hoisting）

**==状态提升==**是一种将状态移到其调用方以使组件变为无状态的模式。

当需要执行以下操作时，应该提升状态：

- 与多个可组合函数共享状态。
- 创建可在应用中重复使用的无状态可组合项。

在您从可组合函数中提取状态后，生成的可组合函数称为无状态函数。通过从可组合函数中提取状态，可以将其变为无状态。

- 无状态可组合项是指没有状态的可组合项，这意味着它不会保存、定义或修改新状态。
- 有状态可组合项是指具有可以随时间变化的状态的可组合项。

在设计可组合项时，应该让可组合项拥有尽可能少的状态，并允许在提升状态有意义的情况下通过在可组合项的 API 中公开状态来实现状态提升。



当应用于可组合项时，这通常意味着向可组合项引入以下两个形参：

- `value: T` 形参，即要显示的当前值。
- `onValueChange: (T) -> Unit` - 回调 lambda，会在值更改时触发，以便可以在其他位置更新状态（例如，当用户在文本框中输入一些文本时）。

```kotlin
@Composable
fun TipTimeLayout() {
   var amountInput by remember { mutableStateOf("") }
   val amount = amountInput.toDoubleOrNull() ?: 0.0
   val tip = calculateTip(amount)

   Column(
       modifier = Modifier
            .statusBarsPadding()
            .padding(horizontal = 40.dp)
            .verticalScroll(rememberScrollState())
            .safeDrawingPadding(),
       horizontalAlignment = Alignment.CenterHorizontally,
       verticalArrangement = Arrangement.Center
   ) {
       Text(
           text = stringResource(R.string.calculate_tip),
           modifier = Modifier
               .padding(bottom = 16.dp, top = 40.dp)
               .align(alignment = Alignment.Start)
       )
       EditNumberField(
           value = amountInput,
           onValueChange = { amountInput = it },
           modifier = Modifier
               .padding(bottom = 32.dp)
               .fillMaxWidth()
       )
       Text(
           text = stringResource(R.string.tip_amount, tip),
           style = MaterialTheme.typography.displaySmall
       )
       Spacer(modifier = Modifier.height(150.dp))
   }
}

@Composable
fun EditNumberField(
   value: String,
   onValueChange: (String) -> Unit,
   modifier: Modifier = Modifier
) {
   TextField(
       value = value,
       onValueChange = onValueChange,
       singleLine = true,
       label = { Text(stringResource(R.string.bill_amount)) },
       keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
       modifier = modifier
   )
}
```



### 02 计算自定义小费

使用 `@StringRes` 注解为该函数形参添加注解，指明 `label` 形参应为字符串资源引用：

```kotlin
@Composable
fun EditNumberField(
    @StringRes label: Int,
    value: String,
    onValueChanged: (String) -> Unit,
    modifier: Modifier = Modifier
)
```

#### 设置操作按钮

键盘操作按钮是指键盘端的按钮。

| **属性**                                                     | **键盘上的操作按钮**                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`ImeAction.Search`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/ImeAction?hl=zh-cn#Search()) 用户想要执行搜索时使用。 | ![该图片表示用于执行搜索的“Search”图标。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/67fd7f2efed7e677.png?hl=zh-cn) |
| [`ImeAction.Send`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/ImeAction?hl=zh-cn#Send()) 用户想要发送输入字段中的文本时使用。 | ![该图片表示用于发送输入字段中的文本的“Send”图标。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/b19be317a5574818.png?hl=zh-cn) |
| [`ImeAction.Go`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/ImeAction?hl=zh-cn#Go()) 用户想要跳转到输入文本的目的地时使用。 | ![该图片表示用于前往输入字段中的文本目标的“Go”图标。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/ac61541c3a56b2bb.png?hl=zh-cn) |

在此任务中，您将为文本框设置两个不同的操作按钮：

- 为 **Bill Amount** 文本框设置 **Next** 操作按钮，用于指示用户已完成当前输入并想移到下一个文本框。
- 为 **Tip Percentage** 文本框设置 **Done** 操作按钮，用于指示用户已完成输入。

包含这些按钮的键盘示例如下面这些图片所示：

| ![img](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/46559a252132af44.png?hl=zh-cn) | ![img](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/b8972b81b513b0a.png?hl=zh-cn) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |



```kotlin
TextField(
    keyboardOptions = KeyboardOptions.Default.copy(
        keyboardType = KeyboardType.Number,
        imeAction = ImeAction.Next
    )
)
```

- 使用 [`KeyboardOptions.Default.copy()`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/text/KeyboardOptions?hl=zh-cn#copy(androidx.compose.ui.text.input.KeyboardCapitalization,kotlin.Boolean,androidx.compose.ui.text.input.KeyboardType,androidx.compose.ui.text.input.ImeAction)) 函数，确保使用其他默认选项。
- `imeAction = ImeAction.Next` 设置键盘操作按钮。

#### 添加开关

开关属于选择控件，可用于输入决策或声明偏好设置。

切换开关具有两种状态，可让用户在两个选项之间进行选择。切换开关由滑道、滑块和可选图标组成，如下面这些图片所示：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/b4f7f68b848bcc2b.png?hl=zh-cn" alt="b4f7f68b848bcc2b.png" style="zoom:80%;" />

```kotlin
@Composable
fun RoundTheTipRow(
    roundUp: Boolean,
    onRoundUpChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .size(48.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Text(text = stringResource(id = R.string.round_up_tip))
        Switch(
            checked = roundUp,
            onCheckedChange = onRoundUpChange,
            modifier = Modifier
                .fillMaxWidth()
                .wrapContentWidth(Alignment.End)
        )
    }
}
```

```kotlin
private fun calculateTip(amount: Double, tipPercent: Double = 15.0, roundUp: Boolean): String {
    var tip = tipPercent / 100 * amount
    if (roundUp) tip = kotlin.math.ceil(tip)
    return NumberFormat.getCurrencyInstance().format(tip)
}	
```

[`kotlin.math.ceil(x)`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.math/ceil.html) 函数会对给定值进行向上取整。例如，它会将 10.65 取整为 11.00。此函数可以接受 `Double` 或 `Float` 数字。

#### 添加对横屏显示的支持

Android 设备有多种外形规格（手机、平板电脑、可折叠设备和 ChromeOS 设备），并且屏幕尺寸各异。您的应用应同时支持竖屏和横屏显示。

```kotlin
Column(
    modifier = Modifier
        .statusBarsPadding()
        .padding(horizontal = 40.dp)
        .verticalScroll(rememberScrollState())，
    horizontalAlignment = Alignment.CenterHorizontally,
    verticalArrangement = Arrangement.Center
) {...}
```

向修饰符 modifier 添加 `.verticalScroll(rememberScrollState())`，使列能够垂直滚动。`rememberScrollState()` 会创建并自动记住滚动状态。

#### 向文本字段添加前置图标

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-calculate-tip/img/9318c9a2414c4add.png?hl=zh-cn" alt="9318c9a2414c4add.png" style="zoom: 33%;" />

```kotlin
@Composable
fun EditNumberField(
    @StringRes label: Int,
    @DrawableRes leadingIcon: Int, // +
    keyboardOptions: KeyboardOptions,
    value: String,
    onValueChanged: (String) -> Unit,
    modifier: Modifier = Modifier
) {
  TextField(
    value = value,
    leadingIcon = { Icon(painter = painterResource(id = leadingIcon), null) }, // +
    //...
	)
}
```

### 03 编写自动化测试

#### **自动化测试的类型**

##### 本地测试

本地测试是一种自动化测试，可直接测试一小段代码，以确保其能够正常运行。

借助本地测试，您可以测试函数、类和属性。本地测试在工作站上执行，这意味着它们在开发环境中运行，不需要使用实体设备或模拟器。这是在计算机上运行本地测试的一大亮点。在计算机资源方面，本地测试的开销也非常低，因此即使资源有限，此类测试也可以快速运行。Android Studio 已准备好自动运行本地测试。

##### **UI 测试**

在 Android 开发环境中，UI 测试是一种界面测试。借助 UI 测试，您可以测试应用中依赖于 Android API 及其平台 API 和服务的部分。

与本地测试不同，界面测试会启动应用的全部或部分内容、模拟用户互动，并检查应用响应是否适当。在此课程中，界面测试均在实体设备或模拟器上运行。

在 Android 上运行 UI 测试时，测试代码实际上会内置到其自己的 Android 应用软件包 (APK) 中，就像常规 Android 应用一样。APK 是一个压缩文件，其中包含了在设备或模拟器上运行应用所需的所有代码和文件。该测试 APK 将与常规应用 APK 一起安装在设备或模拟器上。然后，测试 APK 会针对应用 APK 运行测试。

#### 编写本地测试

本地测试会直接测试应用代码中的方法，因此要测试的方法必须对测试类和方法可用。

如将 `private` 标识变为 `internal`，并添加 `@VisibleForTesting` 注解。

- 这会将该方法设为公开状态，但会向其他元素指明该方法仅为测试目的而公开。

**test** 目录要求的应用软件包结构与应用代码所在的 `main` 目录相同。

- 如果您的应用代码会写入 **main > java > com > example > tiptime**，则您的本地测试将写入 **test > java > com > example > tiptime**。

编写自动化测试需要注意以下几点：

- 以方法的形式编写自动化测试。
- 使用 `@Test` 为方法添加注解。这样一来，**编译器就能知道相应方法属于测试方法，**从而相应地运行该方法。
- 测试方法使用逻辑的方式与常规应用方法不同。测试方法不关注内容的实现方式。它们会严格检查给定输入的预期输出。也就是说，测试方法仅执行一组指令以断言应用的界面或逻辑能够正常运行。
- 测试通常以“断言”结尾，该断言用于确保满足指定条件。断言采用方法调用的形式，其名称中包含“assert”。例如：`assertTrue()` 是 Android 测试中常用的断言。

`assertEquals()` 方法接受 2 个形参：一个预期值和一个实际值。如果这些值相等，则断言和测试通过。如果不相等，则断言和测试失败。

```kotlin
import org.junit.Assert.assertEquals
import org.junit.Test
import java.text.NumberFormat

class TipCalculatorTests {

    @Test
    fun calculateTip_20PercentNoRoundup() {
        val amount = 10.00
        val tipPercent = 20.00
        val expectedTip = NumberFormat.getCurrencyInstance().format(2)
        val actualTip = calculateTip(amount = amount, tipPercent = tipPercent, false)
        assertEquals(expectedTip, actualTip)
    }
}
```

`JUnit` 库中有许多断言。您可能遇到的一些常见断言包括：

- `assertEquals()`
- `assertNotEquals()`
- `assertTrue()`
- `assertFalse()`
- `assertNull()`
- `assertNotNull()`
- `assertThat()`

#### 编写 UI 测试

UI 测试代码与本地测试代码完全不同。

UI 测试会**测试应用的实际实例及其界面**，因此，您必须设置界面内容，类似于在 `MainActivity.kt` 文件的 `onCreate()` 方法中设置的内容。您需要先执行该操作，然后才能编写 UI 测试。



编译器知道 `androidTest` 目录中带有 `@Test` 注解的方法引用的是 UI 测试，而 `test` 目录中带有 `@Test` 注解的方法引用的是本地测试。

在使用 Compose 的 UI 测试中，可以直接**对界面组件调用断言**。

```kotlin
import java.text.NumberFormat

@Test
fun calculate_20_percent_tip() {
    composeTestRule.setContent {
        TipTimeTheme {
            Surface (modifier = Modifier.fillMaxSize()){
                TipTimeLayout()
            }
        }
    }
   composeTestRule.onNodeWithText("Bill Amount")
      .performTextInput("10")
   composeTestRule.onNodeWithText("Tip Percentage").performTextInput("20")
   
  val expectedTip = NumberFormat.getCurrencyInstance().format(2)
   
  composeTestRule.onNodeWithText("Tip Amount: $expectedTip").assertExists(
      "No node with this text was found."
   )
}
```

