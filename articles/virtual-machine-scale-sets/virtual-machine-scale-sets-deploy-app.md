---
title: "在虚拟机规模集上部署应用"
description: "使用扩展在 Azure 虚拟机规模集上部署应用。"
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 371295efea1eab66361b9aba21a55bbd2826c69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>在虚拟机规模集上部署应用程序

本文介绍在预配规模集时如何通过不同方式安装软件。

可能需查看[规模集设计概述](virtual-machine-scale-sets-design-overview.md)一文，其中描述了针对虚拟机规模集的一些限制。

## <a name="capture-and-reuse-an-image"></a>捕获并重复使用映像

可使用 Azure 中的虚拟机为规模集准备基础映像。 此过程会在存储帐户中创建一个托管磁盘，可将其引用为规模集的基本映像。 

请执行以下步骤：

1. 创建一个 Azure 虚拟机
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. 远程进入虚拟机，根据你的偏好自定义系统。

   现在可按需安装应用程序。 但是如果现在安装应用程序，升级应用程序时可能会更复杂，因为可能需要先删除该应用程序。 可改用此步骤来安装应用程序可能需要的所有先决条件，如特定运行时或操作系统功能。

3. 按照 [Linux][linux-vm-capture] 或 [Windows][windows-vm-capture] 的“捕获计算机”教程执行操作。

4. 使用在上一步中捕获的映像 URI 创建[虚拟机规模集][vmss-create]。

有关磁盘的详细信息，请参阅[托管磁盘概述](../virtual-machines/windows/managed-disks-overview.md)和[使用附加数据磁盘](virtual-machine-scale-sets-attached-disks.md)。

## <a name="already-provisioned"></a>预配规模集时进行安装

可将虚拟机扩展应用于虚拟机规模集。 借助虚拟机扩展，可将规模集中的虚拟机作为一个组进行自定义。 有关扩展的详细信息，请参阅[虚拟机扩展](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

有三个可用的主要扩展，具体哪个可用取决于操作系统是基于 Linux 还是基于 Windows。

### <a name="windows"></a>Windows

对于基于 Windows 的操作系统，请使用自定义脚本 v1.8 扩展或 PowerShell DSC 扩展。

#### <a name="custom-script"></a>自定义脚本

自定义脚本扩展在规模集中的每个虚拟机实例上运行脚本。 配置文件或变量指明将哪些文件下载到虚拟机，然后运行什么命令。 可用它来运行安装程序、脚本、批处理文件、任何可执行文件等。

PowerShell 使用哈希表进行设置。 此示例配置自定义脚本扩展，运行安装 IIS 的 PowerShell 脚本。

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>对于可能包含敏感信息的任何设置，请使用 `-ProtectedSetting` 切换。

---------


Azure CLI 使用 json 文件进行设置。 此示例配置自定义脚本扩展，运行安装 IIS 的 PowerShell 脚本。 将以下 json 文件另存为 settings.json。

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

然后，运行此 Azure CLI 命令。

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>对于可能包含敏感信息的任何设置，请使用 `--protected-settings` 切换。

### <a name="powershell-dsc"></a>PowerShell DSC

可使用 PowerShell DSC 自定义规模集 VM 实例。 Microsoft.Powershell 发布的 DSC 扩展在每个虚拟机实例上部署并运行提供的 DSC 配置。 配置文件或变量告知扩展 .zip 包的位置，及要运行的脚本函数组合。

PowerShell 使用哈希表进行设置。 此示例部署安装 IIS 的 DSC 包。

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>对于可能包含敏感信息的任何设置，请使用 `-ProtectedSetting` 切换。

-----------

Azure CLI 使用 json 进行设置。 此示例部署安装 IIS 的 DSC 包。 将以下 json 文件另存为 settings.json。

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

然后，运行此 Azure CLI 命令。

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>对于可能包含敏感信息的任何设置，请使用 `--protected-settings` 切换。

### <a name="linux"></a>Linux

创建期间，Linux 可使用自定义脚本 v2.0 扩展或使用 cloud-init。

自定义脚本是简单的扩展，可将文件下载到虚拟机实例，并运行命令。

#### <a name="custom-script"></a>自定义脚本

将以下 json 文件另存为 settings.json。

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

使用 Azure CLI 将此扩展添加到现有虚拟机规模集。 规模集中的每个虚拟机都会自动运行扩展。

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>对于可能包含敏感信息的任何设置，请使用 `--protected-settings` 切换。

#### <a name="cloud-init"></a>Cloud-Init

创建规模集时会使用 Cloud-init。 首先，创建一个名为 cloud-init.txt 的本地文件，并向该文件添加配置。 有关示例，请参阅[此主题](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt)

使用 Azure CLI 创建规模集。 `--custom-data` 字段接受 cloud-init 脚本的文件名。

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>如何管理应用程序更新？

如果是通过扩展部署应用程序，请以某种方式更改扩展定义。 此更改会使扩展重新部署到所有虚拟机实例。 必须更改扩展的某些内容，例如重命名引用的文件，否则 Azure 不知道该扩展已更改。

如果将应用程序引入自己的操作系统映像，请使用自动化部署管道进行应用程序更新。 设计体系结构，帮助分阶段的规模集快速切换到生产环境。 [Azure Spinnaker 驱动程序的工作原理](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure)很好地示范了这种方法 - [http://www.spinnaker.io/](http://www.spinnaker.io/)。

[Packer](https://www.packer.io/) 和 [Terraform](https://www.terraform.io/) 支持 Azure Resource Manager，因此也可以“代码方式”定义映像并在 Azure 中将进行生成，然后在规模集中使用 VHD。 但是，这种做法会给应用商店映像造成问题，在这种情况下，扩展/自定义脚本变得更加重要，因为无法直接在应用商店中处理代码。

## <a name="what-happens-when-a-scale-set-scales-out"></a>扩展规模集会发生什么情况？
将一个或多个虚拟机添加到规模集时，会自动安装应用程序。 例如，如果规模集包含定义的扩展，则每次创建新虚拟机时，这些扩展都会在新虚拟机上运行。 如果规模集基于自定义映像，所有新虚拟机都是自定义源映像的副本。 如果规模集虚拟机是容器主机，则可使用启动代码加载自定义脚本扩展中的容器。 或者，扩展可能会安装注册群集协调器的代理，例如 Azure 容器服务。


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>如何跨更新域推出 OS 更新？
假设要在更新 OS 映像的同时让虚拟机规模集持续运行。 PowerShell 和 Azure CLI 可更新虚拟机映像，一次一个虚拟机。 [升级虚拟机规模集](./virtual-machine-scale-sets-upgrade-scale-set.md)一文还进一步介绍了可用于跨虚拟机规模集执行操作系统升级的选项。

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 管理规模集。](virtual-machine-scale-sets-windows-manage.md)
* [创建规模集模板。](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md

