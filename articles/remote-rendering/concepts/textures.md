---
title: 纹理
description: 纹理资源工作流
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: dc38b53705c24cb12a001237a9a80ec66ec33e14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613789"
---
# <a name="textures"></a>纹理

纹理是不可变[共享资源](../concepts/lifetime.md)。 纹理可从 [blob 存储](../how-tos/conversion/blob-storage.md)加载并直接应用于模型，如[教程：更改环境和材料](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)所示。 不过，通常情况下，纹理会成为[转换的模型](../how-tos/conversion/model-conversion.md)的一部分，在该模型中，纹理由其[材料](materials.md)引用。

## <a name="texture-types"></a>纹理类型

不同的纹理类型具有不同的用例：

* “2D 纹理”主要用于[材料](materials.md)。
* “立方体贴图”可用于[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支持的纹理格式

为 ARR 指定的所有纹理都必须采用 [DDS 格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好使用纹理图和纹理压缩。 若要自动执行转换过程，请参阅 [TexConv 命令行工具](../resources/tools/tex-conv.md)。

## <a name="loading-textures"></a>加载纹理

加载纹理时，必须指定其预期类型。 如果类型不匹配，纹理加载将失败。
加载具有相同 URI 的纹理两次将返回相同的纹理对象，因为它是[共享的资源](../concepts/lifetime.md)。

与加载模型类似，有两种用于在源 blob 存储中为纹理资产寻址的变体：

* 纹理资产可以通过其 SAS URI 寻址。 相关的加载函数为具有参数 `LoadTextureFromSASParams` 的 `LoadTextureFromSASAsync`。 加载[内置纹理](../overview/features/sky.md#built-in-environment-maps)时也使用此变体。
* 如果 [Blob 存储已链接到帐户](../how-tos/create-an-account.md#link-storage-accounts)，则可直接通过 Blob 存储参数对纹理进行寻址。 在本例中，相关的加载函数为具有参数 `LoadTextureParams` 的 `LoadTextureAsync`。

下面的示例代码演示如何通过其 SAS URI （或内置纹理）加载纹理 - 请注意，只有加载函数/参数不同于另一种情况：

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
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

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

纹理类型和内容可能存在限制，具体取决于要使用的纹理。 例如，[PBR 材料](../overview/features/pbr-materials.md)的粗糙度图必须为灰度。

> [!CAUTION]
> ARR 中的所有 Async 函数都返回异步操作对象。 在完成操作之前，必须存储对这些对象的引用。 否则，C# 垃圾回收器可能会提前删除该操作，并且该操作永远不能完成。 在上面的示例代码中，成员变量“_textureLoad”用于保存引用，直到“已完成”事件到达。

## <a name="api-documentation"></a>API 文档

* [C # 纹理类](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.texture)
* [C # RemoteManager LoadTextureAsync ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C # RemoteManager LoadTextureFromSASAsync ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [C + + 纹理类](https://docs.microsoft.com/cpp/api/remote-rendering/texture)
* [C + + RemoteManager：： LoadTextureAsync ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C + + RemoteManager：： LoadTextureFromSASAsync ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>后续步骤

* [材料](materials.md)
* [天空](../overview/features/sky.md)
