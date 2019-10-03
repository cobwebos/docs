---
title: 示例：将人脸添加到 PersonGroup - 人脸 API
titleSuffix: Azure Cognitive Services
description: 使用人脸 API 添加图像中的人脸。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0415dcae08c188c1758150c4b8b0df4dee014ce6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448599"
---
# <a name="add-faces-to-a-persongroup"></a>将人脸添加到 PersonGroup

本指南演示了如何将大量人员和人脸添加到 PersonGroup 对象。 此同一策略还适用于 LargePersonGroup、FaceList 和 LargeFaceList 对象。 此示例是通过 C# 使用 Azure 认知服务人脸 API .NET 客户端库编写的。

## <a name="step-1-initialization"></a>步骤 1：初始化

下面的代码声明了多个变量并实现了一个帮助程序函数来调度人脸添加请求：

- `PersonCount` 是总人数。
- `CallLimitPerSecond` 是因订阅层而异的每秒调用数上限。
- `_timeStampQueue` 是用于记录请求时间戳的队列。
- `await WaitCallLimitPerSecondAsync()` 将等待，直至可以发送下一请求。

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>步骤 2：授权 API 调用

使用客户端库时，必须将订阅密钥传递给 **FaceClient** 类的构造函数。 例如：

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

若要获取订阅密钥，请从 Azure 门户转到 Azure 市场。 有关详细信息，请参阅[订阅](https://www.microsoft.com/cognitive-services/sign-up)。

## <a name="step-3-create-the-persongroup"></a>步骤 3：创建 PersonGroup

创建用于保存人员的 PersonGroup，命名为“MyPersonGroup”。
为了确保整体验证，请求时间排入 `_timeStampQueue` 队列。

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>步骤 4：为 PersonGroup 创建人员

可同时创建所有人员，为避免超出调用限制，还会应用 `await WaitCallLimitPerSecondAsync()`。

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>步骤 5：向人员添加人脸

添加到不同人员的人脸是并发处理的。 为单个特定人员添加的人脸是按顺序处理的。
同样，为了确保请求频率在限制范围内，还会调用 `await WaitCallLimitPerSecondAsync()`。

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>摘要

在本指南中，你已学习了如何创建包含大量人员和人脸的 PersonGroup。 请注意以下几点：

- 此策略也适用于 FaceLists 和 LargePersonGroups。
- 为 LargePersonGroups 中的不同 FaceLists 或人员添加或删除人脸是并发处理的。
- 为 LargePersonGroup 中的某个特定 FaceList 或人员添加或删除人脸是按顺序处理的。
- 为简单起见，本指南省略了如何处理潜在的异常。 若要提高可靠性，请应用适当的重试策略。

其中解释并演示了以下功能：

- 使用 [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API 创建 PersonGroup。
- 使用 [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 创建人员。
- 使用 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 向人员添加人脸。

## <a name="related-topics"></a>相关主题

- [识别图像中的人脸](HowtoIdentifyFacesinImage.md)
- [检测图像中的人脸](HowtoDetectFacesinImage.md)
- [使用大规模功能](how-to-use-large-scale.md)
