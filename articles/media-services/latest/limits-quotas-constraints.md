---
title: Azure 媒体服务 v3 中的配额和限制 | Microsoft Docs
description: 本主题介绍 Azure 媒体服务 v3 中的配额和限制
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: d5fc14adab956fae23aad24fa7bc488c8c2041e3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621679"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure 媒体服务 v3 中的配额和限制

本文介绍 Azure 媒体服务 v3 中的配额和限制。

| 资源 | 默认限制 | 
| --- | --- | 
| 每个 Azure 媒体服务帐户的资产数 | 1,000,000|
| 动态清单筛选器|100|
| 每个作业的 JobInputs | 50（固定）|
| 每个作业的 JobOutputs | 20（固定） |
| 转换中的 TransformOutput 数 | 20（固定） |
| 每个 JobInput 的文件数|10（固定）|
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。 <sup>(1)</sup> |
| 每个媒体服务帐户的作业数 | 500,000 <sup>(2)</sup>（固定）|
| 列出转换数|对响应进行分页，每页 1000 个转换|
| 列出作业数|对响应进行分页，每页 500 个作业|
| 每个媒体服务帐户的实时事件数 |5|
| 单个订阅中的媒体服务帐户数 | 25（固定） |
| 在运行每个实时事件的状态的实时输出 |3|
| 最大实时输出持续时间 | 25 个小时 |
| 存储帐户 | 100<sup>(4)</sup>（固定） |
| 每个媒体服务帐户的流式处理端点（已停止或正在运行）|2 （固定）|
| 流式处理策略 | 100 <sup>(3)</sup> |
| 每个媒体服务帐户的转换数 | 100（固定）|
| 一次与一个资产关联的唯一流式处理定位符 | 100<sup>(5)</sup>（固定） |
| 内容密钥策略 |30 | 

<sup>1</sup> 在 Azure Blob 存储中，单个 Blob 目前支持的最大大小为 5 TB。 但是，Azure 媒体服务会根据服务使用的 VM 大小应用其他限制。 如果源文件大于 260 GB，作业可能会失败。 如果你有 4K 内容（超过了 260 GB 的限制），请通过 amshelp@microsoft.com 联系我们，我们会提供可能的缓解措施来支持你的方案。

<sup>2</sup> 这个数字包括已排队的、已完成的、活动的和已取消的作业。 不包括已删除的作业。 

即使记录总数低于最大配额，也会自动删除帐户中所有超过 90 天的作业记录。 

<sup>3</sup> 使用自定义的[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于流式处理定位符。 不应为每个流式处理定位符创建新的流式处理策略。

<sup>4</sup> 存储帐户必须来自同一 Azure 订阅。

<sup>5</sup> 流式处理定位符不用于管理按用户的访问控制。 要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。

## <a name="support-ticket"></a>支持票证

对于不固定的资源，可以通过开具[支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)请求提高配额。 请在请求中包含有关所需的配额更改、用例方案和所需区域的详细信息。 <br/>请**不要**创建更多的 Azure 媒体服务帐户以求获取更高的限制。

## <a name="next-steps"></a>后续步骤

[概述](media-services-overview.md)
