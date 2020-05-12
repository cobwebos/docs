---
title: 将应用程序部署到 Azure 虚拟机规模集
description: 了解如何将应用程序部署到规模集中的 Linux 和 Windows 虚拟机实例
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 7c39088890680b6cfc903083283ed09cb7618d16
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124918"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>在虚拟机规模集上部署应用程序

若要在规模集中的虚拟机 (VM) 实例上运行应用程序，首先需要安装应用程序组件和所需文件。 本文介绍如何为规模集中的实例生成自定义 VM 映像，或在现有 VM 实例上自动运行安装脚本。 本文还将介绍如何跨规模集管理应用程序或 OS 更新。


## <a name="build-a-custom-vm-image"></a>生成自定义 VM 映像
使用 Azure 平台映像之一在规模集中创建实例时，不会安装或配置其他软件。 可以自动安装这些组件，但这会增加将 VM 实例预配到规模集所需的时间。 如果将多个配置更改应用到 VM 实例，那么这些配置脚本和任务会产生管理开销。

若要减少配置管理和预配 VM 的时间，可以创建自定义 VM 映像，使其在规模集中预配实例后立即准备好运行应用程序。 若要深入了解如何通过规模集创建和使用自定义 VM 映像，请参阅以下教程：

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>使用自定义脚本扩展安装应用
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 若要详细了解如何使用自定义脚本扩展安装应用，请参阅以下教程：

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager 模板](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>使用 PowerShell DSC 将应用安装到 Windows VM
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) 是一个管理平台，用于定义目标计算机的配置。 DSC 配置定义要在计算机上安装的内容，以及如何配置主机。 本地配置管理器 (LCM) 引擎在每个目标节点上运行，此类节点根据推送的配置处理请求的操作。

通过 PowerShell DSC 扩展，可使用 PowerShell 在规模集中自定义 VM 实例。 以下示例：

- 指示 VM 实例从 GitHub 下载 DSC 包 - https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip 
- 设置用于运行安装脚本的扩展 - `configure-http.ps1`
- 使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 获取有关规模集的信息
- 使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) 将扩展应用到 VM 实例

DSC 扩展会应用于名为 myResourceGroup 的资源组中的 myScaleSet VM 实例   。 按如下所示输入自己的名称：

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

如果规模集上的升级策略为*手动*，则使用 [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance) 更新 VM 实例。 此 cmdlet 会将更新的规模集配置应用于 VM 实例，并安装应用程序。


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>使用 cloud-init 将应用安装到 Linux VM
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 来安装程序包和写入文件，或者配置用户和安全性。 在初始启动期间运行 cloud-init 时，无需额外的步骤和代理即可应用配置。

Cloud-init 还支持不同的发行版。 例如，不需使用  apt-get install 或  yum install 来安装包， 而是可定义要安装的程序包的列表。 Cloud-init 将对所选发行版自动使用本机包管理工具。

有关详细信息，包括示例 cloud-init.txt 文件，请参阅[使用 cloud-init 自定义 Azure VM](../virtual-machines/linux/using-cloud-init.md)  。

若要创建规模集并使用 cloud-init 文件，请将 `--custom-data` 参数添加到 [az vmss create](/cli/azure/vmss) 命令并指定 cloud-init 文件的名称。 以下示例会在 myResourceGroup 中创建名为 myScaleSet 的规模集，并配置包含名为 cloud-init.txt 的文件的 VM 实例    。 按如下所示输入自己的名称：

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>使用 OS 更新安装应用程序
新的 OS 版本可用时，可使用或生成新的自定义映像并[将 OS 升级部署](virtual-machine-scale-sets-upgrade-scale-set.md)到规模集中。 每个 VM 实例均会升级到指定的最新映像。 可使用预安装了应用程序的自定义映像、自定义脚本扩展或 PowerShell DSC 使应用程序在你执行升级时自动可用。 执行此过程时，可能需要为应用程序维护制定计划，确保不存在版本兼容问题。

如果使用预安装了应用程序的自定义 VM 映像，则可将应用程序更新与部署管道集成，以便生成新的映像并在规模集中部署 OS 升级。 此方法可使管道选取最新的应用程序版本，创建和验证 VM 映像，然后升级规模集中的 VM 实例。 若要运行跨自定义 VM 映像生成并部署应用程序更新的部署管道，可[创建 Packer 映像并使用 Azure DevOps Services 进行部署](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)，也可以使用其他平台，如 [Spinnaker](https://www.spinnaker.io/) 或 [Jenkins](https://jenkins.io/)。


## <a name="next-steps"></a>后续步骤
生成应用程序并将其部署到规模集时，可参阅[规模集设计概述](virtual-machine-scale-sets-design-overview.md)。 若要深入了解如何管理规模集，请参阅[使用 PowerShell 管理规模集](virtual-machine-scale-sets-windows-manage.md)。
