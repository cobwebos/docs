---
title: 从基本内部升级到标准内部 - Azure 负载均衡器
description: 本文介绍如何将 Azure 内部负载均衡器从基本 SKU 升级到标准 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: c2c909d8ef2be982d4dd4a70b5f35d03e8e71418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659962"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升级 Azure 内部负载均衡器 - 无需出站连接
[Azure 标准负载均衡器](load-balancer-overview.md)通过区域冗余提供了丰富的功能集和高可用性。 要了解有关负载均衡器 SKU 的更多，请参阅[比较表](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)。

升级分为两个阶段：

1. 迁移配置
2. 将 VM 添加到标准负载均衡器的后端池

本文介绍配置迁移。 将 VM 添加到后端池可能因特定环境而异。 不过，本文提供了一些概要性的普通[建议](#add-vms-to-backend-pools-of-standard-load-balancer)。

## <a name="upgrade-overview"></a>升级概述

我们提供了一个用于执行以下操作的 Azure PowerShell 脚本：

* 在指定的位置创建标准内部 SKU 负载均衡器。 请注意，标准内部负载均衡器不会提供[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。
* 将基本 SKU 负载均衡器的配置无缝复制到新创建的标准负载均衡器。

### <a name="caveatslimitations"></a>注意事项/限制

* 脚本仅支持不需要出站连接的内部负载均衡器升级。 如果您需要某些 VM 的[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)，请参阅[此页面](upgrade-InternalBasic-To-PublicStandard.md)获得说明。 
* 标准负载均衡器具有新的公共地址。 不可能将与现有基本负载均衡器关联的 IP 地址无缝移动到标准负载均衡器，因为它们具有不同的 SKU。
* 如果标准负载均衡器是在不同区域中创建的，您将无法将旧区域中现有的 VM 与新创建的标准负载均衡器相关联。 要解决此限制，请确保在新区域中创建新的 VM。
* 如果您的负载均衡器没有任何前端 IP 配置或后端池，则运行脚本时可能会遇到错误。 请确保它们不为空。

## <a name="download-the-script"></a>下载脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)下载迁移脚本。
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

如果已安装某些 Azure Az 模块并且无法卸载它们（或者不想卸载），可以使用脚本下载链接中的“手动下载”选项卡手动下载该脚本。**** 此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动下载包](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 检查所需的参数：

   * **rgName： [字符串]： 必需**– 这是现有基本负载均衡器和新的标准负载均衡器的资源组。 要查找此字符串值，请导航到 Azure 门户，选择基本负载均衡器源，然后单击负载均衡器的 **"概述**"。 资源组位于该页上。
   * **旧 LB 名称： [字符串]： 必需**– 这是要升级的现有基本平衡器的名称。 
   * **新定位： [String]： 必需**– 这是将创建标准负载均衡器的位置。 建议将所选基本负载均衡器的相同位置继承到标准负载平衡器，以便更好地与其他现有资源关联。
   * **新 LB 名称： [字符串]： 必需**– 这是要创建的标准负载均衡器的名称。
1. 使用相应的参数运行脚本。 完成该脚本可能需要 5 到 7 分钟时间。

    **示例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>将 VM 添加到标准负载均衡器的后端池

首先，仔细检查脚本成功创建了新的标准内部负载均衡器，其精确配置从基本内部负载均衡器迁移过来。 可以从 Azure 门户验证此结果。

请务必通过标准负载均衡器发送少量流量作为手动测试。
  
以下是有关如何将 VM 添加到新创建的标准内部负载均衡器的后端池的一些方案，以及我们对每个方案的建议：

* **将现有 VM 从旧基本内部负载均衡器的后端池移动到新创建的标准内部负载均衡器的后端池**。
    1. 若要执行本快速入门中的任务，请登录 [Azure 门户](https://portal.azure.com)。
 
    1. 选择左侧菜单上**的所有资源**，然后从资源列表中选择**新创建的标准负载均衡器**。
   
    1. 在“设置”下，选择“后端池”。********
   
    1. 选择与基本负载均衡器的后端池匹配的后端池，选择以下值： 
      - **虚拟机**：从基本负载均衡器的匹配后端池下拉并选择 VM。
    1. 选择“保存”。****
    >[!NOTE]
    >对于具有公共 IP 的 VM，您需要首先创建标准 IP 地址，而该地址不保证相同的 IP 地址。 取消 VM 与基本 IP 的关联，并将其与新创建的标准 IP 地址相关联。 然后，您将能够按照说明将 VM 添加到标准负载均衡器的后端池中。 

* **创建新 VM 以添加到新创建的标准内部负载均衡器的后端池**。
    * 有关如何创建 VM 并将其与标准负载均衡器关联的更多说明[，请参阅此处](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>用于将配置从 v1 迁移到 v2 的 Azure PowerShell 脚本是否存在任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 脚本是否还会将流量从基本负载均衡器切换到新创建的标准负载均衡器？

不是。 该 Azure PowerShell 脚本只会迁移配置。 实际的流量迁移由你负责和控制。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时我遇到了一些问题。 如何求助？
  
可以向 slbupgradesupport@microsoft.com 发送电子邮件、向 Azure 支持部门提交支持案例，或同时采取这两种措施。

## <a name="next-steps"></a>后续步骤

[了解标准负载均衡器](load-balancer-overview.md)
