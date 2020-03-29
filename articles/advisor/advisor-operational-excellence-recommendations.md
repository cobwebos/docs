---
title: 使用 Azure 顾问提高 Azure 订阅的操作功能
description: 使用 Advisor 优化 Azure 订阅的卓越操作并成熟
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443076"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>借助 Azure 顾问实现卓越运营

Azure Advisor 卓越运营建议帮助客户提供流程和工作流效率、资源可管理性和部署最佳实践。 您可以在"顾问"仪表板的 **"卓越运营"** 选项卡上从顾问那里获得这些建议。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知

我们建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)是一项免费服务，可在你受到 Azure 服务问题影响时提供个性化指导和支持。 顾问会识别未配置警报的订阅，并建议创建一个警报。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>设计存储帐户以防止达到最大订阅限制

Azure 区域每个订阅最多可以支持 250 个存储帐户。 达到限制后，您将无法在该区域/订阅组合中创建任何存储帐户。 Advisor 将检查您的订阅和表面建议，以便您为接近最大限制的任何存储帐户进行设计。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 云专家

在运行业务关键型工作负载时，在需要时有权访问技术支持至关重要。 顾问标识在其支持计划中不包含技术支持的潜在业务关键型订阅，并建议升级到包含技术支持的选项。

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure Advisor 将检测在其条件部分中指定的无效查询的警报规则。 日志警报规则在 Azure Monitor 中创建，用于按指定时间间隔运行分析查询。 查询结果决定了是否需要触发警报。 随着时间的推移，分析查询可能会因所引用资源、表或命令的变化而变得无效。 Advisor 将建议您更正警报规则中的查询，以防止其自动禁用，并确保监视 Azure 中资源的覆盖范围。 [了解有关故障排除警报规则的更多](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>使用 Azure 策略遵循最佳实践

Azure Policy 是 Azure 中的一项服务，用于创建、分配和管理策略。 这些策略对资源强制实施不同的规则和效果。 以下是帮助您实现运营出色的 Azure 策略建议： 
1. 使用 Azure 策略管理标记：此策略在创建或更新任何资源时添加或替换指定的标记和值。 可以通过触发修正任务来修正现有资源。 此外，这不会修改资源组上的标记。
2. 使用 Azure 策略强制实施地理合规性要求：该策略使您能够限制组织在部署资源时可以指定的位置。 
3. 为部署指定允许的虚拟机 SKU：此策略使您能够指定组织可以部署的一组虚拟机 SKU。
4. 使用 Azure 策略强制实施"不使用托管磁盘的审核 VM"
5. 使用 Azure 策略使用"从资源组继承标记"：当创建或更新任何资源时，策略会添加或替换父资源组中的指定标记和值。 可以通过触发修正任务来修正现有资源。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
