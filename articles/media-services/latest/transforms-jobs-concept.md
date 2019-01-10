---
title: Azure 媒体服务中的转换和作业 | Microsoft Docs
description: 使用媒体服务时，需要创建一个转换来描述处理视频的的规则或规范。 本文概述了何为转换及其使用方法。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742773"
---
# <a name="transforms-and-jobs"></a>转换和作业
 
[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 

[作业](https://docs.microsoft.com/rest/api/media/jobs)是针对 Azure 媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 **作业**指定输入视频位置和输出位置等信息。 可以使用以下各项指定输入视频的位置：HTTPS URL、SAS URL、或[媒体服务资产](https://docs.microsoft.com/rest/api/media/assets)。  

## <a name="typical-workflow"></a>典型工作流

1. 创建转换 
2. 在该转换下提交作业 
3. 列出转换 
4. 请删除将来不打算使用的转换。 

假设希望提取所有视频的第一帧作为缩略图，应采取的步骤如下： 

1. 定义脚本或者用于处理视频的规则 -“使用视频的第一帧作为缩略图”。 
2. 对于每个视频，请告知服务： 
    1. 在何处查找该视频；  
    2. 在何处写入输出缩略图。 

**转换**可帮助你一次性创建脚本（步骤 1），然后使用该脚本提交作业（步骤 2）。

## <a name="transforms"></a>转换

可以直接使用 v3 API 或者使用任何已发布的 SDK 在媒体服务帐户中创建转换。 媒体服务 v3 API 由 Azure 资源管理器驱动，因此，也可以使用资源管理器模板在媒体服务帐户中创建和部署转换。 可以使用基于角色的访问控制来锁定对转换的访问。

### <a name="transform-definition"></a>转换定义

下表显示了转换的属性并给出了其定义。

|名称|Description|
|---|---|
|ID|资源的完全限定的资源 ID。|
|名称|资源的名称。|
|properties.created |创建转换时的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.description |转换的可选详细说明。|
|properties.lastModified |上次更新转换的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.outputs |转换应该生成的一个或多个 TransformOutput 的数组。|
|type|资源的类型。|

有关完整定义，请参阅[转换](https://docs.microsoft.com/rest/api/media/transforms)。

如前所述，转换可帮助你创建用于处理视频的脚本或规则。 单个转换可以应用多个规则。 例如，转换可以指定以给定的比特率将每个视频编码成 MP4 文件，并从该视频的第一帧生成缩略图。 针对要包含在转换中的每个规则，请添加一个 TransformOutput 条目。

> [!NOTE]
> 尽管转换定义支持更新操作，但你应该谨慎确保在所有进行中的作业完成之后才进行修改。 通常，你会更新现有的转换来更改说明，或修改基础 TransformOutputs 的优先级。 若要重新编写脚本，请创建新的转换。

## <a name="jobs"></a>作业

创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 使用事件网格监视事件可以获取作业的进度和状态。 有关详细信息，请参阅[使用事件网格监视事件](job-state-events-cli-how-to.md )。

### <a name="jobs-definition"></a>作业定义

下表显示了作业的属性并给出了其定义。

|名称|Description|
|---|---|
|id|资源的完全限定的资源 ID。|
|名称   |资源的名称。|
|properties.correlationData |客户提供的关联数据（不可变），作为作业和作业输出状态更改通知的一部分返回。 有关详细信息，请参阅[事件架构](media-services-event-schemas.md)<br/><br/>该属性还可用于媒体服务的多租户用法。 可在作业中输入租户 ID。 |
|properties.created |创建作业时的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.description |客户为作业提供的说明（可选）。|
|properties.input|作业的输入。|
|properties.lastModified    |上次更新作业的 UTC 日期和时间，格式为“YYYY-MM-DDThh:mm:ssZ”。|
|properties.outputs|作业的输出。|
|properties.priority    |处理作业的优先级。 较高优先级的作业在较低优先级的作业之前处理。 如果未设置，则默认为正常。|
|properties.state   |作业的当前状态。|
|type   |资源的类型。|

有关完整定义，请参阅[作业](https://docs.microsoft.com/rest/api/media/jobs)。

> [!NOTE]
> 尽管作业定义支持更新操作，但提交作业后可以修改的属性只有说明和优先级。 此外，仅当作业仍处于排队状态时，对优先级所做的更改才有效。 如果作业已开始处理或已完成，则更改优先级不起作用。

### <a name="pagination"></a>分页

媒体服务 v3 支持作业分页。

> [!TIP]
> 应始终使用下一个链接来枚举集合，而不依赖特定的页面大小。

如果查询响应包含许多项，则服务将返回一个“\@odata.nextLink”属性来获取下一页结果。 这可用于逐页浏览整个结果集。 无法配置页面大小。 

如果在逐页浏览集合时创建或删除作业，则会在返回的结果中反映此更改（如果这些更改位于集合中尚未下载的部分）。 

以下 C# 示例演示如何枚举帐户中的作业。

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

有关 REST 示例，请参阅[作业 - 列出](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>后续步骤

[流式处理视频文件](stream-files-dotnet-quickstart.md)
