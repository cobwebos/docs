---
title: 跨订阅迁移你的面部数据-人脸
titleSuffix: Azure Cognitive Services
description: 本指南说明如何将存储的人脸数据从一个面部订阅迁移到另一个。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: nitinme
ms.openlocfilehash: fd0e7079b3b70a6a6b8166cc7fc7518070e7153d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120804"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>将人脸数据迁移到其他人脸订阅

本指南说明如何将人脸数据（如保存的 Person group 对象）转移到不同的 Azure 认知服务人脸订阅。 若要移动数据，可以使用快照功能。 这样，在转移或扩展操作时就无需反复生成并训练 PersonGroup 或 FaceList 对象。 例如，你可能会使用免费试用版订阅创建一个 Person group 对象，现在想要将其迁移到付费订阅。 或者，可能需要在不同区域中的不同订阅之间同步人脸数据，以进行大规模企业操作。

此迁移策略同样适用于 LargePersonGroup 和 LargeFaceList 对象。 如果你不熟悉本指南中的概念，请查看[人脸识别概念](../concepts/face-recognition.md)指南中的相关定义。 本指南使用带有 c # 的面部 .NET 客户端库。

## <a name="prerequisites"></a>必备条件

需要准备好以下各项：

- 两个面部订阅密钥，一个用于现有数据，另一个用于迁移到。 若要订阅面部服务并获取你的密钥，请按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明进行操作。
- 与目标订阅相对应的人脸订阅 ID 字符串。 在 Azure 门户中选择“概述”可以找到该字符串。  
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

本指南使用一个简单的控制台应用来运行人脸数据迁移。 有关完整实现，请参阅 GitHub 上的[面部快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)。

1. 在 Visual Studio 中创建新的控制台应用 .NET Framework 项目。 将其命名为 **FaceApiSnapshotSample**。
1. 获取所需的 NuGet 包。 在解决方案资源管理器中，右键单击该项目并选择“管理 NuGet 包”  。 选择“浏览”选项卡，然后选择“包括预发行版”   。 找到并安装以下包：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>创建人脸客户端

在 **Program.cs** 的 *Main* 方法中，创建两个 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 实例，分别对应于源订阅和目标订阅。 此示例使用东亚区域中的人脸订阅作为源，将美国西部订阅用作目标。 此示例演示如何将数据从一个 Azure 区域迁移到另一个 Azure 区域。 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

填写源订阅和目标订阅的订阅密钥值与终结点 URL。


## <a name="prepare-a-persongroup-for-migration"></a>让 PersonGroup 做好迁移准备

需要使用源订阅中 PersonGroup 的 ID 将它迁移到目标订阅。 使用 [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) 方法检索 PersonGroup 对象的列表。 然后获取 [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) 属性。 此过程根据使用的 PersonGroup 对象而异。 在本指南中，源 PersonGroup ID 存储在 `personGroupId` 中。

> [!NOTE]
> [示例代码](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)将创建并训练要迁移的新 PersonGroup。 在大多数情况下，你应该已有一个可用的 PersonGroup。

## <a name="take-a-snapshot-of-a-persongroup"></a>创建 PersonGroup 的快照

快照是特定人脸数据类型的临时远程存储。 它可用作一种剪贴板，用于将数据从一个订阅复制到另一个订阅。 首先创建源订阅中数据的快照。 然后将此快照应用到目标订阅中的新数据对象。

使用源订阅的 FaceClient 实例创建 PersonGroup 快照。 请将 [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) 与 PersonGroup ID 和目标订阅 ID 配合使用。 如果有多个目标订阅，请将其添加为第三个参数中的数组项。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 创建和应用快照的过程不会中断对源或者目标 PersonGroup 或 FaceList 的任何常规调用。 请不要同时发出会更改源对象的调用，例如，[FaceList 管理调用](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或 [PersonGroup 训练](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)调用。 快照操作可能会先于或晚于这些操作运行，或者可能会遇到错误。

## <a name="retrieve-the-snapshot-id"></a>检索快照 ID

用于创建快照的方法是异步的，因此必须等待该操作完成。 快照操作不可取消。 在以下代码中，`WaitForOperation` 方法监视异步调用。 它每隔 100 毫秒检查一次状态。 完成该操作后，通过分析 `OperationLocation` 字段来检索操作 ID。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

典型的 `OperationLocation` 值如下所示：

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation` 帮助程序方法如下：

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

在操作状态显示为 `Succeeded` 后，通过分析返回的 OperationStatus 实例的 `ResourceLocation` 字段来获取快照 ID。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

典型的 `resourceLocation` 值如下所示：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>将快照应用到目标订阅

接下来，使用随机生成的 ID 在目标订阅中新建 PersonGroup。 然后，使用目标订阅的 FaceClient 实例将快照应用到此 PersonGroup。 传入快照 ID 和新的 PersonGroup ID。

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 快照对象仅在 48 小时内有效。 仅当你打算在不久后要使用某个快照进行数据迁移时，才创建该快照。

快照应用请求将返回另一个操作 ID。 若要获取此 ID，请分析返回的 applySnapshotResult 实例的 `OperationLocation` 字段。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

由于快照应用过程也是异步的，因此请同样使用 `WaitForOperation` 来等待该过程完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>测试数据迁移

应用快照后，目标订阅中的新 PersonGroup 会填充原始人脸数据。 默认情况下，还会复制训练结果。 新的 PersonGroup 已准备好进行人脸识别调用，而无需重新训练。

若要测试数据迁移，请运行以下操作，并比较这些操作在控制台中列显的结果：

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

使用以下帮助程序方法：

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

现在可以使用目标订阅中的新 PersonGroup。 

将来若要再次更新目标 PersonGroup，请创建一个新的 PersonGroup 用于接收快照。 为此，请遵循本指南中的步骤。 一次只能向一个 PersonGroup 对象应用快照。

## <a name="clean-up-resources"></a>清理资源

迁移完人脸数据后，请手动删除快照对象。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>后续步骤

接下来，请参阅相关的 API 参考文档、浏览使用快照功能的示例应用，或遵循下面所述的操作指南开始使用其他 API 操作：

- [快照参考文档 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [面部快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [添加人脸](how-to-add-faces.md)
- [检测图像中的人脸](HowtoDetectFacesinImage.md)
- [识别图像中的人脸](HowtoIdentifyFacesinImage.md)
