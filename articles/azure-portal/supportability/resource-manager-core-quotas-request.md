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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843661"
---
# <a name="quota-increase-requests"></a>配额增加请求

虚拟机和虚拟机规模集的资源管理器 vCPU 配额在每个区域的每个订阅的两层强制实施。

第一层是所有 VM 系列的总区域 vCPU 限制。 第二层是每个 VM 系列 vCPU 限制，如 D 系列 vCPU。 每当部署新的虚拟机时，该 VM 系列的新 vCPU 和现有 vCPU 使用量的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外，在所有 VM 系列中部署的新 vCPU 和现有 vCPU 计数总数不应超过为订阅批准的区域 vCPU 总配额。 如果超过其中任一配额，则不允许 VM 部署。
可以从 Azure 门户请求提高 VM 系列的 vCPU 配额限制。 VM 系列配额的增加会自动将区域总 vCPU 限制增加相同数量。

创建新订阅时，默认的"区域总计"vCPU 可能不等于所有单个 VM 系列的默认 vCPU 配额的总和。 这一事实可能导致订阅具有足够的配额，每个单独的 VM 系列，您要部署。 对于所有部署，可能没有足够的区域可用 CPU 配额。 在这种情况下，您需要提交请求以显式增加"区域总 vCPU"限制。 区域 vCPU 总限制不能超过该区域所有 VM 系列中已批准配额的总和。

有关配额的详细信息，请参阅虚拟机[vCPU 配额](../../virtual-machines/windows/quotas.md)和[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

