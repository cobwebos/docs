---
title: 单面渲染
description: 介绍单面呈现设置和用例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682008"
---
# <a name="single-sided-rendering"></a>单面渲染

大多数呈现器使用[后端剔除](https://en.wikipedia.org/wiki/Back-face_culling)来提高性能。 但是，当网格[在剪切的情况下处于](cut-planes.md)打开状态时，用户通常会查看三角形的背面。 如果这些三角形剔除，结果将不会令人满意。

可靠地避免此问题的一种方法是，呈现*两个*三角形。 由于不使用后面对剔除会影响性能，因此默认情况下，Azure 远程渲染仅会切换为与切口平面相交的网格的双面渲染。

*单面呈现*设置允许您自定义此行为。

> [!CAUTION]
> 单面呈现设置是一项实验性功能。 将来可能会再次将其删除。 请不要更改默认设置，除非它确实解决了应用程序中的关键问题。

## <a name="prerequisites"></a>必备条件

单面呈现设置仅对已使用设置为`opaqueMaterialDefaultSidedness` `SingleSided`的选项进行[转换](../../how-tos/conversion/configure-model-conversion.md)的网格有效。 默认情况下，此选项设置`DoubleSided`为。

## <a name="single-sided-rendering-setting"></a>单面呈现设置

有三种不同的模式：

**正常：** 在此模式下，网格将始终在转换时呈现。 这意味着，使用`opaqueMaterialDefaultSidedness`设置为的`SingleSided`进行转换的网格将始终在启用后面对剔除时呈现，即使它们相交于切口平面也是如此。

**DynamicDoubleSiding：** 在此模式下，当切口平面与网格相交时，它会自动切换到双面渲染。 此模式是默认模式。

**AlwaysDoubleSided：** 强制所有单面几何在任何时候都是双向的。 此模式主要是公开的，因此你可以轻松地比较单面和双面呈现之间的性能影响。

可以按如下所示更改单面呈现设置：

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

* [剪切平面](cut-planes.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
