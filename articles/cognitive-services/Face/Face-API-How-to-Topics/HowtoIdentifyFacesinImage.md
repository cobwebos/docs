---
title: 示例：识别图像中的人脸 - 人脸
titleSuffix: Azure Cognitive Services
description: 本指南演示如何使用事先根据已知人员创建的 PersonGroup 对象来识别未知的人脸。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169920"
---
# <a name="example-identify-faces-in-images"></a>示例：在图像中识别人脸

本指南演示如何使用事先根据已知人员创建的 PersonGroup 对象来识别未知的人脸。 这些示例是使用 Azure 认知服务人脸客户端库以 C# 编写的。

## <a name="preparation"></a>准备工作

此示例演示：

- 如何创建 PersonGroup。 此 PersonGroup 包含已知人员的列表。
- 如何为每个人员分配人脸。 这些人脸用作识别人员的基线。 我们建议使用清晰的人脸正面照， 例如证件照。 合适的照片包含姿势不同、衣服颜色不同或发型不同的同一个人的人脸。

若要执行本示例，请准备好：

- 一些包含人脸的照片。 [下载](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna、Bill 和 Clare 的示例照片。
- 一系列测试照片。 照片可以包含或不包含 Anna、Bill 或 Clare 的人脸。 它们用于测试识别功能。 另外，请通过前面的链接选择一些示例图像。

## <a name="step-1-authorize-the-api-call"></a>步骤 1：授权 API 调用

每次调用人脸 API 都需要订阅密钥。 此密钥可以通过查询字符串参数传递，或在请求标头中指定。 若要通过查询字符串传递订阅密钥，请参阅 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 的请求 URL 示例：
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

作为替代方法，也可以在 HTTP 请求标头 **ocp-apim-subscription-key:&lt;订阅密钥&gt;** 中指定订阅密钥。
使用客户端库时，订阅密钥通过 FaceClient 类的构造函数传入。 例如：
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
若要获取订阅密钥，请从 Azure 门户转到 Azure 市场。 有关详细信息，请参阅[订阅](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="step-2-create-the-persongroup"></a>步骤 2：创建 PersonGroup

在此步骤中，名为“MyFriends”的 PersonGroup 包含 Anna、Bill 和 Clare。 每个人都注册了多个人脸。 必须在图像中检测这些人脸。 执行所有这些步骤以后，就有了一个 PersonGroup，如下图所示：

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>步骤 2.1：定义 PersonGroup 的人员
人是标识的基本单元。 一个人可以注册一个或多个已知人脸。 PersonGroup 是人员的集合。 每个人在特定的 PersonGroup 中定义。 识别是针对 PersonGroup 进行的。 任务是先创建一个 PersonGroup，然后在其中创建人员，例如 Anna、Bill、Clare。

首先，使用 [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API 创建新的 PersonGroup。 相应的客户端库 API 是 FaceClient 类的 CreatePersonGroupAsync 方法。 指定的用于创建组的组 ID 对于每个订阅都是唯一的。 还可以使用其他 PersonGroup API 来获取、更新或删除 PersonGroup。 

定义组之后，可以使用 [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 在该组中定义人员。 客户端库方法是 CreatePersonAsync。 可以在创建每个人之后向其添加人脸。

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a>步骤 2.2：检测人脸并将其注册到正确的人
进行检测时，会向[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 发送“POST”Web 请求，将图像文件置于 HTTP 请求正文中。 使用客户端库时，人脸检测是通过 FaceClient 类的 Detect..Async 方法之一完成的。

对于检测到的每个人脸，调用 [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 将其添加到正确的人。

以下代码演示了在图像中检测人脸并将其添加到人这一过程：

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
如果图像包含多个人脸，只会添加最大的人脸。 可将其他人脸添加到该人。 将“targetFace = left, top, width, height”格式的字符串传递给 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 的 targetFace 查询参数。 也可以使用 AddPersonFaceAsync 方法的 targetFace 可选参数来添加其他人脸。 添加到该人的每个人脸都会获得唯一的持久人脸 ID。 可以在 [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) 和 [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 中使用此 ID。

## <a name="step-3-train-the-persongroup"></a>步骤 3：训练 PersonGroup

必须先训练 PersonGroup，然后才能使用它来进行识别。 在添加或删除任何人或者编辑某人的已注册人脸后，必须重新训练 PersonGroup。 训练由 [PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API 完成。 使用客户端库时，会调用 TrainPersonGroupAsync 方法：
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
训练是一个异步过程。 即使 TrainPersonGroupAsync 方法已经返回，它也可能还未完成。 可能需要查询训练状态。 使用 [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API 或客户端库的 GetPersonGroupTrainingStatusAsync 方法。 以下代码演示了一个等待 PersonGroup 训练完成的简单逻辑：
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>步骤 4：根据定义的 PersonGroup 标识人脸

当人脸服务执行识别时，它会计算某个测试人脸与组中所有人脸的相似性。 它将返回与该测试人脸最有可比性的人。 此过程是通过 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API 或客户端库的 IdentifyAsync 方法完成的。

必须使用前面的步骤检测测试人脸。 然后将人脸 ID 作为另一个参数传递给识别 API。 可以一次性识别多个人脸 ID。 结果包含所有识别到的结果。 默认情况下，识别过程仅返回与测试人脸最匹配的一个人。 可根据需要指定可选参数 maxNumOfCandidatesReturned，让识别过程返回更多候选项。

以下代码演示识别过程：

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

完成这些步骤后，请尝试识别不同的人脸。 看看能否根据上传的用于人脸检测的图像来正确识别 Anna、Bill 或 Clare 的人脸。 请看以下示例：

![识别不同的人脸](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>步骤 5：大规模请求

根据以往的设计限制，一个 PersonGroup 最多可以包含 10,000 个人。
若要详细了解高达百万人规模的方案，请参阅[如何使用大规模功能](how-to-use-large-scale.md)。

## <a name="summary"></a>总结

本指南介绍了如何创建 PersonGroup 并识别某个人。 其中解释并演示了以下功能：

- 使用 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API 检测人脸。
- 使用 [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API 创建 PersonGroup。
- 使用 [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 创建人员。
- 使用 [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API 训练 PersonGroup。
- 使用 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API 根据 PersonGroup 识别未知人脸。

## <a name="related-topics"></a>相关主题

- [人脸识别的概念](../concepts/face-recognition.md)
- [检测图像中的人脸](HowtoDetectFacesinImage.md)
- [添加人脸](how-to-add-faces.md)
- [使用大规模功能](how-to-use-large-scale.md)
