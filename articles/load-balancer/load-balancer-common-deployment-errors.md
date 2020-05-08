---
title: 排查常见部署错误
titleSuffix: Azure Load Balancer
description: 描述如何解决部署 Azure 负载均衡器时的常见错误
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791079"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>排查 Azure 负载均衡器的常见 Azure 部署错误

本文介绍一些常见的 Azure 负载均衡器部署错误，并提供解决这些错误的信息。 如果需要某个错误代码的信息，而本文没有提供该信息，请告知我们。 在此页的底部，你可以留下反馈。 将跟踪 GitHub 问题的反馈。

## <a name="error-codes"></a>错误代码

| 错误代码 | 详细信息和缓解措施 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 公共 IP SKU 和负载均衡器 SKU 必须匹配。 确保 Azure 负载均衡器和公共 IP Sku 匹配。 建议为生产工作负荷使用标准 SKU。 了解有关[sku 中的差异](./skus.md)的详细信息  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 当未指定 SKU 或未部署标准公共 Ip 时，虚拟机规模集默认为基本负载均衡器。 使用单个实例上的标准公共 Ip 重新部署虚拟机规模集，以确保在从 Azure 门户部署虚拟机规模集时选择标准负载均衡器或只选择标准 LB。 |
|MaxAvailabilitySetsInLoadBalancerReached | 负载均衡器的后端池最多可包含150个可用性集。 如果没有为后端池中的 Vm 显式定义可用性集，则每个虚拟机都将进入其各自的可用性集。 因此，部署150独立 Vm 将意味着它会有150个可用性集，从而达到限制。 你可以部署可用性集，并将更多 Vm 添加到其中作为一种解决方法。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 对于给定的负载均衡器类型（内部、公用），不能有多个规则具有同一虚拟机规模集引用的相同后端端口和协议。 更新规则以更改此重复规则创建。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 对于给定的负载均衡器类型（内部、公用），不能有多个规则具有同一虚拟机规模集引用的相同后端端口和协议。 更新规则参数以更改此重复规则创建。 |
|AnotherInternalLoadBalancerExists| 在负载均衡器后端，只能有一个类型为 "内部引用" 的负载均衡器用于同一组 Vm/网络接口。 更新部署，确保只创建一个相同类型的负载均衡器。 |
|CannotUseInactiveHealthProbe| 不能有未被配置为虚拟机规模集运行状况的任何规则使用的探测。 确保正在使用设置的探测。 |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 不能有多个相同类型的负载均衡器（内部、公共）。 最多可以有一个内部负载均衡器和一个公共负载均衡器。 |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 多位置组虚拟机规模集或跨可用性区域虚拟机规模集不支持基本负载均衡器。 改用标准负载均衡器。 |
|ResourceDeploymentFailure| 如果负载平衡器处于 "失败" 状态，请按照以下步骤操作，使其从 "失败" 状态恢复：<ol><li>中转到https://resources.azure.com，然后用你的 Azure 门户凭据进行登录。</li><li>选择 "**读/写**"。</li><li>在左侧展开 "**订阅**"，然后展开包含要更新的负载平衡器的订阅。</li><li>展开 " **ResourceGroups**"，然后展开包含要更新的负载均衡器的资源组。</li><li>选择 "**LoadBalancers**"，**然后选择要**更新的负载均衡器，LoadBalancer_1 "。 **LoadBalancer_1** > </li><li>在**LoadBalancer_1**显示页上，选择 "**获取** > **编辑**"。</li><li>将**ProvisioningState**值从 "**失败**" 更新为 "**成功**"。</li><li>选择“PUT” ****。</li></ol>|
|  |  |

## <a name="next-steps"></a>后续步骤

* 查看 Azure 负载均衡器[SKU 比较表](./skus.md)
* 了解[Azure 负载均衡器限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
