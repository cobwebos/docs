---
title: 从基本内部版升级到标准内部版 - Azure 负载均衡器
description: 本文介绍如何将 Azure 内部负载均衡器从基本 SKU 升级到标准 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 960897abca67bf2a43c4c056b8dfa8cce0119faa
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871586"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升级 Azure 内部负载均衡器 - 不需出站连接
[Azure 标准负载均衡器](load-balancer-overview.md)通过区域冗余提供丰富的功能和高可用性。 有关负载均衡器 SKU 的详细信息，请参阅[比较表](https://docs.microsoft.com/azure/load-balancer/skus#skus)。

本文介绍了一个 PowerShell 脚本，该脚本使用与基本负载均衡器相同的配置来创建标准负载均衡器，并将流量从基本负载均衡器迁移到标准负载均衡器。

## <a name="upgrade-overview"></a>升级概述

我们提供了一个用于执行以下操作的 Azure PowerShell 脚本：

* 在指定的位置创建标准内部 SKU 负载均衡器。 请注意，标准内部负载均衡器不会提供[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。
* 将基本 SKU 负载均衡器的配置无缝复制到新创建的标准负载均衡器。
* 将专用 Ip 从基本负载均衡器无缝移动到新创建的标准负载均衡器。
* 将 Vm 从基本负载均衡器的后端池无缝移动到标准负载均衡器的后端池

### <a name="caveatslimitations"></a>注意事项/限制

* 脚本仅支持内部负载均衡器升级，无需任何出站连接。 如果需要某些 Vm 的[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)，请参阅此[页面](upgrade-InternalBasic-To-PublicStandard.md)了解相关说明。 
* 基本负载均衡器需要与后端 Vm 和 Nic 位于同一资源组中。
* 如果在不同的区域中创建标准负载均衡器，则无法将旧区域中的 Vm 关联到新创建的标准负载均衡器。 若要解决此限制，请确保在新区域中创建新的 VM。
* 如果你的负载均衡器没有任何前端 IP 配置或后端池，你可能会遇到运行脚本时遇到的错误。 请确保它们不为空。

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>将 IP 分配方法更改为静态前端 IP 配置（如果此步骤已为静态，则忽略此步骤）

1. 选择左侧菜单中的 "**所有服务**"，选择 "**所有资源**"，然后从 "资源" 列表中选择你的基本负载均衡器。

2. 在 "**设置**" 下，选择 "**前端 ip 配置**"，然后选择第一个前端 ip 配置。 

3. 对于**分配**，选择 "**静态**"

4. 对于基本负载均衡器的所有前端 IP 配置，重复步骤3。


## <a name="download-the-script"></a>下载脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureILBUpgrade/3.0)下载迁移脚本。
## <a name="use-the-script"></a>使用脚本

根据本地 PowerShell 环境的设置和首选项，可以使用两个选项：

* 如果你尚未安装 Azure Az 模块或者不介意卸载 Azure Az 模块，最佳做法是使用 `Install-Script` 选项运行该脚本。
* 如果需要保留 Azure Az 模块，则最佳做法是下载并直接运行该脚本。

若要确定是否安装了 Azure Az 模块，请运行 `Get-InstalledModule -Name az`。 如果未看到任何已安装的 Az 模块，可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用 Install-Script 方法安装

只有尚未在计算机上安装 Azure Az 模块时才能使用此选项。 如果已安装，则以下命令将显示错误。 可以卸载 Azure Az 模块，或者另一个选项手动下载并运行该脚本。
  
使用以下命令运行该脚本：

`Install-Script -Name AzureILBUpgrade`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用脚本安装

如果已安装某些 Azure Az 模块并且无法卸载它们（或者不想卸载），可以使用脚本下载链接中的“手动下载”选项卡手动下载该脚本。  此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动下载包](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 检查所需的参数：

   * **rgName： [String]： Required** –这是现有基本负载均衡器和新标准负载均衡器的资源组。 若要查找此字符串值，请导航到 Azure 门户，选择你的基本负载均衡器源，并单击负载均衡器的**概述**。 资源组位于该页面上。
   * **oldLBName： [String]： Required** –这是要升级的现有基本均衡器的名称。 
   * **newlocation： [String]： Required** –这是将在其中创建标准负载均衡器的位置。 建议将所选基本负载均衡器的同一位置继承到标准负载均衡器，以便更好地与其他现有资源关联。
   * **newLBName： [String]： Required** –这是要创建的标准负载均衡器的名称。
1. 使用相应的参数运行脚本。 完成该脚本可能需要 5 到 7 分钟时间。

    **示例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>用于将配置从 v1 迁移到 v2 的 Azure PowerShell 脚本是否存在任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 脚本还会将我的基本负载均衡器中的流量切换到新创建的标准负载均衡器？

是，它会迁移流量。 如果你想要单独迁移流量，请使用[此脚本](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)，这不会为你移动 vm。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时我遇到了一些问题。 如何求助？
  
可以向 slbupgradesupport@microsoft.com 发送电子邮件、向 Azure 支持部门提交支持案例，或同时采取这两种措施。

## <a name="next-steps"></a>后续步骤

[了解标准负载均衡器](load-balancer-overview.md)
