---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: eadff6f61bd9e5979ae835230de53562ff8542d1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003628"
---
### <a name="virtual-machines"></a>虚拟机

|  |  |
|---------|---------|
| [允许自定义资源组中的 VM 映像](../articles/governance/policy/samples/allow-custom-vm-image.md) |  要求自定义映像来自已批准的资源组。 指定已批准的资源组的名称。 |
| [允许的存储帐户和虚拟网络的 SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 要求存储帐户和虚拟机使用已批准的 SKU。 使用内置策略以确保使用已批准的 SKU。 指定已批准的虚拟机 SKU 数组和已批准的存储帐户 SKU 数组。 |
| [已批准的 VM 映像](../articles/governance/policy/samples/allowed-custom-images.md) | 要求在环境中仅部署已批准的自定义映像。 指定已批准的映像 ID 的数组。 |
| [如果扩展不存在，则进行审核](../articles/governance/policy/samples/audit-ext-not-exist.md) | 如果扩展不是通过虚拟机部署的，则进行审核。 指定扩展发布者和类型以检查是否已部署扩展。 |
| [不允许使用 VM 扩展](../articles/governance/policy/samples/not-allowed-vm-ext.md) | 禁止使用指定的扩展。 指定一个数组，其中包含被禁止的扩展类型。 |