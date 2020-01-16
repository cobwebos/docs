---
title: 了解如何将预留折扣应用于 Azure 数据资源管理器
description: 了解如何将预留折扣应用于 Azure 数据资源管理器加价计价器。
services: data-explorer
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 88ab9c475d417bc935cf5d2d67f1678794fb74d1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995619"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>了解如何将预留折扣应用于 Azure 数据资源管理器

购买 Azure 数据资源管理器预留容量后，预留折扣会自动应用到与预留属性和数量匹配的 Azure 数据资源管理器资源。 预留包括 Azure 数据资源管理器加价费用。 不包括计算、网络、存储或用于操作 Azure 数据资源管理器群集的任何其他 Azure 资源。 应该单独为这些资源购买预留。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>预留折扣应用于 Azure 数据资源管理器群集

预留折扣按小时应用于 Azure 数据资源管理器加价使用量。 对于非整小时运行的 Azure 数据资源管理器资源，预留折扣会自动应用到与预留属性匹配的其他数据资源管理器资源。 折扣可以应用到同时运行的 Azure 数据资源管理器资源。 如果与预留属性匹配的 Azure 数据资源管理器资源不是整小时运行，则无法获得该小时的完整预留折扣权益。

> [!NOTE]
> * 强烈建议为用于 Azure 数据资源管理器群集以最大限度节省预留容量的虚拟机购买[预留容量](../../virtual-machines/windows/prepay-reserved-vm-instances.md)。
> * 购买预留会向所有区域应用折扣。

## <a name="examples"></a>示例

以下示例演示如何根据购买的加价单位及其运行时间来应用 Azure 数据资源管理器预留容量折扣。
例如，对于引擎群集大小： **2 D11_v2 vm**，按需计费的总费用为每小时 Azure 数据资源管理器标记计量的四个单位。 

**应用场景 1** 

为 8 个 Azure 数据资源管理器加价单位购买 Azure 数据资源管理器预留容量。 运行一个总共 16 个核心、具有 2 个 D13_v2 VM 的引擎群集，该群集与剩余的预留属性匹配，费用为每小时 16 个单位的 Azure 数据资源管理器加价单位。 对于 8 核的 Azure 数据资源管理器计算用量，你需要按即用即付价格付费；对于 8 核的 Azure 数据资源管理器加价单位用量，获得一小时的预留折扣。

余下的示例假设购买的 Azure 数据资源管理器预留容量用于 1 个 16 核的 Azure 数据资源管理器群集，并且剩余的预留属性与正在运行的 Azure 数据资源管理器群集匹配。

**应用场景 2** 

在两个不同的区域分别运行两个八核的 Azure 数据资源管理器引擎群集，为时一小时。 对这两个群集以及它们使用的 16 个单位的 Azure 数据资源管理器加价单位应用 16 核的预留折扣。

**应用场景 3** 

下午 1 点到 1:30 运行一个 16 核的 Azure 数据资源管理器引擎群集。 下午 1:30 到 2:00 运行另一个 16 核的 Azure 数据资源管理器引擎群集。 预留折扣同时涵盖这两个数据库。

**应用场景 4** 

下午 1 点到下午 1:45 运行一个 16 核的 Azure 数据资源管理器引擎群集。 下午 1:30 到 2:00 运行另一个 16 核的 Azure 数据资源管理器引擎群集。 对于 15 分钟的重叠期，向你收取即用即付费用。 预留折扣应用于剩余时间的 Azure 数据资源管理器加价用量。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

* [通过 Azure 数据资源管理器预留容量为 Azure 数据资源管理器计算资源预付费](../../data-explorer/pricing-reserved-capacity.md)  
* [什么是适用于 Azure 的预留](save-compute-costs-reservations.md)  
* [管理 Azure 预留](manage-reserved-vm-instance.md)  
* [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)
* [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
* [了解 CSP 订阅的预留使用情况](https://docs.microsoft.com/partner-center/azure-reservations)
