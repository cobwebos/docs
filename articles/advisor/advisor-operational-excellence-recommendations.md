---
title: 通过 Azure 顾问改进 Azure 订阅的操作 excellency
description: 使用顾问优化 Azure 订阅，并使其成为成熟的操作
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443076"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>通过 Azure 顾问实现卓越运营

Azure 顾问操作卓越的建议可帮助客户处理流程和工作流效率、资源可管理性和部署最佳实践。 可以在顾问仪表板的 "**操作卓越**" 选项卡上从 advisor 获取这些建议。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知

我们建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)是一项免费服务，可在你受到 Azure 服务问题影响时提供个性化指导和支持。 顾问会识别未配置警报的订阅，并建议创建一个警报。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>设计存储帐户以避免达到最大订阅限制

对于每个订阅，Azure 区域最多支持250个存储帐户。 达到此限制后，将无法再创建该区域/订阅组合中的任何其他存储帐户。 Advisor 将检查你的订阅和表面建议，以便为接近达到最大限制的任何内容设计更少的存储帐户。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 云专家

在运行业务关键型工作负载时，在需要时有权访问技术支持至关重要。 顾问标识在其支持计划中不包含技术支持的潜在业务关键型订阅，并建议升级到包含技术支持的选项。

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure 顾问将检测到其条件部分中指定了无效查询的警报规则。 日志警报规则在 Azure Monitor 中创建，用于按指定时间间隔运行分析查询。 查询结果决定了是否需要触发警报。 随着时间的推移，分析查询可能会因所引用资源、表或命令的变化而变得无效。 顾问建议你更正警报规则中的查询，以防止它自动禁用，并确保监视 Azure 中的资源。 [详细了解警报规则疑难解答](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>使用 Azure 策略遵循最佳做法

Azure Policy 是 Azure 中的一项服务，用于创建、分配和管理策略。 这些策略对资源强制实施不同的规则和效果。 下面是 Azure 策略建议，可帮助你实现运营 excellency： 
1. 使用 Azure 策略管理标记：在创建或更新任何资源时，此策略将添加或替换指定的标记和值。 可以通过触发修正任务来修正现有资源。 同样，这也不会修改资源组上的标记。
2. 使用 Azure 策略强制实施地区遵从性要求：通过策略，你可以限制组织在部署资源时可以指定的位置。 
3. 为部署指定允许的虚拟机 Sku：通过此策略，你可以指定组织可部署的一组虚拟机 Sku。
4. 使用 Azure 策略强制实施 "不使用托管磁盘的审核 Vm"
5. 使用 "从资源组继承标记" 策略：在创建或更新任何资源时，策略将添加或替换父资源组中指定的标记和值。 可以通过触发修正任务来修正现有资源。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
