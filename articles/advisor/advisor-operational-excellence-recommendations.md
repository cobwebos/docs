---
title: 利用 Advisor 提高操作 excellency
description: 使用 Azure 顾问优化和成熟你的 Azure 订阅的卓越运营。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 036adb7e7d59bd78980c72b210ad41faea277d00
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258483"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>使用 Azure 顾问实现卓越运营

Azure 顾问中的卓越运营建议可以帮助你： 
- 进程和工作流效率。
- 资源可管理性。
- 部署最佳实践。 

可以在顾问仪表板的 " **操作优秀** " 选项卡上获取这些建议。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响时收到通知

建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/) 是一种免费服务，在受 Azure 服务问题影响时提供个性化指导和支持。 顾问标识未配置警报并建议对其进行配置的订阅。


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>设计存储帐户以避免达到最大订阅限制

一个 Azure 区域可以支持每个订阅最多 250 个存储帐户。 达到该限制后，将无法在该区域/订阅组合中创建存储帐户。 顾问会检查你的订阅，并提供建议，以便为接近达到限制的任何区域/订阅设计更少的存储帐户。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 云专家

在运行业务关键型工作负荷时，有必要访问技术支持部门。 Advisor 识别潜在的业务关键订阅，它们的支持计划中未包含技术支持。 建议升级到包括技术支持的选项。

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>删除并重新创建池以删除弃用的内部组件

如果你的池使用的是不推荐使用的内部组件，请删除并重新创建该池，以提高稳定性和性能。

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure 顾问检测到其条件部分中指定了无效查询的警报规则。 可以在 Azure Monitor 中创建日志警报规则，并使用这些规则按指定的时间间隔运行分析查询。 查询结果决定了是否需要触发警报。 分析查询可能会因为引用的资源、表或命令的更改而在一段时间内变得无效。 顾问建议你更正警报规则中的查询，以防止它被自动禁用，并确保在 Azure 中监视资源。 [了解有关排除警报规则的详细信息。](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>使用 Azure 策略建议

Azure 策略是 Azure 中的一项服务，可用于创建、分配和管理策略。 这些策略将对资源强制实施规则和影响。 以下 Azure 策略建议可以帮助你实现运营 excellency： 

**管理标记。** 创建或更新任何资源时，此策略将添加或替换指定的标记和值。 可以通过触发修正任务来修正现有资源。 此策略不会修改资源组上的标记。

**强制执行异地遵从性要求。** 通过此策略，可限制组织在部署资源时可指定的位置。 

**为部署指定允许的虚拟机 Sku。** 此策略可用于指定组织可部署的一组虚拟机 SKU。

**强制 *执行不使用托管磁盘的审核 vm*。**

**Enable *从资源组继承标记*。** 创建或更新任何资源时，此策略将添加或替换父资源组中指定的标记和值。 可以通过触发修正任务来修正现有资源。

## <a name="no-validation-environment-enabled"></a>未启用验证环境
Azure 顾问确定你没有在当前订阅中启用验证环境。 创建主机池时，在 \" \" \" \" "属性" 选项卡中选择了 "无" 作为 "验证环境"。如果至少有一个主机池启用了验证环境，则可通过 Windows 虚拟桌面服务部署确保业务连续性，并及早检测到潜在问题。 [了解详细信息](https://docs.microsoft.com/azure/virtual-desktop/create-validation-host-pool)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>确保生产 (非验证) 环境，使其受益于稳定的功能
Azure 顾问检测到有太多主机池启用了验证环境。 为了使验证环境最好地满足其用途，您应该至少有一个，但在验证环境中的主机池数不得超过一半。 通过在启用了验证环境的主机池与禁用了该功能的主机池之间实现良好的平衡，你将能充分利用 Windows 虚拟桌面提供的某些更新的多阶段部署的好处。 若要解决此问题，请打开主机池的属性， \" 然后 \" 在 "验证环境" 设置旁选择 "否" \" \" 。

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>启用流量分析以查看跨 Azure 资源的流量模式的见解
流量分析是一种基于云的解决方案，可用于查看 Azure 中的用户和应用程序活动。 流量分析会分析网络观察程序网络安全组 (NSG) 流日志，以便深入了解流量。 借助流量分析，可跨 Azure 和非 Azure 部署查看顶级活跃，调查环境中的开放端口、协议和恶意流，并优化网络部署以提高性能。 可以按10分钟到60分钟的处理时间间隔处理流日志，从而更快地分析流量。 为 Azure 资源启用流量分析是一种很好的做法。 


## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问可靠性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问 REST API](/rest/api/advisor/)
