---
title: 跨订阅迁移人脸数据 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 本指南介绍了如何将已存储的人脸数据从一个人脸 API 订阅迁移到另一个人脸 API 订阅。
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913791"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>将人脸数据迁移到其他人脸订阅

本指南演示如何将人脸数据，例如，使用人脸，一个已保存 person Group 对象移动到不同的 Azure 认知服务人脸 API 订阅。 若要移动数据，您可以使用快照功能。 这种方式避免必须反复构建和训练 PersonGroup 或 FaceList 对象时将移动或扩展您的操作。 例如，可能是你使用免费试用版订阅创建了一个 person Group 对象，现在想要将其迁移到付费版订阅。 或者，可能需要将人脸数据同步跨大型企业操作的区域。

此相同的迁移策略也适用于大型人物组和大型人脸列表对象。 如果您不熟悉本指南中的概念，请参阅在其定义[人脸识别概念](../concepts/face-recognition.md)指南。 本指南结合使用人脸 API .NET 客户端库与 C#。

## <a name="prerequisites"></a>必备组件

需要以下项：

- 两个人脸 API 订阅密钥，一个与现有数据，另一个要迁移到。 若要订阅人脸 API 服务并获取你的密钥，请按照中的说明[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。
- 对应于目标订阅人脸 API 订阅 ID 字符串。 若要找到它，请选择**概述**在 Azure 门户中。 
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

本指南使用一个简单的控制台应用程序运行人脸数据迁移。 有关完整实现，请参阅[人脸 API 快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)GitHub 上。

1. 在 Visual Studio 中，创建一个新的控制台应用.NET Framework 项目。 其命名为**FaceApiSnapshotSample**。
1. 获取所需的 NuGet 包。 右键单击你的项目在解决方案资源管理器，然后选择**管理 NuGet 包**。 选择**浏览**选项卡，然后选择**包括预发行版**。 查找并安装以下包：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>创建人脸客户端

在中**Main**中的方法*Program.cs*，创建两个[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)用于源和目标订阅的实例。 此示例在东亚区域中使用人脸订阅，作为源，并为目标的美国西部订阅。 此示例演示如何将数据从一个 Azure 区域迁移到另一个。 如果你的订阅是在不同区域中，更改`Endpoint`字符串。

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

填写订阅密钥值和用于源和目标订阅的终结点 Url。


## <a name="prepare-a-persongroup-for-migration"></a>让 PersonGroup 做好迁移准备

需要源订阅迁移到目标订阅中的 person Group 的 ID。 使用[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)方法来检索 person Group 对象的列表。 然后获取[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)属性。 此过程看起来不同根据哪些 person Group 包含的对象。 在本指南中，源 person Group ID 存储在`personGroupId`。

> [!NOTE]
> [示例代码](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)创建并定型新的 person Group，迁移。 在大多数情况下，您应该已经使用 person Group。

## <a name="take-a-snapshot-of-a-persongroup"></a>Person Group 的快照

快照是临时的远程存储，对于某些人脸数据类型。 它可用作一种剪贴板，用于将数据从一个订阅复制到另一个订阅。 首先，源订阅中需要数据的快照。 然后您将其应用于目标订阅中的新数据对象。

使用源订阅 FaceClient 实例来创建 person Group 的快照。 使用[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)与 person Group ID 和目标订阅的 id。 如果你有多个目标订阅，请将它们添加为第三个参数中的数组项。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 获取并应用快照的过程不会中断调用任何常规的源或目标 Persongroup 或 FaceLists。 不进行更改的源对象，例如的同时调用[FaceList 管理调用](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或[person Group 训练](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)调用，例如。 快照操作之前或之后这些操作可能会运行，或可能会遇到错误。

## <a name="retrieve-the-snapshot-id"></a>检索快照 ID

用来拍摄快照的方法是异步的因此你必须等待其完成。 不能取消快照操作。 在此代码中，`WaitForOperation`方法将监视异步调用。 它会检查状态每隔 100 毫秒。 在操作完成后，通过分析检索操作 ID`OperationLocation`字段。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

典型`OperationLocation`值如下所示：

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

之后的操作状态将显示在`Succeeded`，通过分析获取快照 ID`ResourceLocation`返回 OperationStatus 实例的字段。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

典型`resourceLocation`值如下所示：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>将快照应用于目标订阅

接下来，使用一个随机生成的 id。 在目标订阅中创建新 person Group 然后使用目标订阅的 FaceClient 实例将快照应用于此 person Group。 在快照中传递，ID 和新 person Group id。

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 仅为 48 小时，快照对象有效。 如果你想要用于数据迁移仅拍摄快照不久后终止。

快照应用请求返回另一个操作 id。 若要获取此 ID，分析`OperationLocation`返回的 applySnapshotResult 实例的字段。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

快照应用程序也是异步，因此再次使用`WaitForOperation`等待它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>测试数据迁移

在应用快照后，在目标订阅中新的 person Group 使用原始的人脸数据填充。 默认情况下，训练结果也会被复制。 新 person Group 是准备好进行人脸标识调用，而无需重新训练。

若要测试数据迁移，请运行以下操作，并比较的结果，它们会打印到控制台：

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

现在你可以在目标订阅中使用新 person Group。 

若要在将来再次更新目标 person Group，创建新的 person Group，若要接收该快照。 若要执行此操作，请执行本指南中的步骤。 单个 person Group 对象可以一次只能应用于它的快照。

## <a name="clean-up-resources"></a>清理资源

在完成迁移后面临着数据，请手动删除快照对象。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>后续步骤

接下来，请参阅相关 API 参考文档，了解使用快照功能的示例应用程序或按照开始使用此处所述的其他 API 操作的操作方法指南：

- [快照参考文档 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [人脸 API 快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [添加人脸](how-to-add-faces.md)
- [检测图像中的人脸](HowtoDetectFacesinImage.md)
- [识别图像中的人脸](HowtoIdentifyFacesinImage.md)
