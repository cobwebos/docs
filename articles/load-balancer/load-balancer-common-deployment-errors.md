---
title: 排查常见部署错误
titleSuffix: Azure Load Balancer
description: 介绍如何解决部署 Azure 负载均衡器时出现的常见错误
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 527f71b1980b5a62d3db94fe89a1bce98142e31a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84221014"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>排查使用 Azure 负载均衡器时出现的常见 Azure 部署错误

本文介绍了一些常见的 Azure 负载均衡器部署错误及其解决方法。 如果你正在查找有关错误代码的信息，但本文未提供这些信息，请告知我们。 在此页的底部，你可以留下反馈。 我们将跟踪 GitHub 问题的反馈。

## <a name="error-codes"></a>错误代码

| 错误代码 | 详细信息和缓解措施 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 公共 IP SKU 和负载均衡器 SKU 必须匹配。 请确保 Azure 负载均衡器和公共 IP SKU 匹配。 对于生产型工作负荷，建议使用标准 SKU。 详细了解 [SKU 中的差异](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 当未指定 SKU 或在没有标准公共 IP 的情况下部署 SKU 时，虚拟机规模集默认设置为“基本负载均衡器”。 在单个实例上使用标准公共 IP 重新部署虚拟机规模集，以确保选择“标准负载均衡器”，或者在从 Azure 门户部署虚拟机规模集时直接选择“标准 LB”。 |
|MaxAvailabilitySetsInLoadBalancerReached | 负载均衡器的后端池最多可包含 150 个可用性集。 如果未为后端池中的 VM 显式定义可用性集，则每个 VM 都将进入其各自的可用性集。 因此，部署 150 个独立 VM 意味着会有 150 个可用性集，因此会达到限制。 一种解决方法是部署可用性集并将更多 VM 添加到其中。 |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | 对于基本 SKU 负载均衡器，网络接口和负载均衡器必须位于同一可用性集中。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 对于给定的负载均衡器类型（内部、公共），不能有多个规则使用由同一虚拟机规模集引用的相同后端端口和协议。 更新规则以更改此重复规则创建操作。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 对于给定的负载均衡器类型（内部、公共），不能有多个规则使用由同一虚拟机规模集引用的相同后端端口和协议。 更新规则参数以更改此重复规则创建操作。 |
|AnotherInternalLoadBalancerExists| 你只能使用一个“内部”类型的负载均衡器引用该负载均衡器后端的同一组 VM/网络接口。 更新部署，以确保只创建一个相同类型的负载均衡器。 |
|CannotUseInactiveHealthProbe| 你的探测不能未被为虚拟机规模集运行状况配置的任何规则使用。 确保主动使用已设置的探测。 |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 你不能有多个相同类型（内部、公共）的负载均衡器。 你最多可以有一个内部负载均衡器和一个公共负载均衡器。 |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 多位置组虚拟机规模集或跨可用性区域虚拟机规模集不支持基本负载均衡器。 请改用标准负载均衡器。 |
|MarketplacePurchaseEligibilityFailed | 由于订阅是 EA 订阅，请切换到正确的管理帐户以启用购买。 可在[此处](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase)了解详细信息。 |
|ResourceDeploymentFailure| 如果负载均衡器处于“失败”状态，请按照以下步骤将其从“失败”状态恢复：<ol><li>转到 https://resources.azure.com ，使用你自己的 Azure 门户凭据登录。</li><li>选择“读取/写入”。</li><li>在左侧，展开“订阅”，然后展开包含要更新的负载均衡器的订阅。</li><li>展开“ResourceGroups”，然后展开包含要更新的负载均衡器的资源组。</li><li>选择“Microsoft.Network” > “LoadBalancers”，然后选择要更新的负载均衡器，“LoadBalancer_1”。</li><li>在“LoadBalancer_1”的显示页上，选择“获取” > “编辑”。</li><li>将“ProvisioningState”值从“失败”更新为“成功”。</li><li>选择“PUT” 。</li></ol>|
|  |  |

## <a name="next-steps"></a>后续步骤

* 浏览 Azure 负载均衡器 [SKU 比较表](./skus.md)
* 了解 [Azure 负载均衡器限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
