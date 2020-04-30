---
title: 模型
description: 描述 Azure 远程呈现中的模型
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617955"
---
# <a name="models"></a>模型

Azure 远程呈现中的*模型*是指完全对象表示形式，由[实体](entities.md)和[组件](components.md)组成。 模型是将自定义数据获取到远程呈现服务的主要方式。

## <a name="model-structure"></a>模型结构

模型只有一个[实体](entities.md)作为根节点。 下面可能具有子实体的任意层次结构。 在加载模型时，将返回对此根实体的引用。

每个实体都可能附加了[组件](components.md)。 在最常见的情况下，实体具有引用[网格资源](meshes.md)的*MeshComponents*。

## <a name="creating-models"></a>创建模型

通过从文件格式（例如 FBX 和 GLTF）[转换输入模型](../how-tos/conversion/model-conversion.md)，为运行时创建模型。 转换过程提取所有资源，如纹理、材料和网格，并将其转换为优化的运行时格式。 它还将提取结构信息，并将其转换为 ARR 的实体/组件图结构。

> [!IMPORTANT]
>
> [模型转换](../how-tos/conversion/model-conversion.md)是创建[网格](meshes.md)的唯一方法。 虽然可以在运行时在实体之间共享网格，但没有其他方法可获取与加载模型不同的运行时。

## <a name="loading-models"></a>正在加载模型

转换模型后，可将其从 Azure blob 存储加载到运行时中。

在 blob 存储中对资产进行寻址的方式有两个不同的加载函数：

* 可以通过其 SAS URI 对模型进行寻址。 相关加载函数的`LoadModelFromSASAsync`参数`LoadModelFromSASParams`为。 在加载[内置模型](../samples/sample-model.md)时也使用此变体。
* 如果[blob 存储链接到该帐户](../how-tos/create-an-account.md#link-storage-accounts)，则可以直接通过 blob 存储参数来处理模型。 在这种情况下，相关`LoadModelAsync`的加载`LoadModelParams`函数是带参数的。

下面的代码片段演示了如何加载任意一种函数。 若要使用 SAS URI 加载模型，请使用如下所示的代码：

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

如果要通过直接使用模型的 blob 存储参数加载模型，请使用类似于以下代码片段的代码：

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

之后，您可以遍历实体层次结构并修改实体和组件。 多次加载同一模型会创建多个实例，每个实例都有自己的实体/组件结构副本。 由于网格、材料和纹理是[共享资源](../concepts/lifetime.md)，因此不会再次加载它们的数据。 因此，多次实例化模型会产生相对较少的内存开销。

> [!CAUTION]
> ARR 中的所有*异步*函数都返回异步操作对象。 在完成操作之前，必须存储对这些对象的引用。 否则，c # 垃圾回收器可能会提前删除该操作，并且该操作永远不能完成。 在上面的示例代码中，使用*await*保证本地变量 "loadOp" 在模型加载完成之前保留引用。 但是，如果要改为使用*已完成*的事件，则需要在成员变量中存储异步操作。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [网格](meshes.md)
