---
title: 从基本公共版升级到标准公共版 - Azure 负载均衡器
description: 本文介绍如何将 Azure 基本内部负载均衡器升级到标准公共负载均衡器
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: df7b8e6243bc45b5d5bdd8a9a72e81b0946454ff
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858408"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>升级 Azure 内部负载均衡器 - 需要出站连接
[Azure 标准负载均衡器](load-balancer-overview.md)通过区域冗余提供丰富的功能集和高可用性。 有关负载均衡器 SKU 的详细信息，请参阅[比较表](https://docs.microsoft.com/azure/load-balancer/skus#skus)。 由于标准内部负载均衡器不提供出站连接，因此我们提供了一个解决方案，改为创建标准公共负载均衡器。

升级分为四个阶段：

1. 将配置迁移到标准公共负载均衡器
2. 将 VM 添加到标准公共负载均衡器的后端池
3. 为应从/向 Internet 迄今为止的子网/Vm 设置 NSG 规则

本文介绍配置迁移。 根据特定环境，将 Vm 添加到后端池可能会有所不同。 不过，本文提供了一些概要性的普通[建议](#add-vms-to-backend-pools-of-standard-load-balancer)。

## <a name="upgrade-overview"></a>升级概述

我们提供了一个用于执行以下操作的 Azure PowerShell 脚本：

* 在指定的资源组和位置中创建标准 SKU 公共负载均衡器。
* 将基本 SKU 内部负载均衡器的配置无缝复制到新创建的标准公共负载均衡器。
* 创建启用传出连接的出站规则。

### <a name="caveatslimitations"></a>注意事项/限制

* 脚本支持需要出站连接的内部负载均衡器升级。 如果任何 VM 都不需要出站连接，请参阅[此页](upgrade-basicInternal-standard.md)以获得最佳做法。
* 标准负载均衡器使用新的公共地址。 不能将与现有基本内部负载均衡器关联的 IP 地址无缝移动到标准的公共负载均衡器，因为它们具有不同的 Sku。
* 如果在不同的区域中创建标准负载均衡器，则无法将旧区域中的 Vm 关联到新创建的标准负载均衡器。 若要克服此限制，请确保在新区域中创建新的 VM。
* 如果负载均衡器没有任何前端 IP 配置或后端池，则运行脚本时可能会遇到错误。  确保负载均衡器不是空的。

## <a name="download-the-script"></a>下载脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0)下载迁移脚本。
## <a name="use-the-script"></a>使用脚本

根据本地 PowerShell 环境的设置和首选项，可以使用两个选项：

* 如果你尚未安装 Azure Az 模块或者不介意卸载 Azure Az 模块，最佳做法是使用 `Install-Script` 选项运行该脚本。
* 如果需要保留 Azure Az 模块，则最佳做法是下载并直接运行该脚本。

若要确定是否安装了 Azure Az 模块，请运行 `Get-InstalledModule -Name az`。 如果未看到任何已安装的 Az 模块，可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用 Install-Script 方法安装

只有尚未在计算机上安装 Azure Az 模块时才能使用此选项。 如果已安装，则以下命令将显示错误。 可以卸载 Azure Az 模块，或者另一个选项手动下载并运行该脚本。
  
使用以下命令运行该脚本：

`Install-Script -Name AzurePublicLBUpgrade`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用脚本安装

如果已安装某些 Azure Az 模块并且无法卸载它们（或者不想卸载），可以使用脚本下载链接中的“手动下载”选项卡手动下载该脚本。  此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动下载包](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 检查所需的参数：

   * **oldRgName: [String]:必需** – 这是要升级的现有基本负载均衡器的资源组。 若要查找此字符串值，请导航到 Azure 门户，选择你的基本负载均衡器源，然后单击该负载均衡器的“概览”。  资源组位于该页上。
   * **oldLBName: [String]:必需** – 这是要升级的现有基本负载均衡器的名称。 
   * **newrgName: [String]:必需** – 这是要在其中创建标准负载均衡器的资源组。 它可以是新资源组，也可以是现有资源组。 如果选择现有资源组，请注意，负载均衡器的名称在资源组中必须是唯一的。 
   * **newlocation: [String]:必需** – 这是要在其中创建标准负载均衡器的位置。 建议将所选基本负载均衡器的相同位置继承到标准负载均衡器，以便与其他现有资源更好地相关联。
   * **newLBName: [String]:必需** – 这是要创建的标准负载均衡器的名称。
1. 使用相应的参数运行脚本。 完成该脚本可能需要 5 到 7 分钟时间。

    **示例**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>将 VM 添加到标准负载均衡器的后端池

首先，请仔细检查脚本是否已成功创建一个新的标准公共负载均衡器，其上的配置完全是从基本公共负载均衡器迁移的。 可以从 Azure 门户验证此结果。

确保通过标准负载均衡器发送少量的流量作为手动测试。
  
以下几种方案说明了如何配置将 VM 添加到新建标准公共负载均衡器的后端池，我们提供了每种方案的建议：

* **将现有 VM 从旧基本公共负载均衡器的后端池移到新建标准公共负载均衡器的后端池**。
    1. 若要执行本快速入门中的任务，请登录 [Azure 门户](https://portal.azure.com)。
 
    1. 在左侧菜单中选择“所有资源”，然后从资源列表中选择“新建的标准负载均衡器”。  
   
    1. 在“设置”下，选择“后端池”。  
   
    1. 选择与基本负载均衡器的后端池匹配的后端池，然后选择以下值： 
      - **虚拟机**：单击下拉控件，从基本负载均衡器的匹配后端池中选择 VM。
    1. 选择“保存”  。
    >[!NOTE]
    >对于使用公共 IP 的 VM，在不保证 IP 地址相同的情况下，需要先创建标准 IP 地址。 将 VM 与基本 IP 取消关联，并将 VM 关联到新建的标准 IP 地址。 然后，即可按照说明将 VM 添加到标准负载均衡器的后端池。 

* **创建要添加到新建标准公共负载均衡器的后端池的新 VM**。
    * 在[此处](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)可以找到有关如何创建 VM 并将关联到标准负载均衡器的详细说明。

### <a name="create-an-outbound-rule-for-outbound-connection"></a>为出站连接创建出站规则

按照[说明](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration)创建出站规则，以便执行以下操作：
* 从头开始定义出站 NAT。
* 缩放和优化现有出站 NAT 的行为。

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>为 VM 创建 NSG 规则，避免与 Internet 通信
若要避免 Internet 流量到达 VM，可以在 VM 的网络接口上创建 [NSG 规则](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>用于将配置从 v1 迁移到 v2 的 Azure PowerShell 脚本是否存在任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 脚本是否还会将流量从基本负载均衡器切换到新建的标准负载均衡器？

否。 该 Azure PowerShell 脚本只会迁移配置。 实际的流量迁移由你负责和控制。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时我遇到了一些问题。 如何求助？
  
可以向 slbupgradesupport@microsoft.com 发送电子邮件、向 Azure 支持部门提交支持案例，或同时采取这两种措施。

## <a name="next-steps"></a>后续步骤

[了解标准负载均衡器](load-balancer-overview.md)
