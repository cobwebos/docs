---
title: "将应用程序部署到 Azure 虚拟机规模集 | Microsoft Docs"
description: "了解如何将应用程序部署到规模集中的 Linux 和 Windows 虚拟机实例"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 288bcdf6628f60d0b08fe151e630784d665db56f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>在虚拟机规模集上部署应用程序
若要在规模集中的虚拟机 (VM) 实例上运行应用程序，首先需要安装应用程序组件和所需文件。 本文介绍如何为规模集中的实例生成自定义 VM 映像，或在现有 VM 实例上自动运行安装脚本。 本文还将介绍如何跨规模集管理应用程序或 OS 更新。


## <a name="build-a-custom-vm-image"></a>生成自定义 VM 映像
使用 Azure 平台映像之一在规模集中创建实例时，不会安装或配置其他软件。 可以自动安装这些组件，但这会增加将 VM 实例预配到规模集所需的时间。 如果将多个配置更改应用到 VM 实例，那么这些配置脚本和任务会产生管理开销。

若要减少配置管理和预配 VM 的时间，可以创建自定义 VM 映像，使其在规模集中预配实例后立即准备好运行应用程序。 为规模集实例创建自定义 VM 映像的整个过程如下：

1. 若要为规模集实例创建自定义 VM 映像，请创建并登录 VM ，然后安装和配置应用程序。 可使用 Packer 定义和生成 [Linux](../virtual-machines/linux/build-image-with-packer.md) 或 [Windows](../virtual-machines/windows/build-image-with-packer.md) VM 映像。 或者，可手动创建和配置 VM：

    - 使用 [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md)、[Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md) 或[门户](../virtual-machines/linux/quick-create-portal.md)创建 Linux VM。
    - 使用 [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md) 或[门户](../virtual-machines/windows/quick-create-portal.md)创建 Windows VM。
    - 登录到 [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) 或 [Windows](../virtual-machines/windows/connect-logon.md) VM。
    - 安装和配置所需应用程序和工具。 如果需要特定版本的库或运行时，则自定义 VM 映像使你可以定义一个版本和 

2. 使用 [Azure CLI 2.0](../virtual-machines/linux/capture-image.md) 或 [Azure PowerShell](../virtual-machines/windows/capture-image.md) 捕获 VM。 此步骤会创建自定义 VM 映像，用于稍后在规模集中部署实例。

3. [创建规模集](virtual-machine-scale-sets-create.md)并指定在前面的步骤中创建的自定义 VM 映像。


## <a name="already-provisioned"></a>使用自定义脚本扩展安装应用
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。

自定义脚本扩展与 Azure 资源管理器模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。 

有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/windows/extensions-customscript.md)。


### <a name="use-azure-powershell"></a>使用 Azure PowerShell
PowerShell 使用哈希表存储要下载的文件和要执行的命令。 以下示例：

- 指示 VM 实例从 GitHub 下载脚本 - https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1
- 设置用于运行安装脚本的扩展 - `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- 使用 [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) 获取有关规模集的信息
- 使用 [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) 将扩展应用到 VM 实例

自定义脚本扩展会应用于名为 myResourceGroup 的资源组中的 myScaleSet VM 实例。 按如下所示输入自己的名称：

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

如果规模集上的升级策略是手动的，则使用 [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance) 更新 VM 实例。 此 cmdlet 会将更新的规模集配置应用于 VM 实例，并安装应用程序。


### <a name="use-azure-cli-20"></a>使用 Azure CLI 2.0
若要通过 Azure CLI 使用自定义脚本扩展，请创建 JSON 文件，用于定义要包含的文件和要执行的命令。 这些 JSON 定义可以在规模集部署之间重复使用，以应用一致的应用程序安装。

在当前 shell 中，创建名为“customConfig.json”的文件并粘贴下面的配置。 例如，在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloudConfig.json` 以创建文件并查看可用编辑器的列表。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

使用 [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) 将自定义脚本扩展配置应用到规模集中的 VM 实例。 以下示例将 customConfig.json 配置应用于名为 myResourceGroup 的资源组中的 myScaleSet VM 实例。 按如下所示输入自己的名称：

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

如果规模集上的升级策略是手动的，则使用 [az vmss update-instances](/cli/azure/vmss#update-instances) 更新 VM 实例。 此 cmdlet 会将更新的规模集配置应用于 VM 实例，并安装应用程序。


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>使用 PowerShell DSC 将应用安装到 Windows VM
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) 是一个管理平台，用于定义目标计算机的配置。 DSC 配置定义要在计算机上安装的内容，以及如何配置主机。 本地配置管理器 (LCM) 引擎在每个目标节点上运行，此类节点根据推送的配置处理请求的操作。

通过 PowerShell DSC 扩展，可使用 PowerShell 在规模集中自定义 VM 实例。 以下示例：

- 指示 VM 实例从 GitHub 下载 DSC 包 - https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip
- 设置用于运行安装脚本的扩展 - `configure-http.ps1`
- 使用 [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) 获取有关规模集的信息
- 使用 [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) 将扩展应用到 VM 实例

DSC 扩展会应用于名为 myResourceGroup 的资源组中的 myScaleSet VM 实例。 按如下所示输入自己的名称：

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

如果规模集上的升级策略是手动的，则使用 [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance) 更新 VM 实例。 此 cmdlet 会将更新的规模集配置应用于 VM 实例，并安装应用程序。


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>使用 cloud-init 将应用安装到 Linux VM
[Cloud-init](https://cloudinit.readthedocs.io/latest/) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 在初始启动期间运行 cloud-init 时，无需额外的步骤且无需代理来应用配置。

Cloud-init 还支持不同的分发。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 Cloud-init 将为所选发行版自动使用本机包管理工具。

有关详细信息，包括示例 cloud-init.txt 文件，请参阅[使用 cloud-init 自定义 Azure VM](../virtual-machines/linux/using-cloud-init.md)。

若要创建规模集并使用 cloud-init 文件，请将 `--custom-data` 参数添加到 [az vmss create](/cli/azure/vmss#az_vmss_create) 命令并指定 cloud-init 文件的名称。 以下示例会在 myResourceGroup 中创建名为 myScaleSet 的规模集，并配置包含名为 cloud-init.txt 的文件的 VM 实例。 按如下所示输入自己的名称：

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


## <a name="install-applications-as-a-set-scales-out"></a>规模集横向扩展时安装应用程序
借助规模集，可增加用于运行应用程序的 VM 实例数。 此横向扩展过程可手动启动，也可基于 CPU 或内存使用情况等指标自动启动。

如果已将自定义脚本扩展应用到规模集，则应用程序会安装到每个新的 VM 实例中。 如果规模集基于预安装了应用程序的自定义映像，则每个新的 VM 实例都是以可用状态进行部署的。 

如果规模集 VM 实例是容器主机，则可使用自定义脚本扩展拉取和运行所需容器映像。 自定义脚本扩展还可向 Azure 容器服务等业务流程协调程序注册新的 VM 实例。


## <a name="deploy-application-updates"></a>部署应用程序更新
如果更新应用程序代码、库或包，则可将最新的应用程序状态推送到规模集中的 VM 实例。 如果使用自定义脚本扩展，则不会自动部署应用程序更新。 更改自定义脚本配置，例如指向具有更新的版本名称的安装脚本。 在上一示例中，自定义脚本扩展使用名为 automate_nginx.sh 的脚本，如下所示：

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

对应用程序所做的任何更改均不会向自定义脚本扩展公开，除非安装脚本发生更改。 一种方法是包括随应用程序发布递增的版本号。 自定义脚本扩展现可引用 automate_nginx_v2.sh，如下所示：

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

自定义脚本扩展现可针对 VM 实例运行，从而应用最新的应用程序更新。


### <a name="install-applications-with-os-updates"></a>使用 OS 更新安装应用程序
新的 OS 版本可用时，可使用或生成新的自定义映像并[将 OS 升级部署](virtual-machine-scale-sets-upgrade-scale-set.md)到规模集中。 每个 VM 实例均会升级到指定的最新映像。 可使用预安装了应用程序的自定义映像、自定义脚本扩展或 PowerShell DSC 使应用程序在你执行升级时自动可用。 执行此过程时，可能需要为应用程序维护制定计划，确保不存在版本兼容问题。

如果使用预安装了应用程序的自定义 VM 映像，则可将应用程序更新与部署管道集成，以便生成新的映像并在规模集中部署 OS 升级。 此方法可使管道选取最新的应用程序版本，创建和验证 VM 映像，然后升级规模集中的 VM 实例。 若要运行跨自定义 VM 映像生成并部署应用程序更新的部署管道，可使用 [Visual Studio Team Services](https://www.visualstudio.com/team-services/)、[Spinnaker](https://www.spinnaker.io/) 或 [Jenkins](https://jenkins.io/)。


## <a name="next-steps"></a>后续步骤
生成应用程序并将其部署到规模集时，可参阅[规模集设计概述](virtual-machine-scale-sets-design-overview.md)。 若要深入了解如何管理规模集，请参阅[使用 PowerShell 管理规模集](virtual-machine-scale-sets-windows-manage.md)。
