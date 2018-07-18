---
title: 使用人脸 API 标识图像中的人脸 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 使用认知服务中的人脸 API 标识图像中的人脸。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366730"
---
# <a name="how-to-identify-faces-in-images"></a>如何标识图像中的人脸

本指南演示如何使用事先根据已知人员创建的 PersonGroup 来标识未知人脸。 示例是利用人脸 API 客户端库采用 C# 编写的。

## <a name="concepts"></a> 概念

如果不熟悉本指南中的以下概念，请随时搜索[术语表](../Glossary.md)中的定义：

- 人脸 - 检测
- 人脸 - 标识
- PersonGroup

## <a name="preparation"></a> 准备

本示例演示以下操作：

- 如何创建 PersonGroup - 此 PersonGroup 包含已知人员的列表。
- 如何为每个人分配人脸 - 这些人脸用作标识人的基线。 建议使用清楚的正面照，就像证件照一样。 合格的照片集应该包含同一人不同姿势、不同衣服颜色或不同发型时的人脸。

若要开始演示本示例，需准备一批照片：

- 一些包含人脸的照片。 [单击此处下载示例照片](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)（分别为 Anna、Bill 和 Clare 的照片）。
- 一系列测试照片，可能包含或不包含 Anna、Bill 或 Clare 的人脸，用于测试身份。 也可从前面的链接选择一些示例图片。

## <a name="step1"></a> 步骤 1：授权 API 调用

每次调用人脸 API 都需要订阅密钥。 此密钥可以通过查询字符串参数传递，或在请求头中指定。 若要通过查询字符串传递订阅密钥，请参阅充当示例的[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)的请求 URL：
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

也可在 HTTP 请求头中指定订阅密钥：**ocp-apim-subscription-key：&lt;订阅密钥&gt;**。使用客户端库时，订阅密钥通过 FaceServiceClient 类的构造函数传入。 例如：
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
可从 Azure 门户的“市场”页中获取订阅密钥。 请参阅[订阅](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="step2"></a> 步骤 2：创建 PersonGroup

在此步骤中，我们创建了名为“MyFriends”的 PersonGroup，其中包含三个人：Anna、Bill 和 Clare。 每个人都注册了多个人脸。 需在图片中检测这些人脸。 执行所有这些步骤以后，就有了一个 PersonGroup，如下图所示：

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 定义 PersonGroup 的人
人是标识的基本单元。 一个人可以注册一个或多个已知人脸。 不过，PersonGroup 是人的集合，每个人都是在特定的 PersonGroup 中定义的。 标识是针对 PersonGroup 进行的。 因此，任务就是先创建一个 PersonGroup，然后在其中创建人，例如 Anna、Bill、Clare。

首先，需创建新的 PersonGroup。 请使用 [PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API 来执行此操作。 相应的客户端库 API 是 FaceServiceClient 类的 CreatePersonGroupAsync 方法。 指定用于创建组的组 ID 对每个订阅来说都是唯一的 - 也可使用其他 PersonGroup API 来获取、更新或删除 PersonGroup。 定义组之后，即可使用 [PersonGroup 人 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 在组中定义人。 客户端库方法是 CreatePersonAsync。 可以在创建每个人之后向其添加人脸。

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 检测人脸并将其注册到正确的人
进行检测时，会向[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 发送“POST”Web 请求，将图像文件置于 HTTP 请求正文中。 使用客户端库时，人脸检测是通过 FaceServiceClient 类的 DetectAsync 方法执行的。

每检测到一个人脸，就可以调用 [PersonGroup 人 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)，将其添加到正确的人。

以下代码演示了在图像中检测人脸并将其添加到人这一过程：
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
请注意，如果图像包含多个人脸，则只添加最大的人脸。 若要向该人添加其他人脸，可以将“targetFace = left, top, width, height”格式的字符串传递给 [PersonGroup 人 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 的 targetFace 查询参数，也可以使用 AddPersonFaceAsync 方法的 targetFace 可选参数。 添加到该人的每个人脸都会获得唯一的持久人脸 ID，该 ID 可以在 [PersonGroup 人 - 删除人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e)和[人脸 - 标识](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中使用。
## <a name="step3"></a> 步骤 3：训练 PersonGroup

必须先训练 PersonGroup，然后才能使用它来进行标识。 另外，如果添加或删除了任何人，或者任何人编辑了其注册的人脸，则必须重新训练 PersonGroup。 训练由 [PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API 完成。 使用客户端库时，只需调用 TrainPersonGroupAsync 方法：
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
训练是一个异步过程。 即使 TrainPersonGroupAsync 方法已经返回，它也可能还未完成。 可能需要通过 [PersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API 或客户端库的 GetPersonGroupTrainingStatusAsync 方法来查询训练状态。 下面的代码演示了一个等待 PersonGroup 训练完成的简单逻辑：
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step4"></a> 步骤 4：根据定义的 PersonGroup 标识人脸
进行标识时，人脸 API 可以计算某个测试人脸与组中所有人脸的相似度，然后返回与该测试人脸最有可比性的人。 这是通过[人脸 - 标识](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API 或客户端库的 IdentifyAsync 方法完成的。

测试人脸需使用前面的步骤进行测试，然后需将人脸 ID 作为另一个参数传递给标识 API。 可以立刻标识多个人脸 ID，结果会包含所有标识结果。 默认情况下，标识 API 仅返回与测试人脸最匹配的一个人。 可以根据需要指定可选参数 maxNumOfCandidatesReturned，让标识 API 返回更多候选者。

以下代码演示标识过程：
```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
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
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

完成这些步骤后，可以尝试标识不同的人脸，看能否根据上传的用于人脸检测的图像来正确标识 Anna、Bill 或 Clare 的人脸。 请看以下示例：

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> 步骤 5：请求大规模标识

众所周知，由于以前的设计的限制，一个 PersonGroup 最多只能包含 10,000 个人。
若要详细了解高达百万人规模的方案，请参阅[如何使用大规模功能](how-to-use-large-scale.md)。

## <a name="summary"></a> 摘要

本指南介绍了如何创建 PersonGroup 并标识某个人。 以下是前面解释和演示的功能的快速提醒：

- 使用[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API 检测人脸
- 使用 [PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API 创建 PersonGroup
- 使用 [PersonGroup 人 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 创建人
- 使用 [PersonGroup - 训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API 训练 PersonGroup
- 使用[人脸 - 标识](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API 根据 PersonGroup 标识未知人脸

## <a name="related"></a> 相关主题

- [如何检测图像中的人脸](HowtoDetectFacesinImage.md)
- [如何添加人脸](how-to-add-faces.md)
- [如何使用大规模功能](how-to-use-large-scale.md)
