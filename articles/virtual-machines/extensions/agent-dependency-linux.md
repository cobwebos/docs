---
title: Azure 监视器依赖项虚拟机扩展为 Linux
description: 使用虚拟机扩展在 Linux 虚拟机上部署 Azure 监视器依赖项代理。
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254035"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Azure 监视器依赖项虚拟机扩展为 Linux

用于 VM 的 Azure Monitor 映射功能从 Microsoft Dependency Agent 获取其数据。 Microsoft 发布并支持 Linux 的 Azure VM 依赖项代理虚拟机扩展。 扩展在 Azure 虚拟机上安装依赖项代理。 本文档详细介绍了适用于 Linux 的 Azure VM 依赖项代理虚拟机扩展的支持平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

可以针对 Azure 监视器 VM 部署文章中列出的受支持的操作系统运行 Linux[Supported operating systems](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)的 Azure VM 依赖项代理扩展。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示了 Azure Linux VM 上的 Azure VM 依赖项代理代理扩展的架构。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>属性值

| “属性” | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| 发布者 | 微软.Azure.监视.依赖代理 |
| type | 依赖代理Linux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以使用 Azure 资源管理器模板中上一节中详述的 JSON 架构在 Azure 资源管理器模板部署期间运行 Azure VM 依赖项代理扩展。

虚拟机扩展的 JSON 可以嵌套在虚拟机资源中。 或者，您可以将它放置在资源管理器 JSON 模板的根级或顶层。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。

下面的示例假定依赖项代理扩展嵌套在虚拟机资源中。 嵌套扩展资源时，JSON 将放置在虚拟机`"resources": []`的对象中。


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

将扩展 JSON 放在模板的根目录时，资源名称包含对父虚拟机的引用。 该类型反映嵌套配置。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将依赖项代理 VM 扩展部署到现有虚拟机。  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 检索。 要查看给定 VM 扩展的部署状态，请使用 Azure CLI 运行以下命令：

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>支持

如果本文中的任何一点都需要更多帮助，请与[MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家联系。 或者，您可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”****。 有关如何使用 Azure 支持的信息，请阅读[Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
