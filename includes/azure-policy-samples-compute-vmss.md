---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 725d37a6521c874d14291ea7e3c53ea94907eb17
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138093"
---
### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

|  |  |
|---------|---------|
| [在 VM 未使用托管磁盘时审核](../articles/azure-policy/scripts/create-vm-managed-disk.md) | 如果创建了未使用托管磁盘的虚拟机，则进行审核。|
| [使用托管磁盘创建 VM](../articles/azure-policy/scripts/use-managed-disk-vm.md) | 要求虚拟机使用托管磁盘。|
| [拒绝混合使用权益](../articles/azure-policy/scripts/deny-hybrid-use.md) | 禁止使用 Azure 混合使用权益 (AHUB)。 在想禁止使用本地许可证时使用。 |
| [只允许特定 VM 平台映像](../articles/azure-policy/scripts/allow-certain-vm-image.md) | 要求虚拟机使用 UbuntuServer 的特定版本。 |