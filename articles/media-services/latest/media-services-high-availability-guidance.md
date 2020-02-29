---
title: Azure 媒体服务高可用性
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
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202281"
---
# <a name="azure-media-services-high-availability-guidance"></a>Azure 媒体服务高可用性指南

Azure 媒体服务编码服务是一个区域批处理平台，当前不是针对单个区域中的高可用性设计的。 如果出现区域数据中心服务中断或基础组件或从属服务（如存储、SQL 等）出现故障，则编码服务当前不提供服务的即时故障转移。 本文介绍如何部署媒体服务来维护具有故障转移功能的高可用性体系结构并确保应用程序的最佳可用性。 

按照本文中所述的指导原则和最佳做法，在单个区域发生服务中断的情况下，你将会降低编码故障、延迟和最大程度减少恢复时间的风险。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>如何构建跨区域编码系统

* [创建](create-account-cli-how-to.md)两个（或多个） Azure 媒体服务帐户。
* 订阅每个帐户中的**JobStateChange**消息。

    * 在媒体服务 v3 中，可以通过 Azure 事件网格完成此操作。 有关详细信息，请参阅：
    
       * [事件网格示例](../../event-grid/receive-events.md)， 
       * [媒体服务事件的 Azure 事件网格架构](media-services-event-schemas.md)， 
       * [通过 Azure 门户或 CLI 注册事件](reacting-to-media-services-events.md)（也可以通过 EVENTGRID 管理 SDK 来完成）
       * [EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) （本机支持媒体服务事件）。 
       
        还可以通过 Azure Functions 使用事件网格事件。
    * 在 Media Services v2 中，这是通过[NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md)实现的。
* [创建作业](transforms-jobs-concept.md)时：

    * 从当前使用的帐户列表中随机选择一个帐户（此列表通常包含两个帐户，但如果检测到问题，则它可能只包含一个帐户）。 如果列表为空，则引发警报，以便操作员能够调查。
    * 一般指导原则是，每个任务或[JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)都需要一个[媒体保留单位](media-reserved-units-cli-how-to.md)（除非使用 v3 中的[VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) ）。
    * 获取所选帐户的[媒体保留单位](media-reserved-units-cli-how-to.md)（mru）的计数。 如果当前的**媒体保留单位**计数尚未达到最大值，请添加作业所需的 mru 数，并更新该服务。 如果你的作业提交率较高，而你经常查询 Mru 以找到最大值，请将分布式缓存用于值合理的超时值。
    * 保留即时作业数的计数。
* 当你的 JobStateChange 处理程序收到作业已达到计划状态的通知时，记录其输入计划状态的时间以及所使用的区域/帐户。    
* 当 JobStateChange 处理程序收到作业已达到处理状态的通知时，将该作业的记录标记为 "正在处理"。
* 当你的 JobStateChange 处理程序收到作业已达到完成/误码/已取消状态的通知时，将该作业的记录标记为最终状态并减少即时作业计数。 获取所选帐户的媒体保留单位数，并将当前的 MRU 数字与即时作业计数进行比较。 如果即时计数小于 MRU 计数，请将其递减并更新服务。
* 有一个单独的进程，该进程定期查看作业的记录。 如果作业处于计划状态，并且在给定区域的合理时间内未提前处理状态，请从当前使用的帐户列表中删除该区域。

    * 根据你的业务要求，你可以立即取消这些作业并将其重新提交到另一个帐户。 或者，您可以将它们更多时间移到下一个状态。   
    * 一段时间后，将该帐户添加回当前使用的列表（假设区域已恢复）。
    
如果发现 MRU 计数已启动并降低，请将减量逻辑移动到定期任务。 将即时计数与当前的 MRU 计数进行比较，以查看它是否需要更新 Mru。

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>如何构建点播视频点播跨区域流式处理 

* 视频点播跨区域的流式处理涉及到复制[资产](assets-concept.md)、[内容密钥策略](content-key-policy-concept.md)（如果已使用）、[流式处理策略](streaming-policy-concept.md)和[流式处理](streaming-locators-concept.md)定位符。 
* 需要在这两个区域中创建策略，并使其保持最新。 
* 创建流式处理定位符时，需要使用相同的定位符 Id 值、ContentKey Id 值和 ContentKey 值。  
* 如果要对内容进行编码，建议对区域 A 中的内容进行编码并将其发布，然后将编码内容复制到区域 B 并使用与区域 A 相同的值发布该内容。
* 你可以使用流量管理器来存放源和密钥传送服务（在媒体服务配置中，这将类似于自定义密钥服务器 URL）。

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-account-cli-how-to.md)
* 查看[代码示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
