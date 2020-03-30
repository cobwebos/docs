---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334859"
---
>[!NOTE]
>对于未修复的资源，请打开一个支持票证，要求增加配额。 不要创建其他 Azure 媒体服务帐户以尝试获取更高的限制。

| 资源 | 限制 | 
| --- | --- | 
| 单个订阅中的 Azure 媒体服务帐户 | 25（固定） |
| 每个媒体服务帐户的媒体预留单位 |25 (S1)<br/>10 （S2， S3）<sup>1</sup> | 
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
| 存储帐户 | 1，000<sup>5</sup> （固定） |
| 策略 | 1，000，000<sup>6</sup> |
| 文件大小| 在某些情况下，在媒体服务中支持的最大文件大小有限制。<sup>7</sup> |

<sup>1</sup>如果将类型（例如，从 S2 更改为 S1），则将重置最大预留单位限制。

<sup>2</sup>此数字包括排队、已完成、处于活动状态和取消的作业。 它不包括已删除的作业。 您可以使用**IJob.Delete**或删除**HTTP**请求删除旧作业。

自 2017 年 4 月 1 日起，您的帐户中任何超过 90 天的作业记录及其关联的任务记录将自动删除。 即使记录总数低于最大配额，也会发生自动删除。 要存档作业和任务信息，请使用["管理资产"中描述的代码与媒体服务 .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md)。

<sup>3</sup>当您请求列出作业实体时，每个请求最多返回 1，000 个作业。 要跟踪所有提交的作业，请使用顶部或跳过[OData 系统查询选项](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))中描述的查询。

<sup>4</sup>定位器不是用于管理每个用户的访问控制。 要向单个用户授予不同的访问权限，请使用数字版权管理 （DRM） 解决方案。 有关详细信息，请参阅使用[Azure 媒体服务保护内容](../articles/media-services/previous/media-services-content-protection-overview.md)。

<sup>5</sup>存储帐户必须来自同一 Azure 订阅。

<sup>6</sup>对于不同的媒体服务策略，有 1，000，000 个策略的限制。 例如定位器策略或内容密钥授权策略。 

>[!NOTE]
> 如果始终使用相同的日期和访问权限，请使用相同的策略 ID。 有关信息和示例，请参阅[使用媒体服务 .NET SDK 管理资产](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>在 Azure Blob 存储中，单个 Blob 支持的最大大小当前最多为 5 TB。 媒体服务会根据服务使用的 VM 大小应用其他限制。 大小限制适用于你上传的文件，也适用于由于媒体服务处理（编码或分析）而生成的文件。 如果源文件大于 260 GB，作业可能会失败。 

下表显示了媒体保留单位（S1、S2 和 S3）的限制。 如果源文件大于表中定义的限制，则编码作业将失败。 编码持续时间较长的 4K 分辨率源时，需要使用 S3 媒体保留单位才能达到所需的性能。 如果 S3 媒体保留单位上的 4K 内容大于 260-GB 限制，请开具支持票证。

|媒体保留单位类型    |最大输入大小 (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
