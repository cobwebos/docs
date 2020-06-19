---
title: 单面渲染
description: 介绍单面渲染设置和用例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759092"
---
# <a name="single-sided-rendering"></a>单面渲染

大多数呈现器使用[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling)来提高性能。 但是，在使用[剪切平面](cut-planes.md)来切开网格时，用户常常将查看三角形的背面。 如果这些三角形被剔除，那么结果看起来没有说服力。

要可靠地避免这一问题，三角形的*两面*都需要渲染。 由于不使用背面剔除会影响性能，因此默认情况下，Azure 远程渲染仅在处理与剪切平面相交的网格时才切换到双面渲染。

通过“单面渲染”设置，可自定义此行为。

> [!CAUTION]
> 单面渲染设置是一项实验性功能。 未来可能会再次将其移除。 请勿更改默认设置，除非它确实解决了应用程序中的关键问题。

## <a name="prerequisites"></a>先决条件

仅在已将 `opaqueMaterialDefaultSidedness` 选项设置为 `SingleSided` 的情况下[转换](../../how-tos/conversion/configure-model-conversion.md)网格时，单面渲染设置还对网格有效果。 此选项默认设置为 `DoubleSided`。

## <a name="single-sided-rendering-setting"></a>单面渲染设置

有 3 种不同的模式：

**正常：** 在此模式下，网格将始终在转换时渲染。 这意味着如果在 `opaqueMaterialDefaultSidedness` 设置为 `SingleSided` 的情况下转换网格，则即使这些网格不与剪切平面相交，也始终会在启用背面剔除后渲染它们。

**DynamicDoubleSiding：** 在此模式下，当剪切平面与网格相交时，它会自动切换到双面渲染。 此模式是默认模式。

**AlwaysDoubleSided：** 强制始终对所有单面几何图形进行双面渲染。 此模式多半是公开的，因此你可轻松比较单面渲染与双面渲染之间的性能影响。

可按如下所示更改单面渲染设置：

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
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>后续步骤

* [剪切平面](cut-planes.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
