## 01 使用 Compose 实现多屏幕导航

现代 Android 开发中，使用 Jetpack Navigation 组件创建多屏幕应用。

借助 Navigation Compose 组件，可以使用声明式方法在 Compose 中轻松构建多屏幕应用。



### 1.1 定义路线并创建 `NavHostController`

#### 导航组件的组成部分

Navigation 组件有三个主要部分：

- **NavController**：负责在目标页面（即应用中的屏幕）之间导航。
- **NavGraph**：用于映射要导航到的可组合项目标页面。
- **NavHost**：此可组合项充当容器，用于显示 NavGraph 的当前目标页面。

#### 在应用中为目标页面定义路线

在 Compose 应用中，导航（navigation）的一个基本概念就是**路由（route）**。

**==路由== 是可映射至目标页面 并 作为其唯一标识符 的字符串。**

- 类似于 URL，不同 URL 会映射到网站上的不同页面。

**==目标页面==** 通常是与用户看到的内容相对应的 单个可组合项或一组可组合项。



可以**使用枚举类来定义应用的路由**。

- 应用中的屏幕是有限数量的，因此路由的数量也是有限的。
- Kotlin 中的枚举类具有 `name` 属性，该属性会返回 类属性名称 对应的字符串。

添加一个枚举类用于定义路由：

```kotlin
enum class CupcakeScreen() {
		Start,
		Flavor,
		Pickup,
		Summary
}
```

#### 为应用添加 `NavHost`

`NavHost` 是一个可组合项，用于根据给定路由来显示对应的目标页面可组合项。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-navigation/img/fae7688d6dd53de9.png?hl=zh-cn" alt="fae7688d6dd53de9.png" style="zoom: 33%;" />

`NavHost` 参数说明如下：

- **`navController`**：可以使用此对象在屏幕之间导航。是 `NavHostController` 类的实例。
  - 通过调用 navController 的 `navigate()` 方法可以导航到另一个目标页面。
  - 通过从 Composable 调用 `rememberNavController()` 来获取 `NavHostController`。
- **`startDestination`**：以字符串形式表示的路由，用于定义 `NavHost` 首次显示时默认使用的目标页面。
- 与其他可组合项一样，`NavHost` 也接受 `modifier` 参数。

> [`NavHostController`](https://developer.android.com/reference/androidx/navigation/NavHostController?hl=zh-cn) 是 [`NavController`](https://developer.android.com/reference/androidx/navigation/NavController?hl=zh-cn) 类的子类。
>
> 可提供**与 `NavHost` 可组合项搭配使用**的额外功能。



向 `CupcakeScreen.kt` 中的 `CupcakeApp` 可组合项添加一个 `NavHost`。

1. 首先需要创建一个 `navController` 的引用。该引用会被 `NavHost` 以及后续步骤中的 `AppBar` 使用。因此先在 `CupcakeApp()` 可组合项中声明该变量。

```kotlin
@Composable
fun CupcakeApp(
    viewModel: OrderViewModel = viewModel(),
    navController: NavHostController = rememberNavController() // *
) {
    Scaffold(
        topBar = {...}
    ) { innerPadding ->
        // *
        NavHost(
            navController = navController,
            startDestination = CupcakeScreen.Start.name,
            modifier = Modifier.padding(innerPadding)
        ) {

        }
    }
}
```

#### 在 `NavHost` 中处理路由

与其他可组合项一样，`NavHost` 接受函数类型作为其内容。

在 `NavHost` 的函数体中为每一个路由分别**调用 `composable()` 函数**。



`composable()` 函数有两个必需参数：

- **`route`**：与路由名称对应的字符串。可以是任何**唯一的字符串**。例如 `CupcakeScreen` 枚举类的常量的 name 属性。
- **`content`**：定义特定路线显示的可组合项。

> `composable()` 函数是 `NavGraphBuilder` 的扩展函数。

```kotlin
NavHost(
    navController = navController,
    startDestination = CupcakeScreen.Start.name,
    modifier = Modifier.padding(innerPadding)
) {
    composable(route = CupcakeScreen.Start.name) {
        StartOrderScreen(
            quantityOptions = DataSource.quantityOptions,
            onNextButtonClicked = {
                viewModel.setQuantity(it)
                navController.navigate(CupcakeScreen.Flavor.name)
            },
            modifier = Modifier
                .fillMaxSize()
                .padding(dimensionResource(id = R.dimen.padding_medium))
        )
    }

    composable(route = CupcakeScreen.Flavor.name) {
      	// 获取对 LocalContext.current 的引用。
      	// Context 是一个抽象类，其实现由 Android 系统提供。它允许访问 特定于应用程序的资源 和 类，以及对应用程序级操作（例如启动活动等）的调用。
      	// 可以使用此变量从视图模型中的资源 ID 列表中获取字符串以显示口味列表。
        val context = LocalContext.current
        SelectOptionScreen(
            subtotal = uiState.price,
            options = DataSource.flavors.map { id -> context.resources.getString(id) },
            onSelectionChanged = { viewModel.setFlavor(it) },
            onNextButtonClicked = { navController.navigate(CupcakeScreen.Pickup.name) },
            onCancelButtonClicked = {
                cancelOrderAndNavigateToStart(viewModel, navController)
            },
            modifier = Modifier.fillMaxHeight()
        )
    }

    composable(route = CupcakeScreen.Pickup.name) {
        SelectOptionScreen(
            subtotal = uiState.price,
            options = uiState.pickupOptions,
            onSelectionChanged = { viewModel.setDate(it) },
            onNextButtonClicked = { navController.navigate(CupcakeScreen.Summary.name) },
            onCancelButtonClicked = {
                cancelOrderAndNavigateToStart(viewModel, navController)
            },
            modifier = Modifier.fillMaxHeight()
        )
    }

    composable(route = CupcakeScreen.Summary.name) {
        val context = LocalContext.current
        OrderSummaryScreen(
            orderUiState = uiState,
            onCancelButtonClicked = {
                cancelOrderAndNavigateToStart(viewModel, navController)
            },
            onSendButtonClicked = { subject: String, summary: String ->
                shareOrder(context, subject = subject, summary = summary)
            },
            modifier = Modifier.fillMaxHeight()
        )
    }

```

### 1.2 在多个路由之间导航

`NavHostController`（`rememberNavController()` 调用中的 `navController` 属性）负责在多个路线之间导航。

由于此属性是在 `CupcakeApp` 可组合项中定义的，如果要从应用中的不同屏幕访问该属性，有以下两种方式：

1. 将 `navController` 作为参数传递给每个可组合项。
2. 在 `NavHost` 中处理所有应用导航，在 NavHost 中向屏幕传入处理按钮点击事件的逻辑。

第二种方式的优点：

- 导航逻辑会保存在一个集中位置，避免允许各个屏幕在应用中随意导航的意外，让代码更易于维护并预防 bug。
- 导航逻辑将各个界面相隔离，各个屏幕可以保持独立，无需了解应用中其他屏幕的信息。

#### 为每个屏幕添加按钮处理函数

1. 为 `StartOrderScreen` 屏幕中的纸杯蛋糕数量的按钮 添加点击处理程序：
   - `quantityOptions` 参数的类型为 `List<Pair<Int, Int>>` ，即一对 Int 类型的值组成的列表。Pair 对中的项可以由 `first` 属性或 `second` 属性访问。
   - 向 `onNextButtonClicked` 的调用中传入 Pair 对的第二项 `item.second`（即纸杯蛋糕的数量）。

```kotlin
@Composable
fun StartOrderScreen(
    quantityOptions: List<Pair<Int, Int>>,
    onNextButtonClicked: (Int) -> Unit, // 添加一个按钮点击的处理函数
    modifier: Modifier = Modifier
){
...
  	// 数量选项按钮，向 onClick 参数传入 lambda 表达式，在表达式中调用 `onNextButtonClicked`
  	quantityOptions.forEach { item ->
        SelectQuantityButton(
            labelResourceId = item.first,
            onClick = { onNextButtonClicked(item.second) }
        )
    }
}
```

2. 为 `SelectOptionScreen` 屏幕的 Cancel 和 Next 按钮添加处理程序：

```kotlin
@Composable
fun SelectOptionScreen(
    subtotal: String,
    options: List<String>,
    onSelectionChanged: (String) -> Unit = {},
    onCancelButtonClicked: () -> Unit = {}, // * 添加 Cancel 按钮的点击处理程序
    onNextButtonClicked: () -> Unit = {}, // * 添加 Next 按钮的点击处理程序
    modifier: Modifier = Modifier
){
  	// 为 Cancel 按钮的 onClick 参数传入 onCancelButtonClicked。
  	OutlinedButton(
      modifier = Modifier.weight(1f), 
      onClick = onCancelButtonClicked
    ) {
    		Text(stringResource(R.string.cancel))
		}
  
  	// 为 Next 按钮的 onClick 参数传入 onNextButtonClicked。
    Button(
      modifier = Modifier.weight(1f),
      enabled = selectedValue.isNotEmpty(),
      onClick = onNextButtonClicked
    ) {
        Text(stringResource(R.string.next))
    }
}
```

3. 为 SummaryScreen 屏幕上的 **Cancel** 和 **Send** 按钮添加按钮处理程序函数。

```kotlin
@Composable
fun OrderSummaryScreen(
    orderUiState: OrderUiState,
    onCancelButtonClicked: () -> Unit, // *
    onSendButtonClicked: (String, String) -> Unit, // *
    modifier: Modifier = Modifier
){
  	val orderSummary = stringResource(
        R.string.order_details,
        numberOfCupcakes,
        orderUiState.flavor,
        orderUiState.date,
        orderUiState.quantity
    )
    val newOrder = stringResource(R.string.new_cupcake_order)
  	// ...
  
  	// 为 Cancel 按钮的 onClick 参数传递 onCancelButtonClicked。
  	OutlinedButton(
        modifier = Modifier.fillMaxWidth(),
        onClick = onCancelButtonClicked
    ) {
        Text(stringResource(R.string.cancel))
    }
  	// 为 Send 按钮的 onClick 参数传递 onSendButtonClicked。
  	// 向函数调用传入参数 newOrder 和 orderSummary。
    Button(
      modifier = Modifier.fillMaxWidth(),
      onClick = { onSendButtonClicked(newOrder, orderSummary) }
    ) {
        Text(stringResource(R.string.send))
    }
}
```

#### 导航到其他路由

调用 `NavHostController` 实例上的 `navigate()` 方法即可导航到其他路由。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-navigation/img/fc8aae3911a6a25d.png?hl=zh-cn" alt="fc8aae3911a6a25d.png" style="zoom: 33%;" />

`navigation` 方法接受一个字符串参数：对应于 `NavHost` 中定义的某一个路由。如果路由与 NavHost 中的任一 composable() 调用匹配，应用便会转到该屏幕。

调用 `navigate()` 的效果：屏幕不仅会发生变化，而且会放置在**返回堆栈（back stack）的顶部**。因此点按**系统返回（Back）按钮**即可返回上一个屏幕。



**应用会将每个屏幕堆叠在上一个屏幕上，而返回按钮可以移除这些屏幕**。

从栈底部的 `startDestination` 到显示在最顶部的屏幕的历史记录称为**==返回堆栈（back stack）==**。

```kotlin
composable(route = CupcakeScreen.Start.name) {
    StartOrderScreen(
        quantityOptions = DataSource.quantityOptions,
        onNextButtonClicked = {
          	// 利用传入该函数的表示纸杯蛋糕数量的参数，更新 ViewModel
            viewModel.setQuantity(it)
          	// *
            navController.navigate(CupcakeScreen.Flavor.name)
        },
        modifier = Modifier
            .fillMaxSize()
            .padding(dimensionResource(id = R.dimen.padding_medium))
    )
}
```

```kotlin
composable(route = CupcakeScreen.Flavor.name) {
    val context = LocalContext.current
    SelectOptionScreen(
        subtotal = uiState.price,
        onNextButtonClicked = {
            navController.navigate(CupcakeScreen.Pickup.name) // *
        },
        onCancelButtonClicked = {
          cancelOrderAndNavigateToStart(viewModel, navController)
        }, // * 见下文
        options = flavors.map { id -> context.resources.getString(id) },
        onSelectionChanged = { viewModel.setFlavor(it) }
    )
}
```

```kotlin
composable(route = CupcakeScreen.Pickup.name) {
    SelectOptionScreen(
        subtotal = uiState.price,
        onNextButtonClicked = {
            navController.navigate(CupcakeScreen.Summary.name) // *
        },
      	onCancelButtonClicked = {
          cancelOrderAndNavigateToStart(viewModel, navController)
        },
        options = uiState.pickupOptions,
        onSelectionChanged = { viewModel.setDate(it) }
    )
}
```

```kotlin
composable(route = CupcakeScreen.Summary.name) {
   val context = LocalContext.current
   OrderSummaryScreen(
       orderUiState = uiState,
       onCancelButtonClicked = {
         cancelOrderAndNavigateToStart(viewModel, navController)
       },
       onSendButtonClicked = { subject: String, summary: String ->
					// Todo
       }
   )
}
```

#### 跳转至起始屏幕

与系统返回 **Back** 按钮不同，**Cancel** 按钮不会返回上一个屏幕。而是**跳转返回起始屏幕，并移除返回堆栈中的所有屏幕。**

可以通过调用 `popBackStack()` 方法来实现此目的。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-navigation/img/2f382e5eb319b4b8.png?hl=zh-cn" alt="2f382e5eb319b4b8.png" style="zoom: 33%;" />

`popBackStack()` 方法有两个必需参数。

- **`route`**：字符串，表示**希望返回到的目标页面的路由**。
- **`inclusive`**：布尔值。
  - 如果为 true，还会移除指定目标路由。
  - 如果为 false，`popBackStack()` 将移除起始目标页面之上的所有页面，但不包含该起始目标页面。该起始目标页面将作为最顶层的屏幕显示给用户。



当用户在任何屏幕上点按 **Cancel** 按钮时，应用会重置视图模型中的状态并调用 `popBackStack()`。

首先将实现一个方法来执行此操作，然后为包含 **Cancel** 按钮的所有三个屏幕的相应参数传入该方法。

```kotlin
private fun cancelOrderAndNavigateToStart(
 		viewModel: OrderViewModel,
    navController: NavHostController
) {
		viewModel.resetOrder()
  	navController.popBackStack(CupcakeScreen.Start.name, inclusive = false)
}
```

### 1.3 导航到其他应用

在订单摘要屏幕上，用户可以将其订单发送到其他应用。

此选项会打开一个 ShareSheet（覆盖屏幕底部部分的界面组件），其中会显示分享选项。这部分界面不属于 Cupcake 应用，而是由 Android 操作系统提供的。

`navController` 无法调用系统界面（例如分享屏幕），需要使用 intent 来完成该操作。

**intent 会请求系统执行某项操作，通常用于呈现新的 activity。** 

- 可以向 `ACTION_SEND` intent 提供某些数据（例如字符串），并为这些数据提供适当的分享操作。

**设置 intent 的基本过程如下：**

1. 创建一个 intent 对象并指定意图，例如 `ACTION_SEND`。
2. 指定随 intent 一同发送的其他数据的**类型**。
   - `"text/plain"`：一段文本
   - `"image/*"`
   - `"video/*"`
3. 通过调用 `putExtra()` 方法，向 intent 传递其他数据，例如要分享的文本或图片。
   - 此 intent 将接受两个附加物：`EXTRA_SUBJECT` 和 `EXTRA_TEXT`。
4. 调用上下文的 `startActivity()` 方法，并传入从 intent 创建的 activity。



```kotlin
private fun shareOrder(context: Context, subject: String, summary: String) {
		val intent = Intent(Intent.ACTION_SEND).apply {
      	type = "text"
      	putExtra(Intent.EXTRA_SUBJECT, subject)
      	putExtra(Intent.EXTRA_TEXT, summary)
    }
  
  	context.startActivity(
      	// 从 intent 中创建一个 activity
    		Intent.createChooser(
        		intent,
          	context.getString(R.string.new_cupcake_order)
        )
    )
}
```

```kotlin
composable(route = CupcakeScreen.Summary.name) {
  	// 获取对上下文对象的引用，以便将其传递给 shareOrder() 函数。
    val context = LocalContext.current
    OrderSummaryScreen(
        orderUiState = uiState,
        onCancelButtonClicked = {
            cancelOrderAndNavigateToStart(viewModel, navController)
        },
        onSendButtonClicked = { subject: String, summary: String ->
            shareOrder(context, subject = subject, summary = summary) // *
        },
        modifier = Modifier.fillMaxHeight()
    )
}
```

<img src="02 Navigation in Jetpack Compose.assets/image-20240126205124105.png" alt="image-20240126205124105" style="zoom: 67%;" />

### 1.4 让 AppBar 响应导航

- 系统返回 Back 按钮由 Android 操作系统提供，位于屏幕底部。
- Up 按钮位于应用的 `AppBar` 中。

在您的应用上下文中， Back 按钮和 Up 按钮的作用相同，都是返回上一个屏幕。



可以使用返回堆栈中的信息来显示正确的标题，并适时显示向上按钮。

CupcakeAppBar 可组合项应当能够感知当前屏幕，以便标题适当更新。

```kotlin
// 为每一个屏幕添加一个 title 文本
enum class CupcakeScreen(@StringRes val title: Int) {
    Start(title = R.string.app_name),
    Flavor(title = R.string.choose_flavor),
    Pickup(title = R.string.choose_pickup_date),
    Summary(title = R.string.order_summary)
}
```

```kotlin
fun CupcakeAppBar(
    currentScreen: CupcakeScreen, // *
    canNavigateBack: Boolean,
    navigateUp: () -> Unit = {},
    modifier: Modifier = Modifier
) {
    TopAppBar(
        title = { Text(stringResource(currentScreen.title)) }, // AppBar 标题显示为屏幕对应的标题文本
        modifier = modifier,
        navigationIcon = {
            if (canNavigateBack) {
                IconButton(onClick = navigateUp) {
                    Icon(
                        imageVector = Icons.Filled.ArrowBack,
                        contentDescription = stringResource(R.string.back_button)
                    )
                }
            }
        }
    )
}
```



**仅当返回堆栈上有可组合项时才应显示向上按钮**。

如需检查这一点，需要建立对返回堆栈的引用。

```kotlin
// 对 backStack 的引用。
// 使用 by 委托 navController 的 currentBackStackEntry 方法
val backStackEntry by navController.currentBackStackEntryAsState()
// 通过 backStackEntry 获取当前屏幕
val currentScreen = CupcakeScreen.valueOf(
    backStackEntry?.destination?.route ?: CupcakeScreen.Start.name
)
```



```kotlin
import androidx.navigation.compose.currentBackStackEntryAsState

@Composable
fun CupcakeApp(
    viewModel: OrderViewModel = viewModel(),
    navController: NavHostController = rememberNavController()
){
		// 对 backStack 的引用。
  	// 使用 by 委托 navController 的 currentBackStackEntry 方法
    val backStackEntry by navController.currentBackStackEntryAsState()
  	// 通过 backStackEntry 获取当前屏幕
    val currentScreen = CupcakeScreen.valueOf(
        backStackEntry?.destination?.route ?: CupcakeScreen.Start.name
    )
		Scaffold(
        topBar = {
            CupcakeAppBar(
              	// 检查 backStack 是否为空，不为空时显示 Up 按钮
                canNavigateBack = navController.previousBackStackEntry != null,
              	// 通过 navController.navigateUp 方法返回上一个屏幕
                navigateUp = { navController.navigateUp() },
                currentScreen = currentScreen
            )
        }
    ) { innerPadding ->
    ...
    }
}
```



## 02 测试 Cupcake 应用

UI 测试位于名称为 `androidTest` 的源代码集中。

仅用于此 UI 测试模块的依赖项，不应为其他模块（例如包含应用代码的 `main` 模块）编译。

因此在添加仅供 UI 测试使用的依赖项时，需要使用 `androidTestImplementation` 关键字在应用模块的 `build.gradle.kts` 文件中声明。这样可确保**仅在运行 UI 测试时才会编译界面测试依赖项。**

为 UI 测试添加依赖项：

```kotlin
androidTestImplementation(platform("androidx.compose:compose-bom:2023.05.01"))
androidTestImplementation("androidx.compose.ui:ui-test-junit4")
androidTestImplementation("androidx.navigation:navigation-testing:2.6.0")
androidTestImplementation("androidx.test.espresso:espresso-intents:3.5.1")
androidTestImplementation("androidx.test.ext:junit:1.1.5")
```

#### 创建 UI 测试目录

```
app/src/androidTest/java // 目录
app/src/androidTest/java/com.example.cupcake.test // 包名称
```

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-test-cupcake/img/8da2547b2ef1cc8e.png" alt="8da2547b2ef1cc8e.png" style="zoom: 67%;" />

### 2.1 设置 nav host

Compose 中的界面测试需要使用 Compose 测试规则。测试 Jetpack Navigation 也是一样。不过，**测试导航需要通过 Compose 测试规则进行一些额外的设置**。

在测试 Compose Navigation 时，我们无权访问在应用代码中访问的同一个 `NavHostController`。不过可以使用 `TestNavHostController` 并为此导航控制器配置测试规则。



1. **创建一条测试规则**：在 `CupcakeScreenNavigationTest.kt` 中，使用 `createAndroidComposeRule` 创建一条测试规则，并将 `ComponentActivity` 作为类型参数传递。

```kotlin
// CupcakeScreenNavigationTest.kt
import androidx.activity.ComponentActivity
import androidx.compose.ui.test.junit4.createAndroidComposeRule
import org.junit.Rule

class CupcakeScreenNavigationTest {
  @get:Rule
  val composeTestRule = createAndroidComposeRule<ComponentActivity>()
}
```

为了确保应用导航到正确的位置，需要在应用执行导航操作时**引用 `TestNavHostController` 实例来检查 nav host 的导航路由。**

2. 将 `TestNavHostController` 实例实例化为 `lateinit` 变量。
   - 在 Kotlin 中，`lateinit` 关键字用于声明**属性可在声明对象后初始化**。

```kotlin
// CupcakeScreenNavigationTest.kt
import androidx.navigation.testing.TestNavHostController

class CupcakeScreenNavigationTest {
    @get:Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()
    private lateinit var navController: TestNavHostController

}
```



3. 指定要用于界面测试的可组合项。

```kotlin
class CupcakeScreenNavigationTest {
    @get:Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()
    private lateinit var navController: TestNavHostController
    
  	fun setupCupcakeNavHost() {
        composeTestRule.setContent {
            CupcakeApp()
        }
    }
}    
```

4. 在测试类中创建 `TestNavHostContoller` 对象。您稍后将使用此对象确定导航状态，因为应用使用控制器在 Cupcake 应用中的各个屏幕之间导航。	
   - 初始化之前创建的 `navController` 变量，注册 navController。
   - 将该 `TestNavHostController` 实例传递给 `CupcakeApp` 可组合项。

```kotlin
class CupcakeScreenNavigationTest {
    @get:Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()
    private lateinit var navController: TestNavHostController
    
  	@Before
  	fun setupCupcakeNavHost() {
        composeTestRule.setContent {
            navController = TestNavHostController(LocalContext.current).apply {
                navigatorProvider.addNavigator(ComposeNavigator())
            }
            CupcakeApp(navController = navController)
        }
    }
}    
```

junit 库提供了 `@Before` 注解。

**如果某个方法带有 `@Before` 注解，它会在每个带有 `@Test` 注解的方法之前运行。**



由于 `CupcakeScreenNavigationTest` 类中的每一项测试均涉及测试导航的一个方面，因此，**每一项测试都依赖于创建的 `TestNavHostController` 对象**。

**可以使用 `@Before` 注解来自动设置 navController**，而不必针对每个测试手动调用 `setupCupcakeNavHost()` 函数。



### 2.2 编写导航测试

#### 验证起始目标页面

验证 **Start Order 屏幕**是否为应用启动时的当前目标页面路线。

创建的 `AndroidComposeTestRule` 会自动启动应用，并在执行任何 `@Test` 方法之前显示 `CupcakeApp` 可组合项。因此，您无需在测试方法中采取任何额外步骤即可启动应用。

```kotlin
class CupcakeScreenNavigationTest {
		...
	
    @Test
    fun cupcakeNavHost_verifyStartDestination() {
				 assertEquals(
				 		CupcakeScreen.Start.name, 
				 		navController.currentBackStackEntry?.destination?.route
				 )
    }
}
```

#### 创建辅助方法

1. **assertCurrentRouteName()**。
   - 向 `NavController` 类添加一个名为 `assertCurrentRouteName()` 的扩展函数，并在方法签名中传递预期路线名称的字符串。
   - 该方法断言 navController 的 currentBackStackEntry 的目标页面路由与 `expectedRouteName` 相同。

```kotlin
// test/ScreenAssertions.kt
fun NavController.assertCurrentRouteName(expectedRouteName: String) {
		assertEquals(expectedRouteName, currentBackStackEntry?.destination?.route)
}
```

上面验证起始目标页面的测试可以使用该方法。

```kotlin
@Test
fun cupcakeNavHost_verifyStartDestination() {
    navController.assertCurrentRouteName(CupcakeScreen.Start.name)
}
```



很多测试还需要与界面组件互动。通常可以使用 ResourceString 找到这些组件。可以使用 `Context.getString()` 方法按可组合项的资源字符串访问可组合项。

在 Compose 中编写 UI 测试时，实现此方法的方式如下所示：

```kotlin
composeTestRule.onNodeWithText(
  composeTestRule.activity.getString(R.string.my_string)
)
```

添加扩展函数可简化该指令：

```kotlin
// test/ComposeRuleExtensions.kt
import androidx.activity.ComponentActivity
import androidx.annotation.StringRes
import androidx.compose.ui.test.SemanticsNodeInteraction
import androidx.compose.ui.test.junit4.AndroidComposeTestRule
import androidx.compose.ui.test.onNodeWithText
import androidx.test.ext.junit.rules.ActivityScenarioRule

fun <A : ComponentActivity> AndroidComposeTestRule<ActivityScenarioRule<A>, A>.onNodeWithStringId(
    @StringRes id: Int
): SemanticsNodeInteraction = onNodeWithText(activity.getString(id))

```

经简化后使用以下指令查找可组合项：

```kotlin
composeTestRule.onNodeWithStringId(R.string.my_string)
```

#### 验证 Start 屏幕没有向上按钮

```kotlin
class CupcakeScreenNavigationTest {
		...
	
    @Test
    fun cupcakeNavHost_verifyBackNavigationNotShownOnStartOrderScreen() {
				val backText = composeTestRule.activity
						.getString(R.string.back_button)
      	// 断言屏幕上不包含此 contentDescription 的节点。
				composeTestRule.onNodeWithContentDescription(backText)
						.assertDoesNotExist()
    }
}
```

#### 验证导航到 Flavor 屏幕

```kotlin
@Test
fun cupcakeNavHost_clickOneCupcake_navigatesToSelectFlavorScreen(){
  	// 按字符串资源 ID 找到 One Cupcake 按钮，然后对其执行点击操作。
  	composeTestRule.onNodeWithStringId(R.string.one_cupcake)
        .performClick()
  	// 断言当前路由名称是 Flavor 屏幕名称。
  	navController.assertCurrentRouteName(CupcakeScreen.Flavor.name)
}
```

创建一个导航至 Flavor 屏幕的辅助方法，这样在后续测试中就不必重复编写该代码：

- 此方法用于让界面为导航做好准备，因而调用此方法后，界面的 **Next** 按钮应处于可点击的状态。

```kotlin
private fun navigateToFlavorScreen() {
		composeTestRule.onNodeWithStringId(R.string.one_cupcake)
        .performClick()
    composeTestRule.onNodeWithStringId(R.string.chocolate)
        .performClick()
}
```

创建一个导航至 Pickup 屏幕和 Summary 屏幕的辅助方法：

```kotlin
private fun navigateToPickupScreen() {
    navigateToFlavorScreen()
    composeTestRule.onNodeWithStringId(R.string.next)
        .performClick()
}
private fun navigateToSummaryScreen() {
    navigateToPickupScreen()
    composeTestRule.onNodeWithText(getFormattedDate())
        .performClick()
    composeTestRule.onNodeWithStringId(R.string.next)
        .performClick()
}

private fun getFormattedDate(): String {
    val calendar = Calendar.getInstance()
    calendar.add(java.util.Calendar.DATE, 1)
    val formatter = SimpleDateFormat("E MMM d", Locale.getDefault())
    return formatter.format(calendar.time)
}
```

有时需要测试 Up 按钮的功能，以确保该按钮可以让用户返回上一个屏幕。

创建一个辅助函数来查找并点击向上按钮：

```kotlin
private fun performNavigateUp() {
    val backText = composeTestRule.activity.getString(R.string.back_button)
    composeTestRule.onNodeWithContentDescription(backText).performClick()
}
```

```kotlin
@Test
fun cupcakeNavHost_clickNextOnFlavorScreen_navigatesToPickupScreen() {
    navigateToFlavorScreen()
    composeTestRule.onNodeWithStringId(R.string.next)
        .performClick()
    navController.assertCurrentRouteName(CupcakeScreen.Pickup.name)
}

@Test
fun cupcakeNavHost_clickBackOnFlavorScreen_navigatesToStartOrderScreen() {
    navigateToFlavorScreen()
    performNavigateUp()
    navController.assertCurrentRouteName(CupcakeScreen.Start.name)
}

@Test
fun cupcakeNavHost_clickCancelOnFlavorScreen_navigatesToStartOrderScreen() {
    navigateToFlavorScreen()
    composeTestRule.onNodeWithStringId(R.string.cancel)
        .performClick()
    navController.assertCurrentRouteName(CupcakeScreen.Start.name)
}

@Test
fun cupcakeNavHost_clickNextOnPickupScreen_navigatesToSummaryScreen() {
    navigateToPickupScreen()
    composeTestRule.onNodeWithText(getFormattedDate())
        .performClick()
    composeTestRule.onNodeWithStringId(R.string.next)
        .performClick()
    navController.assertCurrentRouteName(CupcakeScreen.Summary.name)
}

@Test
fun cupcakeNavHost_clickBackOnPickupScreen_navigatesToFlavorScreen() {
    navigateToPickupScreen()
    performNavigateUp()
    navController.assertCurrentRouteName(CupcakeScreen.Flavor.name)
}

@Test
fun cupcakeNavHost_clickCancelOnPickupScreen_navigatesToStartOrderScreen() {
    navigateToPickupScreen()
    composeTestRule.onNodeWithStringId(R.string.cancel)
        .performClick()
    navController.assertCurrentRouteName(CupcakeScreen.Start.name)
}

@Test
fun cupcakeNavHost_clickCancelOnSummaryScreen_navigatesToStartOrderScreen() {
    navigateToSummaryScreen()
    composeTestRule.onNodeWithStringId(R.string.cancel)
        .performClick()
    navController.assertCurrentRouteName(CupcakeScreen.Start.name)
}
```

### 2.3 为 Order 屏幕编写测试

导航只是 Cupcake 应用功能的一个方面。用户还会与应用的各个屏幕互动。需要验证这些屏幕上显示的内容以及在这些屏幕上执行的操作是否会产生正确的结果。

**SelectOptionScreen** 是应用的一个重要部分。

#### 测试 Choose Flavor 屏幕内容

```kotlin
// CupcakeOrderScreenTest.kt
class CupcakeOrderScreenTest {
		@get:Rule
		val composeTestRule = createAndroidComposeRule<ComponentActivity>()
  
    @Test
    fun selectOptionScreen_verifyContent() {
				// 将 Compose 规则内容设置为 SelectOptionScreen。
      	// 这样做可以确保 SelectOptionScreen 可组合项会直接启动，因此无需进行导航。

    		val flavors = listOf(
          "Vanilla", "Chocolate", "Hazelnut", "Cookie", "Mango"
        )
    		val subtotal = "$100"
      
      	composeTestRule.setContent {
        		SelectOptionScreen(subtotal = subtotal, options = flavors)
    		}
      	
      	// 检查屏幕上是否显示了每一个选项
      	flavors.forEach { flavor ->
            composeTestRule.onNodeWithText(flavor).assertIsDisplayed()
        }
      
      	// 检查屏幕上的总价是否正确显示
        composeTestRule.onNodeWithText(
            composeTestRule.activity.getString(
                R.string.subtotal_price,
                subtotal
            )
        ).assertIsDisplayed()
      
      	// 检查选项被选择之前， Next 按钮不会启用
      	composeTestRule.onNodeWithStringId(R.string.next)
      			.assertIsNotEnabled()
    }
}
```

