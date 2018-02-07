---
title: "使用 Azure 模板创建 Linux 虚拟机规模集 | Microsoft Docs"
description: "了解如何使用 Azure 资源管理器模板来部署示例应用和配置自动缩放规则，以便快速创建 Linux 虚拟机规模集"
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
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 16e9c0b30710d711ef2789f7781b17e72889d4da
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>使用 Azure 模板创建 Linux 虚拟机规模集
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 在此入门文章中，可以使用 Azure 资源管理器模板创建 Linux 虚拟机规模集。 也可使用 [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md)、[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) 或 [Azure 门户](virtual-machine-scale-sets-create-portal.md)创建规模集。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="define-a-scale-set-in-a-template"></a>在模板中定义规模集
Azure 资源管理器模板允许部署成组的相关资源。 模板以 JavaScript 对象表示法 (JSON) 编写，可以为应用程序定义整个 Azure 基础结构环境。 在单个模板中，可以创建虚拟机规模集、安装应用程序，以及配置自动缩放规则。 在借助变量和参数的情况下，可以重复使用此模板来更新现有的规模集，或者创建更多的规模集。 可以通过 Azure 门户、Azure CLI 2.0、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。

有关模板的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)

若要使用模板创建规模集，请定义相应的资源。 虚拟机规模集资源类型的核心部件包括：

| 属性                     | 属性说明                                  | 示例模板值                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 要创建的 Azure 资源类型                            | Microsoft.Compute/virtualMachineScaleSets |
| 名称                         | 规模集名称                                       | myScaleSet                                |
| location                     | 要创建规模集的位置                     | 美国东部                                   |
| sku.name                     | 每个规模集实例的 VM 大小                  | Standard_A1                               |
| sku.capacity                 | 一开始需要创建的 VM 实例数           | #N/A                                         |
| upgradePolicy.mode           | 更改发生时的 VM 实例升级模式              | 自动                                 |
| imageReference               | 用于 VM 实例的平台或自定义映像 | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | 每个 VM 实例的名称前缀                     | myvmss                                    |
| osProfile.adminUsername      | 每个 VM 实例的用户名                        | azureuser                                 |
| osProfile.adminPassword      | 每个 VM 实例的密码                        | P@ssw0rd!                                 |

 以下示例显示了核心规模集资源定义。 若要自定义规模集模板，可以更改 VM 大小或初始容量，也可以使用其他平台或自定义映像。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
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

 虚拟网络接口卡 (NIC) 配置未显示，这样是为了精简示例。 其他组件（例如负载均衡器）也未显示。 完整的规模集模板显示在[本文末尾](#deploy-the-template)。


## <a name="install-an-application"></a>安装应用程序
部署规模集时，可以通过 VM 扩展来完成部署后配置和自动化任务，例如安装某个应用。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 若要对规模集应用某个扩展，请将 *extensionProfile* 节添加到前面的资源示例中。 扩展配置文件通常定义以下属性：

- 扩展类型
- 扩展发布者
- 扩展版本
- 配置或安装脚本的位置
- 可在 VM 实例上执行的命令

[基于 Linux 的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)模板使用自定义脚本扩展来安装 [Bottle](http://bottlepy.org/docs/dev/)（Python Web 框架）和简单的 HTTP 服务器。 

两个脚本在 **fileUris** - （*installserver.sh* 和 *workserver.py*）中定义。 从 GitHub 下载这些文件以后，*commandToExecute* 就可以运行 `bash installserver.sh` 来安装和配置应用：

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


## <a name="deploy-the-template"></a>部署模板
可以通过下面的“部署到 Azure”按钮部署[基于 Linux 的 Python HTTP 服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)模板。 此按钮可打开 Azure 门户、加载完整的模板，以及提示输入一些参数，例如规模集名称、实例计数和管理员凭据。

[![将模板部署到 Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

也可使用 Azure CLI 2.0 通过 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) 命令安装基于 Linux 的 Python HTTP 服务器，如下所示：

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

响应提示，为 VM 实例提供规模集名称、实例计数和管理员凭据。 创建规模集和支持资源需要数分钟。


## <a name="test-your-sample-application"></a>测试示例应用程序
若要查看运行中的应用，请使用 [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show) 命令获取负载均衡器的公共 IP 地址，如下所示：

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

以 *http://publicIpAddress:9000/do_work* 格式将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![NGINX 中的默认网页](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>清理资源
如果不再需要资源组、规模集和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>后续步骤
此快速入门文章介绍了如何使用 Azure 模板创建 Linux 规模集，以及如何使用自定义脚本扩展在 VM 实例上安装基本的 Python Web 服务器。 若要改进可伸缩性和自动化，请阅读以下操作指南文章，了解如何扩展规模集：

- [在虚拟机规模集上部署应用程序](virtual-machine-scale-sets-deploy-app.md)
- 通过 [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md)、[Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) 或 [Azure 门户](virtual-machine-scale-sets-autoscale-portal.md)自动进行缩放
- [对规模集 VM 实例使用自动 OS 升级](virtual-machine-scale-sets-automatic-upgrade.md)
