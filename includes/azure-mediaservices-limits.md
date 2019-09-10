---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "67173331"
---
>[!NOTE]
>对于不固定的资源，请打开支持票证，要求增加配额。 不要创建其他 Azure 媒体服务帐户，尝试获得更高的限制。

| Resource | 默认限制 | 
| --- | --- | 
| 单个订阅中的 Azure 媒体服务帐户 | 25（固定） |
| 每个媒体服务帐户的媒体保留单位数 |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| 每个媒体服务帐户的作业数 | 50,000<sup>2</sup> |
| 每个作业的链接任务数 | 30（固定） |
| 每个媒体服务帐户的资产 | 1,000,000|
| 每个任务的资产数 | 50 |
| 每个作业的资产数 | 100 |
| 一次与一个资产关联的唯一定位符数 | 5<sup>4</sup> |
| 每个媒体服务帐户的实时频道 |5|
| 每个频道的停止状态节目数 |50|
| 每个频道的运行状态节目数 |3|
| 每个媒体服务帐户停止或运行的流式处理终结点|2|
| 每个流式处理终结点的流式处理单位数 |10 |
| 存储帐户 | 1,000<sup>5</sup>（固定） |
| 策略 | 1,000,000<sup>6</sup> |
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。<sup>7</sup> |

<sup>1</sup>如果更改类型（例如，从 S2 更改为 S1），则会重置最大保留单位限制。

<sup>2</sup>此数字包括已排队的、已完成的、活动的和已取消的作业。 但不包括已删除的作业。 可以使用**IJob**或**delete** HTTP 请求删除旧作业。

从2017年4月1日起，将自动删除帐户中超过90天的所有作业记录及其相关的任务记录。 即使记录总数低于最大配额，也会自动删除。 若要存档作业和任务信息，请使用使用[媒体服务 .NET SDK 管理资产](../articles/media-services/previous/media-services-dotnet-manage-entities.md)中所述的代码。

<sup>3</sup>发出列出作业实体的请求时，每个请求最多返回1000个作业。 若要跟踪所有已提交的作业，请使用 top 或 skip 查询，如[OData 系统查询选项](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))中所述。

<sup>4</sup>定位符不用于管理按用户的访问控制。 若要向各个用户授予不同的访问权限，请使用数字权限管理（DRM）解决方案。 有关详细信息，请参阅[通过 Azure 媒体服务保护内容](../articles/media-services/previous/media-services-content-protection-overview.md)。

<sup>5</sup>存储帐户必须来自同一 Azure 订阅。

<sup>6</sup>不同媒体服务策略的策略数限制为1000000。 例如，对于定位器策略或 ContentKeyAuthorizationPolicy。 

>[!NOTE]
> 如果始终使用相同的日期和访问权限，请使用相同的策略 ID。 有关信息和示例，请参阅[使用媒体服务 .NET SDK 管理资产](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>单个 blob 支持的最大大小目前在 Azure Blob 存储中最多为 5 TB。 根据服务使用的 VM 大小，更多限制适用于 Media Services。 大小限制适用于你上传的文件，还适用于由于媒体服务处理（编码或分析）而生成的文件。 如果源文件大于 260 GB，作业可能会失败。 

下表显示了媒体保留单位 S1、S2 和 S3 的限制。 如果源文件大于表中定义的限制，则编码作业将失败。 如果对长时间的4K 分辨率源进行编码，则需要使用 S3 媒体保留单位来实现所需的性能。 如果你的4k 内容大于 S3 媒体保留单位的 260 GB 限制， amshelp@microsoft.com请联系我们以获取支持你的方案的可能的缓解措施。

|媒体保留单位类型   |最大输入大小（GB）|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
