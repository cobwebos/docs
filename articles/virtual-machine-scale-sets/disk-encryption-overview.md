---
title: 为虚拟机规模集启用 Azure 磁盘加密
description: 本文提供有关为虚拟机规模集启用 Microsoft Azure 磁盘加密的说明
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278977"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure 磁盘加密

Azure 磁盘加密为虚拟机的操作系统和数据磁盘提供卷加密，帮助保护数据，以便符合在组织安全性与符合性方面所做的承诺。 要了解更多信息，请参阅[Azure 磁盘加密：Linux VM](../virtual-machines/linux/disk-encryption-overview.md)和[Azure 磁盘加密：Windows VM](../virtual-machines/windows/disk-encryption-overview.md)  

在以下情况下，Azure 磁盘加密也可以应用于 Windows 和 Linux 虚拟机规模集：
- 使用托管磁盘创建的规模集。 本机（或非托管）磁盘规模集不支持 Azure 磁盘加密。
- Windows 规模集中的操作系统和数据卷。
- Linux 规模集中的数据卷。 目前，Linux 规模集不支持 OS 磁盘加密。

通过[使用 Azure CLI 加密虚拟机规模集](disk-encryption-cli.md)或[使用 Azure PowerShell 加密虚拟机规模集](disk-encryption-powershell.md)教程，仅在几分钟内即可了解虚拟机规模集的 Azure 磁盘加密基础知识。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 资源管理器加密虚拟机规模集](disk-encryption-azure-resource-manager.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)
- [将 Azure 磁盘加密与虚拟机规模集扩展排序配合使用](disk-encryption-extension-sequencing.md)
