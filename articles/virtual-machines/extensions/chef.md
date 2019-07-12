---
title: 适用于 Azure VM 的 Chef 扩展 | Microsoft Docs
description: 使用 Chef VM 扩展将 Chef 客户端部署到虚拟机。
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: e72536cc6f9ec3b94016d16de8502e70bc7107aa
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706088"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>适用于 Linux 和 Windows 的 Chef VM 扩展

Chef Software 为 Linux 和 Windows 提供了一个 DevOps 自动化平台，用于管理物理服务器配置和虚拟服务器配置。 Chef VM 扩展是一个用于在虚拟机上启用 Chef 的扩展。

## <a name="prerequisites"></a>系统必备

### <a name="operating-system"></a>操作系统

Chef VM 扩展在 Azure 中的所有[扩展支持的 OS](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) 上都受支持。

### <a name="internet-connectivity"></a>Internet 连接

Chef VM 扩展要求目标虚拟机连接到 Internet 以便从内容分发网络 (CDN) 检索 Chef 客户端有效负载。  

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示了 Chef VM 扩展的架构。 此扩展至少需要 Chef 服务器 URL、验证客户端名称以及 Chef 服务器的验证密钥，可以从安装 [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) 或独立的 [Chef 服务器](https://downloads.chef.io/chef-server)时下载的 starter-kit.zip 中的 `knife.rb` 文件中找到这些值。 因为验证密钥被视为敏感数据，因此应当将其配置在 **protectedSettings** 元素下，这意味着它只能在目标虚拟机上解密。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>核心属性值

| 名称 | 值/示例 | 数据类型
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string （date） |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux)、`ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.12` | string （double） |

### <a name="settings"></a>设置

| 名称 | 值/示例 | 数据类型 | 必需？
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | Y |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Y |
| settings/runlist | `recipe[mycookbook::default]` | string | Y |

### <a name="protected-settings"></a>受保护的设置

| 名称 | 示例 | 数据类型 | 必需？
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以使用模板部署一台或多台虚拟机，安装 Chef 客户端，连接到 Chef 服务器并根据 [Run-list](https://docs.chef.io/run_lists.html) 定义的内容在服务器上执行初始配置。

可以在找到包含 Chef VM 扩展的示例资源管理器模板[Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)。

虚拟机扩展的 JSON 配置可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/resource-manager-template-child-resource.md)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将 Chef VM 扩展部署到现有的 VM。 将 **validation_key** 替换为你的验证密钥的内容（此文件以 `.pem` 作为扩展名）。  将 **validation_client_name**、**chef_server_url** 和 **run_list** 替换为初学者工具包中的 `knife.rb` 文件中的那些值。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>故障排除和支持

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

扩展执行输出将记录到以下文件：

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>错误代码及其含义

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 51 | VM 的操作系统不支持此扩展 | |

可以在 [Chef VM 扩展自述文件](https://github.com/chef-partners/azure-chef-extension)中找到更多故障排除信息。

## <a name="next-steps"></a>后续步骤

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
