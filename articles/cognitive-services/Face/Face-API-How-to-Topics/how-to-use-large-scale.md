---
title: 示例：使用大规模功能 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 使用人脸 API 中的大规模功能。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 5a4085f713d66859a464ab59b00d856921db8ec3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124485"
---
# <a name="example-use-the-large-scale-feature"></a>示例：使用大规模使用的功能

本指南是有关如何从现有 PersonGroup 和 FaceList 对象分别纵向扩展到 LargePersonGroup 和 LargeFaceList 对象的高级文章。 本指南演示迁移过程， 其假设读者基本熟悉 PersonGroup 和 FaceList 对象、[训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)操作和人脸识别功能。 若要详细了解这些主题，请参阅[人脸识别](../concepts/face-recognition.md)概念指南。

LargePersonGroup 和 LargeFaceList 统称为大规模操作。 LargePersonGroup 最多可以包含 100 万个人，其中每个人最多有 248 张人脸。 LargeFaceList 最多可以包含 100 万张人脸。 大规模操作类似于传统的 PersonGroup 和 FaceList，但因采用新体系结构而有一些差异。 

这些示例是使用 Azure 认知服务人脸 API 客户端库以 C# 编写的。

> [!NOTE]
> 为了在大规模的 Identification 和 FindSimilar 操作中提高人脸搜索的性能，我们引入了一个“训练”操作用于预处理 LargeFaceList 和 LargePersonGroup。 训练时间从几秒到约半小时不等，具体取决于实际容量。 如果以前的某个训练操作成功，则在训练期间，可以执行 Identification 和 FindSimilar。 缺点在于，在完成迁移到大规模训练的最新后处理前，新添加的人员和人脸不会出现在结果中。

## <a name="step-1-initialize-the-client-object"></a>步骤 1：初始化客户端对象

使用人脸 API 客户端库时，订阅密钥和订阅终结点将通过 FaceServiceClient 类的构造函数传入。 例如：

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

若要获取订阅密钥及其相应的终结点，请从 Azure 门户转到 Azure 市场。
有关详细信息，请参阅[订阅](https://azure.microsoft.com/services/cognitive-services/directory/vision/)。

## <a name="step-2-code-migration"></a>步骤 2：代码迁移

本部分重点介绍如何将 PersonGroup 或 FaceList 实现迁移到 LargePersonGroup 或 LargeFaceList。 虽然 LargePersonGroup 或 LargeFaceList 与 PersonGroup 或 FaceList 在设计和内部实现方面存在差异，但用于后向兼容的 API 接口是类似的。

不支持数据迁移。 请改为重新创建 LargePersonGroup 或 LargeFaceList。

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>将 PersonGroup 迁移到 LargePersonGroup

从 PersonGroup 迁移到 LargePersonGroup 的过程较为简单。 它们采用完全相同的组级别操作。

对于 PersonGroup 或 Person 相关的实现，只需将 API 路径或 SDK 类/模块更改为 LargePersonGroup 和 LargePersonGroup Person。

将 PersonGroup 中的所有人脸和人员添加到新的 LargePersonGroup。 有关详细信息，请参阅[添加人脸](how-to-add-faces.md)。

### <a name="migrate-a-facelist-to-a-largefacelist"></a>将 FaceList 迁移到 LargeFaceList

| FaceList API | LargeFaceList API |
|:---:|:---:|
| 创建 | 创建 |
| 删除 | 删除 |
| 获取 | 获取 |
| 列出 | 列出 |
| 更新 | 更新 |
| - | 定型 |
| - | 获取定型状态 |

上表对 FaceList 和 LargeFaceList 的列级操作进行了对比。 如表中所示，与 FaceList 相比，LargeFaceList 附带了新的操作（“训练”和“获取训练状态”）。 训练 LargeFaceList 是 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 操作的前提条件。 FaceList 不需要训练。 以下代码片段是一个用于等待训练 LargeFaceList 的帮助器函数：

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

以前，添加了人脸的 FaceList 和 FindSimilar 的典型用法如下所示：

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

将它迁移到 LargeFaceList 时，它会变成：

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

如上所示，数据管理和 FindSimilar 部分几乎一样。 唯一的例外是，全新的预处理训练操作必须在 LargeFaceList 中完成，然后 FindSimilar 才能正常工作。

## <a name="step-3-train-suggestions"></a>步骤 3：训练建议

尽管训练操作可以加快 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 和 [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)，但训练时间非常漫长，尤其是涉及大规模操作时。 下表列出了不同规模的估计训练时间。

| 人脸或人员的规模 | 估计训练时间 |
|:---:|:---:|
| 1,000 | 1-2 秒 |
| 10,000 | 5-10 秒 |
| 100,000 | 1-2 分钟 |
| 1,000,000 | 10-30 分钟 |

为了更好地利用大规模功能，我们建议采用以下策略。

### <a name="step-31-customize-time-interval"></a>步骤 3.1：自定义时间间隔

如 `TrainLargeFaceList()` 中所示，某个以毫秒为单位的时间间隔可以延迟无限期的训练状态检查过程。 对于包含更多人脸的 LargeFaceList，使用较大间隔可减少调用计数和成本。 请根据 LargeFaceList 的预期容量自定义该时间间隔。

同样的策略也适用于 LargePersonGroup。 例如，在训练包含 100 万人的 LargePersonGroup 时，`timeIntervalInMilliseconds` 可能为 60,000（即 1 分钟间隔）。

### <a name="step-32-small-scale-buffer"></a>步骤 3.2：小规模缓冲区

LargePersonGroup 或 LargeFaceList 中的人员/人脸仅在训练后才可搜索。 在动态方案中，新人员或人脸会不断增加，且必须立即可供搜索，但训练时间可能超过所需时间。 

若要缓解此问题，请仅对新添加的条目使用额外的小规模 LargePersonGroup 或 LargeFaceList 作为缓冲区。 由于规模较小，此缓冲区所需训练时间较短。 在此临时缓冲区中应可实现即时搜索功能。 在使用此缓冲区的同时，通过按稀疏间隔执行主训练来对主 LargePersonGroup 或 LargeFaceList 执行训练。 例如，在午夜执行或每日执行一次。

示例工作流：

1. 创建一个主 LargePersonGroup 或 LargeFaceList（主集合）。 和一个缓冲区 LargePersonGroup 或 LargeFaceList（缓冲区集合）。 缓冲区集合仅用于新添加的人员或人脸。
1. 同时向主集合和缓冲区集合添加新人员或人脸。
1. 仅按短时间间隔训练缓冲区集合，以确保新添加的条目生效。
1. 同时对主集合和缓冲区集合调用 Identification 或 FindSimilar。 合并结果。
1. 当缓冲区集合大小增加到阈值或在系统空闲时，创建新的缓冲区集合。 对主集合触发训练操作。
1. 完成对主集合的训练操作后，删除旧的缓冲区集合。

### <a name="step-33-standalone-training"></a>步骤 3.3：独立训练

如果可以接受相对较长的时间延迟，则不需要在添加新数据后立即触发训练操作。 相反，可从主逻辑中拆分定型操作并定期触发该操作。 此策略适用于可接受延迟的动态方案。 可将它应用到静态方案，以进一步降低训练频率。

假设存在类似于 `TrainLargeFaceList` 的 `TrainLargePersonGroup` 函数。 通过调用 `System.Timers` 中的 [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) 类，针对 LargePersonGroup 的独立训练的典型实现为：

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

有关数据管理和识别相关实现的详细信息，请参阅[添加人脸](how-to-add-faces.md)和[在图像中识别人脸](HowtoIdentifyFacesinImage.md)。

## <a name="summary"></a>摘要

本指南介绍了如何将现有 PersonGroup 或 FaceList 代码（不是数据）迁移到 LargePersonGroup 或 LargeFaceList：

- LargePersonGroup 和 LargeFaceList 的工作原理类似于 PersonGroup 或 FaceList，但 LargeFaceList 需要训练操作。
- 采取适当的训练策略可对大规模数据集执行动态数据更新。

## <a name="next-steps"></a>后续步骤

请遵循操作指南了解如何将人脸添加到 PersonGroup，或针对 PersonGroup 执行“识别”操作。

- [添加人脸](how-to-add-faces.md)
- [在图像中识别人脸](HowtoIdentifyFacesinImage.md)