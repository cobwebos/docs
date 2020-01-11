---
title: Azure 资源管理器 vCPU 配额增加请求 | Microsoft Docs
description: Azure 资源管理器 vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897099"
---
# <a name="quota-increase-requests"></a>配额增加请求

每个区域的每个订阅在两个级别上为虚拟机和虚拟机规模集资源管理器 vCPU 配额。 

第一层是区域个 vcpu 限制（跨所有 VM 系列），第二层是每个 VM 系列个 vcpu 限制（如 D 系列个 vcpu）。 每当部署新 VM 时，该 VM 系列的新的和现有个 vcpu 使用的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的 vCPU 计数不应超过为订阅批准的总区域个 vcpu 配额。 如果超过了上述任一配额，将不允许部署 VM。
可以从 Azure 门户中为 VM 序列请求增加个 vcpu 配额限制。 VM 序列配额增加会自动增加相同数量的区域个 vcpu 限制。 

创建新订阅时，默认的区域个 vcpu 不能等于所有单个 VM 序列的默认 vCPU 配额之和。 这可能会导致订阅中每个要部署的单个 VM 序列具有足够的配额，但没有足够的配额来容纳所有部署的区域个 vcpu。 在这种情况下，你将需要提交请求，以显式增加区域个 vcpu 限制。 区域个 vcpu 限制的总大小不能超过区域的所有 VM 系列上批准的配额的总和。

在[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制](https://aka.ms/quotalimits)页上了解有关配额的详细信息。 

