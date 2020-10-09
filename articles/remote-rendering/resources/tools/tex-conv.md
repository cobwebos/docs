---
title: TexConv-纹理转换工具
description: TexConv 命令行工具的用户手册
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680019"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv-纹理转换工具

TexConv 是一个命令行工具，用于将纹理从 PNG、TGA、JPEG 和 DDS 等典型输入格式处理为适用于运行时消耗的优化格式。
尽管最常见的情况是将单个输入文件转换 `A.xxx` 为优化格式，但 `B.yyy` 该工具还有许多其他选项可供高级使用。

## <a name="command-line-help"></a>命令行帮助

使用参数运行 TexConv.exe `--help` 将列出所有可用选项。 此外，TexConv 将在执行时打印所使用的选项，以帮助了解其功能。 有关详细信息，请参阅此输出。

## <a name="general-usage"></a>常规用法

TexConv 始终 **只生成一个输出** 文件。 它可以使用 **多个输入** 文件来组合的输出。 对于程序集，它还需要一个 **通道映射**，该映射会告诉它哪个通道 (*红、绿、蓝* 或 *Alpha*) 从哪个输入文件中执行，并将其移到输出图像的哪个通道。

最直接的命令行如下所示：

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` 指定输出文件和格式
- `-in0` 指定第一个输入图像
- `-rgba` 指示输出图像应使用所有四个通道，并且应从输入图像使用1:1

## <a name="multiple-input-files"></a>多个输入文件

若要组合多个输入文件的输出，请使用带有递增数字的选项指定每个输入文件 `-in` ：

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

组合2d 纹理中的立方体贴图时，还可以使用 `-right` 、 `-left` 、、 `-top` `-bottom` 、 `-front` 、或 `-back` 、、、、 `-px` `-nx` `-py` `-ny` `-pz` 和 `-nz` 。

若要将这些输入映射到输出文件，需要正确的通道映射。

## <a name="channel-mappings"></a>通道映射

通道映射选项指定用于填充给定输出通道的输入。 您可以分别为每个通道指定输入，如下所示：

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

此处，输出的 RGB 通道使用第一个输入图像进行填充，但会交换红色和蓝色。 输出的 alpha 通道将用第二个输入图像的红色通道中的值填充。

为每个通道单独指定映射可提供最大的灵活性。 为方便起见，可以使用 "swizzling" 运算符编写相同的操作：

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>输出通道

可用的通道映射选项如下：

- `-r`、 `-g` 、 `-b` 、 `-a` ：这些指定单个通道分配
- `-rg` ：指定红色和绿色通道分配。
- `-rgb` ：指定红色、绿色和蓝色通道分配。
- `-rgba` ：指定所有四个通道分配。

仅提及 R、RG 或 RGB 通道，指示 TexConv 创建仅具有1个、2个或3个通道的输出文件。

### <a name="input-swizzling"></a>输入 swizzling

当说明哪些输入纹理应填充哪个输出通道时，可以 swizzle 输入：

- `-rgba in0` 等效于 `-rgba in0.rgba`
- `-rgba in0.bgra` 将 swizzle 输入通道
- `-rgb in0.rrr` 会将红色通道复制到所有通道中

还可以用黑色或白色填充频道：

- `-rgb in0 -a white` 将创建一个4通道输出纹理，但将 alpha 设置为完全不透明
- `-rg black -b white` 将创建一个完全蓝色的纹理

## <a name="common-options"></a>常用选项

最有趣的选项如下所示。 列出了更多选项 `TexConv --help` 。

### <a name="output-type"></a>输出类型

- `-type 2D` ：输出将为常规二维图像。
- `-type Cubemap` ：输出将为立方体贴图图像。 仅限 DDS 输出文件支持。 如果指定此项，则可将立方体贴图从6个常规2D 输入图像组合在一起。

### <a name="image-compression"></a>图像压缩

- `-compression none` ：输出图像将未压缩。
- `-compression medium` ：如果支持，输出图像将使用压缩，而不会牺牲太多质量。
- `-compression high` ：如果支持，输出图像将使用压缩和牺牲质量来取代较小的文件。

### <a name="mipmaps"></a>Mipmap

默认情况下，当输出格式支持 TexConv 时，它将生成 mipmap。

- `-mipmaps none` ：将不生成 mipmap。
- `-mipmaps Linear` ：如果支持，将使用 box 筛选器生成 mipmap。

### <a name="usage-srgb--gamma-correction"></a>SRGB/伽玛更正) 使用 (

`-usage`选项指定输出的用途，并告诉 TexConv 是否将伽玛更正应用于输入和输出文件。 用法仅影响 RGB 通道。 Alpha 通道始终被视为包含 "线性" 值。 如果未指定 usage，则 "自动" 模式将尝试检测第一个输入图像的格式和文件名的使用情况。 例如，单通道和双通道输出格式始终是线性的。 检查输出，查看 TexConv 做出的决策。

- `-usage Linear` ：输出图像包含不表示颜色的值。 这通常是金属和粗糙度纹理以及所有类型的蒙版的情况。

- `-usage Color` ：输出图像表示颜色，如漫射/albedo 地图。 将在输出 DDS 标头中设置 sRGB 标志。

- `-usage HDR` ：输出文件应使用超过8位/像素进行编码。 因此，所有值都以线性空间存储。 对于 HDR 纹理，数据是否表示颜色或其他数据并不重要。

- `-usage NormalMap` ：输出图像表示一个相切空间法向图。 将规范化值，并稍微优化 mipmap 计算。

- `-usage NormalMap_Inverted` ：输出为一个相切空间法线地图，其中 Y 指向与输入相反的方向。

### <a name="image-rescaling"></a>图像重新缩放

- `-minRes 64` ：指定输出的最小分辨率。 如果输入图像较小，将获取升级。
- `-maxRes 1024` ：指定输出的最大分辨率。 如果输入图像较大，则会 downscaled。
- `-downscale 1` ：如果大于0，则输入图像将在分辨率 N 次中减半。 使用此应用程序可降低总体质量。

## <a name="examples"></a>示例

### <a name="convert-a-color-texture"></a>转换颜色纹理

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>转换普通地图

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>创建 HDR 立方体贴图

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

HDR cubemaps 的一个绝佳来源是 [hdrihaven.com](https://hdrihaven.com/hdris/)。

### <a name="bake-multiple-images-into-one"></a>制作多个图像

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>提取单个通道

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
