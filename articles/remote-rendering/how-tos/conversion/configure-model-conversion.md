---
title: 配置模型转换
description: 所有模型转换参数的说明
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681566"
---
# <a name="configure-the-model-conversion"></a>配置模型转换

本章记录模型转换的选项。

## <a name="settings-file"></a>设置文件

如果在输入模型旁边的`ConversionSettings.json`输入容器中找到调用的文件，则用于为模型转换过程提供其他配置。

文件的内容应满足以下 json 架构：

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

示例`ConversionSettings.json`文件可能是：

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>几何参数

* `scaling`- 此参数均匀缩放模型。 缩放可用于增大或收缩模型，例如，在桌面上显示建筑模型。 由于渲染引擎期望以米为单位指定长度，因此当以不同单位定义模型时，将出现此参数的另一个重要用途。 例如，如果以厘米为单位定义模型，则应用 0.01 的比例应以正确的大小呈现模型。
某些源数据格式（例如 .fbx）提供单位缩放提示，在这种情况下，转换隐式将模型缩放到仪表单位。 源格式提供的隐式缩放将应用于缩放参数之上。
最终缩放因子应用于场景图形节点的几何顶点和局部变换。 根实体的转换的缩放保持未修改。

* `recenterToOrigin`- 指出应转换模型，以便其边界框以原点为中心。
如果源模型远离原点，则居中非常重要，因为在这种情况下，浮点精度问题可能会导致渲染伪影。

* `opaqueMaterialDefaultSidedness`- 渲染引擎假定不透明材质是双面材质。
如果这不是预期的行为，则应将此参数设置为"单面"。 有关详细信息，请参阅[单面渲染](../../overview/features/single-sided-rendering.md)。

### <a name="material-overrides"></a>材料覆盖

* `material-override`- 此参数允许在[转换过程中定制](override-materials.md)材料的处理。

### <a name="color-space-parameters"></a>颜色空间参数

渲染引擎期望颜色值在线性空间中。
如果使用 Gamma 空间定义模型，则应将这些选项设置为 true。

* `gammaToLinearMaterial`- 将材料颜色从伽马空间转换为线性空间
* `gammaToLinearVertex`- 将顶点颜色从伽马空间转换为线性空间

> [!NOTE]
> 对于 FBX 文件，默认情况下这些`true`设置设置为。 对于所有其他文件类型，默认值为`false`。

### <a name="scene-parameters"></a>场景参数

* `sceneGraphMode`- 定义源文件中的场景图的转换方式：
  * `dynamic`（默认）：文件中的所有对象都作为 API 中的[实体](../../concepts/entities.md)公开，可以独立转换。 运行时的节点层次结构与源文件中的结构相同。
  * `static`：所有对象都在 API 中公开，但不能独立转换。
  * `none`：场景图折叠成一个对象。

每个模式具有不同的运行时性能。 在`dynamic`模式下，即使未移动零件，性能成本也会与图形中的[实体](../../concepts/entities.md)数线性缩放。 仅当应用程序需要单独移动零件时，才应使用它，例如对于"爆炸视图"动画。

模式`static`导出完整场景图，但此图形中的零件相对于其根部分具有恒定变换。 但是，仍可以移动、旋转或缩放对象的根节点，无需显著的性能成本。 此外，[空间查询](../../overview/features/spatial-queries.md)将返回单个部分，每个部分可以通过[状态重写](../../overview/features/override-hierarchical-state.md)进行修改。 使用此模式，每个对象的运行时开销可以忽略不计。 它非常适合需要每个对象检查但无需每个对象转换更改的大型场景。

该`none`模式具有最少的运行时开销，并且加载时间稍好。 在此模式下，无法检查或转换单个对象。 例如，用例是照片测量模型，它们最初没有有意义的场景图。

> [!TIP]
> 许多应用程序将加载多个模型。 您应该根据所使用的方式优化每个模型的转换参数。 例如，如果要显示汽车模型，以便用户分崩离析并进行详细检查，则需要使用`dynamic`模式进行转换。 但是，如果您还希望将汽车放置在展示室环境中，则可以将该车型转换为`sceneGraphMode``static`设置为 或 甚至`none`。

### <a name="physics-parameters"></a>物理参数

* `generateCollisionMesh`- 如果需要支持模型上[的空间查询](../../overview/features/spatial-queries.md)，必须启用此选项。 在最坏的情况下，创建冲突网格可以使转换时间加倍。 具有冲突同流器的模型加载时间较长，使用`dynamic`场景图时，它们的运行时性能开销也较高。 为了获得总体最佳性能，您应该在所有不需要空间查询的模型上禁用此选项。

### <a name="unlit-materials"></a>未点燃的材料

* `unlitMaterials`- 默认情况下，转换将倾向于创建[PBR 材料](../../overview/features/pbr-materials.md)。 此选项告诉转换器将所有材料视为[颜色材料](../../overview/features/color-materials.md)。 如果您的数据已包含照明，例如通过摄影测量创建的模型，此选项允许您快速对所有材质强制实施正确的转换，而无需单独[覆盖每个材质](override-materials.md)。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>使用 Phong 材料模型从较旧的 FBX 格式进行转换

* `fbxAssumeMetallic`- 较旧版本的 FBX 格式使用 Phong 材料模型定义其材料。 转换过程必须推断这些材料如何映射到渲染器的[PBR模型](../../overview/features/pbr-materials.md)。 通常这工作得很好，但当材质没有纹理、高镜面值和非灰色反光度颜色时，可能会出现歧义。 在这种情况下，转换必须在确定高镜面值的优先级、定义高反射性金属材料（使反光度颜色溶解的地方）或确定反光度颜色的优先级、定义像闪亮的彩色塑料一样的东西之间做出选择。 默认情况下，转换过程假定高镜面值意味着在模棱两可的情况下的金属材料。 可以将其参数设置为`false`切换到相反。

### <a name="coordinate-system-overriding"></a>坐标系重写

* `axis`- 覆盖坐标系单位矢量。 默认值为`["+x", "+y", "+z"]`。 理论上，FBX 格式具有一个标头，其中定义了这些矢量，转换使用该信息来转换场景。 glTF 格式还定义了固定坐标系。 实际上，某些资产的标头中的信息不正确，或者使用不同的坐标系约定保存。 此选项允许您覆盖坐标系以进行补偿。 例如：`"axis" : ["+x", "+z", "-y"]`通过反转 Y 轴方向来交换 Z 轴和 Y 轴并保持坐标系的传递。

### <a name="vertex-format"></a>顶点格式

可以调整网格的顶点格式，以交换精度以节省内存。 较低的内存占用空间使您能够加载更大的模型或实现更好的性能。 但是，根据数据的不同，错误的格式可能会显著影响渲染质量。

> [!CAUTION]
> 当模型不再适合内存时，或者优化以获得最佳性能时，更改顶点格式应该是最后的手段。 更改可以轻松地引入渲染伪影，无论是明显的，也可以是细微的。 除非您知道要查找什么，否则不应更改默认值。

这些调整是可能的：

* 可以显式包含或排除特定数据流。
* 可以降低数据流的准确性，以减少内存占用。

文件中的`vertex`以下部分是可选`.json`的。 对于未显式指定的每个部分，转换服务将回退到其默认设置。

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

通过强制组件 到`NONE`，可以保证输出网格没有相应的流。

#### <a name="component-formats-per-vertex-stream"></a>每个顶点流的组件格式

这些格式允许用于相应的组件：

| 顶点组件 | 支持的格式（粗体 = 默认值） |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**， 16_16_16_16_FLOAT |
|颜色0| **8_8_8_8_UNSIGNED_NORMALIZED**， 无 |
|颜色1| 8_8_8_8_UNSIGNED_NORMALIZED，**无**|
|一般| **8_8_8_8_SIGNED_NORMALIZED**， 16_16_16_16_FLOAT， 无 |
|正切值| **8_8_8_8_SIGNED_NORMALIZED**， 16_16_16_16_FLOAT， 无 |
|双正态| **8_8_8_8_SIGNED_NORMALIZED**， 16_16_16_16_FLOAT， 无 |
|特克斯科德0| **32_32_FLOAT**， 16_16_FLOAT， 无 |
|特克斯科德1| **32_32_FLOAT**， 16_16_FLOAT， 无 |

#### <a name="supported-component-formats"></a>支持的组件格式

格式的内存占用空间如下：

| 格式 | 说明 | 每个顶点的字节数 |
|:-------|:------------|:---------------|
|32_32_FLOAT|双组分全浮点精度|8
|16_16_FLOAT|双组分半浮点精度|4
|32_32_32_FLOAT|三个组件全浮点精度|12
|16_16_16_16_FLOAT|四组半浮点精度|8
|8_8_8_8_UNSIGNED_NORMALIZED|四个分量字节，规范化为`[0; 1]`范围|4
|8_8_8_8_SIGNED_NORMALIZED|四个分量字节，规范化为`[-1; 1]`范围|4

#### <a name="best-practices-for-component-format-changes"></a>组件格式更改的最佳做法

* `position`：精度降低就足够了，这是罕见的。 **16_16_16_16_FLOAT**引入了明显的量化伪影，即使是对于小型模型也是如此。
* `normal`， `tangent` `binormal`， ： 通常这些值一起更改。 除非有来自正常量化产生的明显的照明伪影，否则没有理由提高其精度。 但是，在某些情况下，这些组件可以设置为**NONE：**
  * `normal`，`tangent``binormal`并且仅在模型中至少应点亮一个材质时才需要。 在 ARR 中，当模型上随时使用[PBR 材料](../../overview/features/pbr-materials.md)时，就是这种情况。
  * `tangent``binormal`并且仅在任何照明材质使用法线贴图纹理时才需要。
* `texcoord0`：`texcoord1`纹理坐标在值保持在`[0; 1]`范围内且寻址纹理的最大大小为 2048 x 2048 像素时，可以使用较低的精度 **（16_16_FLOAT**）。 如果超过这些限制，纹理映射的质量将受到影响。

#### <a name="example"></a>示例

假设您有一个摄影测量模型，该模型将照明烘焙到纹理中。 渲染模型所需的全部是顶点位置和纹理坐标。

默认情况下，转换器必须假定您可能希望在某个时间在模型上使用 PBR 材质，以便它将为您生成`normal`、`tangent`和`binormal`数据。 因此，每个顶点内存的用量`position`是 （12 字节`texcoord0`） = （8`normal`字节） = `tangent` （4 字节） `binormal` = （4 字节） = （4 字节） = 32 字节。 这种类型的较大模型可以很容易地具有数百万顶点，从而产生可能占用多个千兆字节内存的模型。 如此大量的数据将影响性能，您甚至可能耗尽内存。

知道您永远不需要模型上的动态照明，`[0; 1]`并且知道所有纹理坐标都在范围内，您可以设置`normal`、`tangent`和`binormal`到`NONE`和`texcoord0`到 到 一半`16_16_FLOAT`精度 （），因此每个顶点仅产生 16 个字节。 将网格数据减半使您能够加载更大的模型，并可能提高性能。

## <a name="typical-use-cases"></a>典型用例

为给定用例查找良好的导入设置可能是一个繁琐的过程。 另一方面，转换设置可能对运行时性能产生重大影响。

某些类别的用例符合特定的优化条件。 下面给出了一些示例。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>用例：建筑可视化/大型户外地图

* 这些类型的场景往往是静态的，这意味着它们不需要可移动的部分。 因此，`sceneGraphMode`可以设置为`static`或，`none`这提高了运行时性能。 使用`static`模式时，仍可以移动、旋转和缩放场景的根节点，例如，在 1：1 比例（对于第一人称视图）和桌面视图之间动态切换。

* 当您需要移动零件时，这通常也意味着您需要支持光线转换或其他[空间查询](../../overview/features/spatial-queries.md)，以便可以首先选取这些零件。 另一方面，如果您不打算移动某些内容，则很可能也不需要它来参与空间查询，因此可以关闭`generateCollisionMesh`标志。 此交换机对转换时间、加载时间和每帧更新成本的运行时也产生重大影响。

* 如果应用程序不使用[切割平面](../../overview/features/cut-planes.md)，`opaqueMaterialDefaultSidedness`应关闭标志。 性能提升通常为 20%-30%。 切割平面仍可以使用，但在查看对象的内部部分时不会有背面，这看起来有悖常理。 有关详细信息，请参阅[单面渲染](../../overview/features/single-sided-rendering.md)。

### <a name="use-case-photogrammetry-models"></a>用例：摄影测量模型

渲染摄影测量模型时，通常不需要场景图，因此您可以将`sceneGraphMode`设置为`none`。 由于这些模型很少包含复杂的场景图，因此此选项的影响应该微不足道。

由于照明已烘焙到纹理中，因此无需动态照明。 因此：

* 将`unlitMaterials`标志设置为`true`将所有材料转换为未亮[的颜色材料](../../overview/features/color-materials.md)。
* 从顶点格式中删除不需要的数据。 请参阅上面[的示例](#example)。

### <a name="use-case-visualization-of-compact-machines-etc"></a>用例：紧凑型机器的可视化等。

在这些用例中，模型在小卷中通常具有非常高的细节。 渲染器经过大量优化，可很好地处理此类情况。 但是，上一个用例中提到的大多数优化都不适用于此处：

* 单个零件应可选择且可移动，因此`sceneGraphMode`必须留给 。 `dynamic`
* 光线强制转换通常是应用程序的组成部分，因此必须生成冲突套。
* 启用了标志后，`opaqueMaterialDefaultSidedness`切割平面看起来更好。

## <a name="next-steps"></a>后续步骤

* [模型转换](model-conversion.md)
* [颜色材料](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)
* [模型转换期间覆盖材料](override-materials.md)
