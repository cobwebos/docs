---
title: 配置模型转换
description: 所有模型转换参数的说明
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681566"
---
# <a name="configure-the-model-conversion"></a>配置模型转换

本章介绍模型转换的选项。

## <a name="settings-file"></a>设置文件

如果在输入模型`ConversionSettings.json`旁边的输入容器中找到一个名为的文件，则该文件用于为模型转换过程提供其他配置。

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

### <a name="geometry-parameters"></a>Geometry 参数

* `scaling`-此参数统一缩放模型。 缩放可用于增大或缩小模型，例如，用于在表顶部显示构建模型。 由于呈现引擎需要以米为单位指定长度，因此在不同单位中定义模型时，会出现此参数的另一重要用途。 例如，如果模型是以厘米定义的，则应用比例0.01 应以正确的大小呈现模型。
一些源数据格式（例如 fbx）提供了一个单元缩放提示，在这种情况下，转换会将模型隐式缩放为计量单位。 将在缩放参数的顶部应用源格式提供的隐式缩放。
最终缩放因子应用于场景图形节点的几何顶点和局部转换。 根实体的转换的缩放仍保持不变。

* `recenterToOrigin`-指出应将模型转换为使其边界框在原点居中。
如果源模型从原点置换到远处，则中心非常重要，因为在这种情况下，浮点精度问题可能导致渲染项目。

* `opaqueMaterialDefaultSidedness`-呈现引擎假定不透明材料是双面的。
如果这不是预期的行为，则应将此参数设置为 "SingleSided"。 有关详细信息，请参阅单面[渲染](../../overview/features/single-sided-rendering.md)。

### <a name="material-overrides"></a>材料覆盖

* `material-override`-此参数允许在[转换过程中自定义](override-materials.md)材料的处理。

### <a name="color-space-parameters"></a>颜色空间参数

呈现引擎要求颜色值处于线性空间。
如果模型是使用伽玛空间定义的，则应将这些选项设置为 true。

* `gammaToLinearMaterial`-将材料颜色从伽玛空间转换为线性空间
* `gammaToLinearVertex`-将顶点颜色从伽玛空间转换为线性空间

> [!NOTE]
> 对于 FBX 文件，默认情况下这些`true`设置设置为。 对于所有其他文件类型，默认值为`false`。

### <a name="scene-parameters"></a>场景参数

* `sceneGraphMode`-定义如何转换源文件中的场景图：
  * `dynamic`（默认值）：文件中的所有对象都作为 API 中的[实体](../../concepts/entities.md)公开，并可单独转换。 运行时的节点层次结构与源文件中的结构相同。
  * `static`：所有对象都在 API 中公开，但无法独立进行转换。
  * `none`：场景图折叠为一个对象。

每个模式都具有不同的运行时性能。 在`dynamic`模式下，即使没有移动部件，性能开销也会随图中的[实体](../../concepts/entities.md)数线性缩放。 仅当为应用程序单独移动部件时才应使用此方法，例如，对于 "爆炸视图" 动画。

`static`模式将导出整个场景图，但此图形内的部件相对于其根部分具有恒定变换。 但是，仍可以移动、旋转或缩放对象的根节点，而不会产生显著的性能开销。 此外，[空间查询](../../overview/features/spatial-queries.md)将返回各个部分，并且可以通过[状态替代](../../overview/features/override-hierarchical-state.md)来修改每个部分。 在此模式下，每个对象的运行时开销都可忽略不计。 它非常适合于你仍需要按对象检查但不需要每个对象转换的大型场景。

此`none`模式具有最小的运行时开销，并且加载时间更少。 无法在此模式下检查或转换单个对象。 例如，用例是在第一个位置没有有意义的场景图的 photogrammetry 模型。

> [!TIP]
> 许多应用程序将加载多个模型。 应根据每个模型的使用方式优化转换参数。 例如，如果想要显示一辆汽车的模型，以使用户能够详细地进行查看和检查，则需要将其转换为`dynamic`模式。 但是，如果你还想要在显示房间环境中放置汽车，则可以将该模型转换为`sceneGraphMode` ，将`static`设置为`none`甚至。

### <a name="physics-parameters"></a>物理学参数

* `generateCollisionMesh`-如果需要对模型进行[空间查询](../../overview/features/spatial-queries.md)支持，则必须启用此选项。 在最糟糕的情况下，创建冲突网格可以翻倍转换时间。 具有冲突网格的模型需要更长时间才能加载， `dynamic`使用场景图时，它们也具有更高的运行时性能开销。 为了获得最佳性能，应在不需要空间查询的所有模型上禁用此选项。

### <a name="unlit-materials"></a>Unlit 材料

* `unlitMaterials`-默认情况下，转换将更倾向于创建[.pbr 材料](../../overview/features/pbr-materials.md)。 此选项告知转换器将所有材料视为[颜色材料](../../overview/features/color-materials.md)。 如果您的数据已经包含了光照（例如通过 photogrammetry 创建的模型），则此选项可让您快速强制对所有材料进行正确的转换，而无需单独[覆盖每个材料](override-materials.md)。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>使用冯氏材料模型从较旧的 FBX 格式转换

* `fbxAssumeMetallic`-较早版本的 FBX 格式使用冯氏材料模型定义其材料。 转换过程必须推断这些材料映射到呈现器的[.pbr 模型](../../overview/features/pbr-materials.md)的方式。 通常，这种方法很有效，但当材料没有纹理、高反射值和非灰色 albedo 颜色时，可能会出现不明确的情况。 在这种情况下，转换必须选择确定高反射值的优先级，定义高度反光的金属材料，其中 albedo 颜色溶解，或确定 albedo 颜色的优先级，定义类似于光亮的彩色塑料的颜色。 默认情况下，转换过程假设在应用歧义的情况下，高反射值是指金属材料。 此参数可设置为`false`以切换到相反的。

### <a name="coordinate-system-overriding"></a>坐标系统替代

* `axis`-重写坐标系统单位矢量。 默认值为`["+x", "+y", "+z"]`。 理论上，FBX 格式有一个标头，其中定义了这些矢量，转换使用该信息来转换场景。 GlTF 格式也定义固定坐标系统。 实际上，某些资产在其标头中包含不正确的信息，或者使用不同的坐标系统约定保存。 此选项允许您替代坐标系以进行补偿。 例如： `"axis" : ["+x", "+z", "-y"]`将交换 Z 轴和 y 轴，并通过反转 Y 轴方向来使坐标系统保持左右手使用习惯。

### <a name="vertex-format"></a>顶点格式

可以调整网格的顶点格式，以节省内存的交易精度。 使用较低的内存占用空间，可以加载更大的模型或获得更好的性能。 但是，根据您的数据，错误的格式可能会显著影响呈现质量。

> [!CAUTION]
> 更改顶点格式应该是最后一种手段，当模型不适合内存，或在优化以实现最佳性能时。 更改可以轻松引入呈现项目，包括明显的项目和微妙的项目。 除非您知道要查找的内容，否则不应更改默认值。

可以进行以下调整：

* 可以显式包括或排除特定数据流。
* 可以减少数据流的准确性以减少内存占用量。

`.json`文件中的以下`vertex`部分是可选的。 对于未显式指定的每个部分，转换服务将回退到其默认设置。

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

通过强制将组件设置`NONE`为，可以保证输出网格没有相应的流。

#### <a name="component-formats-per-vertex-stream"></a>每个顶点流的组件格式

允许对各自的组件使用以下格式：

| 顶点组件 | 支持的格式（粗体 = 默认值） |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**，16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**，无 |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED，**无**|
|一般| **8_8_8_8_SIGNED_NORMALIZED**，16_16_16_16_FLOAT，无 |
|正切值| **8_8_8_8_SIGNED_NORMALIZED**，16_16_16_16_FLOAT，无 |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**，16_16_16_16_FLOAT，无 |
|texcoord0| **32_32_FLOAT**，16_16_FLOAT，无 |
|texcoord1| **32_32_FLOAT**，16_16_FLOAT，无 |

#### <a name="supported-component-formats"></a>支持的组件格式

格式的内存痕迹如下：

| 格式 | 说明 | 每个顶点的字节数 |
|:-------|:------------|:---------------|
|32_32_FLOAT|双组件完整浮点精度|8
|16_16_FLOAT|双组件半浮点精度|4
|32_32_32_FLOAT|三组件完整浮点精度|12
|16_16_16_16_FLOAT|四组件半浮点精度|8
|8_8_8_8_UNSIGNED_NORMALIZED|由四个部分组成的字节`[0; 1]` ，规范化为范围|4
|8_8_8_8_SIGNED_NORMALIZED|由四个部分组成的字节`[-1; 1]` ，规范化为范围|4

#### <a name="best-practices-for-component-format-changes"></a>组件格式更改的最佳实践

* `position`：很少降低准确性。 **16_16_16_16_FLOAT**引入了明显的量化量化项目，即使对于小型模型也是如此。
* `normal`、 `tangent`、 `binormal`：通常将这些值一起更改。 除非存在由正常量化量化导致的明显光线项目，否则没有理由提高其准确度。 但在某些情况下，这些组件可以设置为 "**无**"：
  * `normal`仅`tangent`当模型`binormal`中至少有一个材料应为亮起时，才需要、和。 在 ARR 中，无论何时在模型上使用[.pbr 材料](../../overview/features/pbr-materials.md)，都是如此。
  * `tangent`仅`binormal`当任何 lit 材料使用普通地图纹理时，才需要和。
* `texcoord0`： `texcoord1`当纹理坐标的值保持在`[0; 1]`范围内，并且当已寻址纹理的最大大小为 2048 x 2048 像素时，纹理坐标可以使用降低的精度（**16_16_FLOAT**）。 如果超出了这些限制，纹理映射的质量就会降低。

#### <a name="example"></a>示例

假设有一个 photogrammetry 模型，该模型在纹理中包含照明融入。 呈现模型所需的全部都是顶点位置和纹理坐标。

默认情况下，转换器必须假设你可能想要在一段时间内对某个型号使用 .pbr 材料，因此它将`normal`为`tangent`你生成`binormal` 、和数据。 因此，每个顶点的内存使用量`position`为（12字节） `texcoord0` + （8字节） `normal` + （4字节） `tangent` + （4字节） `binormal` + （4字节） = 32 字节。 此类更大的模型可以轻松地获得数百万个顶点，导致模型可以占用多 gb 内存。 此类大量数据会影响性能，甚至可能会耗尽内存。

知道您在模型上从不需要动态照明，并且知道所有`[0; 1]`纹理坐标都在范围内，则可以将、 `normal` `tangent`和`binormal`设置为`NONE` `texcoord0`半精度（`16_16_FLOAT`），从而使每个顶点只有16个字节。 通过在半部分中剪切网格数据，可以加载更大的模型，并可能提高性能。

## <a name="typical-use-cases"></a>典型用例

查找给定用例的正确导入设置可能是一个枯燥的过程。 另一方面，转换设置可能会对运行时性能产生严重影响。

在某些情况下，有资格使用特定的优化。 下面给出了一些示例。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>用例：结构可视化/大型户外地图

* 这种类型的场景通常是静态的，这意味着它们不需要可移动部件。 因此， `sceneGraphMode`可以将设置为或`static`甚至`none`，以提高运行时性能。 使用`static`模式时，仍可移动、旋转和缩放场景的根节点，例如，在1:1 缩放（对于第一个用户视图）和表顶部视图之间动态切换。

* 当你需要移动某些部分时，这通常也意味着你需要支持 raycasts 或其他[空间查询](../../overview/features/spatial-queries.md)，以便你可以在第一个位置选择这些部分。 另一方面，如果不打算四处移动某些东西，则很可能不需要它来参与空间查询，因此可以关闭`generateCollisionMesh`标志。 此开关对转换时间、加载时间以及运行时的每帧更新成本有重大影响。

* 如果应用程序不使用[cut 飞机](../../overview/features/cut-planes.md)，则应`opaqueMaterialDefaultSidedness`关闭该标志。 性能提升通常为 20%-30%。 剪切平面仍可使用，但在查看对象的内部部分时不会有变化，这看起来很直观。 有关详细信息，请参阅单面[渲染](../../overview/features/single-sided-rendering.md)。

### <a name="use-case-photogrammetry-models"></a>用例： Photogrammetry 模型

在呈现 photogrammetry 模型时，通常不需要场景图，因此，您可以将设置`sceneGraphMode`为。 `none` 由于这些模型极少包含一开始的复杂场景图，此选项的影响应该是不重要的。

由于已将光照融入纹理，因此不需要动态照明。 因此：

* 将`unlitMaterials`标志设置为`true` ，以将所有材料转换为 unlit[颜色材料](../../overview/features/color-materials.md)。
* 从顶点格式中删除不需要的数据。 请参阅上面的[示例](#example)。

### <a name="use-case-visualization-of-compact-machines-etc"></a>用例：精简计算机的可视化效果等。

在这些用例中，模型中的详细信息通常很高。 呈现器进行了大量优化，可以很好地处理这种情况。 但是，在上面的用例中提到的大多数优化在此处不适用：

* 应选择和可移动各个部分，因此`sceneGraphMode`必须保留到。 `dynamic`
* Ray 转换通常是应用程序的一个有机组成部分，因此必须生成冲突网格。
* 启用`opaqueMaterialDefaultSidedness`标志后，剪切平面看起来更好。

## <a name="next-steps"></a>后续步骤

* [模型转换](model-conversion.md)
* [颜色材料](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)
* [在模型转换期间替代材料](override-materials.md)
