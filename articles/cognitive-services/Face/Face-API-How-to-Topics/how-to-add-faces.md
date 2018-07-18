---
title: 使用人脸 API 添加人脸 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 使用认知服务中的人脸 API 在图像中添加人脸。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365458"
---
# <a name="how-to-add-faces"></a>如何添加人脸

本指南介绍了将大量人员和人脸添加到 PersonGroup 的最佳做法。
相同的策略也适用于 FaceList 和 LargePersonGroup。
示例是在 C# 中使用人脸 API 客户端库编写而成。

## <a name="step1"></a> 第 1 步：初始化

声明了几个变量，并实现了用于计划请求的帮助程序函数。

- `PersonCount` 是总人数。
- `CallLimitPerSecond` 是因订阅层而异的每秒调用数上限。
- `_timeStampQueue` 是用于记录请求时间戳的队列。
- `await WaitCallLimitPerSecondAsync()` 会等到发送下一个请求是有效的。

```CSharp
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

## <a name="step2"></a> 第 2 步：授权 API 调用

使用客户端库时，订阅密钥是通过 FaceServiceClient 类的构造函数传入。 例如：

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

可以从 Azure 门户的“市场”页获取订阅密钥。 请参阅[订阅](https://www.microsoft.com/cognitive-services/en-us/sign-up)。

## <a name="step3"></a> 第 3 步：创建 PersonGroup

创建用于保存人员的 PersonGroup，命名为“MyPersonGroup”。
为了确保整体验证，请求时间排入 `_timeStampQueue` 队列。

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> 第 4 步：创建添加到 PersonGroup 中的人员

可同时创建所有人员。为避免超出调用限制，还会应用 `await WaitCallLimitPerSecondAsync()`。

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> 第 5 步：向人员添加人脸

虽然可同时处理向不同人员添加人脸，但对于某个人来说，处理则是依序的。
同样，为了确保请求频率在限制范围内，还会调用 `await WaitCallLimitPerSecondAsync()`。

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a> 总结

本指南介绍了如何创建包含大量人员和人脸的 PersonGroup。 请注意以下几点：

- 此策略也适用于 FaceList 和 LargePersonGroup。
- 可同时处理在 LargePersonGroup 中添加/删除不同 FaceList 或 Person 的人脸。
- 应依序完成对 LargePersonGroup 中某个 FaceList 或 Person 执行的一些操作。
- 为简单起见，本指南省略了潜在异常处理。 若要提高可靠性，应该应用适当的重试策略。

下面快速提示了之前介绍和展示的功能：

- 使用 [PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API 创建 PersonGroup
- 使用 [PersonGroup 人员 - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 创建人员
- 使用 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 向人员添加人脸

## <a name="related"></a> 相关主题
- [如何识别图像中的人脸](HowtoIdentifyFacesinImage.md)
- [如何检测图像中的人脸](HowtoDetectFacesinImage.md)
- [如何使用大规模功能](how-to-use-large-scale.md)
