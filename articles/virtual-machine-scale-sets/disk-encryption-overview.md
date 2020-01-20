---
title: 为虚拟机规模集启用 Azure 磁盘加密
description: 本文介绍如何为虚拟机规模集启用 Microsoft Azure 磁盘加密
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278977"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure 磁盘加密

Azure 磁盘加密为虚拟机的 OS 和数据磁盘提供卷加密，有助于保护和保护数据，以满足组织的安全性和符合性承诺。 若要了解详细信息，请参阅[Azure 磁盘加密： Linux vm](../virtual-machines/linux/disk-encryption-overview.md)和[azure 磁盘加密： Windows vm](../virtual-machines/windows/disk-encryption-overview.md)  

Azure 磁盘加密还可以应用于 Windows 和 Linux 虚拟机规模集，在这些实例中：
- 用托管磁盘创建的规模集。 本机（或非托管）磁盘规模集不支持 Azure 磁盘加密。
- Windows 规模集的 OS 和数据卷。
- Linux 规模集中的数据量。 目前，Linux 规模集不支持 OS 磁盘加密。

只需几分钟即可了解用于虚拟机规模集的 Azure 磁盘加密基础知识，只需几分钟即可使用[Azure CLI 加密虚拟机规模集](disk-encryption-cli.md)或[使用 Azure PowerShell 教程对虚拟机规模集进行加密](disk-encryption-powershell.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 资源管理器加密虚拟机规模集](disk-encryption-azure-resource-manager.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)
- [将 Azure 磁盘加密与虚拟机规模集扩展排序配合使用](disk-encryption-extension-sequencing.md)
