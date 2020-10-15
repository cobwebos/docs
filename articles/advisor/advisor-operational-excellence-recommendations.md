---
title: 使用顾问改进卓越运营
description: 使用 Azure 顾问为 Azure 订阅优化和完善卓越运营。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 25c470a968f2d31ae1190f765046d593190d697b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077382"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>使用 Azure 顾问实现卓越运营

Azure 顾问中的卓越运营建议可在以下方面帮助你： 
- 流程和工作流效率。
- 资源可管理性。
- 部署最佳做法。 

可在“顾问”仪表板的“卓越运营”选项卡上获取这些建议。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知

建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)是一项免费服务，可在你受到 Azure 服务问题影响时提供个性化指导和支持。 顾问会识别未配置警报的订阅，并建议对其进行配置。


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>设计存储帐户，防止达到最大订阅数限制

一个 Azure 区域可以支持每个订阅最多 250 个存储帐户。 达到该限制后，将无法在该区域/订阅组合中创建存储帐户。 顾问会检查你的订阅并提供建议，以便为任何即将达到限制的区域/订阅设计更少的存储帐户。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 云专家

在运行业务关键型工作负载时，在需要时有权访问技术支持至关重要。 顾问会识别在其支持计划中不包含技术支持的潜在业务关键型订阅。 建议升级到包含技术支持的选项。

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>删除并重新创建你的池，以移除弃用的内部组件

如果池使用的是已弃用的内部组件，请删除再重新创建该池以提高稳定性和性能。

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure 顾问会检测在其条件部分指定了无效查询的警报规则。 可在 Azure Monitor 中创建日志警报规则，通过它们按指定的时间间隔运行分析查询。 查询结果决定了是否需要触发警报。 随着时间的推移，分析查询可能会因所引用资源、表或命令的变化而变得无效。 顾问会建议你更正警报规则中的查询以防止自动禁用，并确保监视你在 Azure 中的所有资源。 [详细了解如何排查警报规则问题。](../azure-monitor/platform/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>使用 Azure Policy 建议

Azure Policy 是 Azure 中的一项服务，可用于创建、分配和管理策略。 这些策略对资源强制实施规则和效果。 以下 Azure Policy 建议有助于实现卓越运营： 

**管理标记。** 创建或更新任何资源时，此策略将添加或替换指定的标记和值。 可触发修正任务来修正现有资源。 此策略不会修改资源组上的标记。

**强制实施异地合规性要求。** 通过此策略，可限制组织在部署资源时可指定的位置。 

**指定允许进行部署的虚拟机 SKU。** 此策略可用于指定组织可部署的一组虚拟机 SKU。

**强制审核未使用托管磁盘的 VM。** 

**使能够从资源组继承标记。**  创建或更新任何资源时，此策略将添加或替换父资源组中指定的标记和值。 可触发修正任务来修正现有资源。

## <a name="no-validation-environment-enabled"></a>未启用验证环境
Azure 顾问确定你未在当前订阅中启用验证环境。 创建主机池时，你对属性选项卡中的“验证环境”选择了“否”。若拥有至少一个启用了验证环境的主机池，可通过 Windows 虚拟桌面服务部署确保业务连续性，并可及早发现潜在问题。 [了解详细信息](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>确保生产（非验证）环境受益于稳定功能
Azure 顾问检测到太多的主机池启用了验证环境。 若要使验证环境可最好地实现其用途，你应该至少有一个主机池位于验证环境，但绝不能超过主机池数量的一半。 通过在启用了验证环境的主机池与禁用了该环境的主机池之间实现良好平衡，你将能充分利用 Windows 虚拟桌面随特定更新提供的多阶段部署的好处。 若要解决此问题，请打开主机池的属性，然后在“验证环境”设置旁选择“否”。

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>启用流量分析以深入了解 Azure 资源中的流量模式
流量分析是一种基于云的解决方案，可用于洞察 Azure 中的用户和应用程序活动。 流量分析可分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察流量流。 借助流量分析，你可查看 Azure 部署和非 Azure 部署中的主要通信者，调查环境中的开放端口、协议和恶意流，并优化网络部署来提高性能。 可按 10 和 60 分钟的处理时间间隔处理流日志，从而更快地分析流量。 一种好的做法是为 Azure 资源启用流量分析。 


## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问可靠性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问 REST API](/rest/api/advisor/)