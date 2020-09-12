---
title: 从基本内部版升级到标准内部版 - Azure 负载均衡器
description: 本文介绍如何将 Azure 内部负载均衡器从基本 SKU 升级到标准 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: a6d2b69b0b498601497c4b33fb6bdfede87002df
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500243"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升级 Azure 内部负载均衡器 - 不需出站连接
[Azure 标准负载均衡器](load-balancer-overview.md)通过区域冗余提供丰富的功能和高可用性。 有关负载均衡器 SKU 的详细信息，请参阅[比较表](https://docs.microsoft.com/azure/load-balancer/skus#skus)。

本文介绍了一个 PowerShell 脚本，该脚本会创建一个其配置与基本负载均衡器相同的标准负载均衡器，并会将流量从基本负载均衡器迁移到标准负载均衡器。

## <a name="upgrade-overview"></a>升级概述

我们提供了一个用于执行以下操作的 Azure PowerShell 脚本：

* 在指定的位置中创建标准内部 SKU 负载均衡器。 请注意，标准内部负载均衡器不会提供任何[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。
* 将基本 SKU 负载均衡器的配置无缝复制到新建的标准负载均衡器。
* 将专用 IP 从基本负载均衡器无缝移到新建的标准负载均衡器。
* 将 VM 从基本负载均衡器的后端池无缝移到标准负载均衡器的后端池

### <a name="caveatslimitations"></a>注意事项/限制

* 脚本只支持不需要出站连接的内部负载均衡器升级。 如果需要用于某些 VM 的[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)，请参阅此[页面](upgrade-InternalBasic-To-PublicStandard.md)，了解相关说明。 
* 基本负载均衡器需要与后端 VM 和 NIC 位于同一资源组中。
* 如果在不同的区域中创建标准负载均衡器，则无法将旧区域中的 Vm 关联到新创建的标准负载均衡器。 若要克服此限制，请确保在新区域中创建新的 VM。
* 如果负载均衡器没有任何前端 IP 配置或后端池，则运行脚本时可能会遇到错误。 确保负载均衡器不是空的。

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>将前端 IP 配置的 IP 分配方法更改为“静态”（如果此项已为“静态”，则忽略此步骤）

1. 在左侧菜单中选择“所有服务”，接着选择“所有资源”，然后在资源列表中选择你的基本负载均衡器。 

2. 在“设置”下，选择“前端 IP 配置”，然后选择第一个前端 IP 配置。 

3. 对于“分配”，请选择“静态”

4. 对于基本负载均衡器的所有前端 IP 配置，请重复执行步骤 3。


## <a name="download-the-script"></a>下载脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0)下载迁移脚本。
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

如果已安装某些 Azure Az 模块并且无法卸载它们（或者不想卸载），可以使用脚本下载链接中的“手动下载”选项卡手动下载该脚本。 此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动下载包](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 检查所需的参数：

   * **rgName: [字符串]:必需** – 这是现有基本负载均衡器和新的标准负载均衡器的资源组。 若要查找此字符串值，请导航到 Azure 门户，选择你的基本负载均衡器源，然后单击该负载均衡器的“概览”。 资源组位于该页上。
   * **oldLBName: [String]:必需** – 这是要升级的现有基本负载均衡器的名称。 
   * **newlocation: [String]:必需** – 这是要在其中创建标准负载均衡器的位置。 建议将所选基本负载均衡器的相同位置继承到标准负载均衡器，以方便与其他现有资源相关联。
   * **newLBName: [String]:必需** – 这是要创建的标准负载均衡器的名称。
1. 使用相应的参数运行脚本。 完成该脚本可能需要 5 到 7 分钟时间。

    **示例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>用于将配置从 v1 迁移到 v2 的 Azure PowerShell 脚本是否存在任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 脚本是否还会将流量从基本负载均衡器切换到新建的标准负载均衡器？

是的，它会迁移流量。 如果想要亲自迁移流量，请使用[此脚本](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)（它不会为你移动虚拟机）。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时我遇到了一些问题。 如何求助？
  
可以向 slbupgradesupport@microsoft.com 发送电子邮件、向 Azure 支持部门提交支持案例，或同时采取这两种措施。

## <a name="next-steps"></a>后续步骤

[了解标准负载均衡器](load-balancer-overview.md)
