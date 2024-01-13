在有默认值的情况下，推荐手动传递 `Modifier` 实参。原因在于可组合项可能会进行**重组**（Recomposition），这实质上意味着 `@Composable` 方法中的代码块会再次执行。如果在代码块中创建了 `Modifier` 对象，系统可能会重新创建该对象，并且这种方式效率不高。

- `Modifier.fillMaxSize()` 方法指定组件应填满可用空间。
- [`Modifier.wrapContentSize()`](https://developer.android.com/reference/kotlin/androidx/compose/ui/Modifier?hl=zh-cn#(androidx.compose.ui.Modifier).wrapContentSize(androidx.compose.ui.Alignment,kotlin.Boolean)) 方法会指定可用空间应至少与其内部组件一样大。

在使用了 `fillMaxSize()` 方法的情况下，如果布局内的组件小于可用空间，则可以向 `wrapContentSize()` 方法传入 [`Alignment`](https://developer.android.com/reference/kotlin/androidx/compose/ui/Alignment?hl=zh-cn) 对象，以指定组件应如何在可用空间内对齐。

`Alignment.Center` 会指定组件同时在水平和垂直方向上居中。



通过 `Spacer` Composable 可以解决两个元素之间的间距不足的问题。

`Spacer` 接受 `Modifier` 作为形参。可以设置 `Modifier` 的高度以应用于 `Spacer`。

通常，dp 尺寸以 `4.dp` 为增量进行更改。

```kotlin
Spacer(modifier = Modifier.height(16.dp))
```



生成指定范围内的随机 Int：

```kotlin
result = (1..6).random()
```



默认情况下，**可组合项是无状态的，这意味着它们不存储值，并且可随时被系统重组，从而导致值被重置**。

不过，Compose 提供了一种避免这种情况的便捷方式。可组合函数可以**使用 `remember` 可组合项将对象存储在内存中。**

```kotlin
var result by remember { mutableStateOf(1) }
```

`remember` 可组合项需要传递函数。

`mutableStateOf()` 函数会返回一个**可观察对象**。这意味着，**当 `result` 变量的值变化时，系统会触发重组、反映结果值并刷新界面。**



Image 固定比例：

```kotlin
Image(
    painter = painterResource(id = imageId),
    contentDescription = null,
    modifier = Modifier
        .clip(MaterialTheme.shapes.medium)
        .aspectRatio(12f / 16f)
)
```

