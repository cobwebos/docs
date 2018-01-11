---
title: "使用 Azure 模板创建虚拟机规模集 | Microsoft Docs"
description: "了解如何使用 Azure 资源管理器模板快速创建虚拟机规模集"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: 614c7c82aabab212753529a21d7a770b7a02027e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建虚拟机规模集
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 在此入门文章中，可以使用 Azure 资源管理器模板创建虚拟机规模集。 也可使用 [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md)、[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) 或 [Azure 门户](virtual-machine-scale-sets-create-portal.md)创建规模集。


## <a name="overview-of-templates"></a>模板概述
Azure 资源管理器模板允许部署成组的相关资源。 模板以 JavaScript 对象表示法 (JSON) 编写，可以为应用程序定义整个 Azure 基础结构环境。 在单个模板中，可以创建虚拟机规模集、安装应用程序，以及配置自动缩放规则。 在借助变量和参数的情况下，可以重复使用此模板来更新现有的规模集，或者创建更多的规模集。 可通过 Azure 门户、Azure CLI 2.0 或 Azure PowerShell 部署模板，并可从持续集成/持续交付 (CI/CD) 管道调用它们。

有关模板的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)


## <a name="define-a-scale-set"></a>定义规模集
模板用于定义每个资源类型的配置。 虚拟机规模集资源类型类似于单个 VM。 虚拟机规模集资源类型的核心部件包括：

| 属性                     | 属性说明                                  | 示例模板值                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 要创建的 Azure 资源类型                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | 规模集名称                                       | myScaleSet                                |
| location                     | 要创建规模集的位置                     | 美国东部                                   |
| sku.name                     | 每个规模集实例的 VM 大小                  | Standard_A1                               |
| sku.capacity                 | 一开始需要创建的 VM 实例数           | #N/A                                         |
| upgradePolicy.mode           | 更改发生时的 VM 实例升级模式              | 自动                                 |
| imageReference               | 用于 VM 实例的平台或自定义映像 | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | 每个 VM 实例的名称前缀                     | myvmss                                    |
| osProfile.adminUsername      | 每个 VM 实例的用户名                        | azureuser                                 |
| osProfile.adminPassword      | 每个 VM 实例的密码                        | P@ssw0rd!                                 |

 以下代码片段显示了模板中的核心规模集资源定义。 虚拟网络接口卡 (NIC) 配置未显示，这样是为了精简示例。 若要自定义规模集模板，可以更改 VM 大小或初始容量，也可以使用其他平台或自定义映像。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```


## <a name="install-an-application"></a>安装应用程序
部署规模集时，可以通过 VM 扩展来完成部署后配置和自动化任务，例如安装某个应用。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 若要对规模集应用某个扩展，请将 *extensionProfile* 节添加到前面的资源示例中。 扩展配置文件通常定义以下属性：

- 扩展类型
- 扩展发布者
- 扩展版本
- 配置或安装脚本的位置
- 可在 VM 实例上执行的命令

让我们看看通过扩展安装应用程序的两种方法 - 通过自定义扩展在 Linux 上安装 Python 应用，或者通过 PowerShell DSC 扩展在 Windows 上安装 ASP.NET 应用。

### <a name="python-http-server-on-linux"></a>Linux 上的 Python HTTP 服务器
[基于 Linux 的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)使用自定义脚本扩展来安装 [Bottle](http://bottlepy.org/docs/dev/)（Python Web 框架）和简单的 HTTP 服务器。 

两个脚本在 *fileUris* - （*installserver.sh* 和 *workserver.py*）中定义。 从 GitHub 下载这些文件以后，*commandToExecute* 就可以定义 `bash installserver.sh` 来安装和配置应用：

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```

### <a name="aspnet-application-on-windows"></a>基于 Windows 的 ASP.NET 应用程序
[基于 Windows 的 ASP.NET 应用程序](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)示例模板使用 PowerShell DSC 扩展来安装在 IIS 中运行的 ASP.NET MVC 应用。 

安装脚本从 url 中定义的 GitHub 下载。 扩展然后运行 *IISInstall.ps1* 脚本中的 *InstallIIS*（在 *function* 和 *Script* 中定义）。 ASP.NET 应用本身作为 Web 部署包提供，该包也从 *WebDeployPackagePath* 中定义的 GitHub 下载：

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>部署模板
部署[基于 Linux 的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)或[基于 Windows 的 ASP.NET MVC 应用程序](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)模板最简单的方式是使用 GitHub 的自述文件中的“部署到 Azure”按钮。  也可使用 PowerShell 或 Azure CLI 部署示例模板。

### <a name="azure-cli-20"></a>Azure CLI 2.0
可以使用 Azure CLI 2.0 安装基于 Linux 的 Python HTTP 服务器，如下所示：

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

若要查看运行中的应用，请使用 [az network public-ip list](/cli/azure/network/public-ip#show) 命令获取负载均衡器的公共 IP 地址，如下所示：

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

以 *http://<publicIpAddress>:9000/do_work* 格式将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![NGINX 中的默认网页](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
可以使用 Azure PowerShell 安装基于 Windows 的 ASP.NET 应用程序，如下所示：

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

若要查看运行中的应用，请使用 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取负载均衡器的公共 IP 地址，如下所示：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

以 *http://<publicIpAddress>/MyApp* 格式将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![运行 IIS 网站](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>清理资源
如果不再需要资源组、规模集和所有相关的资源，可以使用 [az group delete](/cli/azure/group#delete) 命令将其删除，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>后续步骤
