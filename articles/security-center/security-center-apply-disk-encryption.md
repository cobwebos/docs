---
title: 在 Azure 安全中心中应用磁盘加密 | Microsoft 文档
description: 本文档演示如何实现 Azure 安全中心建议**应用磁盘加密**。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473272"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>在 Azure 安全中心中应用磁盘加密

Azure 安全中心建议在 Windows 和 Linux VM 上的未加密磁盘上使用 Azure 磁盘加密。 通过磁盘加密，可以对 Windows 和 Linux IaaS VM 磁盘进行加密。  对于 VM 上的 OS 和数据卷，建议使用加密。

磁盘加密使用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能。 这些功能提供 OS 和数据加密来帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 磁盘加密与 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 集成，可帮助你控制和管理密钥保管库订阅中的磁盘加密密钥和机密，同时确保 VM 磁盘中的所有数据可在 [Azure 存储](https://azure.microsoft.com/documentation/services/storage/)中静态加密。

有关受支持的 Windows 和 Linux 版本的列表，请参阅 Azure 磁盘加密文档中支持 VM[和操作系统](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems)。

## <a name="implement-the-recommendation"></a>实现该建议
1. 在 **"建议"** 页中，选择**磁盘加密应用于虚拟机**。
2. 从**不正常的资源**中，选择建议为其提供磁盘加密的 VM。
3. 按照说明操作，对这些 VM 应用加密。

![应用磁盘加密](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

若要加密已被安全中心标识为需要加密的 Azure 虚拟机，建议执行以下步骤：

* 安装和配置 Azure PowerShell。 此步骤用于运行设置先决条件所需的 PowerShell 命令，这些先决条件是加密 Azure 虚拟机所必需的。
* 获取并运行 Azure 磁盘加密先决条件 Azure PowerShell 脚本。
* 加密虚拟机。

[使用 Azure PowerShell 加密 Windows IaaS VM](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - 引导您完成这些步骤，并假定您正在使用 Windows 客户端计算机，从中可以配置磁盘加密。

有许多方法可用于 Azure 虚拟机。 如果您已经精通 Azure PowerShell 或 Azure CLI，则您可能更喜欢使用替代方法。 要了解这些其他方法，请参阅[Azure 磁盘加密](../security/fundamentals/encryption-overview.md)。

## <a name="see-also"></a>请参阅
本文档演示了如何实现安全中心建议“应用磁盘加密”。 要了解有关磁盘加密的更多详细信息，请参阅：

* [使用 Azure 密钥保管库进行加密和密钥管理](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/)（视频，36 分钟 39 秒）-- 了解如何对 IaaS VM 和 Azure 密钥保管库使用磁盘加密管理来帮助保护和保护您的数据。
* [Azure 磁盘加密](../security/fundamentals/encryption-overview.md)（文档）--了解如何为 Windows 和 Linux VM 启用磁盘加密。