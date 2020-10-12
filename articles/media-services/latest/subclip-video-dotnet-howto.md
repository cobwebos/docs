---
title: 使用 Azure 媒体服务编码时对视频进行子剪辑
description: 本主题说明如何在使用 .NET SDK 通过 Azure 媒体服务进行编码时对视频进行子剪辑
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1725fab30ddb4d40e0bf5345683588c878658052
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296560"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>使用媒体服务编码时对视频进行子剪辑 - .NET

在使用[作业](/rest/api/media/jobs)对视频进行编码时，可以对其进行剪裁或子剪辑。 此功能适用于使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 预设或 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 预设生成的任何[转换](/rest/api/media/transforms)。

下面的 C# 示例创建一个作业，该作业在提交编码作业时剪裁资产中的视频。 

## <a name="prerequisites"></a>必备条件

若要完成本主题中所述的步骤，必须：

- [创建 Azure 媒体服务帐户](./create-account-howto.md)
- 创建转换以及输入和输出资产。 可以在[使用 .NET 对视频进行上传、编码和流式处理](stream-files-tutorial-with-api.md)中了解如何创建转换以及输入和输出资产。
- 查看[编码概念](encoding-concept.md)主题。

## <a name="example"></a>示例

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>后续步骤

[如何对自定义转换进行编码](customize-encoder-presets-how-to.md) 
