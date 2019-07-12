---
title: 在 Azure 中重新部署 Windows 虚拟机 | Microsoft Docs
description: 如何在 Azure 中重新部署 Windows 虚拟机以缓解 RDP 连接问题。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: gwallace
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 48b10b6fd854cc6da1f2ea2d48852d162e995946
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710258"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>将 Windows 虚拟机重新部署到新的 Azure 节点
如果在对远程桌面 (RDP) 连接或应用程序对基于 Windows 的 Azure 虚拟机 (VM) 的访问进行故障排除时遇到困难，重新部署 VM 可能会有帮助。 重新部署 VM 时，Azure 会关闭该 VM，并将其移到 Azure 基础结构中的新节点，然后重新打开它，同时保留所有配置选项和关联的资源。 本文介绍了如何使用 Azure PowerShell 或 Azure 门户重新部署 VM。

> [!NOTE]
> 重新部署 VM 后，临时磁盘将丢失，并将更新与虚拟网络接口关联的动态 IP 地址。 


## <a name="using-azure-powershell"></a>使用 Azure PowerShell
请确保已在计算机上安装最新的 Azure PowerShell 1.x。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

以下示例部署 `myResourceGroup` 资源组中名为 `myVM` 的 VM：

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>后续步骤
如果在连接 VM 时遇到问题，可以在 [troubleshooting RDP connections](troubleshoot-rdp-connection.md)（RDP 连接故障排除）或 [detailed RDP troubleshooting steps](detailed-troubleshoot-rdp.md)（详细的 RDP 故障排除步骤）中找到具体的帮助信息。 如果无法访问在 VM 上运行的应用程序，还可以阅读[应用程序故障排除问题](../windows/troubleshoot-app-connection.md)。

