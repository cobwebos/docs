---
title: Azure 应用服务计划概述 | Microsoft Docs
description: 了解针对 Azure 应用服务的应用服务计划的工作原理，以及如何利用它们进行管理。
keywords: 应用服务, azure 应用服务, 缩放, 可缩放, 可伸缩性, 应用服务计划, 应用服务成本
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 268844eae8dc06937529e79d52515cad2f6da3f4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862353"
---
# <a name="azure-app-service-plan-overview"></a>Azure 应用服务计划概述

在应用服务中，应用在应用服务计划中运行。 应用服务计划为要运行的 Web 应用定义一组计算资源。 这些计算资源类似于传统 Web 托管方案中的[_服务器场_](https://wikipedia.org/wiki/Server_farm)。 可将一个或多个应用配置为在相同的计算资源中（或相同的应用服务计划中）运行。

在特定的区域（例如“西欧”）中创建应用服务计划时，将为该区域中的该计划创建一组计算资源。 放入此应用服务计划的任何应用都在应用服务计划定义的这些计算资源中运行。 每个应用服务计划定义：

- 区域（美国西部、美国东部，等等）
- VM 实例数
- VM 实例大小（“小型”、“中型”、“大型”）
- 定价层（“免费”、“共享”、“基本”、“标准”、“高级”、“高级 V2”、“隔离”、“消耗”）

应用服务计划的定价层确定了所提供的应用服务功能和计划费用。 有以下几个类别的定价层：

- **共享计算**：“免费”和“共享”，这两个基本层在其他应用服务应用（包括其他客户的应用）所在的同一个 Azure VM 上运行应用。 这些层针对共享资源中运行的每个应用分配 CPU 配额，且资源不可横向扩展。
- **专用计算**：“基本”、“标准”、“高级”和“高级 V2”层在专用的 Azure VM 上运行应用。 只有同一应用服务计划中的应用可以共享相同的计算资源。 层越高，可用于横向扩展的 VM 实例就越多。
- **隔离**：此层在专用的 Azure 虚拟网络中运行专用的 Azure VM，这些虚拟网络基于应用的计算资源隔离提供网络隔离。 此层提供最大的横向扩展能力。
- **消耗**：此层仅适用于[函数应用](../azure-functions/functions-overview.md)。 它根据工作负荷动态缩放函数。 有关详细信息，请参阅 [Azure Functions 托管计划的比较](../azure-functions/functions-scale.md)。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

每个层还提供应用服务功能的特定子集。 这些功能包括自定义域和 SSL 证书、自动缩放、部署槽位、备份、流量管理器集成，等等。 层越高，可用的功能越多。 若要了解每个定价层支持的功能，请参阅[应用服务计划详细信息](https://azure.microsoft.com/pricing/details/app-service/plans/)。

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> 与“标准”层相比，新的“高级 V2”层提供 [Dv2 系列 VM](../virtual-machines/windows/sizes-general.md#dv2-series)，此系列 VM 配备更快的处理器、SSD 存储以及双倍的内存核心比。 **高级 V2** 还支持通过增加实例数扩大规模，同时仍提供标准计划中的所有高级功能。 **高级 V2** 中包含现有“高级”层中提供的所有功能。
>
> 类似于其他专用层，以下三种 VM 大小可用于此层：
>
> - 小型（一个 CPU 核心，3.5 GiB 内存） 
> - 中型（两个 CPU 核心，7 GiB 内存） 
> - 大型（四个 CPU 核心，14 GiB 内存）  
>
> 有关**高级 V2** 定价信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。
>
> 若要开始使用新的“高级 V2”定价层，请参阅[为应用服务配置“高级 V2”层](app-service-configure-premium-tier.md)。

## <a name="how-does-my-app-run-and-scale"></a>应用如何运行和缩放？

在“免费”和“共享”层中，应用遵循共享 VM 实例上的 CPU 分钟配额，且不能横向扩展。在其他层中，应用按如下所述运行和缩放。

在应用服务中创建某个应用时，该应用将被放入应用服务计划。 该应用运行时，将在应用服务计划中配置的所有 VM 实例上运行。 如果同一应用服务计划中包含多个应用，这些应用将共享相同的 VM 实例。 如果对某个应用使用多个部署槽位，所有部署槽位也在相同的 VM 实例上运行。 如果启用诊断日志、执行备份或运行 Web 作业，它们也使用这些 VM 实例上的 CPU 周期和内存。

于是，应用服务计划便成了应用服务应用的缩放单位。 如果将计划配置为运行五个 VM 实例，该计划中的所有应用将在所有五个实例上运行。 如果为计划配置了自动缩放，该计划中的所有应用将会根据自动缩放设置一起横向扩展。

有关横向扩展应用的信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)。

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>应用服务计划的费用是多少？

本部分介绍应用服务应用的计费方式。 有关区域特定的详细定价信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。

除“免费”层外，应用服务计划会根据所用的计算资源量产生小时费用。

- 在“共享”层中，每个应用遵循 CPU 分钟配额，因此，每个应用会根据 CPU 配额产生小时费用。
- 在专用计算层（“基本”、“标准”、“高级”、“高级 V2”）中，应用服务计划定义了应用可缩放到的 VM 实例数，因此，应用服务计划中的每个 VM 实例会产生小时费用。 不管这些 VM 实例上运行了多少个应用，其计费方式都是相同的。 为了避免意外的费用，请参阅[清理应用服务计划](app-service-plan-manage.md#delete)。
- 在“隔离”层中，应用服务环境定义了运行应用的隔离辅助角色数目，每个辅助角色按小时计费。 此外，运行应用服务环境本身也会产生一笔基本的小时费用。 
- （仅限 Azure Functions）“消耗”层动态分配 VM 实例来为函数应用的工作负荷提供服务，由 Azure 每秒动态计费。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。

使用随附的应用服务功能（配置自定义域、SSL 证书、部署槽位、备份等）不会产生费用。 例外情况包括：

- 应用服务域 - 在 Azure 中购买以及每年续订时付费。
- 应用服务证书 - 在 Azure 中购买以及每年续订时付费。
- 基于 IP 的 SSL 连接 - 每个基于 IP 的 SSL 连接会产生小时费用，但“标准”层或某些更高的层免费提供了一个基于 IP 的 SSL 连接。 基于 SNI 的 SSL 连接免费。

> [!NOTE]
> 如果将应用服务与其他 Azure 服务集成，可能需要考虑其他这些服务的费用。 例如，如果使用 Azure 流量管理器在特定的地理位置缩放应用，则 Azure 流量管理器也会根据用量收取费用。 若要估算 Azure 中的跨服务费用，请参阅[定价计算器](https://azure.microsoft.com/pricing/calculator/)。 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>如果应用需要更多的功能怎么办？

随时可以提高和降低应用服务计划。 只需更改计划的定价层即可。 一开始可以选择一个较低的定价层，以后需要更多的应用服务功能时，可以提高层。

例如，可以在“免费”应用服务计划中开始免费测试 Web 应用。 想要将[自定义 DNS 名称](app-service-web-tutorial-custom-domain.md)添加到 Web 应用时，只需将计划提高到“共享”层。 以后想要添加[自定义 SSL 证书](app-service-web-tutorial-custom-ssl.md)时，可将计划提高到“基本”层。 想要部署[过渡环境](web-sites-staged-publishing.md)时，可提高到“标准”层。 需要更多的核心、内存或存储时，可提高到同一层中的更大 VM 大小。

反之亦然。 如果觉得自己不再需要较高层的功能，可以降低到更低的层，从而节省资金。

有关提高应用服务计划的信息，请参阅[纵向扩展 Azure 中的应用](web-sites-scale.md)。

如果你的应用与其他应用在同一个应用服务计划中，可以通过隔离计算资源来提高你的应用的性能。 为此，可将应用移到单独的应用服务计划中。 有关详细信息，请参阅[将应用移到另一个应用服务计划](app-service-plan-manage.md#move)。

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>应该将应用放入新计划还是现有计划中？

由于应用服务计划分配的计算资源会产生费用（请参阅[应用服务计划的费用是多少？](#cost)），将多个应用放入一个应用服务计划可能会节省资金。 只要现有的计划能够提供足够的资源来处理负载，就可以持续将应用添加到该计划。 但请记住，同一应用服务计划中的所有应用共享相同的计算资源。 若要确定新的应用是否包含所需的资源，需要了解现有应用服务计划的容量，以及新应用预期的负载。 应用服务计划过载可能会导致新应用和现有应用停机。

在以下情况下，请将应用隔离到新应用服务计划中：

- 该应用占用大量资源。
- 想要独立于现有计划中的其他应用缩放该应用。
- 该应用需要其他地理区域中的资源。

这样一来，可以为应用分配新的资源集，并更好地控制应用。

## <a name="manage-an-app-service-plan"></a>管理应用服务计划

> [!div class="nextstepaction"]
> [管理应用服务计划](app-service-plan-manage.md)
