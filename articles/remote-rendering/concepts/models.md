---
title: 模型
description: 描述模型在 Azure 远程呈现中的内容
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617955"
---
# <a name="models"></a>模型

Azure 远程呈现中的*模型*是指由[实体](entities.md)和[组件](components.md)组成的完整对象表示形式。 模型是将自定义数据引入远程呈现服务的主要方式。

## <a name="model-structure"></a>模型结构

模型有一个[实体](entities.md)作为其根节点。 下面说，它可能具有子实体的任意层次结构。 加载模型时，将返回对此根实体的引用。

每个实体可能附加[组件](components.md)。 在最常见的情况下，实体具有*网格组件*，它引用[网格资源](meshes.md)。

## <a name="creating-models"></a>创建模型

通过转换来自 FBX 和 GLTF 等文件格式的[输入模型](../how-tos/conversion/model-conversion.md)，可以创建运行时模型。 转换过程提取所有资源，如纹理、材质和等点，并将其转换为优化的运行时格式。 它还将提取结构信息并将其转换为 ARR 的实体/组件图形结构。

> [!IMPORTANT]
>
> [模型转换](../how-tos/conversion/model-conversion.md)是创建[模样](meshes.md)的唯一方法。 尽管在运行时可以在实体之间共享网格，但除了加载模型之外，没有其他方法可以将网格引入运行时。

## <a name="loading-models"></a>加载模型

转换模型后，可以从 Azure Blob 存储加载到运行时。

有两个不同的加载函数因 Blob 存储中资产的寻址方式而异：

* 模型可以通过其 SAS URI 进行寻址。 相关的加载函数是`LoadModelFromSASAsync`带参数`LoadModelFromSASParams`的。 加载[内置模型](../samples/sample-model.md)时，也可以使用此变体。
* 如果[blob 存储链接到帐户](../how-tos/create-an-account.md#link-storage-accounts)，则可以通过 blob 存储参数直接寻址模型。 在这种情况下，相关的加载功能是`LoadModelAsync`参数 。 `LoadModelParams`

以下代码段演示如何使用任一函数加载模型。 要使用 SAS URI 加载模型，请使用如下所示的代码：

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

如果要直接使用其 blob 存储参数加载模型，请使用类似于以下代码段的代码：

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

之后，您可以遍历实体层次结构并修改实体和组件。 多次加载同一模型可创建多个实例，每个实例都有自己的实体/组件结构副本。 但是，由于网、材质和纹理是[共享资源](../concepts/lifetime.md)，因此它们的数据将不再加载。 因此，多次实例化模型会产生相对较少的内存开销。

> [!CAUTION]
> ARR 中的所有*异步*函数返回异步操作对象。 您必须存储对这些对象的引用，直到操作完成。 否则，C# 垃圾回收器可能会提前删除该操作，并且它永远无法完成。 在上面的示例代码中，使用*await*可确保本地变量"loadOp"在模型加载完成之前保留引用。 但是，如果要改用 *"已完成"* 事件，则需要将异步操作存储在成员变量中。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [网格](meshes.md)
