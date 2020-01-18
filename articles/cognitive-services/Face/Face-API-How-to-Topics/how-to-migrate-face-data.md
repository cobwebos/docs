---
title: 跨订阅迁移你的面部数据-人脸
titleSuffix: Azure Cognitive Services
description: 本指南说明如何将存储的人脸数据从一个面部订阅迁移到另一个。
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169808"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>将人脸数据迁移到其他人脸订阅

本指南说明如何将人脸数据（如保存的 Person group 对象）转移到不同的 Azure 认知服务人脸订阅。 若要移动数据，请使用快照功能。 这样，你就可以避免在移动或展开操作时重复生成和训练 Person group 或 FaceList 对象。 例如，你可能会使用免费试用版订阅创建一个 Person group 对象，现在想要将其迁移到付费订阅。 或者，可能需要在不同区域中的不同订阅之间同步人脸数据，以进行大规模企业操作。

此相同迁移策略还适用于 LargePersonGroup 和 LargeFaceList 对象。 如果不熟悉本指南中的概念，请参阅[面部识别概念](../concepts/face-recognition.md)指南中的相关定义。 本指南使用面向的 .NET 客户端库C#。

## <a name="prerequisites"></a>必备组件

需要以下项：

- 两个面部订阅密钥，一个用于现有数据，另一个用于迁移到。 若要订阅面部服务并获取你的密钥，请按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明进行操作。
- 与目标订阅相对应的人脸订阅 ID 字符串。 若要找到它，请在 Azure 门户中选择 "**概述**"。 
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

本指南使用简单的控制台应用运行面部数据迁移。 有关完整实现，请参阅 GitHub 上的[面部快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)。

1. 在 Visual Studio 中，.NET Framework 项目创建新的控制台应用程序。 将其命名为**FaceApiSnapshotSample**。
1. 获取所需的 NuGet 包。 右键单击 "解决方案资源管理器中的项目，然后选择"**管理 NuGet 包**"。 选择 "**浏览**" 选项卡，然后选择 "**包括预发行**版"。 查找并安装以下包：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>创建人脸客户端

在*Program.cs*的**Main**方法中，为源订阅和目标订阅创建两个[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)实例。 此示例使用东亚区域中的人脸订阅作为源，将美国西部订阅用作目标。 此示例演示如何将数据从一个 Azure 区域迁移到另一个 Azure 区域。 

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

填写源订阅和目标订阅的订阅密钥值和终结点 Url。


## <a name="prepare-a-persongroup-for-migration"></a>让 PersonGroup 做好迁移准备

你需要源订阅中 Person group 的 ID 才能将其迁移到目标订阅。 使用[PersonGroupOperationsExtensions. batchmanagementclient.account.listasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)方法检索 person group 对象的列表。 然后获取[person group. PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)属性。 根据你拥有的 Person group 对象，此过程的外观有所不同。 在本指南中，源 Person group ID 存储在 `personGroupId`中。

> [!NOTE]
> [示例代码](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)创建并训练要迁移的新 person group。 在大多数情况下，你应该已经有了要使用的 Person group。

## <a name="take-a-snapshot-of-a-persongroup"></a>拍摄 Person group 的快照

快照是适用于某些面部数据类型的临时远程存储。 它可用作一种剪贴板，用于将数据从一个订阅复制到另一个订阅。 首先，拍摄源订阅中数据的快照。 然后，将其应用于目标订阅中的新数据对象。

使用源订阅的 FaceClient 实例获取 Person group 的快照。 将[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)与 person group id 和目标订阅的 id 结合使用。 如果有多个目标订阅，请将其添加为第三个参数中的数组项。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 拍摄和应用快照的过程不会中断对源或目标 Persongroup 现或 FaceLists 的任何常规调用。 请勿进行同时进行的调用来更改源对象，例如[FaceList 管理调用](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或[person group 训练](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)调用。 快照操作可能在这些操作之前或之后运行，或者可能会遇到错误。

## <a name="retrieve-the-snapshot-id"></a>检索快照 ID

用于获取快照的方法是异步的，因此您必须等待其完成。 无法取消快照操作。 在此代码中，`WaitForOperation` 方法监视异步调用。 它每隔100毫秒检查一次状态。 操作完成后，通过分析 `OperationLocation` 字段来检索操作 ID。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

典型 `OperationLocation` 值如下所示：

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

在 "操作状态" 显示 `Succeeded`后，通过分析返回的 OperationStatus 实例的 `ResourceLocation` 字段获取快照 ID。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

典型 `resourceLocation` 值如下所示：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>将快照应用于目标订阅

接下来，使用随机生成的 ID 在目标订阅中创建新的 Person group。 然后，使用目标订阅的 FaceClient 实例将快照应用于此 Person group。 传入快照 ID 和新的 Person group ID。

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 快照对象仅在48小时内有效。 仅当你想要在以后将其用于数据迁移时才拍摄快照。

快照应用请求返回另一个操作 ID。 若要获取此 ID，请分析返回的 applySnapshotResult 实例的 `OperationLocation` 字段。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

快照应用程序进程也是异步的，因此再次使用 `WaitForOperation` 等待它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>测试数据迁移

应用快照后，目标订阅中的新 Person group 将使用原始的面部数据进行填充。 默认情况下，还会复制定型结果。 新 Person group 已准备好进行面部标识调用，无需重新训练。

若要测试数据迁移，请运行以下操作，并将它们打印的结果与控制台进行比较：

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

现在，你可以使用目标订阅中的新 Person group。 

若要在将来再次更新目标 Person group，请创建新的 Person group 来接收快照。 为此，请按照本指南中的步骤进行操作。 单个 Person group 对象只能将一个快照应用于一次。

## <a name="clean-up-resources"></a>清理资源

完成数据的迁移后，手动删除快照对象。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>后续步骤

接下来，请参阅相关的 API 参考文档，浏览使用快照功能的示例应用，或按照操作方法指南，开始使用此处提到的其他 API 操作：

- [快照参考文档 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [面部快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [添加人脸](how-to-add-faces.md)
- [检测图像中的人脸](HowtoDetectFacesinImage.md)
- [识别图像中的人脸](HowtoIdentifyFacesinImage.md)
