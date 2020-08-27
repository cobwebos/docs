---
title: 单面渲染
description: 介绍单面渲染设置和用例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: aff636adff48a8882c152eab398a96a8d28f84e0
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892739"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: 着色

大多数呈现器使用[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling)来提高性能。 但是，在使用[剪切平面](cut-planes.md)来切开网格时，用户常常将查看三角形的背面。 如果这些三角形被剔除，那么结果看起来没有说服力。

要可靠地避免这一问题，三角形的*两面*都需要渲染。 由于不使用背面剔除会影响性能，因此默认情况下，Azure 远程渲染仅在处理与剪切平面相交的网格时才切换到双面渲染。

* :::no-loc text="single-sided"::: 呈现*设置允许您自定义此行为。

> [!CAUTION]
> :::no-loc text="single-sided":::呈现设置是一项实验性功能。 未来可能会再次将其移除。 请勿更改默认设置，除非它确实解决了应用程序中的关键问题。

## <a name="prerequisites"></a>先决条件

:::no-loc text="single-sided":::呈现设置仅对已使用设置为的选项进行[转换](../../how-tos/conversion/configure-model-conversion.md)的网格有效 `opaqueMaterialDefaultSidedness` `SingleSided` 。 此选项默认设置为 `DoubleSided`。

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: 呈现设置

有 3 种不同的模式：

**正常：** 在此模式下，网格将始终在转换时渲染。 这意味着如果在 `opaqueMaterialDefaultSidedness` 设置为 `SingleSided` 的情况下转换网格，则即使这些网格不与剪切平面相交，也始终会在启用背面剔除后渲染它们。

**DynamicDoubleSiding：** 在此模式下，当剪切平面与网格相交时，它会自动切换到双面渲染。 此模式是默认模式。

**AlwaysDoubleSided：** 强制始终对所有单面几何图形进行双面渲染。 此模式主要是公开的，因此你可以轻松地比较和呈现之间的性能影响 :::no-loc text="single-sided"::: :::no-loc text="double-sided"::: 。

:::no-loc text="single-sided":::可以按如下所示更改呈现设置：

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Actions()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>后续步骤

* [剪切平面](cut-planes.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
