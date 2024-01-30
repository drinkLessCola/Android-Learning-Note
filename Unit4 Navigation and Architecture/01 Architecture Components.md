## 01 activity 生命周期的阶段

activity 是 Android 中与用户交互的入口点。

- 过去，一个 activity 在一个应用中仅显示一个屏幕。
- 根据当前的最佳实践，一个 activity 可以根据需要在不同屏幕间切换，从而显示多个屏幕。



**==activity 生命周期==**是 activity 会转换的一组状态。

- activity 生命周期从 Android OS 首次创建 activity 时开始，在 Android OS 销毁 activity 时结束。
- 在销毁时，系统将收回 activity 的资源。

当用户在 activity 之间以及应用内外导航时，每个 activity 会在 activity 生命周期中的状态之间切换。



下图显示了 activity 生命周期的所有状态。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/ca808edb1c95f07a.png?hl=zh-cn" alt="ca808edb1c95f07a.png" style="zoom:50%;" />

activity 生命周期中的每种状态都有一个对应的回调方法，您可以在 `Activity` 类中替换此类方法。

核心的生命周期方法集合包括：

- [`onCreate()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onCreate(android.os.Bundle))
- [`onRestart()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onRestart())
- [`onStart()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onStart())
- [`onResume()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onResume())
- [`onPause()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onPause())
- [`onStop()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onStop())
- [`onDestroy()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onDestroy())

Android 会在 activity 从一种状态切换为另一种状态时调用这些回调，而您可以在自己的 activity 中**替换这些方法，通过执行任务来响应这些生命周期状态变化**。下图显示了生命周期状态以及可用的可替换回调。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/468988518c270b38.png?hl=zh-cn" alt="activity 生命周期方案" style="zoom:50%;" />

- `onRestart()` 方法上的星号表示，每次状态在 **Created** 和 **Started** 之间转换时，系统都不会调用此方法。仅当调用 `onStop()` 并且随后重启 activity 时，系统才会调用此方法。

#### `onCreate()`

`onCreate()` 方法是每个 activity 都必须实现的方法。

**作用**：**为 activity 执行所有一次性初始化**。

- 例如，在 `onCreate()` 中，调用 `setContent()` 以指定 activity 的界面布局。

**执行时机**：系统会在 activity 初始化（OS 在内存中创建新的 `Activity` 对象）之后立即调用一次 `onCreate()` 生命周期方法。

**执行效果**：相应 activity 在执行 `onCreate()` 后会被视为**“已创建（created）”**。

替换 `onCreate()` 方法时，您必须调用父类实现(`super.onCreate()`)来完成 activity 的创建，其他生命周期方法也类似。

#### 日志记录

[`Log`](https://developer.android.com/reference/kotlin/android/util/Log?hl=zh-cn) 类会将消息写入 **Logcat**。**Logcat** 是用于记录消息的控制台。

`Log` 指令有三个重要方面：

- 日志消息的**优先级**。在本示例中：
  - [`Log.v()`](https://developer.android.com/reference/kotlin/android/util/Log?hl=zh-cn#v_1) 会记录详细消息。
  - [`Log.d()`](https://developer.android.com/reference/kotlin/android/util/Log?hl=zh-cn#d) 方法会写入调试消息。
  - `Log` 类中的其他方法包括 [`Log.i()`](https://developer.android.com/reference/kotlin/android/util/Log?hl=zh-cn#i_1)（表示信息性消息）、[`Log.w()`](https://developer.android.com/reference/kotlin/android/util/Log?hl=zh-cn#w(kotlin.String, kotlin.String))（表示警告）和[`Log.e()`](https://developer.android.com/reference/kotlin/android/util/Log?hl=zh-cn#e(kotlin.String, kotlin.String))（表示错误消息）。
- 日志的 `tag`（第一个参数）。标签是一个字符串，可让您更轻松地在 Logcat 中找到自己的日志消息。标签通常是类的名称。
- 实际的日志消息称为 `msg`（第二个参数），它是一个简短的字符串。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/a4ff4aa74384ff6.png?hl=zh-cn" alt="a4ff4aa74384ff6.png" style="zoom:50%;" />

#### `onStart()`

**执行时机**：系统会在调用 `onCreate()` 之后立即调用 `onStart()` 生命周期方法。

**执行效果**：`onStart()` 运行后，您的 activity 会显示在屏幕上。

与为初始化 activity 而仅调用一次的 `onCreate()` 不同，`onStart()` 可在 activity 的生命周期内**由系统多次调用**。

**`onStart()` 需要与相应的 `onStop()` 生命周期方法配对使用。**

- 如果用户启动您的应用后又返回设备的主屏幕，**相应 activity 会停止，并且不会再在屏幕上显示**。
- 如果用户从主屏幕重新返回到应用中，activity 会使用所有相同的值从上次停止的位置恢复，并且 `onStart()` 会再次记录到 Logcat 中。
  - 另请注意，系统不会再次调用 `onCreate()` 方法。
  - 设备旋转导致应用屏幕旋转时，会重新调用 `onCreate` 和 `onStart`。

#### 其他生命周期方法

- `onResume()`
- `onRestart()`
- `onPause()`
- `onStop()`
- `onDestroy()`



当 activity 从头开始启动时，您会看到系统按顺序调用以下三个生命周期回调：

- `onCreate()`，在系统创建应用时调用。
- `onStart()`，使应用显示在屏幕上，但用户无法与应用进行交互。
- `onResume()`，将应用置于前台，用户现在可以与应用交互。

`onResume()` 方法尽管名称如此，**但会在启动时调用，即使没有要恢复的 activity 也是如此。**



### 1.2 探索生命周期用例

#### 用例 1：打开和关闭 activity

##### 首次启动应用

当 activity 首次启动时，系统会调用 `onCreate()`、`onStart()` 和 `onResume()` 回调。

- 调用 `onStart()` 后，该应用将在屏幕上变得可见。
- 调用 `onResume()` 时，应用会获得用户焦点，即用户可以与应用交互。

应用在屏幕上完全显示并且具有用户焦点的这部分生命周期称为[前台生命周期](https://developer.android.com/reference/android/app/Activity?hl=zh-cn#activity-lifecycle)。

##### 点击设备上的返回按钮

在 Logcat 中，系统将按上述顺序调用 `onPause()` 和 `onStop()`。

使用**返回**按钮会导致 activity（和应用）从屏幕中移除并移至 activity 堆栈的后侧。

Android 系统销毁 activity 的原因：

- 代码手动调用 activity 的 [`finish()`](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#finish()) 方法，或用户强制退出应用，Android OS 可能会关闭该 activity。
- 如果应用长时间没有在屏幕上显示，OS 也可能会自行关闭您的 activity。
  - Android 这样做是为了延长电池续航时间，并回收应用正在使用的资源，以供其他应用使用。



> `onCreate()` 和 `onDestroy()` 在单个 activity 实例的生命周期内只会调用一次：
>
> - `onCreate()` 用于首次初始化应用。
> - `onDestroy()` 用于作废、关闭或销毁 activity 可能一直在使用的对象，使其不会继续使用资源（如内存）。

#### 用例 2：离开和返回 activity

返回主屏幕/切换到另一个应用的操作会将应用置于后台，而不是完全关闭。此时系统会调用 `onPause()` 和 `onStop()` 方法。

- 在调用 `onPause()` 后，该应用不会再获得焦点。
- 在调用 `onStop()` 之后，该应用将不再显示在屏幕上。

虽然该 activity 已停止，但 `Activity` 对象仍位于后台内存中。Android OS 尚未销毁 activity。用户可能会返回该应用，因此 Android 会保留您的 activity 资源。

重新返回应用时，activity 使用 `onRestart()` 和 `onStart()` 重启，然后使用 `onResume()` 恢复。

- `onRestart()` 只有在 activity 已经创建之后才会被调用，并且会在系统调用 `onStop()` 时进入 **Created** 状态，但是会返回 **Started** 状态，而不是进入 **Destroyed** 状态。
- `onRestart()` 方法用于放置仅在 activity **不**是首次启动时才需要调用的代码。

#### 用例 3：隐藏部分 activity

在应用启动时：

- 调用 `onStart()` 后，该应用将在屏幕上变得可见。
- 调用 `onResume()` 时，应用会获得用户焦点，即用户可以与应用交互。

应用在屏幕上完全显示并且具有用户焦点的这部分生命周期称为[前台生命周期](https://developer.android.com/reference/android/app/Activity?hl=zh-cn#activity-lifecycle)。

当应用进入后台后：

- 在 `onPause()` 后便会丢失焦点。
- 在 `onStop()` 后，该应用将不再可见。



区分**==焦点==**与**==可见性==**之间的差异很重要。

- 某个 activity 有可能在屏幕上部分可见，但没有用户焦点。

  

当点击应用右上角的共享按钮时，共享 activity 会出现在屏幕的下半部分，但相应 activity 在上半部分仍然可见。

此时，系统仅调用了 `onPause()`。

- 系统没有调用 `onStop()`，因为相应 activity 仍然部分可见。但是，该 activity 没有用户焦点，并且用户无法与之交互。
- 位于前台的“共享”activity 具有用户焦点。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-activity-lifecycle/img/9403e9f107707aaa.png?hl=zh-cn" alt="9403e9f107707aaa.png" style="zoom: 33%;" />

**在 `onPause()` 中运行的任何代码都会阻止其他内容显示**，因此请使 `onPause()` 中的代码保持轻量级。

- 例如，当有来电时，`onPause()` 中的代码可能会延迟来电通知。



`onResume()` 和 `onPause()` 都必须处理焦点。

- 当相应 activity 获得焦点时，系统会调用 `onResume()`。
- 当该 activity 失去焦点时，系统会调用 `onPause()`。

###1.3 探索配置变更

当设备的状态发生了根本性改变，以至于系统解决改变的最简单方式就是完全关闭并重建 activity 时，就会发生配置变更。

- 如果用户更改了设备语言，整个布局可能就需要更改为适应不同的文本方向和字符串长度。
- 如果用户将设备插入基座或添加物理键盘，应用布局可能需要利用不同的显示尺寸或布局。
- 如果设备屏幕方向发生变化，比如设备从竖屏旋转为横屏或反过来，布局可能需要改为适应新的屏幕方向。

#### 配置变更会导致系统调用 `onDestroy()`

屏幕旋转是导致 activity 关闭并重启的一种配置变更类型。

当发生配置变更时，**Android 会调用所有 activity 生命周期的关闭回调**。然后，Android 会**从头开始重启相应 activity，同时运行所有生命周期启动回调。**

- 当 activity 关闭时，它会依次调用 `onPause()`、`onStop()` 和 `onDestroy()`。
- 重新启动时，它会依次调用 `onCreate()`、`onStart()`、`onResume()`。



最后一个生命周期回调是 `onDestroy()`。

`onDestroy()` 调用时机：

- 会在 `onStop()` 之后调用。
- 系统会在销毁 activity 之前调用此方法。
- 当应用的代码调用 `finish()`，或者因配置变更而需要销毁并重新创建 activity 时也可能调用。

#### 设备旋转时出现数据丢失情况

当设备旋转，而相应 activity 被关闭并重新创建时，该 activity 会使用**默认值**重新启动，甜点图片、已售甜点的数量和总收入会重置为零。

#### 可组合项的生命周期

应用的界面最初是通过在名称为 Composition 的进程中运行可组合函数而构建的。

创建或更新 Composition 的唯一方法是通过其初始组合和后续重组。

**==重组==**是指 Compose 重新执行状态可能已发生变化的可组合函数，并创建更新后的界面。系统会更新 Composition 以反映这些更改。

- 当应用的状态发生更改时，系统会安排重组。



**可组合函数有自己的生命周期**，并且其生命周期**独立于 activity 生命周期**。

其生命周期由多个事件组成：

- 进入 Composition。
- 重组 0 次或多次。
- 退出 Composition。



为了让 Compose 能够跟踪和触发重组，它需要知晓状态何时发生了更改。

如需向 Compose 指示它应当跟踪某个对象的状态，该对象的类型必须为 [`State`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/State?hl=zh-cn) 或 [`MutableState`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState?hl=zh-cn)。

- `State` 类型不可变，仅允许读取。
- `MutableState` 类型是可变的，并且允许读取和写入。



使用 `mutableStateOf` 声明可以创建可变变量：

这能够让 Compose 在收入值发生更改时触发重组，但无法保留更新后的值。因为每次重新执行可组合项时，该收入值都会重新初始化为初始默认值 `0`。

```kotlin
var revenue = mutableStateOf(0)
```

如需指示 Compose 在重组期间保留并重复使用其值，需要使用 `remember` API 进行声明。

如果 `revenue` 的值发生更改，Compose 会安排读取此值的所有可组合函数进行重组。

```kotlin
var revenue by remember { mutableStateOf(0) }
```

尽管 Compose 会在重组期间记住状态，但在**配置变更**期间不会保留此状态。

**为了让 Compose 在配置更改期间保留状态，必须使用 `rememberSaveable`。**



#### 在配置变量期间使用 `rememberSaveable` 保存值

当 Android OS 销毁并重新创建 activity 时，您可以使用 `rememberSaveable` 函数保存所需的值。

- 如需在重组期间保存值，您需要使用 `remember`。
- 在重组和配置更改期间使用 `rememberSaveable` 保存值。

```kotlin
var revenue by rememberSaveable { mutableStateOf(0) }
// ...
var currentDessertImageId by rememberSaveable {
    mutableStateOf(desserts[currentDessertIndex].imageId)
}
```



> 有时，Android 会关闭整个应用进程，包括与应用相关联的每个 activity。
>
> 发生时机：Android 会在系统承受压力并面临视觉延迟风险时执行此类关闭操作，因此此时不会运行额外的回调或代码。
>
> 效果：应用的进程只会在后台静默地关闭。但对用户来说，应用似乎并没有关闭。当用户返回 Android 系统已关闭的应用时，Android 会重启相应应用。
>
> 您需要确保发生这种情况时不会对用户造成任何数据损失。



## 02 Compose 中的 ViewModel 和状态

[`ViewModel`](https://developer.android.com/topic/libraries/architecture/viewmodel?hl=zh-cn) 是 Android Jetpack 库中的架构组件之一，可用于存储应用数据。当框架在配置更改或其他事件期间销毁并重新创建 activity 时，存储的数据不会丢失。不过，如果 activity 因进程终止而被销毁，数据将会丢失。

`ViewModel` 只能通过快速重新创建 activity 的方式缓存数据。



#### TextField

```
 OutlinedTextField(
               value = "",
               singleLine = true,
               shape = shapes.large,
               modifier = Modifier.fillMaxWidth(),
               colors = TextFieldDefaults.textFieldColors(containerColor = colorScheme.surface),
               onValueChange = { },
               label = { Text(stringResource(R.string.enter_your_word)) },
               isError = false,
               keyboardOptions = KeyboardOptions.Default.copy(
                   imeAction = ImeAction.Done
               ),
               keyboardActions = KeyboardActions(
                   onDone = { }
               )
           )
```

`OutlinedTextField` 可组合项类似于在之前的 Codelab 中应用所使用的 `TextField` 可组合项。

TextField 有两种类型：

- Filled TextField
- Outlined TextField

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/3df34220c3d177eb.png?hl=zh-cn" alt="3df34220c3d177eb.png" style="zoom: 33%;" />

Outlined TextField 的强调效果在视觉上要弱于 Filled TextField。

- 当在表单等有许多文本字段放在一处的位置使用 Outlined TextField 时，其相对较弱的强调效果有助于简化布局。



### 2.1 应用架构

最普适的[架构原则](https://developer.android.com/jetpack/guide?hl=zh-cn#common-principles)是：

- **分离关注点**：应将应用分为函数类，每个类都有各自的责任。
- **通过模型驱动界面**：应该通过模型驱动界面，最好是通过持久性模型。
  - 模型是负责处理应用数据的组件。它们独立于应用中的界面元素和应用组件，因此不受应用的生命周期以及相关的关注点的影响。

#### 推荐的应用架构

基于上面提到的常见架构原则，每个应用应至少有两个层：

- **界面层（UI Layer）**：在屏幕上显示应用数据但独立于数据的层。
- **数据层（Data Layer）**：用于存储、检索和提供应用数据的层。

可以另外添加一个名为“**Domain Layer**”的架构层，以简化和重复使用界面层与数据层之间的交互。该层是可选的。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/a4da6fa5c1c9fed5.png?hl=zh-cn" alt="a4da6fa5c1c9fed5.png" style="zoom: 25%;" />

- 示意图中的箭头表示各个类之间的依赖关系。

##### UI Layer

界面层（或呈现层）的作用：**在屏幕上显示应用数据**。

每当数据因用户互动（例如按了某个按钮）而发生变化时，界面都应随之更新，以反映这些变化。

UI Layer 由以下组件组成：

- **界面元素**：用于在屏幕上呈现数据的组件。您将使用 [Jetpack Compose](https://developer.android.com/jetpack/compose?hl=zh-cn) 构建这些元素。
- **状态容器**：用于**保存数据**、**向界面提供数据**以及**处理应用逻辑**的组件。状态容器的一个示例为 [ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel?hl=zh-cn)。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/6eaee5b38ec247ae.png?hl=zh-cn" alt="6eaee5b38ec247ae.png" style="zoom:33%;" />

##### ViewModel

`ViewModel` 组件用于**存储和暴露界面所使用的状态**。

`ViewModel` 可让您的应用遵循通过模型驱动界面的架构原则。

`ViewModel` 会存储应用相关的数据，这些数据不会在 Android 框架销毁并重新创建 activity 时销毁。

- 与 activity 实例不同，`ViewModel` 对象不会被销毁。
- 应用会在**配置更改**期间自动保留 `ViewModel` 对象，以便它们存储的数据在重组后立即可用。

如需在应用中实现 `ViewModel`，请扩展架构组件库中提供的 `ViewModel` 类，并将应用数据存储在该类中。

##### 界面状态

**界面状态（uiState）**是经过 `ViewModel` 转换的应用数据。

界面是相对用户而言的，而**界面状态是相对应用而言的**。

界面是界面状态的直观呈现。**对界面状态所做的任何更改都会立即反映在界面中。**

**界面是将屏幕上的界面元素与界面状态绑定在一起的结果**。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/9cfedef1750ddd2c.png?hl=zh-cn" alt="9cfedef1750ddd2c.png" style="zoom: 33%;" />

```kotlin
data class NewsItemUiState(
    val title: String,
    val body: String,
    val bookmarked: Boolean = false,
    ...
)
```

**不可变性**

上述界面状态的定义是**不可变**的。

不可变对象可保证**多个来源不会即时更改应用的状态**。

这种保护机制让界面可以专注于发挥单一作用：读取状态并相应地更新其界面元素。

- 因此，**切勿直接在界面中修改界面状态**，**除非界面本身是其数据的唯一来源。**
- 违反这个原则会导致同一条信息有多个可信来源，从而导致数据不一致和轻微的 bug。

### 2.2 添加 ViewModel

向应用添加 `ViewModel`，以存储游戏界面状态（乱序词、单词数和得分）。

添加依赖项：此依赖项用于向 Compose 应用添加**生命周期感知型 ViewModel。**

```kotlin
dependencies {
// other dependencies
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.6.1")
//...
}
```

在 `ui` 软件包中，创建一个名为 `GameViewModel` 的 Kotlin 类/文件，然后从 `ViewModel` 类扩展它。

```kotlin
import androidx.lifecycle.ViewModel

class GameViewModel : ViewModel() {
}
```

在 `ui` 软件包中，为状态界面添加一个名为 `GameUiState` 的模型类。将其设为**数据类**，并为当前的乱序词添加变量。

```kotlin
data class GameUiState(
   val currentScrambledWord: String = ""
)
```

#### StateFlow

[`StateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) 是一个**数据持有者可观察的流**。

- 其 [`value`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/value.html) 属性反映了当前状态值。
- 如需更新状态并将其发送到数据流，需要为 [`MutableStateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-state-flow/index.html) 类的 **value 属性分配一个新值**。

在 Android 中，`StateFlow` 适用于必须维护可观察的不可变状态的类。

可以从 `GameUiState` 公开 `StateFlow`，以便可组合项能够监听界面状态更新，并使屏幕状态在**配置更改**后保持数据不丢失。

在 `GameViewModel` 类中，添加以下 `_uiState` 属性。

```kotlin
import kotlinx.coroutines.flow.MutableStateFlow

class GameViewModel : ViewModel() {
		// Game UI state
		private val _uiState = MutableStateFlow(GameUiState())
}
```



#### 后备属性 Backing property

使用后备属性，可以使 getter 返回确切对象之外的某些其他内容。

Kotlin 框架会为 `var` 属性生成 getter 和 setter。对于 getter 和 setter 方法，可以替换其中一个方法或同时替换这两个方法，并提供自己的自定义行为。

为了实现后备属性，**需要替换 getter 方法以返回只读版本的数据**。如下：

```kotlin
// 声明一个只能在所定义的类中被修改的私有可变变量
private var _count = 0

// 声明另一个不可变公共字段，并重写其 getter，在 getter 中返回私有属性 _count 的值。
val count: Int
		get() = _count
```

假设希望应用数据仅对 `ViewModel` 可见，则可以：

在 `ViewModel` 类之内：

- `_count` 属性设置为 `private` 且可变。因此，只能在 `ViewModel` 类中对其访问和修改。

在 `ViewModel` 类之外：

- 使用 `val` 声明一个 `count` 公共属性（**Kotlin 中的默认可见性修饰符为 `public`**），可从界面控制器等其他类对其进行访问。
- `val` 类型不能包含 setter。它不可变且处于只读状态，因此只能替换 `get()` 方法。
- 当外部类访问此属性时，它会返回 `_count` 的值且其值无法修改。

**后备属性可以防止外部类擅自对 `ViewModel` 内的应用数据进行不安全的更改，但允许外部调用方安全地访问该应用数据的值。**



在 `GameViewModel.kt` 文件中，向名为 `_uiState` 的 `uiState` 添加后备属性 `uiState`，其类型为 `StateFlow<GameUiState>`。

将 `uiState` 设为 `_uiState.asStateFlow()`。**`asStateFlow()` 会使此可变状态流成为只读状态流。**

- 此时只能在 `GameViewModel` 中对 `_uiState` 进行访问和修改。
- 界面可以使用只读属性 `uiState` 读取其值。

```kotlin
import androidx.lifecycle.ViewModel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow

class GameViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(GameUiState())
    val uiState: StateFlow<GameUiState> = _uiState.asStateFlow()
}
```

#### 显示随机乱序词

```kotlin
class GameViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(GameUiState())
    val uiState: StateFlow<GameUiState> = _uiState.asStateFlow()

    private lateinit var currentWord: String
    private var usedWords: MutableSet<String> = mutableSetOf() // 可变集存储游戏中用过的单词

  	// 打乱当前单词的字母顺序
    private fun shuffleCurrentWord(word: String): String {
        val tempWord = word.toCharArray()
        tempWord.shuffle()
        while(String(tempWord).equals(word)) {
            tempWord.shuffle()
        }
        return String(tempWord)
    }

  	// 从列表中随机挑选一个单词并打乱单词的字母顺序
    private fun pickRandomWordAndShuffle(): String {
        currentWord = allWords.random()
        if(usedWords.contains(currentWord)) {
            return pickRandomWordAndShuffle()
        } else {
            usedWords.add(currentWord)
            return shuffleCurrentWord(currentWord)
        }
    }

  	// 用于启动 & 重启游戏
    fun resetGame() {
        usedWords.clear()
        _uiState.value = GameUiState(currentScrambleWord = pickRandomWordAndShuffle())
    }
}
```

向 `GameViewModel` 添加 `init` 代码块，并从中调用 `resetGame()`。

```kotlin
init {
    resetGame()
}
```

现在，在构建应用时仍然不会看到界面有任何变化。因为还没有将 `ViewModel` 中的数据传递给 `GameScreen` 中的可组合项。

### 2.3 构建 Compose 界面

在 Compose 中，更新界面的唯一方式是更改应用的状态。

**您可以控制的是==界面状态==。**每当界面的状态发生变化时，Compose 都会重新创建界面树中已更改的部分。

**可组合项可以接受状态并暴露（expose）事件**。

- 例如，`TextField`/`OutlinedTextField` 接受值并暴露回调 `onValueChange`，该回调会请求回调处理程序以更改值。

```kotlin
var name by remember { mutableStateOf("") }
OutlinedTextField(
		value = name,
		onValueChange = { name = it },
		label = { Text("Name") } 
)
```

由于可组合项接受状态并暴露事件，因此单向数据流模式非常适合 Jetpack Compose。

本部分将重点介绍：

- 如何在 Compose 中实现单向数据流模式。
- 如何实现事件和状态容器。
- 如何在 Compose 中使用 `ViewModel`。

#### 单向数据流

单向数据流 (UDF) 是一种设计模式，在该模式下，**状态向下流动，事件向上流动**。

通过遵循单向数据流，可以将在 **界面中显示状态的可组合项** 与 **应用中存储和更改状态** 的部分解耦。

使用单向数据流的应用的界面更新循环如下所示：

- **事件：**界面的某一部分生成一个事件，并将其向上传递。
  - 例如将按钮点击传递给 ViewModel 进行处理。
  - 或者从应用的其他层传递事件，如指示用户会话已过期。
- **更新状态**：事件处理脚本可能会更改状态。
- **显示状态：**状态容器向下传递状态，而界面会显示此状态。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/61eb7bcdcff42227.png?hl=zh-cn" alt="61eb7bcdcff42227.png" style="zoom: 33%;" />

在应用架构中使用 UDF 模式会产生以下影响：

- `ViewModel` 会存储并暴露界面所使用的状态。
- 界面状态是经过 `ViewModel` 转换的应用数据。
- 界面会向 `ViewModel` 发送用户事件通知。
- `ViewModel` 会处理用户操作并更新状态。
- 更新后的状态将反馈给界面以进行呈现。
- 系统会对导致状态更改的所有事件重复此流程。

#### 传递数据

将 ViewModel 实例传递给界面，即将 `GameViewModel` 传递到 **`GameScreen.kt`** 文件中的 `GameScreen()`。

在 `GameScreen()` 中，使用 ViewModel 实例通过 `collectAsState()` 访问 `uiState`。

```kotlin
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.compose.runtime.collectAsState
import androidx.compose.runtime.getValue

@Composable
fun GameScreen(
	gameViewModel: GameViewModel = viewModel()
) {
		val gameUiState by gameViewModel.uiState.collectAsState()
  	// ...
}
```

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/de93b81a92416c23.png?hl=zh-cn" alt="de93b81a92416c23.png" style="zoom:50%;" />

显示打乱的单词：

```kotlin
fun GameScreen(
    gameViewModel: GameViewModel = viewModel()
) {
    val gameUiState by gameViewModel.uiState.collectAsState()

    Column(
       //...
    ) {
    		// ...
        GameLayout(
            currentScrambledWord = gameUiState.currentScrambleWord,
            modifier = Modifier
                .fillMaxWidth()
                .wrapContentHeight()
                .padding(mediumPadding)
        )
    }
}
```

```kotlin
@Composable
fun GameLayout(
   currentScrambledWord: String,
   modifier: Modifier = Modifier
) {
  Card() {
        Column() {
            Text(/* ... */)
            Text(
                text = currentScrambledWord,  // *
                style = typography.displayMedium
            )
            // ...
        }
    }
}
```

显示用户猜的词：

```kotlin
fun GameLayout(
    userGuess: String,	// *
    currentScrambledWord: String,
    onUserGuessChanged: (String) -> Unit,	// *
    onKeyboardDone: () -> Unit,	// *
) {
    Card() {
        Column() {
            Text(/* ... */)
            Text(
                text = currentScrambledWord,
                style = typography.displayMedium
            )
            Text(/*...*/)
            OutlinedTextField(
                value = userGuess, // *
                onValueChange = onUserGuessChanged, // *
                keyboardActions = KeyboardActions(
                    onDone = { onKeyboardDone() } // *
                )
            )
        }
    }
}
```

```kotlin
@Composable
fun GameScreen(
    gameViewModel: GameViewModel = viewModel()
) {
    val gameUiState by gameViewModel.uiState.collectAsState()
    Column() {
        Text()
        GameLayout(
            currentScrambledWord = gameUiState.currentScrambleWord,
            userGuess = gameViewModel.userGuess, // *
            onUserGuessChanged = { gameViewModel.updateUserGuess(it) }, // *
            onKeyboardDone = {  }, // *
        )
    }
}
```

```kotlin
// GameViewModel.kt
class GameViewModel : ViewModel() {
    var userGuess by mutableStateOf("")
        private set
    
  	fun updateUserGuess(guessedWord: String) {
        userGuess = guessedWord
    }
}
```

### 2.4 验证猜出的单词并更新得分

```kotlin
// GameViewModel
class GameViewModel : ViewModel() {
  	// 检查用户猜的词是否正确
    fun checkUserGuess() {
        if (userGuess.equals(currentWord, ignoreCase = true)) {
            val updatedScore = _uiState.value.score.plus(SCORE_INCREASE)
            updateGameState(updatedScore)
        } else {
            _uiState.update { currentState ->
                currentState.copy(isGuessedWordWrong = true)
            }
        }
        // 重置 UserGuess
        updateUserGuess("")
    }

    private fun updateGameState(updatedScore: Int) {
      	// 已达到单词数上限，更新 isGameOver 标识
         if (usedWords.size == MAX_NO_OF_WORDS) {
            _uiState.update { currentState ->
                currentState.copy(
                    isGuessedWordWrong = false,
                    score = updatedScore,
                    isGameOver = true
                )
            }
        } else {
            _uiState.update { currentState ->
                currentState.copy(
                    isGuessedWordWrong = false,
                    currentScrambleWord = pickRandomWordAndShuffle(),
                    score = updatedScore,
                    currentWordCount = currentState.currentWordCount.inc(),
                )
            }
        }
    }
  
  	// 实现跳过功能
    fun skipWord() {
        updateGameState(_uiState.value.score)
        updateUserGuess("")
    }
}
```

- 如果用户的猜测有误，将 uiState 中的 `isGuessedWordWrong` 设为 `true`。

- 当用户猜测正确时提高 `score` 值，并增加 `currentWordCount`。

- [`MutableStateFlow.`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-state-flow/index.html)[`update()`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/update.html) 会使用指定的值更新 [`MutableStateFlow.value`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-state-flow/value.html)。

- 使用 `copy()` 函数复制对象，您可以更改对象的部分属性，同时保持其余属性不变。示例：

  ```kotlin
  val jack = User(name = "Jack", age = 1)
  val olderJack = jack.copy(age = 2)
  ```

在 `GameUiState` 类中

- 添加一个名为 `isGuessedWordWrong` 的 `Boolean`，并将其初始化为 `false`。
- 针对计数再添加一个变量 `currentWordCount`，并初始化为 `1`。
- 针对累计得分添加一个 `score` 变量并将其初始化为零。
- 添加 `Boolean` 变量 `isGameOver` 并将其设置为 `false`。

```kotlin
data class GameUiState(
   val currentScrambledWord: String = "",
   val isGuessedWordWrong: Boolean = false,
   val currentWordCount: Int = 1,
   val score: Int = 0，
   val isGameOver: Boolean = false
)
```

```kotlin
@Composable
fun GameLayout(
    userGuess: String,
    wordCount: Int,	// * 单词数
    isGuessWrong: Boolean,	// *
    currentScrambledWord: String,
    onUserGuessChanged: (String) -> Unit,
    onKeyboardDone: () -> Unit
) {
  		Text(
          text = stringResource(R.string.word_count, wordCount), // * 展示当前的单词数
          style = typography.titleMedium,
          color = colorScheme.onPrimary
      )
   	// ...
    OutlinedTextField(
        value = userGuess,
				// ...
        onValueChange = onUserGuessChanged,
        // * 根据状态改编 label
        label = {
            if (isGuessWrong) {
                Text(stringResource(R.string.wrong_guess))
            } else {
                Text(stringResource(id = R.string.enter_your_word))
            }
        },
        isError = isGuessWrong, // * 错误状态
        keyboardActions = KeyboardActions(
            onDone = { onKeyboardDone() }
        )
    }
}
```

```kotlin
@Composable
fun GameScreen(
    gameViewModel: GameViewModel = viewModel()
) {
    val gameUiState by gameViewModel.uiState.collectAsState()

    Column() {
        GameLayout(
            wordCount = gameUiState.currentWordCount, // *
            currentScrambledWord = gameUiState.currentScrambleWord,
            onUserGuessChanged = { gameViewModel.updateUserGuess(it) },
            onKeyboardDone = { gameViewModel.checkUserGuess() }, // *
            userGuess = gameViewModel.userGuess,
            isGuessWrong = gameUiState.isGuessedWordWrong, // *
        )
        
        Column() {
            Button(
                modifier = Modifier.fillMaxWidth(),
                onClick = { gameViewModel.checkUserGuess() } // *
            ) {
                Text(
                    text = stringResource(R.string.submit),
                    fontSize = 16.sp
                )
            }

            OutlinedButton(
                onClick = { gameViewModel.skipWord() }, // * 实现跳过功能
                modifier = Modifier.fillMaxWidth()
            ) {
                Text(
                    text = stringResource(R.string.skip),
                    fontSize = 16.sp
                )
            }
        }
      	// * 展示分数
        GameStatus(score = gameUiState.score, modifier = Modifier.padding(20.dp))
    }
}
```



### 2.5 显示游戏结束对话框

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-viewmodel-and-state/img/eb6edcdd0818b900.png?hl=zh-cn" alt="eb6edcdd0818b900.png" style="zoom: 33%;" />

1. 容器
2. 图标（可选）
3. 标题（可选）
4. 辅助文本
5. 分隔线（可选）
6. 操作

```kotlin
AlertDialog(
        onDismissRequest = {
            // Dismiss the dialog when the user clicks outside the dialog or on the back
            // button. If you want to disable that functionality, simply use an empty
            // onCloseRequest.
        },
        title = { Text(text = stringResource(R.string.congratulations)) },
        text = { Text(text = stringResource(R.string.you_scored, score)) },
        modifier = modifier,
        dismissButton = {
            TextButton(
                onClick = {
                    activity.finish()
                }
            ) {
                Text(text = stringResource(R.string.exit))
            }
        },
        confirmButton = {
            TextButton(onClick = onPlayAgain) {
                Text(text = stringResource(R.string.play_again))
            }
        }
    )
```

- `title` 用于在提醒对话框中显示标题
- `text` 用于显示辅助文本
- `dismissButton` 和 `confirmButton` 是文本按钮。
  - 在 `dismissButton` 参数中，显示文本 **Exit**，并通过结束 activity 来终止应用。
  - 在 `confirmButton` 参数中，显示文本 **Play Again**，并重玩游戏。

## 03 为 ViewModel 编写单元测试

如需使用 JUnit 框架来编写单元测试，需要将其作为依赖项添加到应用模块的 **`build.gradle.kts`** 文件中。

在 `app` 模块的 **`build.gradle.kts`** 文件中添加依赖：

```kotlin
plugins {
    ...
}

android {
    ...
}

dependencies {
    ...
    testImplementation("junit:junit:4.13.2")
}
```

- 使用 `implementation` 引入依赖，Android Studio 会将其添加到**生成的应用软件包 (APK) 文件中**。
- 使用 `testImplementation` 引入依赖，表示该配置适用于本地测试源代码，而非应用代码。依赖不会被添加到生成的应用软件包中。

不推荐将单元测试代码及其依赖项嵌入到 APK 文件中：

- 测试代码不会添加用户会使用的任何功能。
- 还会对 APK 大小产生影响。

每个 Android 应用都会经过编译并打包到一个文件中，即所谓的应用软件包 (APK)。

- 内容：该文件包含应用的所有代码、资源（resources）、资产（assets）和清单文件（manifest）。
- 为方便起见，应用软件包文件通常称为 APK，其扩展名为 **.apk**。
- 作用：搭载 Android 的设备使用此文件安装应用。

### 3.1 测试策略

良好的测试策略应围绕代码的不同路径和边界。

在最基本的层面上，可以将测试分类为三种场景：**==成功路径==**、**==错误路径==** 和 **==边界情况==**。

- **成功路径：**成功路径测试（也称为理想路径测试）侧重于测试正向流的功能。正向流是指不存在任何异常或错误情况的流。
- **错误路径**：错误路径测试侧重于测试负向流的功能，即检查应用如何响应**错误情况**或**无效用户输入**。
- **边界情况**：边界情况侧重于测试应用中的边界条件。

#### 创建测试

有效的单元测试通常具有以下四个属性：

- **有针对性**：测试应侧重于某个单元，例如一段代码，通常是某个类或方法。测试应有针对性并侧重于验证单段代码的正确性，而不是同时验证多段代码。
- **易于理解**：当您阅读代码时，代码应当简单且易于理解。开发者应当能够立即一目了然地了解测试背后的意图。
- **确定性**：应保持一致的通过或失败结果。如果您运行测试多次且没有更改任何代码，测试应得到相同的结果。测试应避免不可靠性，也就是在未修改代码的情况下，不得在一个实例中测试失败，而在另一个实例中测试通过。
- **独立性**：测试不需要任何人为互动或设置，即可独立运行。

#### 成功路径

在 `GameViewModel` 的实例已初始化的情况下，如果用正确的猜测词调用 `updateUserGuess()`，随后调用 `checkUserGuess()` 方法，则：

- 系统会将正确的猜测结果传递给 `updateUserGuess()` 方法。
- 调用 `checkUserGuess()` 方法。
- 系统正确更新 `score` 和 `isGuessedWordWrong` 状态的值。

在测试源代码集下创建一个新的软件包 `com.example.android.unscramble.ui.test` 并添加文件 `GameViewModelTest`。

若要为 `GameViewModel` 类编写单元测试，需有该类的一个实例，以便调用该类的方法并验证状态。

```kotlin
class GameViewModelTest {
    private val viewModel = GameViewModel()
}
```

为成功路径编写单元测试：

```kotlin
import org.junit.Test
import org.junit.Assert.assertEquals
import org.junit.Assert.assertFalse

class GameViewModelTest {
    private val viewModel = GameViewModel()

    @Test
    fun gameViewModel_CorrectWordGuessed_ScoreUpdatedAndErrorFlagUnset()  {
      	// 获取当前的游戏界面状态。
      	var currentGameUiState = viewModel.uiState.value
      	// 获取猜测词的字母顺序正确的版本。
      	val correctPlayerWord = getUnscrambledWord(currentGameUiState.currentScrambledWord)
      	
      	viewModel.updateUserGuess(correctPlayerWord)
    		viewModel.checkUserGuess()
      
      	// 断言游戏状态符合预期
      	// 获取新的 UI 状态。
      	currentGameUiState = viewModel.uiState.value
      	// 断言 isGuessedWordWrong 标志被正确更新
      	assertFalse(currentGameUiState.isGuessedWordWrong)
      	// 断言分数被正确更新
       	assertEquals(SCORE_AFTER_FIRST_CORRECT_ANSWER, currentGameUiState.score)
    }
  	
  	// 为使值可读且可重复使用，创建一个伴生对象。
  	companion object {
      	private const val SCORE_AFTER_FIRST_CORRECT_ANSWER = SCORE_INCREASE
    }
}
```

- 以上代码使用 `thingUnderTest_TriggerOfTest_ResultOfTest` 格式来命名测试函数。
- 由于使用了 `MutableStateFlow`，通过 `viewModel.uiState.value` 的方式检索 uiState 是可行的。

#### 错误路径

当不正确的单词作为参数传递给 `viewModel.updateUserGuess()` 方法并调用 `viewModel.checkUserGuess()` 方法时，会发生以下情况：

- `currentGameUiState.score` 属性的值保持不变。
- 由于猜测错误，因此 `currentGameUiState.isGuessedWordWrong` 属性的值会设置为 `true`。

```kotlin
import org.junit.Assert.assertTrue

@Test
fun gameViewModel_IncorrectGuess_ErrorFlagSet() {
		val incorrectPlayerWord = "and"
		 
		viewModel.updateUserGuess(incorrectPlayerWord)
		viewModel.checkUserGuess()
  
  	val currentGameUiState = viewModel.uiState.value
    // 断言分数没有发生改变
    assertEquals(0, currentGameUiState.score)
    // 断言 isGuessedWordWrong 标志被正确更新
    assertTrue(currentGameUiState.isGuessedWordWrong)
}
```

#### 边界情况

当初始化 `GameViewModel` 时，以下情况是正确的。

- `currentWordCount` 属性设置为 `1`。
- `score` 属性设置为 `0`。
- `isGuessedWordWrong` 属性设置为 `false`。
- `isGameOver` 属性设置为 `false`。

```kotlin
import org.junit.Assert.assertNotEquals
@Test
fun gameViewModel_Initialization_FirstWordLoaded() {
		val gameUiState = viewModel.uiState.value
  	val unScrambledWord = getUnscrambledWord(gameUiState.currentScrambledWord)
  	
  	// 断言当前题目的单词顺序已被打乱
    assertNotEquals(unScrambledWord, gameUiState.currentScrambledWord)
    // 断言题目计数为 1
    assertTrue(gameUiState.currentWordCount == 1)
    // 断言初始分数为 0
    assertTrue(gameUiState.score == 0)
    // 断言 isGuessedWordWrong 标志为 false
    assertFalse(gameUiState.isGuessedWordWrong)
    // 断言游戏结束标志为 false
    assertFalse(gameUiState.isGameOver)
}
```

另一种边界情况是，在用户猜测所有单词后测试界面状态。当用户猜对所有单词时，以下情况是正确的：

- 得分是最新的；
- `currentGameUiState.currentWordCount` 属性等于 `MAX_NO_OF_WORDS` 常量的值；
- 属性 `currentGameUiState.isGameOver` 设置为 `true`。

```kotlin
@Test
fun gameViewModel_AllWordsGuessed_UiStateUpdatedCorrectly() {
    var expectedScore = 0
  	var currentGameUiState = viewModel.uiState.value
  	var correctPlayerWord = getUnscrambledWord(currentGameUiState.currentScrambledWord)
    
  	// 重复答题直至完成规定数量
  	repeat(MAX_NO_OF_WORDS) {
        expectedScore += SCORE_INCREASE
        viewModel.updateUserGuess(correctPlayerWord)
        viewModel.checkUserGuess()
        
      	currentGameUiState = viewModel.uiState.value
        correctPlayerWord = getUnscrambledWord(currentGameUiState.currentScrambledWord)
        // 每次答题后断言分数正确地更新
        assertEquals(expectedScore, currentGameUiState.score)
    }
    // 断言答完所有题目后，答题计数被正确更新
    assertEquals(MAX_NO_OF_WORDS, currentGameUiState.currentWordCount)
    // 断言答完所有题目后，游戏结束标志被正确更新为 true
    assertTrue(currentGameUiState.isGameOver)
}
```

#### 测试实例生命周期概览

如果仔细观察 `viewModel` 在测试中的初始化方式，可能会注意到 **`viewModel` 仅初始化了一次，尽管所有测试都使用它。**

- 这不意味着所有测试都重复使用同一 `viewModel` 实例。
- 如果 `gameViewModel_Initialization_FirstWordLoaded` 测试方法在 `gameViewModel_CorrectWordGuessed_ScoreUpdatedAndErrorFlagUnset` 测试方法之后执行，也不会导致初始化测试失败。

测试方法会单独执行，**以避免可变的测试实例状态产生意外的副作用**。

默认情况下，**在执行每个测试方法之前，JUnit 都会==创建测试类的一个新实例==。**

- 目前 `GameViewModelTest` 类中有四种测试方法，因此 `GameViewModelTest` 会实例化四次。每个实例都有自己的 `viewModel` 属性副本。
- 因此，测试执行的顺序无关紧要。