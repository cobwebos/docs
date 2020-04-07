---
title: 单面渲染
description: 描述单面渲染设置和用例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682008"
---
# <a name="single-sided-rendering"></a>单面渲染

大多数渲染器使用[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling)来提高性能。 然而，当用[切割平面](cut-planes.md)切开的网外时，用户通常会看到三角形的背面。 如果这些三角形被剔除，结果看起来并不令人信服。

可靠地防止此问题的方法是呈现双*面*三角形。 由于不使用背面剔除会影响性能，因此默认情况下，Azure 远程渲染仅切换到与切割平面相交的网间的双面渲染。

*单面呈现*设置允许您自定义此行为。

> [!CAUTION]
> 单面渲染设置是一个实验功能。 将来可能会再次删除它。 请不要更改默认设置，除非它确实解决了应用程序中的关键问题。

## <a name="prerequisites"></a>先决条件

单面呈现设置仅对已[转换](../../how-tos/conversion/configure-model-conversion.md)选项`opaqueMaterialDefaultSidedness`设置为`SingleSided`的网件具有效果。 默认情况下，此选项设置为`DoubleSided`。

## <a name="single-sided-rendering-setting"></a>单面渲染设置

有三种不同的模式：

**正常：** 在此模式下，在转换这些模样时，始终呈现这些等点。 这意味着转换到`opaqueMaterialDefaultSidedness`的`SingleSided`模样将始终在启用背面剔除的情况下呈现，即使它们与切割平面相交也是如此。

**动态双衬：** 在此模式下，当剪切平面与网格相交时，它会自动切换到双面渲染。 此模式为默认模式。

**始终双面：** 强制所有单面几何体在任何时候呈现双面几何。 此模式大多公开，因此您可以轻松比较单面渲染和双面渲染之间的性能影响。

更改单面渲染设置可以如下所示：

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>后续步骤

* [切割平面](cut-planes.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
