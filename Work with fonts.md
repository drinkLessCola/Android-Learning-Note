### 设置字体

`Text` 有一个 `fontFamily` 参数，用于设置可组合项中使用的字体。

默认情况下，系统会[添加](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/font/FontFamily?hl=zh-cn#summary) Serif、Sans Serif、等宽和 Cursive 字体系列：

```kotlin
@Composable
fun DifferentFonts() {
		Column {
				Text("Hello World", fontFamily = FontFamily.Serif)
				Text("Hello World", fontFamily = FontFamily.SansSerif)
		}
}
```

![以两种不同字体（使用或不使用 Serif）显示的“Hello World”字样](https://developer.android.com/static/images/jetpack/compose/text-different-fonts.png?hl=zh-cn)

可以使用 `fontFamily` 属性来处理 `res/font` 文件夹中定义的自定义字体和字型：

<img src="https://developer.android.com/static/images/jetpack/compose/text-font-folder.png" alt="Graphical depiction of the res style="zoom:50%;"  > font folder in the development environment " />

```kotlin
val firaSansFamily = FontFamily(
        Font(R.font.firasans_light, FontWeight.Light),
        Font(R.font.firasans_regular, FontWeight.Normal),
        Font(R.font.firasans_italic, FontWeight.Normal, FontStyle.Italic),
        Font(R.font.firasans_medium, FontWeight.Medium),
        Font(R.font.firasans_bold, FontWeight.Bold)
)
```

可以将此 `fontFamily` 传递给 `Text` 可组合项。

由于 `fontFamily` 可以包含不同的粗细，因此您可以手动设置 `fontWeight` 来为文本选择合适的粗细：

```kotlin
Column {
	Text(..., fontFamily = firaSansFamily, fontWeight = FontWeight.Light)
	Text(..., fontFamily = firaSansFamily, fontWeight = FontWeight.Normal)
	Text(..., fontFamily = firaSansFamily, fontWeight = FontWeight.Normal, fontStyle = FontStyle.Italic)
	Text(..., fontFamily = firaSansFamily, fontWeight = FontWeight.Medium)
	Text(..., fontFamily = firaSansFamily, fontWeight = FontWeight.Bold)
}
```

![The words ](https://developer.android.com/static/images/jetpack/compose/text-font-family.png)

### 可下载字体

从 [Compose 1.2.0](https://developer.android.com/jetpack/androidx/releases/compose-ui?hl=zh-cn#1.2.0) 开始，可以使用 Compose 应用中的可下载字体 API 异步下载 [Google 字体](https://fonts.google.com/?hl=zh-cn)，并在您的应用中使用这些字体。（自定义提供下载的字体尚不支持）

#### 以编程方式使用可下载字体

1. 添加依赖项：

   ```kotlin
   dependencies {
       ...
       implementation("androidx.compose.ui:ui-text-google-fonts:1.5.2")
   }
   ```

2. 使用 Google Fonts 的凭据初始化 [`GoogleFont.Provider`](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/googlefonts/GoogleFont.Provider?hl=zh-cn)：

   provider 收到的参数如下：

   - 针对 Google Fonts 的字体提供程序授权。
   - 用于验证 provider 身份的字体提供程序软件包。
   - 用于验证 provider 身份的一系列证书哈希集。

   ```kotlin
   val provider = GoogleFont.Provider(
   	providerAuthority = "com.google.android.gms.fonts",
   	providerPackage = "com.google.android.gms",
   	certificates = R.array.com_google_android_gms_fonts_certs
   )
   ```

3. 定义 `FontFamily`：

   ```kotlin
   val fontFamily = FontFamily(
       Font(
           googleFont = fontName,
           fontProvider = provider,
           weight = FontWeight.Bold,
           style = FontStyle.Italic
       )
   )
   ```

4. 配置要在 Text 可组合函数中使用的 `FontFamily`：

   ```kotlin
   Text(
       fontFamily = fontFamily, text = "Hello World!"
   )
   ```

