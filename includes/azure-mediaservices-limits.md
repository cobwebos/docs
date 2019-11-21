---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 49150daa783280f47cb9401434ebfab8f6204090
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224465"
---
>[!NOTE]
>For resources that aren't fixed, open a support ticket to ask for an increase in the quotas. Don't create additional Azure Media Services accounts in an attempt to obtain higher limits.

| 资源 | 默认限制 | 
| --- | --- | 
| Azure Media Services accounts in a single subscription | 25（固定） |
| Media reserved units per Media Services account |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| 每个媒体服务帐户的作业数 | 50,000<sup>2</sup> |
| 每个作业的链接任务数 | 30（固定） |
| Assets per Media Services account | 1,000,000|
| 每个任务的资产数 | 50 |
| 每个作业的资产数 | 100 |
| 一次与一个资产关联的唯一定位符数 | 5<sup>4</sup> |
| Live channels per Media Services account |5|
| 每个频道的停止状态节目数 |50|
| 每个频道的运行状态节目数 |3|
| Streaming endpoints that are stopped or running per Media Services account|2|
| 每个流式处理终结点的流式处理单位数 |10 |
| 存储帐户 | 1,000<sup>5</sup>（固定） |
| 策略 | 1,000,000<sup>6</sup> |
| 文件大小| In some scenarios, there's a limit on the maximum file size supported for processing in Media Services.<sup>7</sup> |

<sup>1</sup>If you change the type, for example, from S2 to S1, the maximum reserved unit limits are reset.

<sup>2</sup>This number includes queued, finished, active, and canceled jobs. It doesn't include deleted jobs. You can delete old jobs by using **IJob.Delete** or the **DELETE** HTTP request.

As of April 1, 2017, any job record in your account older than 90 days is automatically deleted, along with its associated task records. Automatic deletion occurs even if the total number of records is below the maximum quota. To archive the job and task information, use the code described in [Manage assets with the Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>When you make a request to list job entities, a maximum of 1,000 jobs is returned per request. To keep track of all submitted jobs, use the top or skip queries as described in [OData system query options](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>Locators aren't designed for managing per-user access control. To give different access rights to individual users, use digital rights management (DRM) solutions. For more information, see [Protect your content with Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>The storage accounts must be from the same Azure subscription.

<sup>6</sup>There's a limit of 1,000,000 policies for different Media Services policies. An example is for the Locator policy or ContentKeyAuthorizationPolicy. 

>[!NOTE]
> If you always use the same days and access permissions, use the same policy ID. For information and an example, see [Manage assets with the Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>The maximum size supported for a single blob is currently up to 5 TB in Azure Blob Storage. Additional limits apply in Media Services based on the VM sizes that are used by the service. The size limit applies to the files that you upload and also the files that get generated as a result of Media Services processing (encoding or analyzing). 如果源文件大于 260 GB，作业可能会失败。 

The following table shows the limits on the media reserved units S1, S2, and S3. If your source file is larger than the limits defined in the table, your encoding job fails. If you encode 4K resolution sources of long duration, you're required to use S3 media reserved units to achieve the performance needed. If you have 4K content that's larger than the 260-GB limit on the S3 media reserved units, open a support ticket.

|Media reserved unit type   |Maximum input size (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
