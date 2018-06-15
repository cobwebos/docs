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
ms.openlocfilehash: e9f2f6eaab1b3504e549171ce6c60ecd9b3b5c7b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664400"
---
### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

|  |  |
|---------|---------|
| [在 VM 未使用托管磁盘时审核](../articles/azure-policy/scripts/create-vm-managed-disk.md) | 如果创建了未使用托管磁盘的虚拟机，则进行审核。|
| [使用托管磁盘创建 VM](../articles/azure-policy/scripts/use-managed-disk-vm.md) | 要求虚拟机使用托管磁盘。|
| [拒绝混合使用权益](../articles/azure-policy/scripts/deny-hybrid-use.md) | 禁止使用 Azure 混合使用权益 (AHUB)。 想禁止使用本地许可证时使用。 |
| [只允许特定 VM 平台映像](../articles/azure-policy/scripts/allow-certain-vm-image.md) | 要求虚拟机使用 UbuntuServer 的特定版本。 |