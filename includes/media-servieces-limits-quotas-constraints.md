---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: d6ff823951e6474a35a514036f02c0aabb17bf01
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997668"
---
> [!NOTE]
> 对于不固定的资源，请开具支持票证，要求增加配额。 请勿尝试通过创建更多 Azure 媒体服务帐户的方式来提高配额限制。

### <a name="account-limits"></a>帐户限制

| 资源 | 默认限制 | 
| --- | --- | 
| 单个订阅中的媒体服务帐户数 | 25（固定） |

### <a name="asset-limits"></a>资产限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的资产数 | 1,000,000|

### <a name="storage-media-limits"></a>存储（媒体）限制

| 资源 | 默认限制 | 
| --- | --- | 
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。 <sup>(1)</sup> |
| 存储帐户 | 100<sup>(2)</sup>（固定） |

<sup>1</sup> 在 Azure Blob 存储中，单个 Blob 目前支持的最大大小为 5 TB。 媒体服务会根据服务使用的 VM 大小应用其他限制。 大小限制适用于你上传的文件，也适用于由于媒体服务处理（编码或分析）而生成的文件。 如果源文件大于 260 GB，作业可能会失败。 

下表显示了媒体保留单位（S1、S2 和 S3）的限制。 如果源文件大于表中定义的限制，则编码作业将失败。 编码持续时间较长的 4K 分辨率源时，需要使用 S3 媒体保留单位才能达到所需的性能。 如果 S3 媒体保留单位上的 4K 内容大于 260-GB 限制，请开具支持票证。

|媒体保留单位类型|最大输入大小 (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> 存储帐户必须来自同一 Azure 订阅。

### <a name="jobs-encoding--analyzing-limits"></a>作业（编码和分析）限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的作业数 | 500,000 <sup>(3)</sup>（固定）|
| 每个作业的作业输入数 | 50（固定）|
| 每个作业的作业输出数 | 20（固定） |
| 每个媒体服务帐户的转换数 | 100（固定）|
| 转换某个转换中的输出 | 20（固定） |
| 每个作业输入的文件数|10（固定）|

<sup>3</sup> 此数字包括排队的、完成的、活动的和取消的作业。 不包括已删除的作业。 

即使记录总数低于最大配额，也会自动删除帐户中所有超过 90 天的作业记录。 

### <a name="live-streaming-limits"></a>实时传送视频流限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的直播活动数 <sup>(4)</sup> |5|
| 每个直播活动的实时输出 |3 <sup>(5)</sup> |
| 最长实时输出持续时间 | [DVR 窗口的大小](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> 有关直播活动限制的详细信息，请参阅[直播活动类型比较和限制](../articles/media-services/latest/live-event-types-comparison.md)。

<sup>5</sup> 实时输出在创建时启动，在删除后停止。

### <a name="packaging--delivery-limits"></a>打包和传送限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个媒体服务帐户的流式处理端点（已停止或正在运行）|2（固定）|
| 动态清单筛选器|100|
| 流式处理策略 | 100 <sup>(6)</sup> |
| 一次与一个资产关联的唯一流式处理定位符 | 100<sup>(7)</sup>（固定） |

<sup>6</sup> 使用自定义[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于流定位器。 不应为每个流式处理定位符创建新的流式处理策略。

<sup>7</sup> 流定位器不适用于管理每个用户的访问控制。 要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。

### <a name="protection-limits"></a>保护限制

| 资源 | 默认限制 | 
| --- | --- | 
| 每个内容密钥策略的选项 |30 | 
| 每个帐户的媒体服务密钥交付服务上每种 DRM 类型的每月许可证数|1,000,000|

### <a name="support-ticket"></a>支持票证

对于不固定的资源，可以通过开具[支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)请求提高配额。 请在请求中包含有关所需的配额更改、用例方案和所需区域的详细信息。 <br/>**请勿** 尝试通过创建更多 Azure 媒体服务帐户的方式来提高配额限制。
