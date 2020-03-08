---
title: Azure 媒体服务高可用性编码
description: 了解在发生区域性的数据中心服务中断或故障时如何故障转移到辅助媒体服务帐户。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: f5b02376111a3deba33cd5688330018bd7c370d8
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899212"
---
# <a name="media-services-high-availability-encoding"></a>媒体服务高可用性编码 

Azure 媒体服务编码服务是一个区域批处理平台，当前不是针对单个区域中的高可用性设计的。 如果出现区域性数据中心服务中断或基础组件或从属服务（如存储、SQL）出现故障，则编码服务当前不提供服务的即时故障转移。 本文介绍如何部署媒体服务来维护具有故障转移功能的高可用性体系结构并确保应用程序的最佳可用性。

按照本文中所述的指导原则和最佳做法，在单个区域发生服务中断的情况下，你将会降低编码故障、延迟和最大程度减少恢复时间的风险。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>如何构建跨区域编码系统

* [创建](create-account-cli-how-to.md)两个（或多个） Azure 媒体服务帐户。

    这两个帐户需要在不同的区域中。 有关详细信息，请参阅[部署 Azure 媒体服务服务的区域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 将你的媒体上传到计划从中提交作业的同一区域。 有关如何开始编码的详细信息，请参阅[从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)或[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。

    如果接下来需要将[作业](transforms-jobs-concept.md)重新提交到另一个区域，则可以使用 JobInputHttp 或使用 "[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) " 将源资产容器中的数据复制到备用区域中的资产容器。
* 通过 Azure 事件网格订阅每个帐户中的 JobStateChange 消息。 有关详细信息，请参阅：

    * [音频分析示例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)，演示如何使用 Azure 事件网格监视作业，包括添加回退以防 Azure 事件网格消息由于某种原因而延迟。
    * [媒体服务事件的 Azure 事件网格架构](media-services-event-schemas.md)
    * [通过 Azure 门户或 CLI 注册事件](reacting-to-media-services-events.md)（也可以通过 EVENTGRID 管理 SDK 来完成）
    * [EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) （本机支持媒体服务事件）。

    还可以通过 Azure Functions 使用事件网格事件。
*    创建[作业](transforms-jobs-concept.md)时：
    
    * 从当前使用的帐户列表中随机选择一个帐户（此列表通常包含两个帐户，但如果检测到问题，则它可能只包含一个帐户）。 如果列表为空，则引发警报，以便操作员能够调查。
    * 一般指导原则是，每个[JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)都需要一个[媒体保留单位](media-reserved-units-cli-how-to.md)（除非使用的是[VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) ，但建议使用3个媒体保留单位）。
    * 获取所选帐户的媒体保留单位（Mru）的计数。 如果当前的**媒体保留单位**计数尚未达到最大值，请添加作业所需的 mru 数，并更新该服务。 如果你的作业提交率较高，而你经常查询 Mru 以找到最大值，请将分布式缓存用于值合理的超时值。
    * 保留即时作业数的计数。

* 当你的 JobStateChange 处理程序收到作业已达到计划状态的通知时，记录其输入计划状态的时间以及所使用的区域/帐户。
* 当 JobStateChange 处理程序收到作业已达到处理状态的通知时，将该作业的记录标记为 "正在处理"。
* 当你的 JobStateChange 处理程序收到作业已达到完成/误码/已取消状态的通知时，将该作业的记录标记为最终状态并减少即时作业计数。 获取所选帐户的媒体保留单位数，并将当前的 MRU 数字与即时作业计数进行比较。 如果即时计数小于 MRU 计数，请将其递减并更新服务。
* 有一个单独的进程，该进程定期查看作业记录
    
    * 如果作业处于计划状态，并且在给定区域的合理时间内未提前处理状态，请从当前使用的帐户列表中删除该区域。  根据你的业务要求，你可以立即取消这些作业并将其重新提交到另一个区域。 或者，您可以将它们更多时间移到下一个状态。
    * 根据在帐户上配置的媒体保留单位的数量和提交速度，还可能有作业处于排队状态，因为系统尚未获取进行处理。  如果处于排队状态的作业列表超出了某个区域的可接受限制，则可以取消这些作业并将其提交到另一个区域。  但是，这可能是由于当前负载没有在帐户上配置足够的媒体保留单位的症状。  如果需要，可以通过 Azure 支持请求更高的媒体保留单位配额。
    * 如果已从帐户列表中删除某个区域，请在将其重新添加到列表之前监视该区域的恢复。  可以通过区域中的现有作业来监视区域运行状况（如果尚未取消并重新提交），方法是在一段时间后将该帐户添加回列表，并通过操作员监视 Azure 通信与可能影响的中断有关Azure 媒体服务。
    
如果发现 MRU 计数已启动并降低，请将减量逻辑移动到定期任务。 将即时计数与当前的 MRU 计数进行比较，以查看它是否需要更新 Mru。

## <a name="next-steps"></a>后续步骤

* [构建点播视频跨区域流式处理](media-services-high-availability-streaming.md)
* 查看[代码示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
