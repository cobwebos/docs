---
title: 创建有多个转换输出的 Azure 媒体服务作业
description: 本主题演示如何创建有多个转换输出的 Azure 媒体服务作业。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: e5fe12b02503aff625a1cc2c39a71abcdf3dab0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296770"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>创建有多个转换输出的作业

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本主题说明如何创建有两个转换输出的转换。 第一个调用要编码的输入，以便使用内置 [AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) 预设进行自适应比特率流式处理。 第二个调用输入视频中的音频信号，该信号使用 [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets) 进行处理。 创建转换后，可以提交作业来相应地处理视频。 由于在此示例中我们要指定两个转换输出，因此必须指定两个作业输出。 可以选择将两个作业输出定向到相同的资产（如下所示），也可以将结果写入单独的资产。
 

> [!TIP]
> 在开始开发之前，请查看[使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)（包括有关访问 API、命名约定等的信息）

## <a name="create-a-transform"></a>创建转换

下面的代码演示如何创建产生两个输出的转换。

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```
## <a name="submit-a-job"></a>提交作业

使用一个 HTTPS URL 输入和两个作业输出创建一个作业。

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](/rest/api/media/jobs/get#joberrorcode)。

## <a name="next-steps"></a>后续步骤

[使用 .NET 的 Azure 媒体服务 v3 示例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
