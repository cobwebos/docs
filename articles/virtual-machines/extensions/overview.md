---
title: Azure 虚拟机扩展和功能 | Microsoft Docs
description: 了解什么是 Azure VM 扩展以及如何将其与 Azure 虚拟机配合使用
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: ec201f7f82aea97b9927b85a6b185fad51f6081d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412594"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 虚拟机扩展和功能
Azure 虚拟机 (VM) 扩展是小型应用程序，可在 Azure VM 上提供部署后配置和自动化任务，用户可以使用现有映像并将其自定义为部署的一部分，摆脱构建自定义映像的麻烦。

Azure 平台可承载许多扩展，其中包括 VM 配置、监视、安全性和实用工具应用程序。 发布服务器采用某个应用程序，然后将其包装到扩展并简化安装，用户只需提供必需的参数即可。 

 如果扩展存储库中不存在应用程序，有大量第一方和第三方扩展可供选择，可以通过自己的脚本和命令使用自定义脚本扩展并配置 VM。

扩展的主要应用场景示例：
* VM 配置，用户可以使用 Powershell DSC (Desired State Configuration)、Chef、Puppet 和自定义脚本扩展安装 VM 配置代理并配置 VM。 
* AV 产品（如 Symantec、ESET）。
* VM 漏洞工具，如 Qualys、Rapid7 和 HPE。
* VM 和应用监视工具，如 DynaTrace、Azure 网络观察程序、Site24x7 和 Stackify。

可以使用新的 VM 部署捆绑扩展。 例如，它们可能属于大型部署中的一部分，在 VM 预配上配置应用程序，或针对任何受支持的扩展操作系统后部署运行。

## <a name="how-can-i-find-what-extensions-are-available"></a>如何了解哪些扩展可用？
可以在门户的 VM 边栏选项卡中的扩展下查看可用的扩展，此处显示一小部分，如需查看完整列表，可使用 CLI 工具，请参阅[了解适用于 Linux 的 VM 扩展](features-linux.md)和[了解适用于 Windows 的扩展](features-windows.md)。

## <a name="how-can-i-install-an-extension"></a>如何安装扩展？
可以使用 Azure CLI 2.0、Azure PowerShell、Azure 资源管理器模板和 Azure 门户管理 Azure VM 扩展。 若要试用扩展，可以转到 Azure 门户，选择自定义脚本扩展，然后传入命令/脚本并运行扩展。

如果需要 CLI 或资源管理器模板在门户中添加的相同扩展，请参阅不同的扩展文档，例如 [Windows 自定义脚本扩展](custom-script-windows.md)和 [Linux 自定义脚本扩展](custom-script-linux.md)。

## <a name="how-do-i-manage-extension-application-lifecycle"></a>如何管理扩展应用程序生命周期？
不需要直接连接到 VM 即可安装或删除扩展。 因为在 VM 外管理 Azure 扩展应用程序生命周期且已与 Azure 平台集成，因此还可以获得扩展的集成状态。

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>关于扩展，有什么其他需要考虑的内容？
与其他任何应用程序一样，扩展安装应用程序也有一些要求，对于扩展，存在一系列支持的 Windows 和 Linux OS，并且需要安装 Azure VM 代理。 某些单独的 VM 扩展应用程序可能有其自己的环境先决条件，如对终结点的访问权限。

## <a name="next-steps"></a>后续步骤
* 有关 Linux 代理和扩展工作原理的详细信息，请参阅[适用于 Linux 的 Azure VM 扩展和功能](features-linux.md)。
* 有关 Windows 来宾代理和扩展工作原理的详细信息，请参阅[适用于 Windows 的 Azure VM 扩展和功能](features-windows.md)。  
* 若要安装 Windows 来宾代理，请参阅 [Azure Windows 虚拟机代理概述](agent-windows.md)。  
* 若要安装 Linux 代理，请参阅 [Azure Linux 虚拟机代理概述](agent-linux.md)。  

