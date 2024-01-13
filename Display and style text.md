Compose 提供了：

- 基础的 [`BasicText`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/text/package-summary?hl=zh-cn#BasicText(kotlin.String,androidx.compose.ui.Modifier,androidx.compose.ui.text.TextStyle,kotlin.Function1,androidx.compose.ui.text.style.TextOverflow,kotlin.Boolean,kotlin.Int)) 和 [`BasicTextField`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/text/package-summary?hl=zh-cn#BasicTextField(kotlin.String,kotlin.Function1,androidx.compose.ui.Modifier,kotlin.Boolean,kotlin.Boolean,androidx.compose.ui.text.TextStyle,androidx.compose.foundation.text.KeyboardOptions,androidx.compose.foundation.text.KeyboardActions,kotlin.Boolean,kotlin.Int,androidx.compose.ui.text.input.VisualTransformation,kotlin.Function1,androidx.compose.foundation.interaction.MutableInteractionSource,androidx.compose.ui.graphics.Brush,kotlin.Function1))：用于显示文字以及处理用户输入的主要函数。
- 更高级的 [`Text`](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary?hl=zh-cn#Text(kotlin.String,androidx.compose.ui.Modifier,androidx.compose.ui.graphics.Color,androidx.compose.ui.unit.TextUnit,androidx.compose.ui.text.font.FontStyle,androidx.compose.ui.text.font.FontWeight,androidx.compose.ui.text.font.FontFamily,androidx.compose.ui.unit.TextUnit,androidx.compose.ui.text.style.TextDecoration,androidx.compose.ui.text.style.TextAlign,androidx.compose.ui.unit.TextUnit,androidx.compose.ui.text.style.TextOverflow,kotlin.Boolean,kotlin.Int,kotlin.Function1,androidx.compose.ui.text.TextStyle)) 和 [`TextField`](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary?hl=zh-cn#TextField(kotlin.String,kotlin.Function1,androidx.compose.ui.Modifier,kotlin.Boolean,kotlin.Boolean,androidx.compose.ui.text.TextStyle,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Boolean,androidx.compose.ui.text.input.VisualTransformation,androidx.compose.foundation.text.KeyboardOptions,androidx.compose.foundation.text.KeyboardActions,kotlin.Boolean,kotlin.Int,androidx.compose.foundation.interaction.MutableInteractionSource,androidx.compose.ui.graphics.Shape,androidx.compose.material.TextFieldColors))：遵循 Material Design 准则的可组合项。

## 显示文字

```kotlin
@Composable
fun SimpleText() {
	Text("Hello World")
}
```

### 显示资源中的文字

建议您使用[字符串资源](https://developer.android.com/jetpack/compose/resources?hl=zh-cn#strings)，而不是对 `Text` 值进行硬编码。

- 使用字符串资源时您可以与 Android 视图共享相同的字符串
- 并为您的应用国际化做好准备

```kotlin
@Composable
fun StringResourceText() {
	Text(stringResource(R.string.hello_world))
}
```

## 设置文字样式

`Text` 可组合项有多个用于为其内容设置样式的可选参数。

- `Text` 的所有参数，可查阅 [Compose Text 源代码](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:compose/material/material/src/commonMain/kotlin/androidx/compose/material/Text.kt;l=91?hl=zh-cn)。

每当您设置其中任何一个参数，都会将样式应用于整个文字值。

如果您需要在同一行或段落中应用多种样式，请参阅有关[多种内嵌样式](https://developer.android.com/jetpack/compose/text?hl=zh-cn#multiple-styles)的部分。

### 常用文本样式

#### 更改文字颜色

```kotlin
@Composable
fun BlueText() {
	Text("Hello World", color = Color.Blue)
}
```

#### 更改字号

```kotlin
@Composable
fun BigText() {
	Text("Hello World", fontSize = 30.sp)
}
```

#### 将文字设为斜体

```kotlin
@Composable
fun ItalicText() {
	Text("Hello World", fontStyle = FontStyle.Italic)
}
```

#### 将文字设为粗体

```kotlin
@Composable
fun BoldText() {
	Text("Hello World", fontWeight = FontWeight.Bold)
}
```

#### 阴影

通过 `style` 参数，您可以设置一个**类型为 [`TextStyle`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/TextStyle?hl=zh-cn) 的对象并配置多个参数**，例如阴影。

[`Shadow`](https://developer.android.com/reference/kotlin/androidx/compose/ui/graphics/Shadow?hl=zh-cn) 会接收<u>阴影颜色</u>、<u>偏移量</u>或相对于 `Text` 所在的位置和<u>模糊半径</u>（用来控制模糊效果）。

```kotlin
@Preview(showBackground = true)
@Composable
fun TextShadow() {
	val offset = Offset(5.0f, 10.0f)
  Text(
  	text = "Hello world!",
    style = TextStyle(
    	fontSize = 24.sp,
      shadow = Shadow(
        color = Color.Blue,
        offset = offset,
        blurRadius = 3f
      )
    )
  )
}
```

![带有蓝色阴影的“Hello World”字样](https://developer.android.com/static/images/jetpack/compose/text-shadow.png?hl=zh-cn)

### 为文字添加多种样式

要在同一个可组合文本中设置不同的样式，请使用 `AnnotatedString`，这是一个可以用任意注解样式进行注解的字符串。

AnnotatedString 是一个数据类，包含：

- 一个 `Text` 值。
- 一个 `SpanStyleRange` 组成的 `List`，相当于在文本值内指定位置范围的内联样式。
- 一个 `ParagraphStyleRange` 组成的 `List`，指定文本对齐、文本方向、行高和文本缩进样式。

`TextStyle` 用于 `Text` 可组合项，而 `SpanStyle` 和 `ParagraphStyle` 用于 `AnnotatedString`。

`SpanStyle` 和 `ParagraphStyle` 之间的区别在于：

- `ParagraphStyle` 可应用于整个段落。一旦用 `ParagraphStyle` 标记了一部分文字，该部分就会与其余部分隔开，就像在开头和末尾有换行符一样。
- 而 `SpanStyle` 可以在字符级别应用。

`AnnotatedString` 有一个类型安全的构建器 `buildAnnotatedString`，使其更容易创建。

```kotlin
@Composable
fun MultipleStylesInText() {
	Text(
		buildAnnotatedString {
			withStyle(style = SpanStyle(color = Color.Blue)) {
				append("H")
			}
			append("ello ")
			
			withStyle(style = SpanStyle(
				fontWeight = FontWeight.Bold,
				color = Color.Red)
       ) {
				append("W")
			}
      append("orld")
		}
	)
}
```

![The words ](https://developer.android.com/static/images/jetpack/compose/text-inline-styles.png)

### 使用 Brush API 启用高级样式

要启用更高级的文本样式，你可以使用带有 `TextStyle` 和 `SpanStyle` 的 `Brush` API。

在任何可以使用 `TextStyle` 或 `SpanStyle` 的地方，都可以使用 `Brush`。

#### 使用 Brush 设置文本样式

使用 `TextStyle` 内置的 Brush 来配置文本。

例如，你可以为你的文本设置线性渐变：

```kotlin
val gradientColors = listOf(Cyan, LightBlue, Purple)

Text(
	text = text,
	style = TextStyle(
		brush = Brush.linearGradient(
			colors = gradientColors
		)
	)
)
```

<img src="https://developer.android.com/static/images/jetpack/compose/text-designmain.png" alt="Using Brush API’s `linearGradient` function with a defined list of colors." style="zoom:50%;" />

#### 集成

你虽然可以使用 Brush 搭配 TextStyle 和 SpanStyle，但 Brush 与 TextField 和 buildAnnotatedString 的集成是更加天衣无缝的。

##### 使用 `SpanStyle` 的附加样式

###### 对文本的一部分应用 Brush

如果你只想对文本的一部分应用 Brush，使用 `buildAnnotatedString` 和 `SpanStyle` API。

```kotlin
Text(
	text = buildAnnotatedString {
		append("Do not allow people to dim your shine\n")
		withStyle(
			spanStyle(
				brush = Brush.linearGradient(
					colors = rainbowColors
				)
			)
		) {
      append("because they are blinded.")
    }
    append("\nTell them to put some sunglasses on.")
	}
)
```

<img src="https://developer.android.com/static/images/jetpack/compose/text-style.png" alt="Using a default Brush with linearGradient as a style for Text." style="zoom:50%;" />

###### 一部分文本的透明度

要调整文本中特定一部分的不透明度，使用 `SpanStyle` 的可选 `alpha` 参数。

对文本的两个部分使用相同的笔刷，并更改相应 span 中的 alpha 参数。

在代码示例中，第一个文本跨度显示为半不透明度(alpha = 0.5 f)，而第二个文本跨度显示为完全不透明度(alpha = 1f)。

```kotlin
val brush = Brush.linearGradient(colors = rainbowColors)

buildAnnotatedString {
	withStyle(
		spanStyle(brush = brush, alpha = .5f)
	) {
		append("Text in ")
	}
	
	withStyle(
		SpanStyle(brush = brush, alpha = 1f)
	) {
		append("Compose ❤")
	}
}
```

<img src="https://developer.android.com/static/images/jetpack/compose/text-opacity.png" alt="Using buildAnnotatedString and SpanStyle’s alpha parameter, along with linearGradient to add opacity to a span of text." style="zoom:50%;" />

##### 更多资源

 [Brushing Up on Compose Text Coloring](https://medium.com/androiddevelopers/brushing-up-on-compose-text-coloring-84d7d70dd8fa)

 [Animating brush Text coloring in Compose](https://medium.com/androiddevelopers/animating-brush-text-coloring-in-compose-️-26ae99d9b402)



## 段落样式

要设置段落样式，可以配置参数如 `textAlign` 和 `lineHeight`，或定义自己的 `ParagraphStyle`。

### 文本对齐

通过 `textAlign` 参数，您可以在 `Text` 可组合项的 Surface 区域内设置文本的水平[对齐方式](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/style/TextAlign?hl=zh-cn)。

默认情况下，`Text` 会根据其内容值选择自然的文字对齐方式：

- 对于从左到右书写的文字，如拉丁语、西里尔文或朝鲜文，向 `Text` 容器的左边缘对齐。
- 对于从右到左书写的文字，如阿拉伯语或希伯来语，向 `Text` 容器的右边缘对齐。

```kotlin
@Preview(showBackground = true)
@Composable
fun CenterText() {
	Text(
    "Hello World", 
    textAlign = TextAlign.Center, 
    modifier = Modifier.width(150.dp)
  )
}
```

![The words ](https://developer.android.com/static/images/jetpack/compose/text-centered.png)

如果您想手动设置 `Text` 可组合项的文字对齐方式，最好使用 `TextAlign.Start` 或 `TextAlign.End`（而不要使用 `TextAlign.Left` 和 `TextAlign.Right`），这样系统就可以根据具体语言的首选文字方向，将您的设置解析为向 `Text` 的正确边缘对齐。

### 在一个段落中添加多个样式

要在一个段落中添加多个样式，你可以在一个 `AnnotatedString` 中使用 `ParagraphStyle`，它可以用任意注解样式进行注解。

**一旦你的文本的一部分被标记为 `ParagraphStyle`，这部分就会与其他文本分开，就好像它在开头和结尾存在换行符一样。**

`AnnotatedString` 有一个类型安全的构建器 buildAnnotatedString，使其更容易创建。

下面的代码片段使用 `buildAnnotatedString` 来设置段落风格：

```kotlin
@Composable
fun ParagraphStyle() {
	Text(
		buildAnnotatedString {
			withStyle(style = ParagraphStyle(lineHeight = 30.sp)) {
				withStyle(style = SpanStyle(color = Color.Blue)) {
					append("Hello\n")
				}
				withStyle(
					style = SpanStyle(fontWeight = FontWeight.Bold, color = Color.Red)
				) {
					append("World\n")
				}
        append("Compose")
			}
		}
	)
}
```

<img src="https://developer.android.com/static/images/jetpack/compose/text-paragraph-styles.png" alt="Three paragraphs in three different styles: Blue, red and bold, and plain black" style="zoom:50%;" />

### 调整行高和内边距

`includeFontPadding` 是一个废弃属性，它根据字体规格在文本的第一行顶部和最后一行底部添加额外的内边距。

- 在 Compose 1.2.0 中，`includeFontPadding` 默认设置为 true。

现在建议使用 Compose 1.2.0 中已弃用的 API `PlatformTextStyle` 将 `includeFontPadding` 设置为 false (这将移除多余的内边距)，并进一步调整您的文本。



可以使用 `lineHeight` 参数配置文本的行高。然后可以使用新的 API `LineHeightStyle` 来进一步配置该文本的对齐方式，并移除空格。 

使用文本单位 **“em”** (相对字体大小)而不是 “sp” (缩放像素)来调整 `lineHeight`，以提高精度。

<img src="https://developer.android.com/static/images/jetpack/compose/text-lineheightv3.png" alt="Image showing lineHeight as a measurement based on the lines directly above and below it." style="zoom:50%;" />

```kotlin
@Composable
fun AlignedText() {
    Text(
      text = myText,
      style = LocalTextStyle.current.merge(
        TextStyle(
          lineHeight = 2.5.em,
          // 关闭 includeFontPadding 配置
          platformStyle = PlatformTextStyle(
            includeFontPadding = false
          ),
          // 设置文本对齐方式
          lineHeightStyle = LineHeightStyle(
            alignment = LineHeightStyle.Alignment.Center,
            trim = LineHeightStyle.Trim.None
          )
        )
      )
    )
}
```

除了调整 lineHeight，还可以使用 `LineHeightStyle` 实验 API 进一步居中和样式文本: `LineHeightStyle.Alignment` 和 `LineHeightStyle.Trim`。(`includeFontPadding` 必须设置为 false，Trim 才能生效)

Alignment 和 Trim 使用文本行之间的测量空间来更适当地将其分布到所有行，包括单行文本和文本块的顶行。



- [`LineHeightStyle.Alignment`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/style/LineHeightStyle.Alignment) 定义了如何在行高提供的空间内对齐行。在每行中，您可以将文本对齐到顶部、底部、中间或按比例对齐。
- `LineHeightStyle.Trim` 允许保留或删除从任何 `lineHeight` 和 Alignment 的调整中生成的额外的空间（如文本第一行的顶部和最后一行的底部）。

下面的示例展示了当居中对齐（LineHeightStyle.Alignment.Center）时，不同 [`LineHeightStyle.Trim`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/style/LineHeightStyle.Trim) 配置下多行文本的外观。

<img src="Compose 中的文字.assets/image-20231117165343456.png" alt="image-20231117165343456" style="zoom:50%;" />



## 配置文本布局

### 限制可见行数

使用 `maxLines` 参数限制可组合文本中的可见行数。

```kotlin
@Composable
fun LongText() {
	Text("Hello ".repeat(50), maxLines = 2)
}
```

![A long text passage truncated after two lines](https://developer.android.com/static/images/jetpack/compose/text-maxlines.png)

### 指示文本溢出

设置 `textOverflow` 参数指定文本溢出时的指示符，仅在显示文本被截断时显示。

```kotlin
@Composable
fun OverflowedText() {
	Text("Hello Compose".repeat(50), maxLines = 2, overflow = TextOverflow.Ellipsis)
}
```

<img src="https://developer.android.com/static/images/jetpack/compose/text-overflow.png" alt="A long passage of text truncated after three lines, with an ellipsis at the end" style="zoom:50%;" />

