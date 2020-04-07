---
title: 纹理
description: 纹理资源工作流
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681657"
---
# <a name="textures"></a>纹理

纹理是一种不可变[的共享资源](../concepts/lifetime.md)。 纹理可以从[blob 存储](../how-tos/conversion/blob-storage.md)加载并直接应用于模型，如[教程：更改环境和材质](../tutorials/unity/changing-environment-and-materials.md)所示。 但最常见的情况是，纹理将是[转换模型](../how-tos/conversion/model-conversion.md)的一部分，其中它们被其[材质](materials.md)引用。

## <a name="texture-types"></a>纹理类型

不同的纹理类型具有不同的用例：

* **2D纹理**主要用于[材料](materials.md)。
* **立方体地图**可用于[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支持的纹理格式

给ARR的所有纹理都必须采用[DDS格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好使用 mipmaps 和纹理压缩。 如果要自动执行转换过程，请参阅[TexConv 命令行工具](../resources/tools/tex-conv.md)。

## <a name="loading-textures"></a>加载纹理

加载纹理时，必须指定其预期类型。 如果类型不匹配，纹理加载将失败。
使用同一 URI 加载纹理两次将返回相同的纹理对象，因为它是[共享资源](../concepts/lifetime.md)。

与加载模型类似，在源 Blob 存储中寻址纹理资产有两种变体：

* 纹理资产可以通过其 SAS URI 进行寻址。 相关的加载函数是`LoadTextureFromSASAsync`带参数`LoadTextureFromSASParams`的。 加载[内置纹理](../overview/features/sky.md#built-in-environment-maps)时，也可以使用此变体。
* 如果[blob 存储链接到帐户](../how-tos/create-an-account.md#link-storage-accounts)，则可以通过 blob 存储参数直接寻址纹理。 在这种情况下，相关的加载功能是`LoadTextureAsync`参数 。 `LoadTextureParams`

以下示例代码演示如何通过其 SAS URI（或内置纹理）加载纹理 - 请注意，对于其他情况，只有加载函数/参数不同：

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

根据纹理应该用于什么，纹理类型和内容可能会有限制。 例如[，PBR 材质](../overview/features/pbr-materials.md)的粗糙度贴图必须为灰度。

> [!CAUTION]
> ARR 中的所有*异步*函数返回异步操作对象。 您必须存储对这些对象的引用，直到操作完成。 否则，C# 垃圾回收器可能会提前删除该操作，并且它永远无法完成。 在上述示例代码中，成员变量"_textureLoad"用于保存引用，直到 *"已完成"* 事件到达。

## <a name="next-steps"></a>后续步骤

* [材料](materials.md)
* [天空](../overview/features/sky.md)
