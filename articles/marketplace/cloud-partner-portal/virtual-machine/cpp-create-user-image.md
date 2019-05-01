---
title: 为 Azure 市场创建用户 VM 映像 | Microsoft Docs
description: 列出创建用户 VM 映像所需的步骤和参考。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744102"
---
# <a name="create-a-user-vm-image"></a>创建用户 VM 映像

本文介绍了从通用化 VHD 创建非托管映像所需的两个常规步骤。  将提供参考资料来指导你完成每个步骤：捕获映像并通用化该映像。


## <a name="capture-the-vm-image"></a>捕获 VM 映像

遵照以下文章中的说明捕获对应于访问方法的 VM：

-  PowerShell：[如何从 Azure VM 创建非托管 VM 映像](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI：[如何创建虚拟机或 VHD 的映像](../../../virtual-machines/linux/capture-image.md)
-  API：[虚拟机 - 捕获](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>通用化 VM 映像

由于用户映像是从以前已通用化的 VHD 生成的，因此也应该通用化该映像。  同样，请选择对应于访问机制中的以下文章。  （捕获磁盘时可能已将其通用化。）

-  PowerShell：[通用化 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI：[步骤 2：创建 VM 映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API：[虚拟机 - 通用化](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>后续步骤

接下来，将[创建证书](cpp-create-key-vault-cert.md)并将其存储在新的 Azure Key Vault 中。  建立与 VM 的安全 WinRM 连接需要此证书。
