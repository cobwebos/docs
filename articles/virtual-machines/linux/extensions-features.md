---
title: "适用于 Linux 的虚拟机扩展和功能 | Microsoft 文档"
description: "了解可为 Azure 虚拟机提供哪些扩展，这些虚拟机扩展按它们提供或改进的功能进行分组。"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 8a5b39351f665c51ae7d83f755329e54ff3cf786
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>适用于 Linux 的虚拟机扩展和功能

Azure 虚拟机扩展是小型应用程序，可在Azure 虚拟机上提供部署后配置和自动化任务。 例如，如果虚拟机要求安装软件、防病毒保护或 Docker 配置，便可以使用 VM 扩展来完成这些任务。 可以使用 Azure CLI、PowerShell、Azure Resource Manager 模板和 Azure 门户运行 Azure VM 扩展。 扩展可与新虚拟机部署捆绑在一起，或者针对任何现有系统运行。

本文档提供了 VM 扩展的概述、使用 Azure VM 扩展的先决条件，以及有关如何检测、管理和删除 VM 扩展的指南。 本文档提供的是通用信息，因为有许多 VM 扩展可用，每个扩展可能具有独特的配置。 可以在各个扩展特定的各个文档中找到扩展特定的详细信息。

## <a name="use-cases-and-samples"></a>用例和示例

有许多不同的 Azure VM 扩展可用，每个都有特定用例。 下面是一些示例：

- 使用适用于 Linux 的 DSC 扩展将 PowerShell 所需状态配置应用于虚拟机。 有关详细信息，请参阅 [Azure 所需状态配置扩展](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)。
- 使用 Microsoft 监视代理 VM 扩展配置虚拟机监视功能。 有关详细信息，请参阅[如何监视 Linux VM](tutorial-monitoring.md)。
- 使用 Datadog 扩展配置 Azure 基础结构监视功能。 有关详细信息，请参阅 [Datadog 博客](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)。
- 使用 Docker VM 扩展在 Azure 虚拟机上配置 Docker 主机。 有关详细信息，请参阅 [Docker VM extension](dockerextension.md)（Docker VM 扩展）。

除了进程特定的扩展外，“自定义脚本”扩展也可用于 Windows 和 Linux 虚拟机。 适用于 Linux 的“自定义脚本”扩展允许在虚拟机上运行任何 Bash 脚本。 在设计需要本机 Azure 工具无法提供的配置的 Azure 部署时，自定义脚本很有用。 有关详细信息，请参阅 [Linux VM Custom Script extension](extensions-customscript.md)（Linux VM“自定义脚本”扩展）。


## <a name="prerequisites"></a>必备组件

每个虚拟机扩展可能都有其自己的一组先决条件。 例如，Docker VM 扩展有支持的 Linux 分发的先决条件。 特定于扩展的文档中详细介绍了各个扩展的要求。

### <a name="azure-vm-agent"></a>Azure VM 代理

Azure VM 代理可管理 Azure 虚拟机与 Azure 结构控制器之间的交互。 VM 代理负责部署和管理 Azure 虚拟机的许多功能层面，包括运行 VM 扩展。 Azure VM 代理预先安装在 Azure 应用商店映像上，并可手动安装在受支持的操作系统上。

有关受支持的操作系统以及安装说明的信息，请参阅 [Azure virtual machine agent](../windows/classic/agents-and-extensions.md)（Azure 虚拟机代理）。

## <a name="discover-vm-extensions"></a>发现 VM 扩展

有许多不同的 VM 扩展可与 Azure 虚拟机配合使用。 要查看完整列表，请使用 Azure CLI 运行以下命令，并将示例位置替换为所选位置。

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>运行 VM 扩展

Azure 虚拟机扩展可以在现有虚拟机上运行，当需要在已部署的 VM 上进行配置更改或恢复连接时，这很有用。 VM 扩展还可以与 Azure Resource Manager 模板部署捆绑。 可以将扩展与 Resource Manager 模板配合使用来部署并配置 Azure 虚拟机，在部署后无需干预。

可使用以下方法针对现有虚拟机运行扩展。

### <a name="azure-cli"></a>Azure CLI

可以使用 `az vm extension set` 命令针对现有虚拟机运行 Azure 虚拟机扩展。 此示例针对虚拟机运行自定义脚本扩展。

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

脚本生成的输出类似于以下文本：

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure 门户

可通过 Azure 门户将 VM 扩展应用到现有虚拟机。 为此，请选择虚拟机，选择“扩展”，并单击“添加”。 从可用扩展的列表中选择所需扩展，并按向导中的说明操作。

下图展示了如何从 Azure 门户安装 Linux 自定义脚本扩展。

![安装自定义脚本扩展](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager 模板

VM 扩展可添加到 Azure Resource Manager 模板，并在部署模板的过程中执行。 使用模板部署扩展时，可以创建完全配置的 Azure 部署。 例如，以下 JSON 取自 Resource Manager 模板。 该模板会在每个 VM 上部署一组负载均衡的虚拟机、一个 Azure SQL 数据库，然后安装一个 .NET Core 应用程序。 VM 扩展负责安装软件。

有关详细信息，请参阅完整的 [Resource Manager 模板](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

有关详细信息，请参阅[创作 Azure 资源管理器模板](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions)。

## <a name="secure-vm-extension-data"></a>保护 VM 扩展数据

运行 VM 扩展时，可能需要包括敏感信息，例如凭据、存储帐户名称和存储帐户访问密钥。 许多 VM 扩展包括受保护的配置，该配置对数据进行加密并且仅在目标虚拟机内才对数据进行解密。 每个扩展都有一个特定的受保护的配置架构，扩展特定的文档中详述了各个架构。

以下示例显示了适用于 Linux 的自定义脚本扩展的一个实例。 请注意，要执行的命令包含一组凭据。 在此示例中，不会加密要执行的命令。


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

将**要执行的命令**属性移动到**受保护的**配置可以保护执行字符串。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>排查 VM 扩展的问题

每个 VM 扩展都有特定于扩展的故障排除步骤。 例如，使用自定义脚本扩展时，可在运行该扩展的虚拟机本地找到脚本执行详细信息。 任何特定于扩展的故障排除步骤均在特定于扩展的文档中详细说明。

以下故障排除步骤适用于所有虚拟机扩展。

### <a name="view-extension-status"></a>查看扩展状态

针对虚拟机运行虚拟机扩展后，使用以下 Azure CLI 命令返回扩展状态。 请将示例参数名称替换成自己的值。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

输出类似于以下文本：

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

此外，还可以在 Azure 门户中找到扩展执行状态。 如果要查看扩展的状态，请选择虚拟机，选择“扩展” ，并选择所需的扩展。

### <a name="rerun-a-vm-extension"></a>重新运行 VM 扩展

在某些情况下，可能需要重新运行虚拟机扩展。 如果要重新运行扩展，可以先删除扩展，然后使用所选执行方法重新运行扩展。 若要删除扩展，请使用 Azure CLI 模块运行以下命令。 请将示例参数名称替换成自己的值。

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

在 Azure 门户中可通过下列步骤删除扩展：

1. 选择一个虚拟机。
2. 选择“扩展”。
3. 选择所需的扩展。
4. 选择“卸载”。

## <a name="common-vm-extension-reference"></a>常见 VM 扩展参考
| 扩展名称 | 说明 | 详细信息 |
| --- | --- | --- |
| 适用于 Linux 的自定义脚本扩展 |针对 Azure 虚拟机运行脚本 |[适用于 Linux 的自定义脚本扩展](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Docker 扩展 |安装 Docker 守护程序以支持远程 Docker 命令。 |[Docker VM 扩展](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| VM 访问扩展 |重新获取对 Azure 虚拟机的访问权限 |[VM 访问扩展](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure 诊断扩展 |管理 Azure 诊断 |[Azure 诊断扩展](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 访问扩展 |管理用户和凭据 |[适用于 Linux 的 VM 访问扩展](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
