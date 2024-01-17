## 01 添加可滚动列表

### 创建列表项数据类

```kotlin
import androidx.annotation.DrawableRes
import androidx.annotation.StringRes

data class Affirmation(
    @StringRes val stringResourceId: Int,
    @DrawableRes val imageResourceId: Int
)
```

DataSource 类用于提供数据。

```kotlin
// com.example.affirmations.data.Datasource.kt
import com.example.affirmations.R
import com.example.affirmations.model.Affirmation

class Datasource() {
    fun loadAffirmations(): List<Affirmation> {
        return listOf<Affirmation>(
            Affirmation(R.string.affirmation1, R.drawable.image1),
            Affirmation(R.string.affirmation2, R.drawable.image2),
            Affirmation(R.string.affirmation3, R.drawable.image3),
            Affirmation(R.string.affirmation4, R.drawable.image4),
            Affirmation(R.string.affirmation5, R.drawable.image5),
            Affirmation(R.string.affirmation6, R.drawable.image6),
            Affirmation(R.string.affirmation7, R.drawable.image7),
            Affirmation(R.string.affirmation8, R.drawable.image8),
            Affirmation(R.string.affirmation9, R.drawable.image9),
            Affirmation(R.string.affirmation10, R.drawable.image10))
    }
}
```

### 创建列表项卡片

```kotlin
import androidx.compose.material3.Text
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.platform.LocalContext

@Composable
fun AffirmationCard(affirmation: Affirmation, modifier: Modifier = Modifier) {
    Card(modifier = modifier) {
        Column {
            Image(
                painter = painterResource(affirmation.imageResourceId),
                contentDescription = stringResource(affirmation.stringResourceId),
                modifier = Modifier
                    .fillMaxWidth()
                    .height(194.dp),
                contentScale = ContentScale.Crop 
              // `contentScale` 用于决定图片应如何缩放和显示。
            )
            Text(
                text = LocalContext.current.getString(affirmation.stringResourceId),
                modifier = Modifier.padding(16.dp),
                style = MaterialTheme.typography.headlineSmall
              // `MaterialTheme.typography.headlineSmall` 用于设置文本主题。
            )
        }
    }
}
```

- `contentScale` 用于决定图片应如何缩放和显示。
- `MaterialTheme.typography.headlineSmall` 用于设置文本主题。

### 创建列表

在 Jetpack Compose 中，可以使用 **`LazyColumn` 可组合项创建==可滚动列表==**。

`LazyColumn` 和 `Column` 的区别在于：

- 懒加载：当要显示的项数量较少时，应使用 `Column`，因为 Compose 会一次性加载所有项。
- 是否可以按需添加内容中的可组合项：
  - `Column` 只能保存**预定义或固定数量**的可组合项。
  - `LazyColumn` 可以按需添加内容，因此非常适合较长的列表，尤其是当列表长度未知时。
- 滚动：`LazyColumn` 提供默认滚动行为，无需添加其他代码。

```kotlin
@Composable
fun AffirmationList(affirmationList: List<Affirmation>, modifier: Modifier = Modifier) {
    LazyColumn(modifier = modifier) {
        items(affirmationList) { affirmation ->
            AffirmationCard(
                affirmation = affirmation,
                modifier = Modifier.padding(8.dp)
            )
        }
    }
}
```

- 在 `LazyColumn` 的 lambda 正文中，调用 `items()` 方法并传入 `affirmationList`。**`items()` 方法用于向 `LazyColumn` 添加项。**
- 调用 `items()` 方法需要 lambda 函数。在该函数中，指定一个 `affirmation` 形参，用于表示 `affirmationList` 中的一个项。

### 显示列表

```kotlin
import com.example.affirmations.data.Datasource

@Composable
fun AffirmationsApp() {
    AffirmationList(
        affirmationList = Datasource().loadAffirmations(),
    )
}
```



## 02 更改应用图标

### 启动器图标

应用图标会出现在多个位置，包括<u>主屏幕、“所有应用”界面和“设置”应用</u>。

应用图标也可能会被称为**启动器**图标。启动器是指当您点按 Android 设备上的主屏幕按钮时所看到的界面，您可以在此界面中查看和整理应用、添加 widget 和快捷方式，以及执行其他多种操作。

不同制造商可能会以不同的形状显示应用图标。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-training-change-app-icon/img/f9a038f44a88d8.png?hl=zh-cn" alt="一张图片，其中展示了各种形状的应用图标" style="zoom:50%;" />。

**设置启动图标的目标**：无论设备型号或屏幕密度如何，您的启动器图标都会呈现清晰简洁的效果。

- 屏幕密度是指屏幕上每英寸的像素数或每英寸的点数 (dpi)。

  - 对于中密度设备 (mdpi)，屏幕上每英寸有 160 个点。

  - 对于超超超高密度设备 (xxxhdpi) 的屏幕上每英寸有 640 个点。

- 为适应不同屏幕密度的设备，您需要提供不同版本的应用图标。



**启动图标资源的位置**：启动器图标文件应放在 `app/src/main/res` 下的 `mipmap` 文件夹中。

<img src="02 .assets/image-20240114194249938.png" alt="image-20240114194249938" style="zoom: 80%;" />

`mdpi`、`hdpi`、`xhdpi` 等是密度限定符，您可以将其附加到资源目录的名称（例如 `mipmap`）上，以**表明其中的资源适用于特定屏幕密度的设备**。以下是 Android 上的[密度限定符](https://developer.android.com/training/multiscreen/screendensities?hl=zh-cn#TaskProvideAltBmp)列表：

- `mdpi` - 适用于中密度屏幕（约 160dpi）的资源
- `hdpi` - 适用于高密度屏幕（约 240dpi）的资源
- `xhdpi` - 适用于超高密度屏幕（约 320dpi）的资源
- `xxhdpi` - 适用于超超高密度屏幕（约 480dpi）的资源
- `xxxhdpi` - 适用于超超超高密度屏幕（约 640dpi）的资源
- `nodpi` - 不能缩放的资源（无论屏幕的像素密度是多少）
- `anydpi` - 可针对任何密度进行缩放的资源

> 为何启动器图标资源位于 `mipmap` 目录中，而其他应用资源位于 `drawable` 目录中。这是因为，某些启动器显示的应用图标尺寸可能会比设备默认密度级别提供的应用图标尺寸要大。例如，在 `hdpi` 设备上，某种设备启动器可能需要改用 `xhdpi` 版本的应用图标。这些目录包含的图标适用于相比默认密度需要更高或更低密度的图标的设备。

为了避免应用图标模糊不清，请务必针对每个密度级别（`mdpi`、`hdpi`、`xhdpi` 等）提供不同的图标位图图片。

请注意，设备屏幕密度不会刚好是 160 dpi、240 dpi、320 dpi 等。A**ndroid 会根据设备的屏幕密度，以最接近的较大密度级别选择资源，然后将其缩小。**

### 自适应图标

自 [Android 8.0 版本](https://developer.android.com/about/versions/oreo?hl=zh-cn)（API 级别 26）起，系统支持自适应图标，这种图标能够使应用图标更加灵活并呈现出有趣的视觉效果。

对于开发者来说，应用图标将由两个图层组成：**前景图层和背景图层。**

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-training-change-app-icon/img/1af36983e3677abe.gif?hl=zh-cn" alt="1af36983e3677abe.gif" style="zoom: 33%;" />

**添加自适应图标的是 API 级别为 26 的平台，因此自适应图标应该在具有 `-v26` 资源限定符的 `mipmap` 资源目录中声明**。

- 这个限定符意味着，此目录中的资源只会应用到搭载 API 26 (Android 8.0) 或更高版本的设备。
- 在搭载版本 25 或更低版本，且支持密度级别 mipmap 目录的设备上，系统会忽略此目录中的资源文件。

自适应图标的声明存放在 `mipmap-anydpi-v26` 目录下的 `ic_launcher.xml` 中：

```xml
<!-- ic_launcher.xml -->
<?xml version="1.0" encoding="utf-8"?>
<adaptive-icon xmlns:android="http://schemas.android.com/apk/res/android">
    <background android:drawable="@drawable/ic_launcher_background"/>
    <foreground android:drawable="@drawable/ic_launcher_foreground"/>
    <monochrome android:drawable="@drawable/ic_launcher_foreground" />
</adaptive-icon>
```

- `<adaptive-icon>` 通过为应用图标的 `<background>` 图层和 `<foreground>` 图层提供资源可绘制对象，完成对图标的声明。
- `drawable` 文件夹中以 XML 文件的形式，包含了启动器图标的前景和背景图形所对应的矢量。就可绘制图标而言，矢量是指一系列在编译时绘制图像的指令。

<img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-training-change-app-icon/img/4edd7a2c7c15f5c3.png?hl=zh-cn" alt="4edd7a2c7c15f5c3.png" style="zoom: 33%;" /><img src="https://developer.android.com/static/codelabs/basic-android-kotlin-compose-training-change-app-icon/img/7061ee6ad4cd4780.png?hl=zh-cn" alt="7061ee6ad4cd4780.png" style="zoom:33%;" />



**矢量图形与位图图形的区别：**

尽管矢量可绘制对象和位图图片描述的都是图片，但二者之间存在一些重要的区别。

- 除了各个像素处的颜色信息之外，位图图片对其存储的图片并不太了解。
- 矢量图形知道如何绘制用于定义图片的形状。**这些指令由一组点、线条和曲线以及相关颜色信息组成。**其优势在于，**矢量图形可针对任意屏幕密度的任意画布尺寸进行缩放，而不会损害图片质量。**

**使用自适应图标的好处：**

使用[矢量可绘制对象](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources?hl=zh-cn)只需要定义一次图片，而不必针对所有密度级别提供各种版本的位图资源。这样可以减少应用的大小，并使其更易于维护。

但不能删除所有的应用图标位图图片，仍需要这些文件，以便应用图标能够在旧版 Android 上呈现出较高的质量，这被称为**向后兼容性**。

- 在搭载 **Android 8.0 或更高版本**（API 26 及更高版本）的设备上，系统可以使用**自适应图标**（由前景矢量可绘制对象、背景矢量可绘制对象以及在其上应用的 OEM 蒙版组合而成）。以下是您项目中的相关文件：

  ```
  res/drawable/ic_launcher_background.xml
  res/drawable/ic_launcher_foreground.xml
  res/mipmap-anydpi-v26/ic_launcher.xml
  res/mipmap-anydpi-v26/ic_launcher_round.xml
  ```

- 在搭载 **Android 8.0 以下**的任意版本（但高于您的应用所需的最低 API 级别）的设备上，系统将使用**旧版启动器图标**（位于不同密度级别的 `mipmap` 文件夹中的位图图片）。以下是您项目中的相关文件：

  ```
  res/mipmap-mdpi/ic_launcher.webp
  res/mipmap-mdpi/ic_launcher_round.webp
  res/mipmap-hdpi/ic_launcher.webp
  res/mipmap-hdpi/ic_launcher_round.webp
  res/mipmap-xhdpi/ic_launcher.png
  res/mipmap-xhdpi/ic_launcher_round.webp
  res/mipmap-xxhdpi/ic_launcher.webp
  res/mipmap-xxhdpi/ic_launcher_round.webp
  res/mipmap-xxxhdpi/ic_launcher.webp
  res/mipmap-xxxhdpi/ic_launcher_round.webp
  ```



**对自适应图标的要求：**

系统对矢量图标的前景和背景图层资源有特定要求，例如它们的尺寸都必须为 108dp x 108dp。

**==安全区域==**是一个位于**前景图层中心且直径为 66dpi 的圆圈**。

- 由于图标的边缘可能会根据设备制造商提供的蒙版的形状进行剪裁，请务必将图标的关键信息放置在“[安全区域](https://medium.com/google-design/designing-adaptive-icons-515af294c783)”内。
- 该安全区域之外的内容不应是重要信息（例如背景颜色），即使被裁剪掉也没关系。

