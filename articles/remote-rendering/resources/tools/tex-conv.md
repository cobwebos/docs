---
title: TexConv - 纹理转换工具
description: TexConv 命令行工具的用户手册
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680019"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - 纹理转换工具

TexConv 是一种命令行工具，用于将 PNG、TGA、JPEG 和 DDS 等典型输入格式的纹理处理为优化的格式，以便进行运行时消耗。
虽然最常见的方案是将单个输入文件`A.xxx`转换为优化的格式`B.yyy`，但该工具有许多其他选项可供高级使用。

## <a name="command-line-help"></a>命令行帮助

使用`--help`参数运行 TexConv.exe 将列出所有可用的选项。 此外，TexConv 在执行时打印使用的选项，以帮助了解它在做什么。 有关详细信息，请参阅此输出。

## <a name="general-usage"></a>一般用法

TexConv 始终**只生成一个输出**文件。 它可以使用**多个输入**文件来组装输出。 对于程序集，它还需要一个**通道映射**，它告诉它从哪个通道（*红色、绿色、蓝色*或*Alpha）* 从哪个通道获取输入文件，并将其移动到输出图像的哪个通道中。

最直接的命令行是：

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`指定输出文件和格式
- `-in0`指定第一个输入图像
- `-rgba`告诉输出图像应使用所有四个通道，并且它们应从输入图像中取 1：1

## <a name="multiple-input-files"></a>多个输入文件

要从多个输入文件组装输出，请使用具有增加数量`-in`的选项指定每个输入文件：

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

When assembling a cubemap from 2D textures, one can also use `-right`, `-left`, `-top`, `-bottom`, `-front`, `-back` or `-px`, `-nx`, `-py`, `-ny`, `-pz`, `-nz`.

要将这些输入映射到输出文件，需要适当的信道映射。

## <a name="channel-mappings"></a>通道映射

通道映射选项指定从哪个输入填充给定的输出通道。 您可以单独指定每个通道的输入，如下所示：

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

在这里，输出的RGB通道将使用第一个输入图像填充，但红色和蓝色将被交换。 输出的 Alpha 通道将填充来自第二个输入图像的红色通道的值。

分别指定每个通道的映射提供了最大的灵活性。 为方便起见，可以使用"旋转"运算符编写相同的文章：

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>输出通道

提供以下通道映射选项：

- `-r``-g`，、、、、、`-b``-a`这些指定单通道分配
- `-rg`：指定红色和绿色通道分配。
- `-rgb`：指定红色、绿色和蓝色通道分配。
- `-rgba`：指定所有四个通道分配。

仅提及 R、RG 或 RGB 通道，指示 TexConv 分别创建仅具有 1 个、2 个或 3 个通道的输出文件。

### <a name="input-swizzling"></a>输入旋转

当说明哪个输入纹理应填充哪个输出通道时，可以旋转输入：

- `-rgba in0` 等效于 `-rgba in0.rgba`
- `-rgba in0.bgra`会旋转输入通道
- `-rgb in0.rrr`将红色通道复制到所有通道

也可以用黑色或白色填充通道：

- `-rgb in0 -a white`将创建 4 个通道输出纹理，但将 alpha 设置为完全不透明
- `-rg black -b white`将创建一个完全蓝色的纹理

## <a name="common-options"></a>常用选项

下面列出了最有趣的选项。 更多选项由`TexConv --help`列出。

### <a name="output-type"></a>输出类型

- `-type 2D`：输出将是常规的 2D 图像。
- `-type Cubemap`： 输出将是立方体贴图图像。 仅适用于 DDS 输出文件。 指定此选项后，可以从 6 个常规 2D 输入图像中组装立方体贴图。

### <a name="image-compression"></a>图像压缩

- `-compression none`：输出图像将解压缩。
- `-compression medium`：如果支持，输出映像将使用压缩而不牺牲太多质量。
- `-compression high`：如果支持，输出映像将使用压缩和牺牲质量来选择较小的文件。

### <a name="mipmaps"></a>米普映射

默认情况下，当输出格式支持时，TexConv 会生成 mipmap。

- `-mipmaps none`：不会生成 Mipmap。
- `-mipmaps Linear`： 如果支持，将使用框筛选器生成 mipmap。

### <a name="usage-srgb--gamma-correction"></a>用法（sRGB / 伽玛校正）

该`-usage`选项指定输出的用途，从而告诉 TexConv 是否对输入和输出文件应用伽马校正。 使用率仅影响 RGB 通道。 Alpha 通道始终被视为包含"线性"值。 如果未指定用法，"自动"模式将尝试从第一个输入图像的格式和文件名中检测使用情况。 例如，单通道和双通道输出格式始终为线性格式。 检查输出以查看 TexConv 做出什么决定。

- `-usage Linear`： 输出图像包含不表示颜色的值。 金属和粗糙纹理以及各种蒙版通常都是这种情况。

- `-usage Color`： 输出图像表示颜色，如漫反射/阿尔贝多贴图。 sRGB 标志将在输出 DDS 标头中设置。

- `-usage HDR`：输出文件应使用超过 8 位/像素进行编码。 因此，所有值都存储在线性空间中。 对于 HDR 纹理，数据是表示颜色还是其他数据并不重要。

- `-usage NormalMap`：输出图像表示切线空间法线贴图。 值将规范化，mipmap 计算将稍加优化。

- `-usage NormalMap_Inverted`：输出是切面法线贴图，Y 指向与输入相反的方向。

### <a name="image-rescaling"></a>图像重新缩放

- `-minRes 64`：指定输出的最小分辨率。 如果输入图像较小，它将升级。
- `-maxRes 1024`：指定输出的最大分辨率。 如果输入图像较大，它将缩小缩放。
- `-downscale 1`：如果大于 0，则输入图像将在 N 倍的分辨率下减半。 使用此选项可应用整体质量降低。

## <a name="examples"></a>示例

### <a name="convert-a-color-texture"></a>转换颜色纹理

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>转换法线地图

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>创建 HDR 多维数据集图

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

HDR 立方体映射的一个很好的来源[是hdrihaven.com。](https://hdrihaven.com/hdris/)

### <a name="bake-multiple-images-into-one"></a>将多个图像烘焙为一个

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>提取单个通道

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
