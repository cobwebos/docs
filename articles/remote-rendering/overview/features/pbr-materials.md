---
title: PBR 材料
description: 描述 .PBR 材料类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: ad7fc7d9d02cd9a9a6fe74534a7c674fe0ac778d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893248"
---
# <a name="pbr-materials"></a>PBR 材料

*.Pbr 材料* 是 Azure 远程呈现中支持的 [材料类型](../../concepts/materials.md) 之一。 它们用于应该接收真实照明的 [网格](../../concepts/meshes.md) 。

.PBR 代表 **P**hysically **B**ased **R**endering，表示材料以物理上的方式描述图面的视觉对象属性，这样，在所有照明条件下均可获得实际的结果。 大多数新式游戏引擎和内容创建工具都支持 .PBR 材料，因为它们被视为实时渲染的现实世界方案的最佳近似值。

![ARR 呈现的 Helmet glTF 示例模型](media/helmet.png)

但 .PBR 材料并不是通用的解决方案。 有一些材料反映颜色的不同，具体取决于查看角度。 例如，某些结构或汽车会进行绘制。 标准 .PBR 模型不会处理这些类型的材料，Azure 远程呈现目前不支持这些类型的材料。 这包括 .PBR 扩展， *如 (多* 层) 和 (为汽车) 绘制的 *Coat* 。

## <a name="common-material-properties"></a>常用材料属性

这些属性是所有材料共有的：

* **albedoColor：** 此颜色与其他颜色（如*albedoMap*或* :::no-loc text="vertex "::: 颜色*）相乘。 如果对材料启用了 *透明度* ，则使用 alpha 通道调整不透明度， `1` 这意味着完全不透明并 `0` 表示完全透明。 默认值为白色。

  > [!NOTE]
  > 当某个 .PBR 材料完全透明（如完全透明的玻璃部分）时，它仍会反映该环境。 类似于 sun 的亮点仍在反射中可见。 这不同于 [颜色材料](color-materials.md)。

* **albedoMap：** 每像素 albedo 值的 [2d 纹理](../../concepts/textures.md) 。

* **alphaClipEnabled** 和 **AlphaClipThreshold：** 如果 *alphaClipEnabled* 为 true，则不会绘制 albedo alpha 值小于 *alphaClipThreshold* 的所有像素。 即使不启用透明度，也可以使用 Alpha 剪辑，因而速度要快得多。 不过，Alpha 剪裁材料的呈现速度仍比完全不透明材料慢。 默认情况下，alpha 剪辑处于禁用状态。

* **textureCoordinateScale** 和 **textureCoordinateOffset：** 将刻度与 UV 纹理坐标相乘，并向其添加偏移量。 可用于拉伸和移动纹理。 默认小数位数为1，1) ，偏移量为 (0，0)  (。

* **useVertexColor：** 如果网格包含 :::no-loc text="vertex"::: 颜色且已启用此选项，则网格的 :::no-loc text="vertex"::: 颜色将被相乘到 *albedoColor* 和 *albedoMap*中。 默认情况下， *useVertexColor* 处于禁用状态。

* **isDoubleSided：** 如果将 sidedness 设置为 true，则即使相机正在查看背景面，也会呈现带有此材料的三角形。 对于 .PBR 材料，也可以为背面正确计算。 默认情况下，此选项处于禁用状态。 另请参阅[ :::no-loc text="Single-sided"::: 呈现](single-sided-rendering.md)。

* **TransparencyWritesDepth：** 如果对材料设置了 TransparencyWritesDepth 标志，而材料是透明的，则使用此材料的对象也会对最终的深度缓冲区产生影响。 请参阅下一节中的 "不 *透明* " 部分。 如果用例需要更变得合理的 [延迟阶段 reprojection](late-stage-reprojection.md) 完全透明的场景，则建议启用此功能。 对于混合的不透明/透明场景，此设置可能会引入可能 reprojection 行为或 reprojection 项目。 出于此原因，一般用例的默认设置和推荐设置是禁用此标志。 书写的深度值取自离相机最近的对象的每像素深度层。

## <a name="pbr-material-properties"></a>.PBR 材料属性

以物理方式呈现的核心概念是使用 *BaseColor*、 *Metalness*和 *粗糙度* 属性来模拟各种真实的资料。 有关 .PBR 的详细说明超出了本文的范围。 有关 .PBR 的详细信息，请参阅 [其他来源](http://www.pbr-book.org)。 以下属性特定于 .PBR 材料：

* **baseColor：** 在 .PBR 材料中， *albedo 颜色* 称为 *基准颜色*。 在 Azure 远程呈现中， *albedo 颜色* 属性已经通过公共材料属性提供，因此没有其他基本颜色属性。

* **粗糙度** 和 **roughnessMap：** 粗糙度定义图面的大致程度或平滑程度。 粗糙面使光源的显示方向大于平滑曲面，这使反射模糊而不是清晰。 值范围从 `0.0` 到 `1.0` 。 当 `roughness` 等于时 `0.0` ，反射将会清晰。 当 `roughness` 等于时 `0.5` ，反射将变得模糊。

  如果同时提供了粗糙度值和粗糙度映射，最终值将为这两个值的乘积。

* **metalness** 和 **metalnessMap：** 在物理学中，此属性对应于 surface 是导电还是 dielectric。 导电材料具有不同的反射属性，它们往往在无 albedo 颜色的情况下反射。 在 .PBR 材料中，此属性会影响表面反映周围环境的程度。 值的范围从 `0.0` 到 `1.0` 。 当 metalness 为时 `0.0` ，albedo 颜色会完全可见，而材料看起来像塑料或 ceramics。 当 metalness 为时 `0.5` ，它看起来像是着色的金属。 当 metalness 为时 `1.0` ，表面几乎完全丢失了其 albedo 颜色，只反映了周围面。 例如，如果 `metalness` 为 `1.0` ，并且为，则 `roughness` `0.0` 表面看起来像真实的镜像。

  如果同时提供了 metalness 值和 metalness 映射，最终值将是这两个值的乘积。

  ![用不同的 metalness 和粗糙度值渲染的球](./media/metalness-roughness.png)

  在上图中，右下角的球看起来像一种真实的金属材料，左下角看起来就像 ceramic 或塑料。 Albedo 颜色还会根据物理属性进行更改。 随着粗糙度的增加，材料丢失反射清晰度。

* **normalMap：** 若要模拟精细的详细信息，可以提供 [普通地图](https://en.wikipedia.org/wiki/Normal_mapping) 。

* **occlusionMap** 和 **aoScale：** 通过向封闭像素区域添加阴影， [环境封闭](https://en.wikipedia.org/wiki/Ambient_occlusion) 使对象 crevices 看起来更逼真。 封闭值的范围 `0.0` 为到 `1.0` ，其中， `0.0` 表示暗度 (封闭像素) ， `1.0` 表示无 occlusions。 如果二维纹理作为封闭图提供，则将启用该效果， *aoScale* 将作为乘数。

  ![在环境封闭的情况下呈现的对象](./media/boom-box-ao2.gif)

* **透明：** 对于 .PBR 材料，只有一个透明度设置：已启用或未启用。 不透明度由 albedo 颜色的 alpha 通道定义。 启用后，调用更复杂的呈现管道来绘制半透明的图面。 Azure 远程呈现实现了真正的 [顺序独立透明度](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT) 。

  透明的几何呈现成本高昂。 如果在表面上只需要孔洞，例如，对于树的叶，最好改为使用 alpha 剪辑。

  ![显示在上图中从零到完全透明通知的球体 ](./media/transparency.png) ，最右侧的球体完全透明，但反射仍可见。

  > [!IMPORTANT]
  > 如果在运行时应将任何材料从不透明切换到透明，则呈现器必须使用 *TileBasedComposition* [呈现模式](../../concepts/rendering-modes.md)。 此限制不适用于转换为透明材料开头的材料。

## <a name="technical-details"></a>技术详细信息

Azure 远程呈现使用带有 GGX 的 NDF、Schlick 菲涅尔衰减和 GGX Smith 相关可见术语的 Cook-Torrance 微面 BRDF 和朗伯漫射术语。 此模型是目前事实上的行业标准。 有关更深入的详细信息，请参阅此文章： [基于物理的呈现-可 Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Azure 远程呈现中使用的 *Metalness* .pbr 模型的替代方法是 *反射-光泽* 性 .pbr 模型。 此模型可以表示一系列更广泛的材料。 不过，它的成本更高，通常不适用于实时事例。
并非始终可以从*反射-光泽*转换为*Metalness* ，因为存在无法转换为* (BaseColor，Metalness) *的* (扩散、镜面) *值对。 其他方向的转换更为简单且更精确，因为所有 * (BaseColor，Metalness) * 对都对应于定义良好的 * (漫射、镜面) * 对。

## <a name="api-documentation"></a>API 文档

* [C # PbrMaterial 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C # RemoteManager CreateMaterial ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C + + PbrMaterial 类](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterial)
* [C + + RemoteManager：： CreateMaterial ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>后续步骤

* [颜色材料](color-materials.md)
* [纹理](../../concepts/textures.md)
* [网格](../../concepts/meshes.md)
