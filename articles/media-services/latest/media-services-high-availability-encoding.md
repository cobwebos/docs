---
title: Azure 媒体服务高可用性编码
description: 了解如何在区域数据中心中断或发生故障时故障转移到辅助媒体服务帐户。
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
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934188"
---
# <a name="media-services-high-availability-encoding"></a>媒体服务高可用性编码 

Azure 媒体服务编码服务是一个区域批处理平台，当前不是为单个区域内的高可用性而设计的。 如果基础组件或从属服务（如存储、SQL）发生区域数据中心中断或故障，编码服务当前不会提供服务的即时故障转移。 本文介绍如何部署媒体服务，以维护具有故障转移的高可用性体系结构，并确保应用程序的最佳可用性。

通过遵循本文中描述的指南和最佳实践，您将降低编码失败、延迟的风险，并在单个区域中发生中断时将恢复时间降至最低。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>如何构建跨区域编码系统

* [创建](create-account-cli-how-to.md)两个（或更多）Azure 媒体服务帐户。

    这两个帐户需要在不同的区域。 有关详细信息，请参阅部署[Azure 媒体服务的区域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 将媒体上载到计划提交作业的同一区域。 有关如何开始编码的详细信息，请参阅[从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)或[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。

    如果随后需要将[作业](transforms-jobs-concept.md)重新提交到其他区域，则可以使用 JobInputHttp 或使用[Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)将数据从源资产容器复制到备用区域中的资产容器。
* 通过 Azure 事件网格订阅每个帐户中的作业状态更改消息。 有关详细信息，请参阅：

    * [音频分析示例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)演示如何使用 Azure 事件网格监视作业，包括添加回退，以防 Azure 事件网格消息因某种原因延迟。
    * [媒体服务事件的 Azure 事件网格架构](media-services-event-schemas.md)
    * [通过 Azure 门户或 CLI 注册事件](reacting-to-media-services-events.md)（您也可以使用事件网格管理 SDK 进行注册）
    * [Microsoft.Azure.EventGrid SDK（](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)支持本地媒体服务事件）。

    还可以通过 Azure 函数使用事件网格事件。
* 创建[作业](transforms-jobs-concept.md)时：

    * 从当前使用的帐户列表中随机选择一个帐户（此列表通常包含两个帐户，但如果检测到问题，则可能只包含一个帐户）。 如果列表为空，请引发警报，以便操作员可以进行调查。
    * 一般指导是，每个[作业输出](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)都需要一个[介质保留单元](media-reserved-units-cli-how-to.md)（除非您使用[视频Analyzer预设](analyzing-video-audio-files-concept.md)，其中建议每个作业输出保留 3 个介质单元）。
    * 获取所选帐户的媒体保留单位 （MRUs） 计数。 如果当前**媒体预留单位**计数尚未达到最大值，请添加作业所需的 MRUs 编号并更新服务。 如果作业提交率较高，并且经常查询 MRUS 以查找最大值，请使用具有合理超时的值的分布式缓存。
    * 保留机上作业数的计数。

* 当您的 JobStateChange 处理程序收到作业已达到计划状态的通知时，记录其进入计划状态的时间以及所使用的区域/帐户。
* 当 JobStateChange 处理程序收到作业已达到处理状态的通知时，将作业的记录标记为处理。
* 当您的 JobStateChange 处理程序收到作业已达到"已完成/错误/已取消"状态的通知时，将作业的记录标记为最终，并递减机上作业计数。 获取所选帐户的媒体保留单位数，并将当前 MRU 编号与机上作业计数进行比较。 如果您的机上计数小于 MRU 计数，则将其取消并更新服务。
* 有一个单独的流程，定期查看作业记录
    
    * 如果给定区域的计划状态中作业未在合理时间内推进到处理状态，请从当前使用的帐户列表中删除该区域。  根据您的业务需求，您可以决定立即取消这些作业，并将其重新提交到其他区域。 或者，您可以给他们更多的时间移动到下一个状态。
    * 根据帐户上配置的媒体保留单位的数量和提交率，也可能有处于系统尚未拾取处理的排队状态的作业。  如果排队状态中的作业列表超出区域中可接受的限制，则可以取消这些作业并提交到其他区域。  但是，这可能是未在帐户上为当前负载配置足够的媒体保留单元的症状。  如有必要，可以通过 Azure 支持请求更高的媒体预留单位配额。
    * 如果从帐户列表中删除了区域，则监视其以进行恢复，然后再将其添加回列表中。  可以通过区域的现有作业（如果未取消并重新提交）监视区域运行状况，在一段时间后将帐户重新添加到列表中，以及通过操作员监视 Azure 通信中可能影响的中断Azure 媒体服务。
    
如果您发现 MRU 计数在上下颠簸很多，则将递减逻辑移动到定期任务。 让作业前提交逻辑将机上计数与当前 MRU 计数进行比较，以查看是否需要更新 MRU。

## <a name="next-steps"></a>后续步骤

* [构建视频点播跨区域流](media-services-high-availability-streaming.md)
* 查看[代码示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
