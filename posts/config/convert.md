　　convert是imagemagick包里的一个工具，可以对图片进行简单处理。

<br>

　　截取图片：

```shell
convert input -crop wxh+dx+dy output
```

其中w和h是output的宽和高，dx和dy是output左上角相对于input左上角的偏移。

<br>

　　横向拼接图片(两张图片的高度必须相同)：

```shell
convert 1.jpg 2.jpg +append output.jpg
```

<br>

　　纵向拼接图片(两张图片的宽度必须相同)：

```shell
convert 1.jpg 2.jpg -append output.jpg
```

<br>

　　横向＋纵向：

```shell
convert \( 1.jpg 2.jpg 3.jpg +append \) \( 4.jpg 5.jpg 6.jpg +append \) \( 7.jpg 8.jpg 9.jpg +append \) -append output.jpg
```

　　更多用法见[官网手册](https://www.imagemagick.org/Usage)

