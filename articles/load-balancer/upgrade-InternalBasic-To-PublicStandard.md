---
title: 从基本公共公共升级到标准公共-Azure 负载均衡器
description: 本文介绍如何将 Azure 基本内部负载均衡器升级到标准的公共负载均衡器
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: f5ff4ca94f9e9c6bd03cde6b948331e42cc6225a
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618199"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>升级 Azure 内部负载均衡器-需要出站连接
[Azure 标准负载均衡器](load-balancer-overview.md)通过区域冗余提供丰富的功能集和高可用性。 若要了解有关负载均衡器 SKU 的详细信息，请参阅[比较表](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)。 由于标准内部负载均衡器不提供出站连接，因此我们提供了一个解决方案来改为创建标准的公共负载均衡器。

升级分为三个阶段：

1. 将配置迁移到标准的公共负载均衡器
2. 将 Vm 添加到标准公共负载均衡器的后端池
3. 为应从/向 Internet 迄今为止的子网/Vm 设置 NSG 规则

本文介绍了配置迁移。 根据特定环境，将 Vm 添加到后端池可能会有所不同。 不过，[还提供了](#add-vms-to-backend-pools-of-standard-load-balancer)一些高级建议。

## <a name="upgrade-overview"></a>升级概述

可执行以下操作的 Azure PowerShell 脚本：

* 在指定的资源组和位置中创建标准 SKU 公共负载均衡器。
* 将基本 SKU 内部负载均衡器的配置无缝复制到新创建的标准公共负载均衡器。

### <a name="caveatslimitations"></a>Caveats\Limitations

* 脚本支持内部负载均衡器升级，需要出站连接。 如果没有任何 Vm 需要出站连接，请参阅[此页](upgrade-basicInternal-standard.md)以获得最佳实践。
* 标准负载均衡器具有新的公用地址。 不能将与现有基本内部负载均衡器关联的 IP 地址无缝移动到标准的公共负载均衡器，因为它们具有不同的 Sku。
* 如果在不同的区域中创建标准负载均衡器，则无法将旧区域中的 Vm 关联到新创建的标准负载均衡器。 若要解决此限制，请确保在新区域中创建新的 VM。
* 如果你的负载均衡器没有任何前端 IP 配置或后端池，你可能会遇到运行脚本时遇到的错误。  请确保它们不为空。

## <a name="download-the-script"></a>下载脚本

从[PowerShell 库](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0)下载迁移脚本。
## <a name="use-the-script"></a>使用脚本

根据本地 PowerShell 环境的设置和首选项，有两个选项可供选择：

* 如果尚未安装 Azure Az 模块，或不介意卸载 Azure Az 模块，最佳选择是使用 `Install-Script` 选项运行脚本。
* 如果需要保留 Azure Az 模块，最佳做法是下载并直接运行该脚本。

若要确定是否安装了 Azure Az 模块，请运行 `Get-InstalledModule -Name az`。 如果看不到任何已安装的 Az 模块，则可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用安装脚本方法安装

若要使用此选项，你必须在计算机上未安装 Azure Az 模块。 如果已安装，则以下命令将显示错误。 可以卸载 Azure Az 模块，也可以使用其他选项手动下载并运行该脚本。
  
使用以下命令运行该脚本：

`Install-Script -Name AzurePublicLBUpgrade`

此命令还会安装所需的 Az 模块。  

### <a name="install-using-the-script-directly"></a>直接使用脚本安装

如果你安装了某些 Azure Az 模块并且无法卸载它们（或不想卸载它们），可以使用脚本下载链接中的 "**手动下载**" 选项卡手动下载该脚本。 此脚本将作为原始 nupkg 文件下载。 若要安装此 nupkg 文件中的脚本，请参阅[手动包下载](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

若要运行该脚本，请执行以下操作：

1. 使用 `Connect-AzAccount` 连接到 Azure。

1. 使用 `Import-Module Az` 导入 Az 模块。

1. 检查所需的参数：

   * **oldRgName： [String]： Required** –这是要升级的现有基本负载均衡器的资源组。 若要查找此字符串值，请导航到 Azure 门户，选择你的基本负载均衡器源，并单击负载均衡器的**概述**。 资源组位于该页面上。
   * **oldLBName： [String]： Required** –这是要升级的现有基本均衡器的名称。 
   * **newrgName： [String]： Required** –这是将在其中创建标准负载均衡器的资源组。 它可以是新的资源组或现有的资源组。 如果选择现有的资源组，请注意，负载均衡器的名称在资源组中必须是唯一的。 
   * **newlocation： [String]： Required** –这是将在其中创建标准负载均衡器的位置。 建议将所选基本负载均衡器的同一位置继承到标准负载均衡器，以便更好地与其他现有资源关联。
   * **newLBName： [String]： Required** –这是要创建的标准负载均衡器的名称。
1. 使用适当的参数运行该脚本。 完成此操作可能需要5到7分钟。

    **示例**

   ```azurepowershell
   ./AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>将 Vm 添加到标准负载均衡器的后端池

首先，请仔细检查脚本是否已成功创建了一个新的标准公共负载均衡器，并从基本的公共负载均衡器迁移了精确的配置。 可以从 Azure 门户验证此验证。

请确保通过标准负载均衡器将少量流量发送为手动测试。
  
以下几个方案介绍了如何将 Vm 添加到新创建的标准公共负载均衡器的后端池，并提供每个 Vm 的建议：

* **将现有的 vm 从旧的基本公共负载均衡器的后端迁移到新创建的标准公共负载均衡器的后端池**。
    1. 若要执行本快速入门中的任务，请登录 [Azure 门户](https://portal.azure.com)。
 
    1. 选择左侧菜单中的 "**所有资源**"，然后从资源列表中选择**新创建的标准负载均衡器**。
   
    1. 在“设置”下，选择“后端池”。
   
    1. 选择与基本负载均衡器的后端池匹配的后端池，并选择以下值： 
      - **虚拟机**：下拉菜单，并从基本负载均衡器的匹配后端池中选择 vm。
    1. 选择“保存”。
    >[!NOTE]
    >对于具有公共 ip 的 Vm，需要首先创建标准 IP 地址，并且不保证相同的 IP 地址。 将 Vm 与基本 ip 解除关联，并将其与新创建的标准 IP 地址相关联。 然后，你将能够按照说明将 Vm 添加到标准负载均衡器的后端池。 

* **创建新的 vm 以添加到新创建的标准公共负载均衡器的后端池**。
    * 可在[此处](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)找到有关如何创建 VM 并将其与标准负载均衡器相关联的详细说明。

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>为虚拟机创建 NSG 规则，以避免 Internet 之间的通信
如果要避免 Internet 流量到达 Vm，可以在 Vm 的网络接口上创建[NSG 规则](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

## <a name="common-questions"></a>常见问题

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 脚本是否存在将配置从 v1 迁移到 v2 的任何限制？

是的。 请参阅[注意事项/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 脚本还会将我的基本负载均衡器中的流量切换到新创建的标准负载均衡器？

不是。 Azure PowerShell 脚本仅迁移配置。 实际的流量迁移是您在控制中的责任。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>使用此脚本时，我遇到了一些问题。 如何获取帮助？
  
你可以向 slbupgradesupport@microsoft.com发送电子邮件、使用 Azure 支持提供支持案例，或同时执行这两项操作。

## <a name="next-steps"></a>后续步骤

[了解标准负载均衡器](load-balancer-overview.md)
