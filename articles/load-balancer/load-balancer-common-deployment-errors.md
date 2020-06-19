---
title: 排查常见部署错误
titleSuffix: Azure Load Balancer
description: 描述如何解决在部署 Azure 负载均衡器时的常见错误
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: b596e349d789584de07943332ede6f6897a1fd22
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658638"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>排查使用 Azure 负载均衡器时的常见 Azure 部署错误

本文介绍了一些常见的 Azure 负载均衡器部署错误，并提供了有关如何解决这些错误的信息。 如果你正在查找有关错误代码的信息，但本文未提供这些信息，请告知我们。 可在此页底部留下反馈。 反馈将在 GitHub 问题中进行跟踪。

## <a name="error-codes"></a>错误代码

| 错误代码 | 详细信息和缓解措施 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 公共 IP SKU 和负载均衡器 SKU 必须匹配。 确保 Azure 负载均衡器和公共 IP SKU 匹配。 对于生产型工作负载，建议使用标准 SKU。 详细了解 [SKU 中的差异](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 当未指定 SKU 或在没有标准公共 IP 的情况下部署时，虚拟机规模集将默认为基本负载均衡器。 在各个实例上使用标准公共 IP 重新部署虚拟机规模集，以确保选择标准负载均衡器，或者在从 Azure 门户部署虚拟机规模集时选择一个标准 LB。 |
|MaxAvailabilitySetsInLoadBalancerReached | 负载均衡器的后端池最多可包含 150 个可用性集。 如果没有为后端池中的 VM 显式定义可用性集，则每个 VM 都将进入其各自的可用性集。 因此，部署 150 个独立 VM 意味着它将有 150 个可用性集，从而达到限制。 你可以部署一个可用性集，并将更多 VM 添加到其中作为解决方法。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 对于具有相同后端端口和由相同虚拟机规模集引用的协议的给定负载均衡器类型（内部、公共），不能有多个规则。 更新规则以更改此重复规则创建。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 对于具有相同后端端口和由相同虚拟机规模集引用的协议的给定负载均衡器类型（内部、公共），不能有多个规则。 更新规则参数以更改此重复规则创建。 |
|AnotherInternalLoadBalancerExists| 在负载均衡器后端，只能有一个类型为“内部”的负载均衡器引用同一组 VM/网络接口。 更新部署，确保只创建一个相同类型的负载均衡器。 |
|CannotUseInactiveHealthProbe| 不能有未被任何为虚拟机规模集运行状况配置的规则所使用的探测。 确保正在使用所设置的探测。 |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 不能有多个相同类型（内部、公共）的负载均衡器。 最多可以有一个内部负载均衡器和一个公共负载均衡器。 |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 多位置组虚拟机规模集或跨可用性区域虚拟机规模集不支持基本负载均衡器。 改用标准负载均衡器。 |
|MarketplacePurchaseEligibilityFailed | 由于订阅是 EA 订阅，请切换到正确的管理帐户以启用购买。 可在[此处](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase)了解详细信息。 |
|ResourceDeploymentFailure| 如果负载均衡器处于“失败”状态，请按照以下步骤将其从“失败”状态恢复：<ol><li>转到 https://resources.azure.com ，使用你自己的 Azure 门户凭据登录。</li><li>选择“读取/写入”。</li><li>在左侧，展开“订阅”，然后展开包含要更新的负载均衡器的订阅。</li><li>展开“ResourceGroups”，然后展开包含要更新的负载均衡器的资源组。</li><li>选择“Microsoft.Network” > “LoadBalancers”，然后选择要更新的负载均衡器，“LoadBalancer_1”。</li><li>在“LoadBalancer_1”的显示页上，选择“获取” > “编辑”。</li><li>将“ProvisioningState”值从“失败”更新为“成功”。</li><li>选择“PUT” 。</li></ol>|
|  |  |

## <a name="next-steps"></a>后续步骤

* 查看 Azure 负载均衡器 [SKU 比较表](./skus.md)
* 了解 [Azure 负载均衡器限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
