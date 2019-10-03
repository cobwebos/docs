---
title: 使用 Azure 媒体服务编码时，子剪辑视频
description: 本主题介绍如何使用 Azure 媒体服务.NET SDK 编码时，子剪辑视频
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305074"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>子剪辑时使用媒体服务的.NET 编码的视频

您可以修整或使用对其进行编码时，子剪辑视频[作业](https://docs.microsoft.com/rest/api/media/jobs)。 此功能适用于任何[转换](https://docs.microsoft.com/rest/api/media/transforms)生成使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)预设，或者[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)预设。

以下C#的示例创建修剪资产中的视频，因为它将提交编码作业的作业。 

## <a name="prerequisites"></a>必备组件

若要完成本主题中所述的步骤，必须：

- [创建 Azure 媒体服务帐户](create-account-cli-how-to.md)
- 创建转换，并输入和输出资产。 您可以了解如何创建转换，并在输入和输出资产[上传、 编码和流式处理视频使用.NET](stream-files-tutorial-with-api.md)教程。
- 审阅[编码概念](encoding-concept.md)主题。

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

[如何进行编码自定义转换](customize-encoder-presets-how-to.md) 
