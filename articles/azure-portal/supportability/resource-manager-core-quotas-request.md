---
title: Azure 资源管理器 vCPU 配额增加请求
description: Azure 资源管理器 vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843661"
---
# <a name="quota-increase-requests"></a>配额增加请求

每个区域的每个订阅在两个级别上为虚拟机和虚拟机规模集资源管理器 vCPU 配额。

第一层是所有 VM 系列的区域个 vcpu 限制总计。 第二层是每个 VM 系列个 vcpu 限制，如 D 系列个 vcpu。 每当要部署新的虚拟机时，该 VM 系列的新的和现有个 vcpu 使用的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的 vCPU 计数不应超过为订阅批准的总区域个 vcpu 配额。 如果超过了其中任一配额，则不允许进行 VM 部署。
可以从 Azure 门户中为 VM 序列请求增加个 vcpu 配额限制。 VM 序列配额增加会自动增加相同数量的区域个 vcpu 限制。

创建新订阅时，默认的区域个 vcpu 不能等于所有单个 VM 序列的默认 vCPU 配额之和。 这种情况可能会导致订阅的配额足以满足每个要部署的单个 VM 系列的要求。 对于所有部署，它可能缺乏足够的区域个 vcpu 配额。 在这种情况下，你将需要提交请求，以显式增加区域个 vcpu 限制。 区域个 vcpu 限制的总大小不能超过区域的所有 VM 系列上批准的配额的总和。

有关配额的详细信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)、 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

