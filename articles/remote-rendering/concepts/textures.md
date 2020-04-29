---
title: 纹理
description: 纹理资源工作流
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681657"
---
# <a name="textures"></a>纹理

纹理是不可变的[共享资源](../concepts/lifetime.md)。 纹理可从[blob 存储](../how-tos/conversion/blob-storage.md)加载并直接应用于模型，如[教程：更改环境和资料](../tutorials/unity/changing-environment-and-materials.md)中所示。 不过，通常情况下，纹理会成为[转换后的模型](../how-tos/conversion/model-conversion.md)的一部分，在此模型中，其[材料](materials.md)会引用它们。

## <a name="texture-types"></a>纹理类型

不同的纹理类型具有不同的用例：

* **2D 纹理**主要用在[材料](materials.md)中。
* **Cubemaps**可用于[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支持的纹理格式

为 ARR 指定的所有纹理都必须采用[DDS 格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好是 mipmap 和纹理压缩。 若要自动执行转换过程，请参阅[TexConv 命令行工具](../resources/tools/tex-conv.md)。

## <a name="loading-textures"></a>加载纹理

加载纹理时，必须指定其预期类型。 如果类型不匹配，纹理加载将失败。
加载具有相同 URI 的纹理两次将返回相同的纹理对象，因为它是[共享资源](../concepts/lifetime.md)。

类似于加载模型，有两种用于在源 blob 存储中为纹理资产寻址的变体：

* 纹理资产可以通过其 SAS URI 进行寻址。 相关加载函数的`LoadTextureFromSASAsync`参数`LoadTextureFromSASParams`为。 加载[内置纹理](../overview/features/sky.md#built-in-environment-maps)时也使用此变体。
* 如果[blob 存储链接到该帐户](../how-tos/create-an-account.md#link-storage-accounts)，纹理可以直接由 blob 存储参数进行寻址。 在这种情况下，相关`LoadTextureAsync`的加载`LoadTextureParams`函数是带参数的。

下面的示例代码演示如何通过其 SAS URI （或内置纹理）加载纹理-请注意，只有加载函数/参数不同于另一种情况：

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

对于纹理类型和内容，可能存在限制，具体取决于要用于的纹理。 例如， [.pbr 材料](../overview/features/pbr-materials.md)的粗糙度映射必须为灰色。

> [!CAUTION]
> ARR 中的所有*异步*函数都返回异步操作对象。 在完成操作之前，必须存储对这些对象的引用。 否则，c # 垃圾回收器可能会提前删除该操作，并且该操作永远不能完成。 在上面的示例代码中，成员变量 "_textureLoad" 用于保存引用，直到*完成*的事件到达。

## <a name="next-steps"></a>后续步骤

* [材料](materials.md)
* [昼](../overview/features/sky.md)
