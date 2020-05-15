---
title: Azure 虚拟机规模集概述
description: 了解 Azure 虚拟机规模集和应用程序自动缩放方法。
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 09/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 986a2722515ac49aad9e655d1dcef06f5ce2e3dc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198414"
---
# <a name="what-are-virtual-machine-scale-sets"></a>什么是虚拟机规模集？
使用 Azure 虚拟机规模集可以创建并管理一组完全相同的、负载均衡的 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 规模集为应用程序提供高可用性，用于集中管理、配置和更新大量 VM。 可以使用虚拟机规模集为计算、大数据和容器工作负荷等领域构建大规模的服务。


## <a name="why-use-virtual-machine-scale-sets"></a>为何使用虚拟机规模集？
为了提供冗余性并改进性能，通常会跨多个实例来分发应用程序。 客户可能会通过负载均衡器来访问应用程序，由负载均衡器负责将请求分发到某个应用程序实例。 如果你需要进行维护或更新某个应用程序实例，则必须将客户分发到其他可用的应用程序实例。 为了满足客户的额外需求，可能需要增加运行应用程序的应用程序实例的数量。

Azure 虚拟机规模集为跨多个 VM 运行的应用程序提供管理功能，可以[自动缩放资源](virtual-machine-scale-sets-autoscale-overview.md)，还可以对流量进行负载均衡。 规模集具有下述主要优势：

- **易于创建和管理多个 VM**
    - 有多个运行应用程序的 VM 时，必须在整个环境中维持一致的配置。 为了确保应用程序性能可靠，所有 VM 的 VM 大小、磁盘配置和应用程序安装都应匹配。
    - 使用规模集时，所有 VM 实例都是根据相同的基础 OS 映像和配置来创建的。 使用这种方法可以轻松地管理数百个 VM，不需执行其他的配置任务或网络管理。
    - 规模集支持使用 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)进行基本的第 4 层流量分发操作，以及使用 [Azure 应用程序网关](../application-gateway/application-gateway-introduction.md)进行更高级的第 7 层流量分发和 TLS 终止操作。

- **提供高可用性和应用程序复原能力**
    - 规模集用于运行应用程序的多个实例。 如果某个 VM 实例出现问题，客户可以继续通过其他 VM 实例来访问应用程序，将中断的可能性降至最低。
    - 若要提高可用性，可以使用[可用性区域](../availability-zones/az-overview.md)，在单个或多个数据中心自动分发规模集中的 VM 实例。

- **允许应用程序随资源需求变化自动进行缩放**
    - 客户对应用程序的需求可能会在一天或一周内变化。 规模集可以遵循客户需求，在应用程序需求增加时自动增加 VM 实例数，在需求减少时减少 VM 实例数。
    - 另外，自动缩放还可以尽量降低在需求较低时运行应用程序的不必要的 VM 实例数，并可让客户在需求增加时继续享受可以接受的性能级别，而且会自动添加更多的 VM 实例。 此功能有助于降低成本，并根据需要高效地创建 Azure 资源。

- **大规模工作**
    - 规模集最多支持 1,000 个 VM 实例。 如果创建和上传自己的自定义 VM 映像，则该限制为 600 个 VM 实例。
    - 若要尽量提高生产工作负荷的性能，请使用 [Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)。


## <a name="differences-between-virtual-machines-and-scale-sets"></a>虚拟机和规模集的差异
规模集是使用虚拟机构建的。 使用规模集时，会提供管理层和自动层来运行和缩放应用程序。 可以改用手动方式来创建和管理单个 VM，也可以集成现有的工具，以便构建类似级别的自动化。 下表概述了规模集的优势（与手动管理多个 VM 实例相比）。

| 方案                           | VM 的手动组                                                                    | 虚拟机规模集 |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| 添加额外的 VM 实例        | 用于创建、配置和确保符合性的手动过程                             | 自动根据中心配置进行创建 |
| 流量均衡和分发 | 用于创建和配置 Azure 负载均衡器或应用程序网关的手动过程      | 可以自动创建 Azure 负载均衡器或应用程序网关以及与之集成 |
| 高可用性和冗余性   | 手动创建可用性集，或者跨可用性区域分发和跟踪 VM | 跨可用性区域或可用性集自动分发 VM 实例 |
| VM 缩放                     | 手动监视和 Azure 自动化                                                 | 根据主机指标、来宾中指标、Application Insights 或计划自动缩放 |

规模集不需额外付费。 只需为基础的计算资源（例如 VM 实例、负载均衡器或托管磁盘存储）付费。 在使用 VM 的过程中，管理和自动化功能（例如自动缩放和冗余性）不需额外付费。

## <a name="how-to-monitor-your-scale-sets"></a>如何监视规模集

使用[用于 VM 的 Azure Monitor](../azure-monitor/insights/vminsights-overview.md)，它具有简单的载入过程，并将自动从规模集中的 VM 收集重要的 CPU、内存、磁盘和网络性能计数器。 它还包括额外的监视功能和预定义的可视化效果，这些可视化效果可以帮助你专注于规模集的可用性和性能。

使用 Application Insights 启用对[虚拟机规模集应用程序](../azure-monitor/app/azure-vm-vmss-apps.md)的监视，以收集有关应用程序的详细信息，包括页面视图、应用程序请求和异常。 通过配置[可用性测试](../azure-monitor/app/monitor-web-app-availability.md)来模拟用户流量，进一步验证应用程序的可用性。

## <a name="next-steps"></a>后续步骤
若要开始，请在 Azure 门户中创建第一个虚拟机规模集。

> [!div class="nextstepaction"]
> [在 Azure 门户中创建规模集](quick-create-portal.md)
