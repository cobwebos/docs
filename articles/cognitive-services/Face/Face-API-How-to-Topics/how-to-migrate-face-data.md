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
ms.openlocfilehash: 95b339e8d7f2c5c63c30e002411152b50cece2a5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448775"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>将人脸数据迁移到其他人脸订阅

本指南介绍了如何使用快照功能将人脸数据（如已保存的人脸 PersonGroup）迁移到其他人脸 API 订阅。 这样一来，就不用在迁移或扩展操作时重复生成和训练 PersonGroup 或 FaceList 了。 例如，可能已使用免费试用订阅创建了 PersonGroup，现在要将它迁移到付费订阅，或者可能需要跨区域同步人脸数据，以便执行大规模的企业操作。

这种相同的迁移策略也适用于 LargePersonGroup 和 LargeFaceList 对象。 如果不熟悉本指南中的概念，请查看[术语表](../Glossary.md)中的相关概念定义。 本指南结合使用人脸 API .NET 客户端库与 C#。

## <a name="prerequisites"></a>必备组件

- 两个人脸 API 订阅密钥（一个包含现有数据，另一个是迁移目标）。 请按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明操作，订阅人脸 API 服务并获取密钥。
- 对应于目标订阅的人脸 API 订阅 ID 字符串（位于 Azure 门户上的“概览”边栏选项卡中）。 
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。


## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

本指南使用简单的控制台应用来执行人脸数据迁移。 有关完整实现，请参阅 GitHub 上的[人脸 API 快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)。

1. 在 Visual Studio 中，新建“控制台应用(.NET Framework)”项目，并将它命名为“FaceApiSnapshotSample”。 
1. 获取所需的 NuGet 包。 在解决方案资源管理器中，右键单击项目并选择“管理 NuGet 包”。 单击“浏览”选项卡，选择“包括预发行版”，然后找到并安装以下包：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>创建人脸客户端

在 Program.cs 的 Main 方法中，创建两个 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 实例，分别对应于来源订阅和目标订阅。 此示例使用东亚区域的人脸订阅作为来源订阅，并使用美国西部的订阅作为目标订阅。 这会演示如何将数据从一个 Azure 区域迁移到另一个 Azure 区域。 如果订阅位于其他区域，需要更改 `Endpoint` 字符串。

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

需要填写来源订阅和目标订阅的订阅密钥值和终结点 URL。


## <a name="prepare-a-persongroup-for-migration"></a>让 PersonGroup 做好迁移准备

必须有来源订阅中 PersonGroup 的 ID，才能将它迁移到目标订阅。 使用 [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) 方法检索 PersonGroup 对象列表；再获取 [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) 属性。 此过程因 PersonGroup 对象而异。 在本指南中，来源 PersonGroup ID 存储在 `personGroupId` 中。

> [!NOTE]
> [示例代码](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)创建并训练新的 PersonGroup 以供迁移，但在大多数情况下，你应该已有 PersonGroup 可供使用。

## <a name="take-snapshot-of-persongroup"></a>拍摄 PersonGroup 快照

快照是特定人脸数据类型的临时远程存储。 它可用作一种剪贴板，用于将数据从一个订阅复制到另一个订阅。 用户先“拍摄”来源订阅中数据的快照，再将快照“应用”到目标订阅中的新数据对象。

通过结合使用 [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) 与 PersonGroup ID 和目标订阅 ID，使用来源订阅的 FaceClient 实例拍摄 PersonGroup 快照。 如果有多个目标订阅，可以将它们作为数组项添加到第三个参数中。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 拍摄并应用快照的过程不会破坏任何对来源或目标 PersonGroup（或 FaceList）的常规调用。 不过，不建议同时执行更改来源对象的调用（例如，[人脸列表管理调用](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist)或[人员组 - 训练](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup/train)调用），因为快照操作可能会先于或晚于这些操作执行，也可能会遇到错误。 

## <a name="retrieve-the-snapshot-id"></a>检索快照 ID

快照生成方法是异步的因此你将需要等待其完成 （不能取消操作的快照）。 在下面的代码中，`WaitForOperation` 方法监视异步调用，即每 100 毫秒检查一次状态。 当操作完成后，你便能检索操作 ID。 可以通过分析 `OperationLocation` 字段来获取 ID。 

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

当操作状态标记为 `Succeeded` 时，就可以通过分析返回的 OperationStatus 实例的 `ResourceLocation` 字段来获取快照 ID 了。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

典型的 `resourceLocation` 值如下所示：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>将快照应用到目标订阅

接下来，使用随机生成的 ID 在目标订阅中新建 PersonGroup。 然后，使用目标订阅的 FaceClient 实例将快照应用到此 PersonGroup，同时传递快照 ID 和新的 PersonGroup ID。 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 快照对象仅在 48 小时内有效。 只有在打算不久后使用快照进行数据迁移时，才应拍摄快照。

快照应用请求会返回另一个操作 ID。 可以通过分析返回的 applySnapshotResult 实例的 `OperationLocation` 字段来获取此 ID。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

由于快照应用流程也是异步的，因此再次使用 `WaitForOperation` 等待它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>测试数据迁移

在快照应用后，目标订阅中的新 PersonGroup 应填充有原始人脸数据。 默认情况下，还会复制训练结果。因此，新的 PersonGroup 可以执行人脸识别调用，无需重新训练。

若要测试数据迁移，可以执行下面的操作，并比较控制台中打印输出的结果。

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

现在可以开始使用目标订阅中的新 PersonGroup 了。 

日后若要再次更新目标 PersonGroup，需要新建用于接收快照的 PersonGroup（按照本指南中的步骤操作）。 一次只能向一个 PersonGroup 对象应用快照。

## <a name="clean-up-resources"></a>清理资源

建议在完成人脸数据迁移后立即手动删除快照对象。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>相关主题

- [快照参考文档 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [人脸 API 快照示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [如何添加人脸](how-to-add-faces.md)
- [如何检测图像中的人脸](HowtoDetectFacesinImage.md)
- [如何识别图像中的人脸](HowtoIdentifyFacesinImage.md)
