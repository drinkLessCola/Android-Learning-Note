## 01 构建具有动态导航栏的自适应应用

### 1.1 更改屏幕

#### 在状态发生变化时更改屏幕

除了通过 `NavHostController` 类从一个屏幕导航到另一个屏幕，还可以利用运行时可变状态，通过条件语句更改屏幕。

在 Compose 中，当状态发生变化时，屏幕会重组。因此可以使用简单的条件更改屏幕，以响应状态的变化。

```kotlin
@Composable
fun ReplyHomeScreen(
    replyUiState: ReplyUiState,
    onTabPressed: (MailboxType) -> Unit = {},
    onEmailCardPressed: (Int) -> Unit = {},
    onDetailScreenBackPressed: () -> Unit = {},
    modifier: Modifier = Modifier
) {
		if (replyUiState.isShowingHomepage) {
  			// 邮件列表屏幕
        ReplyAppContent(
            replyUiState = replyUiState,
            onTabPressed = onTabPressed,
            onEmailCardPressed = onEmailCardPressed,
            navigationItemContentList = navigationItemContentList,
            modifier = modifier

        )
    } else {
      	// 邮件详情屏幕
        ReplyDetailsScreen(
            replyUiState = replyUiState,
            onBackPressed = onDetailScreenBackPressed,
            modifier = modifier
        )
    }
}
```

- `replyUiState` 对象是状态对象。当 `replyUiState` 对象的 `isShowingHomepage` 属性发生变化时，系统会重组 `ReplyHomeScreen` 可组合项，并在运行时重新判断 `if/else` 语句。
- 此方法支持在不使用 `NavHostController` 类的情况下，在不同屏幕之间导航。

#### 创建自定义返回处理程序

使用 `NavHost` 可组合项在屏幕间切换的一个好处是，**之前的屏幕会保存到返回堆栈中**。借助这些已保存的屏幕，**系统返回按钮可在调用时轻松导航回上一个屏幕**。

如果应用不使用 `NavHost` 进行导航，就必须手动处理返回按钮，创建自定义返回处理程序：

1. 在可组合项的第一行中，添加一个 `BackHandler` 可组合项。
2. 在 `BackHandler` 可组合项的正文中，调用 `onBackPressed()` 函数。

```kotlin
import androidx.activity.compose.BackHandler

@Composable
fun ReplyDetailsScreen(
    replyUiState: ReplyUiState,
    modifier: Modifier = Modifier,
    onBackPressed: () -> Unit = {},
) {
    BackHandler {
        onBackPressed()
    }
}
```

### 1.2 在大屏设备上运行应用

#### 使用可调整大小的模拟器

- 使用屏幕尺寸各异的多个模拟器进行测试较为麻烦，尤其是在同时针对多种屏幕尺寸进行构建时。
- 可能还需要测试正在运行的应用如何响应屏幕尺寸的变化，例如：
  - 屏幕方向变化。
  - 桌面设备中的窗口大小变化。
  - 可折叠设备的折叠状态变化。

可以使用 Android Studio 中推出的**可调整大小的模拟器**解决该问题。

![Reply 应用在主屏幕上显示了详情屏幕以及抽屉式导航栏和电子邮件列表。示例电子邮件显示在电子邮件列表的右侧。示例电子邮件下方显示了“Reply”和“Reply All”按钮。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/c04d320eaabd6364.png?hl=zh-cn)

### 1.3 让布局适应不同的屏幕尺寸

#### 断点

要想为同一应用显示不同的布局，就需要针对不同的状态使用条件。

要构建自适应应用，需要根据屏幕尺寸更改布局。**布局发生更改的测量点称为 ==断点==**。Material Design 创建了一个可以涵盖大多数 Android 屏幕的[主观断点范围](https://m3.material.io/foundations/adaptive-design/large-screens/overview)。

![此表格显示了不同设备类型和设置的断点范围（以 dp 为单位）。0 至 599 dp 适用于处于竖屏模式的手机、处于横屏模式的手机、较小窗口大小、列数为 4 以及最小外边距为 8 的情况。600 至 839 dp 适用于处于竖屏模式或横屏模式的可折叠设备和小型平板电脑、中等窗口大小类别、列数为 12 以及最小外边距为 12 的情况。840 dp 或更高适用于处于竖屏模式或横屏模式的大型平板电脑、较大窗口大小类别、列数为 12 以及最小外边距为 32 的情况。表格备注指明，外边距和间距较为灵活，尺寸不必相等；处于横屏模式的手机可视为一种例外情况，仍适用于 0 至 599 dp 的断点范围。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/74bb1db3132462ae.png?hl=zh-cn)

#### 使用窗口大小类别

为 Compose 引入的 `WindowSizeClass` API 简化了 Material Design 断点的实现。

窗口大小类别针对宽度和高度引入了三种尺寸：Compact、Medium 和 Expanded。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/42db283a2ba29045.png?hl=zh-cn" alt="此图表显示基于宽度的窗口大小类别。" style="zoom: 67%;" />



![此图表显示基于高度的窗口大小类别。](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/2c932129fca12cea.png?hl=zh-cn)

在 Reply 应用中实现 `WindowSizeClass` API：

1. 添加依赖：material3-window-size-class

   ```kotlin
   implementation("androidx.compose.material3:material3-window-size-class")
   ```

2. 创建一个变量 `windowSize`，以便在任意给定时间存储应用窗口的大小，确定要在各种可组合项中显示的布局。并将 `windowSize` 传入 `ReplyApp` 可组合项。

   ```kotlin
   import androidx.compose.material3.windowsizeclass.calculateWindowSizeClass
   class MainActivity : ComponentActivity() {
   
       @OptIn(ExperimentalMaterial3WindowSizeClassApi::class)
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContent {
               ReplyTheme {
                   Surface {
                       val windowSize = calculateWindowSizeClass(
                         	activity = this
                       )
                       ReplyApp(
                           windowSize = windowSize.widthSizeClass
                       )
                   }
               }
           }
       }
   }
   ```

   ```kotlin
   import androidx.compose.material3.windowsizeclass.WindowWidthSizeClass
   
   @Composable
   fun ReplyApp(
       windowSize: WindowWidthSizeClass,
       modifier: Modifier = Modifier
   ) {
   ...
   }
   ```

3. 向预览组件的 `ReplyApp` 可组合项传入 `windowSize` 参数值 `WindowWidthSizeClass.Compact`。

   ```kotlin
   @Preview(showBackground = true)
   @Composable
   fun ReplyAppPreview() {
       ReplyTheme {
           ReplyApp(
               windowSize = WindowWidthSizeClass.Compact,
           )
       }
   }
   ```

4. 在 `ReplyApp` 可组合项中，基于 `windowWidthSizeClass` 值添加 `when` 语句，为后续根据屏幕尺寸更改应用布局奠定基础。

   ```kotlin
   @Composable
   fun ReplyApp(
       windowSize: WindowWidthSizeClass,
       modifier: Modifier = Modifier
   ) {
       val viewModel: ReplyViewModel = viewModel()
       val replyUiState = viewModel.uiState.collectAsState().value
   
       when (windowSize) {
           WindowWidthSizeClass.Compact -> {
           }
           WindowWidthSizeClass.Medium -> {
           }
           WindowWidthSizeClass.Expanded -> {
           }
           else -> {
           }
       }
   ```

   

### 1.4 实现自适应导航布局

目前，底部导航栏适用于所有屏幕尺寸。

此导航元素设计不太合理，因为用户会发现在较大的屏幕上很难触及这些基本的导航元素。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/8bc57664775332fe.png?hl=zh-cn" alt="Reply 应用的底部导航栏。" style="zoom:33%;" />

在[响应式界面的导航](https://developer.android.com/guide/topics/large-screens/navigation-for-responsive-uis?hl=zh-cn#responsive_ui_navigation)中针对不同的窗口大小类别，提供了不同的导航元素模式建议。对于 Reply 应用，可以实现以下元素：

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/ac785e15d779f5dc.png?hl=zh-cn" alt="表格列出了窗口大小类别以及相应显示的一些项。“较小宽度”可显示“底部导航栏”。“中等宽度”可显示“侧边导航栏”。“较大宽度”可显示“持续存在的抽屉式导航栏（前缘）”。" style="zoom: 50%;" />

- [侧边导航栏（NavigationRail）](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary?hl=zh-cn#NavigationRail(androidx.compose.ui.Modifier,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,kotlin.Function1,androidx.compose.foundation.layout.WindowInsets,kotlin.Function1))支持从应用的一侧访问较小的导航选项。
- [永久性抽屉式导航栏（PermanentNavigationDrawer）](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary?hl=zh-cn#PermanentNavigationDrawer(kotlin.Function0,androidx.compose.ui.Modifier,kotlin.Function0))是针对较大屏幕提供的另一种选择。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/fdb4e7b5bd686ba8.png?hl=zh-cn" alt="Reply 应用中的示例侧边导航栏垂直显示了 4 个图标：“Inbox”“Sent”“Drafts”和“Spam”。" style="zoom: 25%;" />

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/d5c215bae68b12f9.png?hl=zh-cn" alt="Reply 应用中的永久性抽屉式导航栏垂直列出了 4 个标签页，每个标签页均有相应图标和名称：“Inbox”“Sent”“Drafts”和“Spam”。" style="zoom:33%;" />

#### 实现抽屉式导航栏

如要为较大的屏幕创建抽屉式导航栏，可以使用 `navigationType` 参数。

1. 添加一个 `Enum` 类，以表示不同类型的导航元素。

```kotlin
// WindowStateUtils.kt
package com.example.reply.ui.utils

enum class ReplyNavigationType {
    BOTTOM_NAVIGATION, 
  	NAVIGATION_RAIL, 
  	PERMANENT_NAVIGATION_DRAWER
}

```

2. 在 `ReplyApp` 可组合项中，创建一个 `navigationType` 变量，根据 `when` 语句中的屏幕尺寸，为它们分配适当的 `ReplyNavigationType` 值。
3. 在 `ReplyHomeScreen` 可组合项中使用 `navigationType` 值创建一个分支。

```kotlin
@Composable
fun ReplyApp(
    windowSize: WindowWidthSizeClass,
    modifier: Modifier = Modifier,
) {
    val viewModel: ReplyViewModel = viewModel()
    val replyUiState = viewModel.uiState.collectAsState().value

    val navigationType: ReplyNavigationType // *

    when (windowSize) {
        WindowWidthSizeClass.Compact -> {
            navigationType = ReplyNavigationType.BOTTOM_NAVIGATION
        }
        WindowWidthSizeClass.Medium -> {
            navigationType = ReplyNavigationType.NAVIGATION_RAIL
        }
        WindowWidthSizeClass.Expanded -> {
            navigationType = ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER
        }
        else -> {
            navigationType = ReplyNavigationType.BOTTOM_NAVIGATION
        }
    }

    ReplyHomeScreen(
        navigationType = navigationType, // *
        replyUiState = replyUiState,
        onTabPressed = { mailboxType: MailboxType ->
            viewModel.updateCurrentMailbox(mailboxType = mailboxType)
            viewModel.resetHomeScreenStates()
        },
        onEmailCardPressed = { email: Email ->
            viewModel.updateDetailsScreenStates(
                email = email
            )
        },
        onDetailScreenBackPressed = {
            viewModel.resetHomeScreenStates()
        },
        modifier = modifier
    )
}
```

```kotlin
import androidx.compose.material3.PermanentNavigationDrawer

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ReplyHomeScreen(
    navigationType: ReplyNavigationType,
    replyUiState: ReplyUiState,
    onTabPressed: (MailboxType) -> Unit = {},
    onEmailCardPressed: (Email) -> Unit = {},
    onDetailScreenBackPressed: () -> Unit = {},
    modifier: Modifier = Modifier
) {
  
  if (navigationType == ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER
        && replyUiState.isShowingHomepage
    ) {
    		// 使用抽屉式导航栏
    } else { 
    		// 不使用抽屉式导航栏
    		// 邮件列表屏幕
    		if (replyUiState.isShowingHomepage) {
            ReplyAppContent(
                replyUiState = replyUiState,
                onTabPressed = onTabPressed,
                onEmailCardPressed = onEmailCardPressed,
                navigationItemContentList = navigationItemContentList,
                modifier = modifier
            )
        } else {
          	// 邮件详情屏幕
            ReplyDetailsScreen(
                replyUiState = replyUiState,
                onBackPressed = onDetailScreenBackPressed,
                modifier = modifier
            )
        }
  	}
}
```

4. 创建永久式抽屉式导航栏。
   - 在 if 语句的正文中创建 `PermanentNavigationDrawer` 可组合项，向  `drawerContent` 参数传入 `NavigationDrawerContent` 可组合项。
   - 在 `PermanentNavigationDrawer` 的尾随 lambda 参数中添加 `ReplyAppContent` 可组合项。

```kotlin
PermanentNavigationDrawer(
    drawerContent = {
        NavigationDrawerContent(
            selectedDestination = replyUiState.currentMailbox,
            onTabPressed = onTabPressed,
            navigationItemContentList = navigationItemContentList
        )
    }
) {
    ReplyAppContent(
        replyUiState = replyUiState,
        onTabPressed = onTabPressed,
        onEmailCardPressed = onEmailCardPressed,
        navigationItemContentList = navigationItemContentList,
        modifier = modifier

    )
}
```

#### 实现侧边导航栏

1. 向 else-if 语句中的 `ReplyAppContent` 可组合项传入 `navigationType` 参数。

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ReplyHomeScreen(
    navigationType: ReplyNavigationType,
		// ...
) {
  
  if (navigationType == ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER
        && replyUiState.isShowingHomepage
    ) {
    		// 使用抽屉式导航栏
    } else { 
    		// 不使用抽屉式导航栏
    		// 邮件列表屏幕
    		if (replyUiState.isShowingHomepage) {
            ReplyAppContent(
              	navigationType = navigationType, // *
                replyUiState = replyUiState,
                onTabPressed = onTabPressed,
                onEmailCardPressed = onEmailCardPressed,
                navigationItemContentList = navigationItemContentList,
                modifier = modifier
            )
        } else {
          	// 邮件详情屏幕
            ReplyDetailsScreen(/* ... */)
        }
  	}
}
```

2. 设置侧边导航栏的显示条件。
   - 使用 `AnimatedVisibility` 可组合项封装 `ReplyAppContent` 可组合项中的 `ReplyNavigationRail` 可组合项。当 `ReplyNavigationType` 值为 `NavigationRail` 时将 `visibility` 参数设为 `true`。
   - 使用 `Row` 封装 `AnimatedVisibility` 可组合项和 `Column` 可组合项，使它们水平排列。

```kotlin
@Composable
private fun ReplyAppContent(
    navigationType: ReplyNavigationType, // *
		// ...
) {
    Row(modifier = modifier) {
      	// * 设置 navigationType 为 NAVIGATION_RAIL 时才显示侧边导航栏
        AnimatedVisibility(visible = navigationType == ReplyNavigationType.NAVIGATION_RAIL) {
            ReplyNavigationRail(
                currentTab = replyUiState.currentMailbox,
                onTabPressed = onTabPressed,
                navigationItemContentList = navigationItemContentList,
                modifier = Modifier
            )
        }

        Column() {
          	// 邮件列表
            ReplyListOnlyContent()
          	// 底部导航栏
            ReplyBottomNavigationBar()
        }
    }
}
```

3. 设置底部导航栏的显示条件。
   - 使用 `AnimatedVisibility` 可组合项封装 `ReplyBottomNavigationBar` 可组合项。当 `ReplyNavigationType` 的值为 `BOTTOM_NAVIGATION` 时，`visible` 为 true。

```kotlin
AnimatedVisibility(visible = navigationType == ReplyNavigationType.BOTTOM_NAVIGATION) {
    ReplyBottomNavigationBar(
        currentTab = replyUiState.currentMailbox,
        onTabPressed = onTabPressed,
        navigationItemContentList = navigationItemContentList
    )
}
```

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-navigation-for-large-screens/img/fdb4e7b5bd686ba8.png?hl=zh-cn" alt="Reply 应用在可折叠设备上显示，其中侧边导航栏位于屏幕左侧，电子邮件列表位于右侧。" style="zoom: 33%;" />

## 02 构建具有自适应布局的应用

### 2.1 针对不同屏幕尺寸的预览

使用多个预览时，可以一次性在所有屏幕尺寸上查看所做的更改。

```kotlin
// 为中等屏幕添加预览
@Preview(showBackground = true, widthDp = 700)
@Composable
fun ReplyAppMediumPreview() {
    ReplyTheme {
        Surface {
            ReplyApp(windowSize = WindowWidthSizeClass.Medium)
        }
    }
}

// 为较大屏幕添加预览
@Preview(showBackground = true, widthDp = 1000)
@Composable
fun ReplyAppExpandedPreview() {
    ReplyTheme {
        Surface {
            ReplyApp(windowSize = WindowWidthSizeClass.Expanded)
        }
    }
}
```

### 2.2 实现自适应内容布局

在较大屏幕中，内容看起来只是被延展开了，而未充分利用可用的屏幕空间。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-content-for-large-screens/img/46cbd7374bfcc4a9.png?hl=zh-cn" alt="46cbd7374bfcc4a9.png" style="zoom: 25%;" />

可以通过应用任一[规范布局](https://m3.material.io/foundations/adaptive-design/canonical-layouts)来改进此布局。

对于 Reply 应用，请实现列表-详情视图，因为它最适合用于浏览内容和快速查看详情。利用这种布局，您可以使用可用屏幕向用户显示更多信息，并提高应用的效率。

#### 实现列表-详情视图

1. 在 `WindowStateUtils.kt` 中，创建一个新的 `Enum` 类表示不同类型的内容布局。
   - 如果使用较大屏幕，使用 `LIST_AND_DETAIL` 值，显示完整的列表和详情页面。
   - 否则，使用 `LIST_ONLY` 值，仅显示电子邮件列表内容。

```kotlin
enum class ReplyContentType {
		LIST_ONLY,
		LIST_AND_DETAIL
}
```

2. 在 `ReplyApp` 中声明 `contentType` 变量，并为各种窗口大小分配适当的 `contentType`，以便于根据屏幕尺寸确定适当的内容类型选择。

   并将 `contentType` 作为形参添加到 `ReplyHomeScreen` 可组合项中。

```kotlin
@Composable
fun ReplyApp(
    windowSize: WindowWidthSizeClass,
    modifier: Modifier = Modifier,
) {
    val viewModel: ReplyViewModel = viewModel()
    val replyUiState = viewModel.uiState.collectAsState().value

    val navigationType: ReplyNavigationType
    val contentType: ReplyContentType // *

    when (windowSize) {
        WindowWidthSizeClass.Compact -> {
            navigationType = ReplyNavigationType.BOTTOM_NAVIGATION
            contentType = ReplyContentType.LIST_ONLY // *
        }
        WindowWidthSizeClass.Medium -> {
            navigationType = ReplyNavigationType.NAVIGATION_RAIL
            contentType = ReplyContentType.LIST_ONLY // *
        }
        WindowWidthSizeClass.Expanded -> {
            navigationType = ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER
            contentType = ReplyContentType.LIST_AND_DETAIL // *
        }
        else -> {
            navigationType = ReplyNavigationType.BOTTOM_NAVIGATION
            contentType = ReplyContentType.LIST_ONLY // *
        }
    }

    ReplyHomeScreen(
        navigationType = navigationType,
        contentType = contentType, // *
      	...
    )
}
```

3. 在 `ReplyHomeScreen` 可组合项中，将 `contentType` 值传递给两个 `ReplyAppContent` 可组合项。

```kotlin
@Composable
fun ReplyHomeScreen(
    navigationType: ReplyNavigationType,
    contentType: ReplyContentType,
		...
) {
    if (navigationType == ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER
        		&& replyUiState.isShowingHomepage
       ) {
        PermanentNavigationDrawer(
            drawerContent = {
                PermanentDrawerSheet() {
                    NavigationDrawerContent(...)
                }
            }
        ) {
            ReplyAppContent(
                navigationType = navigationType,
                contentType = contentType, // *
								...
            )
        }
    } else {
        if (replyUiState.isShowingHomepage) {
            ReplyAppContent(
                navigationType = navigationType,
                contentType = contentType,	// *
								...
            )
        } else {
            ReplyDetailsScreen()
        }
    }
}
```

4. 为 `ReplyAppContent` 可组合项添加一个 `if/else` 语句：
   - 当 `contentType` 值为 `LIST_AND_DETAIL` 时，显示 `ReplyListAndDetailContent` 可组合项。
   - 否则显示 `ReplyListOnlyContent` 可组合项。

```kotlin
@Composable
private fun ReplyAppContent(
    navigationType: ReplyNavigationType,
    contentType: ReplyContentType,
		...
) {
    Row(modifier = modifier) {
        AnimatedVisibility(visible = navigationType == ReplyNavigationType.NAVIGATION_RAIL) {
            ReplyNavigationRail(
                currentTab = replyUiState.currentMailbox,
                onTabPressed = onTabPressed,
                navigationItemContentList = navigationItemContentList,
                modifier = Modifier
            )
        }

        Column() {
          	// * if-else 决定展示单独列表 or 列表和详情
            if (contentType == ReplyContentType.LIST_AND_DETAIL) {
                ReplyListAndDetailContent(
                    replyUiState = replyUiState,
                    onEmailCardPressed = onEmailCardPressed,
                    modifier = Modifier.weight(1f)
                )
            } else {
                ReplyListOnlyContent(
                    replyUiState = replyUiState,
                    onEmailCardPressed = onEmailCardPressed
                )
            }
          
          // 底部导航栏
          AnimatedVisibility(
            	visible = navigationType == ReplyNavigationType.BOTTOM_NAVIGATION
          ) {
                ReplyBottomNavigationBar(
                    currentTab = replyUiState.currentMailbox,
                    onTabPressed = onTabPressed,
                    navigationItemContentList = navigationItemContentList,
								)
            }
        }
    }
}
```

5. 移除 `ReplyHomeScreen` 可组合项中抽屉式导航栏显示条件中的 `replyUiState.isShowingHomepage`。
   - Expanded 视图下无需导航到详情视图。

```kotlin
@Composable
fun ReplyHomeScreen(
    navigationType: ReplyNavigationType,
    contentType: ReplyContentType,
		...
) {
    if (navigationType == ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER) { // *
        PermanentNavigationDrawer(
            drawerContent = {
                PermanentDrawerSheet() {
                    NavigationDrawerContent(...)
                }
            }
        ) {
            ReplyAppContent(
                navigationType = navigationType,
                contentType = contentType, // *
								...
            )
        }
    } else {
      	...
    }
}
```

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-content-for-large-screens/img/3a545c9f30ccae08.png?hl=zh-cn" alt="3a545c9f30ccae08.png" style="zoom: 33%;" />

#### 改进列表-详情视图的界面元素

目前，应用会在较大屏幕的主屏幕上显示“详细信息”窗格，但该屏幕包含多余的元素，例如返回按钮、主题标头和额外的内边距，因为它们是专为独立详情屏幕而设计的。

1. 为 `ReplyDetailsScreen` 可组合项添加一个 `Boolean` 形参 `isFullScreen`。
2. 使用 `if` 语句封装 `ReplyDetailsScreenTopBar` 可组合项，使 TopBar 仅在应用全屏时显示。
3. 将 `isFullScreen` 值传递给 `ReplyEmailDetailsCard` 可组合项。
   - 如果屏幕为全屏模式，传递一个横向内边距为 `R.dimen.detail_card_outer_padding_horizontal` 的修饰符。
   - 否则传递一个结束内边距为 `R.dimen.detail_card_outer_padding_horizontal` 的修饰符。

```kotlin
@Composable
fun ReplyDetailsScreen(
    replyUiState: ReplyUiState,
    onBackPressed: () -> Unit,
    modifier: Modifier = Modifier,
    isFullScreen: Boolean = false	// *
) {
    BackHandler {
        onBackPressed()
    }

    Box() {
        LazyColumn() {
            item {
              	// 2. * TopBar 仅在该 Screen 全屏时使用
                if (isFullScreen) {
                    ReplyDetailsScreenTopBar(
                        onBackPressed,
                        replyUiState
                    )
                }
                ReplyEmailDetailsCard(
                    email = replyUiState.currentSelectedEmail,
                    mailboxType = replyUiState.currentMailbox,
                    isFullScreen = isFullScreen,	// *
                    modifier = if (isFullScreen) {
                        Modifier.padding(horizontal = dimensionResource(
                          	R.dimen.detail_card_outer_padding_horizontal))
                    } else {
                        Modifier.padding(end = dimensionResource(
                          	R.dimen.detail_card_outer_padding_horizontal))
                    }
                )
            }
        }
    }
}
```

4. 在 `ReplyEmailDetailsCard` 可组合项中：
   - 如果应用未全屏显示，显示电子邮件的主题。
   - 如果应用全屏显示，由于主题已经在 TopBar 中显示，因此只需添加高度为 `R.dimen.detail_content_padding_top` 的分隔符。

```kotlin
@Composable
private fun ReplyEmailDetailsCard(
    email: Email,
    mailboxType: MailboxType,
    isFullScreen: Boolean,
    modifier: Modifier = Modifier,
) {
    val context = LocalContext.current
    val displayToast = { text: String ->
        Toast.makeText(context, text, Toast.LENGTH_SHORT).show()
    }
    Card(
        modifier = modifier,
        colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(dimensionResource(R.dimen.detail_card_inner_padding))
        ) {
            DetailsScreenHeader(
                email,
                Modifier.fillMaxWidth()
            )
          	
          	// *
            if (isFullScreen) {
                Spacer(modifier = Modifier.height(dimensionResource(id = R.dimen.detail_content_padding_top)))
            } else {
                Text(
                    text = stringResource(email.subject),
                    style = MaterialTheme.typography.bodyMedium,
                    color = MaterialTheme.colorScheme.outline,
                    modifier = Modifier.padding(
                        top = dimensionResource(R.dimen.detail_content_padding_top),
                        bottom = dimensionResource(R.dimen.detail_expanded_subject_body_spacing)
                    ),
                )
            }

            Text(
                text = stringResource(email.body),
                style = MaterialTheme.typography.bodyLarge,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
            )
            DetailsScreenButtonBar(mailboxType, displayToast)
        }
    }
}
```

5. 在 `ReplyHomeScreen.kt` 中，当以独立形式创建 `ReplyDetailsScreen` 可组合项时，为 `isFullScreen` 形参传递 `true` 值。

```kotlin
@Composable
fun ReplyHomeScreen(
    navigationType: ReplyNavigationType,
    contentType: ReplyContentType,
    replyUiState: ReplyUiState,
    ...
) {
    val navigationItemContentList = listOf(...)

    if (navigationType == ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER) {
       // 抽屉式导航栏
    } else {
        if (replyUiState.isShowingHomepage) {
          	// 邮件列表屏幕
            ReplyAppContent(
                navigationType = navigationType,
                contentType = contentType,
                replyUiState = replyUiState,
                ...
            )
        } else {
          	// 邮件详情屏幕
            ReplyDetailsScreen(
                replyUiState = replyUiState,
                isFullScreen = true, // *
                onBackPressed = onDetailScreenBackPressed,
                modifier = modifier
            )
        }
    }
}
```

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-content-for-large-screens/img/a3c92a6b810cb9d1.png?hl=zh-cn" alt="a3c92a6b810cb9d1.png" style="zoom:33%;" />

#### 针对列表-详情视图调整返回处理

使用较大屏幕时，用户不需要导航至 `ReplyDetailsScreen`，因此希望在用户选择<u>返回按钮</u>时**关闭应用**。

对返回处理程序做以下调整：向 `ReplyListAndDetailContent` 可组合项中的 `ReplyDetailsScreen` 可组合项的 `onBackPressed` 形参传递 **==`activity.finish()`==** 函数。

```kotlin
@Composable
fun ReplyListAndDetailContent(
    replyUiState: ReplyUiState,
    onEmailCardPressed: (Email) -> Unit,
    modifier: Modifier = Modifier
) {
    val emails = replyUiState.currentMailboxEmails
    Row(modifier = modifier) {
      	// 邮件列表
        LazyColumn() {
            items(emails, key = { email -> email.id }) { email ->
                ReplyEmailListItem(
                    email = email,
                    selected = replyUiState.currentSelectedEmail.id == email.id,
                    onCardClick = {
                        onEmailCardPressed(email)
                    },
                )
            }
        }
      	// *
        val activity = LocalContext.current as Activity
        ReplyDetailsScreen(
            replyUiState = replyUiState,
            onBackPressed = { activity.finish() }, // *
            modifier = Modifier.weight(1f)
        )
    }
}
```

### 2.3 针对不同屏幕尺寸进行验证

#### 大屏设备应用质量指南

- 了解如何提升应用质量：[核心应用质量指南](https://developer.android.com/docs/quality-guidelines/core-app-quality?hl=zh-cn)。
- 针对所有设备外形规格打造优质应用，参阅：[大屏设备应用质量指南](https://developer.android.com/docs/quality-guidelines/large-screen-app-quality?hl=zh-cn)。
- 应用还必须符合[第 3 层级 - 大屏设备就绪要求](https://developer.android.com/docs/quality-guidelines/large-screen-app-quality?hl=zh-cn#large_screen_ready)。

### 2.4 为自适应应用添加自动化测试

#### 测试各个屏幕尺寸下的布局

创建测试来验证紧凑屏幕中是否存在底部导航元素：

1. 在 `ReplyAppTest` 类中，使用 `createAndroidComposeRule` 创建一条测试规则，并将 `ComponentActivity` 作为类型形参传递。
   - `ComponentActivity` 用于访问空的 activity，而不是 `MainActivity`。

```kotlin
package com.example.reply.test

import androidx.activity.ComponentActivity
import androidx.compose.ui.test.junit4.createAndroidComposeRule
import org.junit.Rule

class ReplyAppTest {
    @get: Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()

}
```

2. 为了区分屏幕上的导航元素，为导航元素添加 `testTag`：
   1. `ReplyNavigationRail` 侧边导航栏。
   2. `ReplyBottomNavigationBar` 底部导航栏。
   3. `PermanentNavigationDrawer` 抽屉式导航栏。

```kotlin
@Composable
private fun ReplyAppContent(
    navigationType: ReplyNavigationType,
    contentType: ReplyContentType,
    replyUiState: ReplyUiState,
  	...
) {
    Row(modifier = modifier) {
      	// 1. 侧边导航栏
        val navigationRailContentDescription = stringResource(R.string.navigation_rail)
        AnimatedVisibility(visible = navigationType == ReplyNavigationType.NAVIGATION_RAIL) {
            ReplyNavigationRail(
                currentTab = replyUiState.currentMailbox,
                onTabPressed = onTabPressed,
                navigationItemContentList = navigationItemContentList,
                modifier = Modifier
                    .testTag(navigationRailContentDescription) // *
            )
        }
				
      	// 邮件列表
        Column() {
            if (contentType == ReplyContentType.LIST_AND_DETAIL) {
                ReplyListAndDetailContent()
            } else {
                ReplyListOnlyContent()
            }
          
          	// 2. 底部导航栏
            AnimatedVisibility(visible = navigationType == ReplyNavigationType.BOTTOM_NAVIGATION) {
                val bottomNavigationContentDescription = stringResource(R.string.navigation_bottom)
                ReplyBottomNavigationBar(
                    currentTab = replyUiState.currentMailbox,
                    onTabPressed = onTabPressed,
                    navigationItemContentList = navigationItemContentList,
                    modifier = Modifier
                        .fillMaxWidth()
                        .testTag(bottomNavigationContentDescription) // *
                )
            }
        }
    }
}
```

```kotlin
@Composable
fun ReplyHomeScreen(
    navigationType: ReplyNavigationType,
    contentType: ReplyContentType,
    replyUiState: ReplyUiState,
    onTabPressed: (MailboxType) -> Unit,
    onEmailCardPressed: (Email) -> Unit,
    onDetailScreenBackPressed: () -> Unit,
    modifier: Modifier = Modifier
) {
    val navigationItemContentList = listOf(...)

    if (navigationType == ReplyNavigationType.PERMANENT_NAVIGATION_DRAWER) {
      	// 3. 抽屉式导航栏
        val navigationDrawerContentDescription = stringResource(id = R.string.navigation_drawer)
        PermanentNavigationDrawer(
            modifier = Modifier.testTag(navigationDrawerContentDescription), // *
            drawerContent = {
                PermanentDrawerSheet(
                  Modifier.width(dimensionResource(id = R.dimen.drawer_width))
                ){
                    NavigationDrawerContent(...)
                }
            }
        ) {
            ReplyAppContent(...)
        }
    } else {
        if (replyUiState.isShowingHomepage) {
            ReplyAppContent()
        } else {
            ReplyDetailsScreen()
        }
    }
}
```

3. 在 `ReplyAppTest` 类中，创建一个测试函数以测试较小屏幕尺寸。
   - 将 `composeTestRule` 的内容设置为 `ReplyApp` 可组合项，并向 `windowSize` 实参传入 `WindowWidthSizeClass.Compact`。
   - 通过测试标记断言底部导航元素存在。对 `composeTestRule` 调用扩展函数 `onNodeWithTagForStringId`，传递导航底部字符串并调用 `assertExists()` 方法。

```kotlin
package com.example.reply.test

import androidx.compose.material3.windowsizeclass.WindowWidthSizeClass
import com.example.reply.ui.ReplyApp
import org.junit.Test
import com.example.reply.R

class ReplyAppTest {
    @get: Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()

    @Test
    fun compactDevice_verifyUsingBottomNavigation() {
        composeTestRule.setContent {
            ReplyApp(
                windowSize = WindowWidthSizeClass.Compact
            )
        }
				
        composeTestRule.onNodeWithTagForStringId(
            R.string.navigation_bottom
        ).assertExists()
    }
}
```

创建测试来验证中等屏幕和较大屏幕是否存在导航栏和永久性抽屉式导航栏：

```kotlin
package com.example.reply.test

import androidx.compose.material3.windowsizeclass.WindowWidthSizeClass
import com.example.reply.ui.ReplyApp
import org.junit.Test
import com.example.reply.R

class ReplyAppTest {
    @get: Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()

    @Test
    fun compactDevice_verifyUsingBottomNavigation() {
        composeTestRule.setContent {
            ReplyApp(
                windowSize = WindowWidthSizeClass.Compact
            )
        }
				
        composeTestRule.onNodeWithTagForStringId(
            R.string.navigation_bottom
        ).assertExists()
    }

    @Test
    fun mediumDevice_verifyUsingNavigationRail() {
        composeTestRule.setContent {
            ReplyApp(
                windowSize = WindowWidthSizeClass.Medium
            )
        }

        composeTestRule.onNodeWithTagForStringId(R.string.navigation_rail)
            .assertExists()
    }

    @Test
    @TestExpandedWidth
    fun expandedDevice_verifyUsingNavigationDrawer() {
        composeTestRule.setContent {
            ReplyApp(
                windowSize = WindowWidthSizeClass.Expanded
            )
        }

        composeTestRule.onNodeWithTagForStringId(R.string.navigation_drawer)
            .assertExists()
    }
}
```

#### 在各个屏幕尺寸下测试配置更改

配置更改是应用生命周期中的一种常见情况。例如将屏幕方向从竖屏更改为横屏时，便会发生配置更改。

当发生配置更改时，请务必测试应用是否保留其状态。

创建模拟配置更改的测试，以测试应用能否在各个屏幕中保留其状态。

1. 在 `ReplyAppStateRestorationTest` 类中，使用 `createAndroidComposeRule` 创建一条测试规则，并将 `ComponentActivity` 作为类型形参传递。

```kotlin
class ReplyAppStateRestorationTest {
    @get: Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()
}
```

2. 创建一个测试函数，用于验证在发生配置更改后，电子邮件在紧凑屏幕中是否仍为选中状态。

   使用 `StateRestorationTester` 测试配置更改。

   1. 创建一个 `StateRestorationTester` 实例，传入参数 `composeTestRule`。
   2. 使用 `StateRestorationTester` 实例设置屏幕内容为 `ReplyApp`，将 `WindowWidthSizeClass.Compact` 作为 `windowSize` 实参传递。
   3. 验证应用中是否显示了第三封电子邮件。对 `composeTestRule` 使用 `assertIsDisplayed()` 方法，用于查找第三封电子邮件的文本。
   4. 点击电子邮件主题，前往电子邮件的详情屏幕。使用 `performClick()` 方法进行导航。
   5. 验证第三封电子邮件是否显示在详情屏幕中。断言存在返回按钮，以确认应用位于详情屏幕中，并验证第三封电子邮件的内容是否已显示。
   6. **使用 `stateRestorationTester.emulateSavedInstanceStateRestore()` 模拟配置更改。**
   7. 重复 5. 再次验证第三封电子邮件是否已显示在详情屏幕中。

```kotlin
class ReplyAppStateRestorationTest {
    @get: Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()


    @Test
    fun compactDevice_selectedEmailRetained_afterConfigChange() {
        val stateRestorationTester = StateRestorationTester(composeTestRule)
        stateRestorationTester.setContent {
            ReplyApp(windowSize = WindowWidthSizeClass.Compact)
        }

        composeTestRule.onNodeWithText(
        		composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
        ).assertIsDisplayed()

        composeTestRule.onNodeWithText(
            composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].subject)
        ).performClick()

				// 断言存在返回按钮，以确认应用位于详情屏幕中。
        composeTestRule.onNodeWithContentDescriptionForStringId(
            R.string.navigation_back
        ).assertExists()

      	// 验证第三封电子邮件的内容已显示。
        composeTestRule.onNodeWithText(
            composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
        ).assertExists()

      	// 模拟配置更改。
        stateRestorationTester.emulateSavedInstanceStateRestore()

        composeTestRule.onNodeWithContentDescriptionForStringId(
            R.string.navigation_back
        ).assertExists()

        composeTestRule.onNodeWithText(
            composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
        ).assertExists()
    }
}
```

3. 创建一个测试函数，用于验证在发生配置更改后，电子邮件在较大屏幕中是否仍为选中状态。

   - 在详情屏幕的子级上查找文本，验证详情屏幕是否显示第三封电子邮件。这种方法可确保您能在详情部分（而不是电子邮件列表）中找到相关文本。

     ```kotlin
     composeTestRule.onNodeWithTagForStringId(R.string.details_screen).onChildren()
         .assertAny(
              hasAnyDescendant(hasText(
              		composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
         		 ))
         )
     ```

```kotlin
class ReplyAppStateRestorationTest {
    @get: Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()

    @Test
    fun expandedDevice_selectedEmailRetained_afterConfigChange() {
        val stateRestorationTester = StateRestorationTester(composeTestRule)
        stateRestorationTester.setContent {
            ReplyApp(windowSize = WindowWidthSizeClass.Expanded)
        }

        composeTestRule.onNodeWithText(
            composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
        ).assertIsDisplayed()

        composeTestRule.onNodeWithText(
            composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].subject)
        ).performClick()

        composeTestRule.onNodeWithTagForStringId(R.string.details_screen).onChildren()
            .assertAny(
                hasAnyDescendant(hasText(
                    composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
            )))

        stateRestorationTester.emulateSavedInstanceStateRestore()
        composeTestRule.onNodeWithTagForStringId(R.string.details_screen).onChildren()
            .assertAny(hasAnyDescendant(hasText(
                composeTestRule.activity.getString(LocalEmailsDataProvider.allEmails[2].body)
            )))
    }
}
```



#### 使用注解对不同屏幕尺寸的测试进行分组

有些测试在屏幕尺寸不兼容的设备上运行会以失败告终。尽管可以使用适当设备逐个运行测试，但如果有很多测试用例，这种方法可能无法支持大规模处理。

要解决此问题，可以**==创建注解来指示测试可以在哪些屏幕尺寸中运行，并为相应的设备配置带注解的测试==**。

1. 在测试目录中，创建包含以下三个注解类的 `TestAnnotations.kt`：`TestCompactWidth`、`TestMediumWidth`、`TestExpandedWidth`。

```kotlin
package com.example.reply.test

annotation class TestCompactWidth
annotation class TestMediumWidth
annotation class TestExpandedWidth
```

2. 将 `TestCompactWidth` 注解放在 `ReplyAppTest` 和 `ReplyAppStateRestorationTest` 中的面向紧凑屏幕的测试的 @Test 注解后面。

```kotlin
    @Test
    @TestCompactWidth
    fun compactDevice_verifyUsingBottomNavigation() { ... }
```

3. 将测试配置为仅运行带有 `TestCompactWidth` 注解的测试。
   - 在 Android Studio 测试运行程序上，点击扳手图标以配置测试。
   - 将测试重命名为 **Compact Test**，然后选择以 **All in Package** 方式运行测试。
   - 点击 **Instrumentation arguments** 字段右侧的三个点 (**...**)。
   - 点击加号 (`+`) 按钮并添加额外的 **annotation** 形参，将其值设为 **com.example.reply.test.TestCompactWidth**。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-content-for-large-screens/img/a277b820697a102f.png?hl=zh-cn" alt="a277b820697a102f.png" style="zoom: 80%;" />

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-adaptive-content-for-large-screens/img/944778497cfa41d4.png?hl=zh-cn" alt="944778497cfa41d4.png" style="zoom:80%;" />

