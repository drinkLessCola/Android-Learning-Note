## 01 使用 Jetpack Compose 实现 Material 主题效果

[Material Design](https://material.io/) 是 Google 设计人员和开发者打造的一套设计系统，旨在帮助开发者针对 Android 以及其他移动平台和网络平台打造优质的数字体验。它提供了一些准则，指导如何以具有可读性、吸引力和一致性的方式构建应用界面。



**每个 `@Composable` 方法都有一个修饰符作为参数**。在 Compose 中，最佳实践是将修饰符参数传递给可组合函数。这是因为它允许父级可组合项将上下文信息传递给子级可组合项。

例如，假设有一个按钮，一个父按钮可能想让其子按钮使用完整尺寸，而另一个父按钮可能想让该子按钮封装内容。这样会提高代码的可重用性。此参数通常名为“修饰符”，应作为函数的参数列表的第一个可选参数显示。**此修饰符将应用于方法的第一个子级**。如需了解详情，请参阅 [Jetpack Compose 的 API 准则](https://android.googlesource.com/platform/frameworks/support/+/androidx-main/compose/docs/compose-api-guidelines.md#elements-accept-and-respect-a-modifier-parameter)。



主题是在 **Theme.kt** 文件中通过名为 `[your app name]+Theme()` 的可组合项定义的（在此应用中是 `WoofTheme()`）。在该函数内，`MaterialTheme` 对象用于设置应用的 `color`、`typography`、`shapes` 和 `content`。

### 1.1 添加颜色

在 Android 系统中，颜色用十六进制颜色值表示。

- 十六进制颜色代码以井号 (#) 字符开头，后跟六个字母和/或数字（代表该颜色的红色、绿色和蓝色 [RGB] 分量）。
- 前两个字母/数字表示红色，后面的两个表示绿色，最后两个表示蓝色。
- 颜色还可以包含 Alpha 值（字母和/或数字），用于表示颜色的透明度（#00 表示不透明度为 0% [完全透明]，#FF 表示不透明度为 100% [完全不透明]）。若添加 alpha 值，则该值为井号 (#) 字符后的十六进制颜色代码的前两个字符。如果未添加 alpha 值，系统会假定它是 #FF，即 100% 不透明（完全不透明）。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/e0349c33dd6fbafe.png?hl=zh-cn" alt="这显示的是用于创建颜色的十六进制数字。" style="zoom: 67%;" />



#### 使用 Material Theme Builder 创建配色方案

为了创建应用的自定义配色方案，我们将使用 [Material Theme Builder](https://m3.material.io/theme-builder#/custom)。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/d6b2e7b613386dfe.png?hl=zh-cn" alt="Material Theme Builder 浅色方案" style="zoom: 50%;" />

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/5087303587b44563.png?hl=zh-cn" alt="Material Theme Builder 生成的深色方案" style="zoom:50%;" />

以下是配色方案中的几个主要角色：

- **primary**（主色）用于整个界面的关键组件。
- **secondary**（副色）用于界面中不太显眼的组件。
- **tertiary**（第三色）用于对比强调，可以平衡主色和副色，或者引起用户对某个元素（例如输入字段）的高度关注。
- **on** 颜色元素（即与颜色搭配的元素）**显示在调色板中其他颜色的上层**，主要应用于**文本、图标和描边**。
  - 在调色板中，我们有一个 **onSurface** 颜色（显示在 **surface** 颜色的上层）和一个 **onPrimary** 颜色（显示在**主要**颜色的上层）。

#### 向主题添加调色板

在 Material Theme Builder 页面上，您可以选择点击 **Export**（导出）按钮，以便下载 **Color.kt** 文件以及包含您在 Theme Builder 中所建自定义主题的 **Theme.kt** 文件。

这样一来，我们创建的自定义主题便会添加到您的应用中。不过，由于生成的 **Theme.kt** 文件不包含动态颜色的代码，因此请将这些文件复制进来。

```kotlin
// Color.kt
package com.example.woof.ui.theme

import androidx.compose.ui.graphics.Color

val md_theme_light_primary = Color(0xFF006C4C)
val md_theme_light_onPrimary = Color(0xFFFFFFFF)
val md_theme_light_primaryContainer = Color(0xFF89F8C7)
val md_theme_light_onPrimaryContainer = Color(0xFF002114)
val md_theme_light_secondary = Color(0xFF4D6357)
val md_theme_light_onSecondary = Color(0xFFFFFFFF)
val md_theme_light_secondaryContainer = Color(0xFFCFE9D9)
val md_theme_light_onSecondaryContainer = Color(0xFF092016)
val md_theme_light_tertiary = Color(0xFF3D6373)
val md_theme_light_onTertiary = Color(0xFFFFFFFF)
val md_theme_light_tertiaryContainer = Color(0xFFC1E8FB)
val md_theme_light_onTertiaryContainer = Color(0xFF001F29)
val md_theme_light_error = Color(0xFFBA1A1A)
val md_theme_light_errorContainer = Color(0xFFFFDAD6)
val md_theme_light_onError = Color(0xFFFFFFFF)
val md_theme_light_onErrorContainer = Color(0xFF410002)
val md_theme_light_background = Color(0xFFFBFDF9)
val md_theme_light_onBackground = Color(0xFF191C1A)
val md_theme_light_surface = Color(0xFFFBFDF9)
val md_theme_light_onSurface = Color(0xFF191C1A)
val md_theme_light_surfaceVariant = Color(0xFFDBE5DD)
val md_theme_light_onSurfaceVariant = Color(0xFF404943)
val md_theme_light_outline = Color(0xFF707973)
val md_theme_light_inverseOnSurface = Color(0xFFEFF1ED)
val md_theme_light_inverseSurface = Color(0xFF2E312F)
val md_theme_light_inversePrimary = Color(0xFF6CDBAC)
val md_theme_light_shadow = Color(0xFF000000)
val md_theme_light_surfaceTint = Color(0xFF006C4C)
val md_theme_light_outlineVariant = Color(0xFFBFC9C2)
val md_theme_light_scrim = Color(0xFF000000)

......
```

```kotlin
// Theme.kt
package com.example.woof.ui.theme

import android.app.Activity
import android.os.Build
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.dynamicDarkColorScheme
import androidx.compose.material3.dynamicLightColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable
import androidx.compose.runtime.SideEffect
import androidx.compose.ui.graphics.toArgb
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalView
import androidx.core.view.WindowCompat

private val LightColors = lightColorScheme(
   primary = md_theme_light_primary,
   onPrimary = md_theme_light_onPrimary,
   primaryContainer = md_theme_light_primaryContainer,
   onPrimaryContainer = md_theme_light_onPrimaryContainer,
   secondary = md_theme_light_secondary,
   onSecondary = md_theme_light_onSecondary,
   secondaryContainer = md_theme_light_secondaryContainer,
   onSecondaryContainer = md_theme_light_onSecondaryContainer,
   tertiary = md_theme_light_tertiary,
   onTertiary = md_theme_light_onTertiary,
   tertiaryContainer = md_theme_light_tertiaryContainer,
   onTertiaryContainer = md_theme_light_onTertiaryContainer,
   error = md_theme_light_error,
   errorContainer = md_theme_light_errorContainer,
   onError = md_theme_light_onError,
   onErrorContainer = md_theme_light_onErrorContainer,
   background = md_theme_light_background,
   onBackground = md_theme_light_onBackground,
   surface = md_theme_light_surface,
   onSurface = md_theme_light_onSurface,
   surfaceVariant = md_theme_light_surfaceVariant,
   onSurfaceVariant = md_theme_light_onSurfaceVariant,
   outline = md_theme_light_outline,
   inverseOnSurface = md_theme_light_inverseOnSurface,
   inverseSurface = md_theme_light_inverseSurface,
   inversePrimary = md_theme_light_inversePrimary,
   surfaceTint = md_theme_light_surfaceTint,
   outlineVariant = md_theme_light_outlineVariant,
   scrim = md_theme_light_scrim,
)

private val DarkColors = darkColorScheme(
   primary = md_theme_dark_primary,
   onPrimary = md_theme_dark_onPrimary,
   primaryContainer = md_theme_dark_primaryContainer,
   onPrimaryContainer = md_theme_dark_onPrimaryContainer,
   secondary = md_theme_dark_secondary,
   onSecondary = md_theme_dark_onSecondary,
   secondaryContainer = md_theme_dark_secondaryContainer,
   onSecondaryContainer = md_theme_dark_onSecondaryContainer,
   tertiary = md_theme_dark_tertiary,
   onTertiary = md_theme_dark_onTertiary,
   tertiaryContainer = md_theme_dark_tertiaryContainer,
   onTertiaryContainer = md_theme_dark_onTertiaryContainer,
   error = md_theme_dark_error,
   errorContainer = md_theme_dark_errorContainer,
   onError = md_theme_dark_onError,
   onErrorContainer = md_theme_dark_onErrorContainer,
   background = md_theme_dark_background,
   onBackground = md_theme_dark_onBackground,
   surface = md_theme_dark_surface,
   onSurface = md_theme_dark_onSurface,
   surfaceVariant = md_theme_dark_surfaceVariant,
   onSurfaceVariant = md_theme_dark_onSurfaceVariant,
   outline = md_theme_dark_outline,
   inverseOnSurface = md_theme_dark_inverseOnSurface,
   inverseSurface = md_theme_dark_inverseSurface,
   inversePrimary = md_theme_dark_inversePrimary,
   surfaceTint = md_theme_dark_surfaceTint,
   outlineVariant = md_theme_dark_outlineVariant,
   scrim = md_theme_dark_scrim,
)

@Composable
fun WoofTheme(
   darkTheme: Boolean = isSystemInDarkTheme(),
   dynamicColor: Boolean = false,
   content: @Composable () -> Unit
) {
   val colorScheme = when {
       dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
           val context = LocalContext.current
           if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
       }

       darkTheme -> DarkColors
       else -> LightColors
   }
   val view = LocalView.current
   if (!view.isInEditMode) {
       SideEffect {
           val window = (view.context as Activity).window
           window.statusBarColor = colorScheme.primary.toArgb()
           WindowCompat.getInsetsController(window, view).isAppearanceLightStatusBars = darkTheme
       }
   }

   MaterialTheme(
       colorScheme = colorScheme,
       shapes = Shapes,
       typography = Typography,
       content = content
   )
}
```



```kotlin
val colorScheme = when {
     dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
         val context = LocalContext.current
         if (darkTheme) dynamicDarkColorScheme(context) 
       	 else dynamicLightColorScheme(context)
     }

     darkTheme -> DarkColors
     else -> LightColors
 }
```

在 `WoofTheme()` 中，`colorScheme` 变量使用了 `when` 语句

- 如果 `dynamicColor` 为 true，并且构建版本为 S 或更高，则检查设备是否采用 `darkTheme`。
  - 如果采用深色主题，`colorScheme` 会设为 `dynamicDarkColorScheme`。
  - 如果没有采用深色主题，则会设为 `dynamicLightColorScheme`。
- 如果应用未使用 `dynamicColor`，则检查该应用是否采用 `darkTheme`。如果采用，那么 `colorScheme` 会设为 `DarkColors`。
- 如果这两种情况都不是，则 `colorScheme` 会设为 `LightColors`。

复制进来的 **Theme.kt** 文件将 `dynamicColor` 设为了 false，而我们在使用的设备采用了浅色模式，因此 `colorScheme` 将设为 `LightColors`。

#### 颜色映射

Material 组件会自动映射到颜色槽。无需为组件明确分配颜色。

- 在无需修改任何代码的情况下，状态栏会变成主色。
- 整个界面中的其他关键组件（例如悬浮操作按钮）也默认为主色。

可以通过在代码中明确设置颜色来覆盖此设置。



使用 `Card` 来封装包含 `DogIcon()` 和 `DogInformation()` 的 `Row`，以区分列表项的颜色与背景。

```kotlin
@Composable
fun DogItem(
    dog: Dog,
    modifier: Modifier = Modifier
) {
  	// DogItem() 的第一个子级 Composable，因此需要将修饰符 modifier 传递给 Card。
    Card(modifier = modifier) {
      	// 并将原先的第一子级 Composable 的修饰符更新为 Modifier
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(dimensionResource(R.dimen.padding_small))
        ) {
            DogIcon(dog.imageResourceId)
            DogInformation(dog.name, dog.age)
        }
    }
}
```



#### Dimens 文件

就像在应用中使用 **strings.xml** 存储字符串一样，可以使用名为 **dimens.xml** 的文件存储尺寸值。

- 这样做可以避免对值进行硬编码。
- 在需要的情况下，可以在一个地方集中做出更改。

```xml
<resources>
   <dimen name="padding_small">8dp</dimen>
   <dimen name="padding_medium">16dp</dimen>
   <dimen name="image_size">64dp</dimen>
</resources>
```

使用 `dimens.xml` 文件中的值：

```kotlin
dimensionResource(id = R.dimen.padding_small)
```



#### 深色主题

- 您的应用可以选择启用 [Force Dark](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme?hl=zh-cn#force-dark)，系统将会为您实现深色主题。
- 也可以选择自己实现深色主题，为用户提供更好的体验，以便您继续完全控制应用主题。

在选择自己的深色主题时，请务必注意，深色主题的颜色必须符合[无障碍功能对比度标准](https://webaim.org/resources/contrastchecker/)。深色主题使用较深的界面颜色，且色彩强度有限。



##### 在预览中查看深色主题

在 **MainActivity.kt** 添加另一个 Preview 可组合项。这样，当您更改代码中的界面布局时，就能**同时看到浅色主题和深色主题的预览效果。**

```kotlin
@Preview
@Composable
fun WoofPreview() {
    WoofTheme(darkTheme = false) {
        WoofApp()
    }
}

@Preview
@Composable
fun WoofDarkThemePreview() {
    WoofTheme(darkTheme = true) {
        WoofApp()
    }
}
```

| **深色主题**                                                 | **浅色主题**                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/406e9c4dc418be98.png?hl=zh-cn) | ![浅色主题的 Woof 预览](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/e23462bea1028033.png?hl=zh-cn) |

##### 在设备或模拟器上查看深色主题

如需在模拟器或实体设备上以深色主题背景查看应用，请执行以下操作：

1. 打开设备的**设置**应用。
2. 搜索**深色主题**，然后点击进入该主题。
3. 开启**深色主题**。
4. 重新打开 **Woof** 应用后，该应用将采用**深色主题**。



#### 动态配色

Material 3 非常注重用户体验，其中推出的动态配色这项新功能就能**==根据用户的壁纸为应用创建主题==**。

- 如果用户喜欢绿色且拥有蓝色的手机背景，Woof 应用也会据此呈现蓝色。
- 动态主题仅适用于搭载 Android 12 及更高版本的特定设备。

若要启用动态配色，打开 **Theme.kt** 文件并前往 `Theme()` 可组合项，然后将 `dynamicColor` 参数设为 **true**。

```kotlin
@Composable
fun WoofTheme(
   darkTheme: Boolean = isSystemInDarkTheme(),
   dynamicColor: Boolean = true,
   content: @Composable () -> Unit
)
```

### 1.2 添加形状

许多形状都是使用 [`RoundedCornerShape`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/shape/RoundedCornerShape?hl=zh-cn) 定义的，它所描述的是圆角矩形。传入的数字会定义角的圆角程度。

- 如果使用 `RoundedCornerShape(0.dp)`，则矩形没有圆角。
- 如果使用 `RoundedCornerShape(50.dp)`，角将变为完全圆形。
- 还可以为每个角添加不同的圆角百分比，进一步自定义形状。

| 0.dp                                                         | 25.dp                                                        | 50.dp                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![设置了形状的 Woof 列表项](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/7aa47654fba1869a.png?hl=zh-cn) | ![设置了形状的 Woof 列表项](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/d0661b773c703f57.png?hl=zh-cn) | ![设置了形状的 Woof 列表项](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/78bbef6f504eff53.png?hl=zh-cn) |



**Shape.kt** 文件用于定义 Compose 中组件的形状。组件分为三种类型：小、中和大。

系统会根据组件的大小将组件分组为[形状类别](https://m3.material.io/styles/shape/shape-scale-tokens#b09934f1-1b0f-4ce4-ade6-4a1f138add6c)。

```kotlin
// Shape.kt
val Shapes = Shapes(
   small = RoundedCornerShape(50.dp), // 这个设置将用来让图片的形状变为圆形。
)
```

#### 将图片形状设置为圆形

- 将 [`clip`](https://developer.android.com/reference/kotlin/androidx/compose/ui/Modifier?hl=zh-cn#(androidx.compose.ui.Modifier).clip(androidx.compose.ui.graphics.Shape)) 属性添加到 `Image` 的 `modifier`，这会将图片裁剪为某种形状。传入 `MaterialTheme.shapes.small`。
- 添加 [`ContentScale`](https://developer.android.com/reference/kotlin/androidx/glance/layout/ContentScale?hl=zh-cn) 和 [`Crop`](https://developer.android.com/reference/kotlin/androidx/glance/layout/ContentScale?hl=zh-cn#Crop()) 属性，这会根据显示大小裁剪图片。

```kotlin
@Composable
fun DogIcon(
    @DrawableRes dogIcon: Int,
    modifier: Modifier = Modifier
) {
    Image(
        modifier = modifier
            .size(dimensionResource(R.dimen.image_size))
            .padding(dimensionResource(R.dimen.padding_small))
            .clip(MaterialTheme.shapes.small), // *
        contentScale = ContentScale.Crop,  
      	painter = painterResource(dogIcon),
        contentDescription = null
    )
}
```

#### 向列表项添加形状

- `Card` 是可以包含一个可组合项并包含装饰选项的 Surface，可通过边框、形状等添加装饰。
- `Card` 默认使用中等形状，因此无需明确地将其设为中型。

```kotlin
val Shapes = Shapes(
    small = RoundedCornerShape(50.dp),
    medium = RoundedCornerShape(bottomStart = 16.dp, topEnd = 16.dp),
)
```

在 **Theme.kt** 文件中 `MaterialTheme()` 会应用我们设置的形状 `Shapes`：

```kotlin
MaterialTheme(
   colors = colors,
   typography = Typography,
   shapes = Shapes,
   content = content
)
```

### 1.3 添加排版

#### Material Design 字型比例(type scale)

字型比例是一系列字体样式的选择，可在应用中使用，确保样式既灵活又一致。

[Material Design 字型比例](https://m3.material.io/styles/typography/type-scale-tokens)包含字型系统支持的 15 种字体样式。命名和分组大致如下：

- 有 display、headline、title、body 和 label 几个文本类别。
- 上述每个类别每个都有大号、中号和小号。

只有在您想自定义应用时，才需要使用这些选项。如果不知道为每个字型比例类别设置什么，可以使用默认的排版缩放设置。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/999a161dcd9b0ec4.png?hl=zh-cn" alt="999a161dcd9b0ec4.png" style="zoom: 50%;" />

字型比例包含可重复使用的文本类别，每个类别都有预期的应用和含义。

- display：作为屏幕上最大的文本，显示字体专用于简短的重要文字或数字，最适合在大屏幕设备上使用。
- headline：大标题最适合用来**在小屏幕设备上显示高强调度的简短文本。**这类样式有助于标记文本的主要段落或重要内容区域。
- title：标题比大标题样式要小，适用于内容相对较短的中强调度文本。
- body：正文样式用于显示应用中较长的文本段落。
- label：标签样式是较小的实用样式，用于显示组件内部的文本或内容正文中非常小的文本（例如字幕）。

#### 字体

##### 创建字体资源目录

在 res 目录下创建 font 文件夹：

<img src="03 Build beautiful apps.assets/image-20240115165559077.png" alt="image-20240115165559077" style="zoom:80%;" />



##### 初始化字体

```kotlin
// ui.theme/Type.kt
val AbrilFatface = FontFamily(
    Font(R.font.abril_fatface_regular)
)

val Montserrat = FontFamily(
    Font(R.font.montserrat_regular),
    Font(R.font.montserrat_bold, FontWeight.Bold),
)
```

- 对于 `montserrat_bold`，还应添加 `FontWeight.Bold`。即使您传入了字体文件的粗体版本，Compose 也不知道该文件是粗体文件，因此您需要明确地将此文件关联到 `FontWeight.Bold`。

然后将不同类型的标题设为您刚刚添加的字体。`Typography` 对象具有上面讨论的 13 种不同字体的参数。您可以根据需要定义任意数量。

在此应用中，我们将设置 `displayLarge`、`displayMedium` 和 `bodyLarge`。

![8ea685b3871d5ffc.png](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-material-theming/img/8ea685b3871d5ffc.png?hl=zh-cn)

```kotlin
val Typography = Typography(
    displayLarge = TextStyle(
        fontFamily = AbrilFatface,
        fontWeight = FontWeight.Normal,
        fontSize = 36.sp
    ),
    displayMedium = TextStyle(
        fontFamily = Montserrat,
        fontWeight = FontWeight.Bold,
        fontSize = 20.sp
    ),
    labelSmall = TextStyle(
        fontFamily = Montserrat,
        fontWeight = FontWeight.Bold,
        fontSize = 14.sp
    ),
    bodyLarge = TextStyle(
        fontFamily = Montserrat,
        fontWeight = FontWeight.Normal,
        fontSize = 14.sp
    ),
)
```

在 **Theme.kt** 文件中 `MaterialTheme()` 会应用我们设置的字型比例 `Typography`：

```kotlin
MaterialTheme(
   colors = colors,
   typography = Typography,
   shapes = Shapes,
   content = content
)
```

##### 向应用文本添加排版

```kotlin
@Composable
fun DogInformation(
    @StringRes dogName: Int,
    dogAge: Int,
    modifier: Modifier = Modifier
) {
    Column(modifier = modifier) {
        Text(
            text = stringResource(dogName),
            style = MaterialTheme.typography.displayMedium, // *
            modifier = Modifier.padding(top = dimensionResource(R.dimen.padding_small))
        )
        Text(
            text = stringResource(R.string.years_old, dogAge), // *
            style = MaterialTheme.typography.bodyLarge
        )
    }
}
```

### 1.4 添加顶部栏

[`Scaffold`](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary?hl=zh-cn#Scaffold(androidx.compose.ui.Modifier,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,androidx.compose.material3.FabPosition,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.foundation.layout.WindowInsets,kotlin.Function1)) 是一种布局，可为各种组件和屏幕元素（如 `Image`、`Row` 或 `Column`）提供槽。

`Scaffold` 还为 [`TopAppBar`](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary?hl=zh-cn#TopAppBar(kotlin.Function0,androidx.compose.ui.Modifier,kotlin.Function0,kotlin.Function1,androidx.compose.foundation.layout.WindowInsets,androidx.compose.material3.TopAppBarColors,androidx.compose.material3.TopAppBarScrollBehavior)) 提供了槽，您将在本部分中使用。

- `TopAppBar` 可用于许多用途。
- `TopAppBar` 有四种不同的类型：居中、小、中和大。

创建顶部栏可组合项：

```kotlin
@Composable
fun WoofTopAppBar(modifier: Modifier = Modifier) {
    @OptIn(ExperimentalMaterial3Api::class)
    CenterAlignedTopAppBar(
        title = {
            Row(
                verticalAlignment = Alignment.CenterVertically
            ) {
                Image(
                    painter = painterResource(id = R.drawable.ic_woof_logo),
                    contentDescription = null,
                    modifier = Modifier
                        .size(dimensionResource(id = R.dimen.image_size))
                        .padding(dimensionResource(id = R.dimen.padding_small))
                )

                Text(
                    text = stringResource(id = R.string.app_name),
                    style = MaterialTheme.typography.displayLarge
                )
            }
        },
        modifier = modifier
    )
}
```

将顶部栏可组合项添加到布局中：

- `Scaffold` 支持 `contentWindowInsets` 参数，该参数有助于为 Scaffold 内容指定边衬区。
- [`WindowInsets`](https://developer.android.com/reference/android/view/WindowInsets?hl=zh-cn) 是屏幕上应用与系统界面相交的部分，应通过 `PaddingValues` 参数将其传递给内容槽。

在 `Scaffold` 中，添加 `topBar` 属性并将其设置为 `WoofTopAppBar()`。

```kotlin
@Composable
fun WoofApp() {
   Scaffold(
       topBar = {
           WoofTopAppBar()
       }
   ) { it ->
       LazyColumn(contentPadding = it) {
           items(dogs) {
               DogItem(
                   dog = it,
                   modifier = Modifier.padding(dimensionResource(id = R.dimen.padding_small))
               )
           }
       }
   }
}
```



##02 使用 Jetpack Compose 添加简单的动画

### 2.1 添加展开图标

#### 添加 Gradle 依赖项

下面将为项目添加 `material-icons-extended` 库依赖项。您将使用此库中的 `Icons.Filled.ExpandLess` ![fae6706c0c9879bd.png](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/fae6706c0c9879bd.png?hl=zh-cn) 和 `Icons.Filled.ExpandMore` ![6814e6aaad3bffdc.png](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/6814e6aaad3bffdc.png?hl=zh-cn) 图标。

1. 在 **Project** 窗格中，依次打开 **Gradle Scripts > build.gradle.kts (Module :app)**。

2. 滚动到 `build.gradle.kts (Module :app)` 文件的末尾。在 `dependencies{}` 代码块中，添加以下行：

   ```kotlin
   implementation("androidx.compose.material:material-icons-extended")
   ```

#### 添加图标可组合项

- 添加一个 [`IconButton()`](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary?hl=zh-cn#IconButton(kotlin.Function0,androidx.compose.ui.Modifier,kotlin.Boolean,androidx.compose.foundation.interaction.MutableInteractionSource,kotlin.Function0)) 可组合项，该可组合项接受 `onClick` 具名形参（一个使用尾随 lambda 语法的 lambda，会在按下此图标时调用），以及一个可选的 `modifier`。
- 在 `IconButton()` lambda 代码块内，添加一个 [`Icon`](https://developer.android.com/reference/kotlin/androidx/compose/material/icons/Icons?hl=zh-cn) 可组合项。
  - 将 `imageVector` 参数设置为 `Icons.Filled.ExpandMore`。
  - 添加值形参 `tint`，并将图标的颜色设为 `MaterialTheme.colorScheme.secondary`。
  - 添加具名形参 `contentDescription`，并将其设置为字符串资源 `R.string.expand_button_content_description`。

```kotlin
@Composable
private fun DogItemButton(
    expanded: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    IconButton(
        onClick = onClick,
        modifier = modifier
    ) {
        Icon(
            imageVector = Icons.Filled.ExpandMore,
            tint = MaterialTheme.colorScheme.secondary,
            contentDescription = stringResource(id = R.string.expand_button_content_description)
        )
    }
}
```

#### 显示图标

在 `DogItem()` 的开头，添加一个变量保存列表项的展开状态。将初始值设置为 `false`。

```kotlin
var expanded by remember { mutableStateOf(false) }
```

- 使用 `mutableStateOf()` 函数，以便 Compose 观察状态值发生的更改，并触发重组来更新界面。
- 使用 `remember()` 函数封装 `mutableStateOf()` 函数调用，以在初始组合期间将值存储在组合中，并在重组期间返回存储的值。

在列表项中显示图标按钮，并将展开图标右对齐。

为了将展开图标右对齐，需要在展开图标前添加一个分隔符 `Spacer` 可组合项，其权重为 `1f`。**由于分隔符是行中唯一加权的子元素，因此该元素会在测量其他未加权子元素的宽度之后，填充行中的剩余空间。**

- [`Modifier.weight()`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/RowScope?hl=zh-cn#(androidx.compose.ui.Modifier).weight(kotlin.Float,kotlin.Boolean)) 会根据界面元素相对于其加权同级元素（相应行或列中的其他子元素）的权重，按比例设置界面元素的宽度和高度。
- 如果系统没有为子级可组合项分配权重（权重为可选形参），那么子级可组合项的高度/宽度将默认为封装内容（封装界面元素中的内容）。
- Kotlin 中的浮点值为十进制数，以在数字末尾附上 `f` 或 `F` 表示。

```kotlin
Row(
   modifier = Modifier
       .fillMaxWidth()
       .padding(dimensionResource(R.dimen.padding_small))
) {
   DogIcon(dog.imageResourceId)
   DogInformation(dog.name, dog.age)
   Spacer(modifier = Modifier.weight(1f))
   DogItemButton(
       expanded = expanded,
       onClick = { /*TODO*/ }
   )
}
```

### 2.2 添加可组合项以显示爱好信息

```kotlin
@Composable
fun DogHobby(
   @StringRes dogHobby: Int,
   modifier: Modifier = Modifier
){
   Column(
       modifier = modifier
   ) {
    	 Text(
           text = stringResource(R.string.about),
           style = MaterialTheme.typography.labelSmall
       )
       Text(
           text = stringResource(dogHobby),
           style = MaterialTheme.typography.bodyLarge
       )
   }
}
```



```kotlin
@Composable
fun DogItem(
   dog: Dog,
   modifier: Modifier = Modifier
) {
   var expanded by remember { mutableStateOf(false) }
   Card(
       modifier = modifier
   ) {
       Column() {
           Row(
               modifier = Modifier
                   .fillMaxWidth()
                   .padding(dimensionResource(R.dimen.padding_small))
           ) {
               DogIcon(dog.imageResourceId)
               DogInformation(dog.name, dog.age)
               Spacer(Modifier.weight(1f))
               DogItemButton(
                   expanded = expanded,
                   onClick = { /*TODO*/ },
               )
           }
           DogHobby(
               dog.hobbies,
               modifier = Modifier.padding(
                   start = dimensionResource(R.dimen.padding_medium),
                   top = dimensionResource(R.dimen.padding_small),
                   end = dimensionResource(R.dimen.padding_medium),
                   bottom = dimensionResource(R.dimen.padding_medium)
               )
           )
       }
   }
}
```

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/3406418742905af8.png?hl=zh-cn" alt="带有展开列表项的 Woof 预览"  />

### 2.3 点击按钮时显示或隐藏业余爱好

按钮点击时将 `expanded` 变量值置反。

```kotlin
DogItemButton(
   expanded = expanded,
   onClick = { expanded = !expanded }
)
```

通过 `if` 条件语句判断是否展示 `DogHobby` 可组合项。

```kotlin
@Composable
fun DogItem(
   dog: Dog,
   modifier: Modifier = Modifier
) {
   var expanded by remember { mutableStateOf(false) }
   Card(
       ...
   ) {
       Column(
           ...
       ) {
           Row(
               ...
           ) {
               ...
           }
           if (expanded) {
               DogHobby(
                   dog.hobbies, modifier = Modifier.padding(
                       start = dimensionResource(R.dimen.padding_medium),
                       top = dimensionResource(R.dimen.padding_small),
                       end = dimensionResource(R.dimen.padding_medium),
                       bottom = dimensionResource(R.dimen.padding_medium)
                   )
               )
           }
       }
   }
}
```

展开后更新图标显示：

```kotlin
@Composable
private fun DogItemButton(
  expanded: Boolean,
   ...
) {
   IconButton(onClick = onClick) {
       Icon(
          // if-else 语句只有一行代码时，大括号是可选的。
           imageVector = if (expanded) Icons.Filled.ExpandLess else Icons.Filled.ExpandMore,
           ...
       )
   }
}
```

### 2.4 添加动画

#### 弹簧动画

[弹簧动画](https://developer.android.com/jetpack/compose/animation?hl=zh-cn#spring)是一种基于物理特性的动画，依靠**弹簧弹力**来驱动。使用弹簧动画时，移动的值和速度是根据应用的弹簧弹力计算得出的。

例如，如果您在屏幕上拖动某个应用图标，然后松开手指释放它，那么该图标便会被一股看不见的力量拉回其原始位置。

![弹簧释放效果](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/af35a9d327d1cab0.gif?hl=zh-cn)

弹簧弹力基于以下两个属性：

- **阻尼比**：弹簧的弹力。
- **刚度**：弹簧的刚度，即弹簧移动到终点的速度。

以下是一些具有[不同阻尼比和刚度](https://developer.android.com/reference/kotlin/androidx/compose/animation/core/Spring?hl=zh-cn)的动画示例。

| ![弹簧效果](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/d8e850d6eeec30a5.gif?hl=zh-cn)高弹力 | ![弹簧效果](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/32eac117de778099.gif?hl=zh-cn)无弹力 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

| ![img](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/7ba20cf822ecb900.gif?hl=zh-cn)高刚度 | ![低刚度](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-woof-animation/img/710c9cad92e2d7ad.gif?hl=zh-cn)很低的刚度 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |



在 `MainActivity.kt` 的 `DogItem()` 内，向 `Column` 布局添加 `modifier` 形参。

使用 ==[`animateContentSize`](https://developer.android.com/jetpack/compose/animation?hl=zh-cn#animateContentSize)== 修饰符链接该修饰符，**以便为大小（列表项高度）变化添加动画效果。**

```kotlin
Column(
   modifier = Modifier
			.animateContentSize()
)
```

在当前实现中的动画效果非常细微，动画会缓入和缓出，但不会有弹，在运行应用时很难觉察出来。

为解决此问题，可以使用可选的 [`animationSpec`](https://developer.android.com/reference/kotlin/androidx/compose/animation/core/AnimationSpec?hl=zh-cn) 形参来自定义动画。

使用 [`DampingRatioNoBouncy`](https://developer.android.com/reference/kotlin/androidx/compose/animation/core/Spring?hl=zh-cn#DampingRatioNoBouncy()) 将其设置为弹簧动画，使其无弹跳，并使用 [`StiffnessMedium`](https://developer.android.com/reference/kotlin/androidx/compose/animation/core/Spring?hl=zh-cn#StiffnessMedium()) 形参让弹簧略硬。

```kotlin
Column (
    modifier = Modifier
        .animateContentSize(
            animationSpec = spring(
                dampingRatio = Spring.DampingRatioNoBouncy,
                stiffness = Spring.StiffnessMedium
            )
        )
)
```

#### `animate*AsState`

==[`animate*AsState()`](https://developer.android.com/jetpack/compose/animation/value-based?hl=zh-cn#animate-as-state)== 函数是 Compose 中最简单的 Animation API 之一，**用于为单个值添加动画效果**。您只需提供结束值（或目标值），该 API 就会从当前值开始向指定结束值播放动画。

Compose 为 `Float`、`Color`、`Dp`、`Size`、`Offset` 和 `Int` 等提供了 `animate*AsState()` 函数。您可以使用接受通用类型的 `animateValueAsState()` 轻松添加对其他数据类型的支持。

使用 `animateColorAsState()` 函数更改列表项处于展开状态时的颜色：

1. 在 `DogItem()` 中，声明一个颜色变量，并将其初始化委托给 `animateColorAsState()` 函数。
   - 根据 `expanded` 布尔值，设置 `targetValue` 具名形参。
     - 如果列表项处于展开状态，将列表项设置为 `tertiaryContainer` 颜色。
     - 否则，将其设置为 `primaryContainer` 颜色。

```kotlin
val color by animateColorAsState(
   targetValue = if (expanded) MaterialTheme.colorScheme.tertiaryContainer
   else MaterialTheme.colorScheme.primaryContainer,
)
```

2. 将 `color` 设置为 `Column` 的背景修饰符：

```kotlin
@Composable
fun DogItem(
   dog: Dog,
   modifier: Modifier = Modifier
) {
   ...
   Card(
       ...
   ) {
       Column(
           modifier = Modifier
               .animateContentSize(
                   ...
                   )
               )
               .background(color = color) // *
       ) {...}
}
```

## 03 测试无障碍功能

### 安装 Android 无障碍套件应用

Android 无障碍套件包含一系列无障碍应用。在此 Codelab 的后面部分，您将需要通过它来使用 **TalkBack** 和**开关控制**。

## 04

`Modifier.size(72.dp)` 与 `Modifier.width(72.dp).height(72.dp)` 等同：

```kotlin
Box(
	modifier = Modifier.size(72.dp)
)
```

```kotlin
Box(
	modifier = Modifier.width(72.dp).height(72.dp)
)
```



`Modifier.sizeIn()`

```kotlin
Row(
	modifier = Modifier
		.fillMaxWidth()
		.sizeIn(minHeight = 72.dp) // *
)
```

```kotlin
Image(
    painter = painterResource(id = hero.imageRes),
    contentDescription = null,
    alignment = Alignment.TopCenter,
    contentScale = ContentScale.FillWidth
)
```

开启夜间模式和非夜间模式两种 Preview：

```kotlin
@Preview("Light Theme")
@Preview("Dark Theme", uiMode = Configuration.UI_MODE_NIGHT_YES)
@Composable
fun HeroesScreenPreview() {
    SuperheroesTheme {
        SuperheroList(heroes = HeroesRepository.heroes)
    }
}
```

Card 的阴影

```kotlin
Card(
    elevation = CardDefaults.cardElevation(defaultElevation = 2.dp),
    modifier = modifier
)
```

动画效果

```kotlin
@OptIn(ExperimentalAnimationApi::class)
@Composable
fun SuperheroList(
    heroes: List<Hero>,
    modifier: Modifier = Modifier,
    contentPadding: PaddingValues = PaddingValues(0.dp)
) {
    val visibleState = remember {
        MutableTransitionState(false).apply {
            targetState = true
        }
    }
		// 列表的渐入效果
    AnimatedVisibility(
        visibleState = visibleState,
        enter = fadeIn(
            animationSpec = spring(dampingRatio = DampingRatioLowBouncy)
        ),
        exit = fadeOut(),
        modifier = modifier
    ) {
        LazyColumn(
            contentPadding = contentPadding,
            modifier = modifier
        ) {
            itemsIndexed(heroes) { index, hero ->
                SuperheroItem(
                    hero = hero,
                    modifier = Modifier
                        .padding(
                            horizontal = dimensionResource(id = R.dimen.padding_large),
                            vertical = dimensionResource(id = R.dimen.padding_medium)
                        )
                  			// 每个列表项从下至上的移入效果
                        .animateEnterExit(
                            enter = slideInVertically(
                                animationSpec = spring(
                                    stiffness = StiffnessVeryLow,
                                    dampingRatio = DampingRatioLowBouncy
                                ),
                                initialOffsetY = { it * (index + 1) }
                            )
                        )
                )
            }
        }
    }

}
```

