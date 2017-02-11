---
title: "在经典门户中创建 VM | Microsoft Docs"
description: "在 Azure 经典门户中创建 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: ce2e72fe1e24968f315b5fbc303c0c4bc63a199d


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>在 Azure 经典门户中创建运行 Windows 的虚拟机
> [!div class="op_single_selector"]
> * [Azure 经典门户](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell：经典部署](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

了解如何通过**新的 Azure 门户**[使用 Resource Manager 部署模型执行这些步骤](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

本教程演示如何在 Azure 经典门户中创建运行 Windows 的 Azure 虚拟机 (VM)。 我们将使用 Windows Server 映像作为示例，但这只是 Azure 提供的众多映像的其中一个。 请注意，映像的选择取决于订阅。 例如，Windows 桌面映像适用于 MSDN 订户。

本部分演示如何使用 Azure 经典门户中的“从库中”选项创建虚拟机。 此选项提供的配置选项多于“快速创建”选项。 例如，如果你想要将虚拟机加入到虚拟网络，需要使用“从库中”选项。

你也可以使用[自己的映像](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)创建 VM。 若要了解此方法和其他方法，请参阅[创建 Windows 虚拟机的不同方式](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="video-walkthrough"></a>视频演练
下面是本教程的演练。

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a id="createvirtualmachine"></a> 创建虚拟机
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>后续步骤
* 了解如何在新的 Azure 门户中[使用 Resource Manager 部署模型创建 VM](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
* 登录到虚拟机。 有关说明，请参阅[登录到运行 Windows Server 的虚拟机](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* 附加磁盘以存储数据。 可以附加空磁盘和包含数据的磁盘。 有关说明，请参阅[将数据磁盘附加到使用经典部署模型创建的 Windows 虚拟机](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。




<!--HONumber=Nov16_HO3-->


