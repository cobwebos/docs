---
title: PBR 材料
description: 描述 PBR 材料类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680253"
---
# <a name="pbr-materials"></a>PBR 材料

*PBR 材质*是 Azure 远程呈现中支持[的材料类型](../../concepts/materials.md)之一。 它们用于应接收逼真的照明[的网。](../../concepts/meshes.md)

PBR 代表**P**hysy **B**ased **R**端端，意味着材料以物理上合理的方式描述表面的视觉属性，因此在所有照明条件下都能够产生实际结果。 大多数现代游戏引擎和内容创建工具都支持 PBR 材料，因为它们被认为是实时渲染的真实场景的最佳近似值。

![由 ARR 渲染的头盔 glTF 示例模型](media/helmet.png)

不过，PBR材料并不是通用解决方案。 有些材质根据视角的不同反射颜色不同。 例如，一些织物或汽车涂料。 这些类型的材料不由标准 PBR 模型处理，并且当前不受 Azure 远程呈现的支持。 这包括 PBR 扩展，如*薄膜*（多层表面）和*透明涂层*（用于汽车涂料）。

## <a name="common-material-properties"></a>常见材料属性

这些属性是所有材料的通用属性：

* **阿贝多颜色：** 此颜色与其他颜色（如*反色映射*或*顶点颜色*）相乘。 如果在材质上启用*了透明度*，则 Alpha 通道用于调整不透明度，`1`其含义完全不透明，`0`含义完全透明。 默认值为白色。

  > [!NOTE]
  > 当 PBR 材料完全透明时，就像一块完全干净的玻璃一样，它仍然反映了环境。 像太阳这样的亮点在反射中仍然可见。 对于[颜色材料](color-materials.md)来说，这是不同的。

* **阿尔贝多地图：** 每像素反贝度值的[2D 纹理](../../concepts/textures.md)。

* **AlphaClip 启用**和**alphaClip 阈值：** 如果*AlphaClip 启用*为 true，则不会绘制所有白数 alpha 值低于*alphaClip 阈值*的像素。 即使不启用透明度，也可以使用 Alpha 裁剪，渲染速度也快得多。 不过，Alpha 剪切材质渲染的速度仍然比完全不透明的材料慢。 默认情况下，禁用 alpha 剪辑。

* **纹理坐标和****纹理坐标偏移：** 比例乘以 UV 纹理坐标，偏移量添加到其中。 可用于拉伸和移动纹理。 默认比例为 （1，1），偏移量为 （0， 0）。

* **使用VertexColor：** 如果网格包含顶点颜色并启用此选项，则网格的顶点颜色将乘以*反色*和*反反多映射*。 默认情况下，顶点颜色被禁用。

* **是双面：** 如果双面设置为 true，则即使摄像机正在查看其背面，也会渲染具有此材质的三角形。 对于 PBR 材料，也为后面正确计算照明。 默认情况下，此选项被禁用。 另请参阅[单面渲染](single-sided-rendering.md)。

## <a name="pbr-material-properties"></a>PBR 材料属性

基于物理的渲染的核心思想是使用*BaseColor、**金属度*和*粗糙度*属性来模拟各种真实材料。 PBR 的详细描述超出了本文的范围。 有关 PBR 的详细信息，请参阅[其他源](http://www.pbr-book.org)。 以下属性特定于 PBR 材料：

* **基色：** 在 PBR 材料中，*色底色*称为*底色*。 在 Azure 远程渲染中，通过公共材质属性已存在*色色*属性，因此没有其他基本颜色属性。

* **粗糙度**和**粗糙度地图：** 粗糙度定义表面的粗糙度或平滑度。 粗糙的表面将光以更平滑的方向散射，而不是平滑表面，这使得反射变得模糊而不是锋利。 值范围是从 到`0.0``1.0`。 当`roughness`相等时`0.0`，反射将是尖锐的。 当`roughness`相等时`0.5`，反射将变得模糊。

  如果同时提供粗糙度值和粗糙度图，则最终值将是两者的产物。

* **金属性和****金属度Map：** 在物理学中，此属性对应于表面是导电的还是介电的。 导电材料具有不同的反射性能，并且往往反射无反光色。 在 PBR 材质中，此属性会影响曲面反映周围环境的频率。 值范围从`0.0`到`1.0`。 当金属性时`0.0`，二贝多色是完全可见的，材料看起来像塑料或陶瓷。 当金属性时`0.5`，它看起来像是漆金属。 当金属性时`1.0`，表面几乎完全失去其白度颜色，只反映周围环境。 `metalness`例如，如果`1.0`是，`roughness``0.0`则曲面看起来像真实世界的镜像。

  如果同时提供金属度值和金属度贴图，则最终值将是两者的产品。

  ![金属性和粗糙度](./media/metalness-roughness.png)

  在上图中，右下角的球体看起来像真正的金属材料，左下角看起来像陶瓷或塑料。 色白性颜色也根据物理属性而变化。 随着粗糙度的增加，材料会失去反射锐度。

* **正常映射：** 要模拟细粒度细节，可以提供[法贴图](https://en.wikipedia.org/wiki/Normal_mapping)。

* **遮挡贴图**和**aoScale：**[环境遮挡](https://en.wikipedia.org/wiki/Ambient_occlusion)通过将阴影添加到遮挡区域，使具有缝隙的对象看起来更逼真。 `0.0`遮挡值范围从`1.0`到 ，其中`0.0`表示黑暗（遮挡），表示`1.0`没有遮挡。 如果 2D 纹理作为遮挡贴图提供，则启用效果 *，aoScale*充当乘数。

  ![遮挡贴图](./media/boom-box-ao2.gif)

* **透明：** 对于 PBR 材质，只有一个透明度设置：是否启用。 不一定是由色光的alpha通道定义的。 启用后，将调用更复杂的渲染管道来绘制半透明曲面。 Azure 远程呈现实现真实[顺序独立透明度](https://en.wikipedia.org/wiki/Order-independent_transparency)（OIT）。

  透明几何图形的渲染成本很高。 如果只需要曲面上的孔，例如树的叶子，最好改用 Alpha 裁剪。

  ![透明度](./media/transparency.png)通知在上面的图像中，最右边的球体是如何完全透明的，但反射仍然可见。

  > [!IMPORTANT]
  > 如果任何材质在运行时应从不透明切换到透明，则渲染器必须使用*基于 Tile 的合成*[呈现模式](../../concepts/rendering-modes.md)。 此限制不适用于首先转换为透明材料的材料。

## <a name="technical-details"></a>技术详细信息

Azure 远程渲染使用带有 GGX NDF 的库克-托伦斯微面 BRDF、Schlick Fresnel 和 GGX Smith 相关可见性术语，带有兰伯特漫反射术语。 这种模式是目前事实上的行业标准。 有关更深入的详细信息，请参阅本文：[基于物理的渲染 - 烹饪托伦斯](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Azure 远程渲染中使用的*金属粗糙度*PBR 模型的替代是*镜面光泽*PBR 模型。 此模型可以代表更广泛的材料范围。 但是，它更昂贵，通常不适用于实时案例。
并不总是可以转换从*镜面光泽*度到*金属粗糙度*，因为有 *（漫反射，镜面）* 值对不能转换为 *（基础颜色，金属度）。* 其他方向的转换更简单、更精确，因为所有 *（BaseColor、金属度）* 对都对应于定义良好的 *（漫反射、镜面）* 对。

## <a name="next-steps"></a>后续步骤

* [颜色材料](color-materials.md)
* [纹理](../../concepts/textures.md)
* [网 格](../../concepts/meshes.md)
