---
title: Azure 资源管理器 vCPU 配额增加请求 | Microsoft Docs
description: Azure 资源管理器 vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076816"
---
# <a name="quota-increase-requests"></a>配额增加请求

为虚拟机和虚拟机规模集资源管理器 vCPU 配额是在为每个订阅，每个区域中的两个层强制实施。 

第一层的总区域的 Vcpu 限制 （跨所有 VM 系列中），第二层是每个 VM 系列的 Vcpu 限制 （例如 D 系列 Vcpu)。 每当新的 VM 时要部署的 VM 系列的新的和现有 Vcpu 使用率总和不能超过批准该特定的 VM 系列的 vCPU 配额。 此外，在所有 VM 系列中部署的总的新的和现有 vCPU 计数不应超过订阅的已批准的总区域的 Vcpu 配额。 如果超过了上述任一配额，将不允许部署 VM。
你可以从 Azure 门户来请求增加的 VM 系列的 Vcpu 配额限制。 中的 VM 系列配额增加自动增加总区域的 Vcpu 限制相同的量。 

创建新订阅时，默认区域 Vcpu 总数可能不是等于默认对所有单个 VM 系列的 vCPU 配额的总和。 这可能导致具有足够的配额为你想要部署，每个单个 VM 系列，但没有足够的配额的区域 Vcpu 总数的所有部署的订阅。 在这种情况下，需要提交请求以显式增大总区域的 Vcpu 数限制。 区域的 Vcpu 总数限制不能超过已批准的配额的和区域的所有 VM 系列上。

了解有关配额的详细信息[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)并[Azure 订阅和服务限制](https://aka.ms/quotalimits)页。 

