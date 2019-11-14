---
title: 适用于 Linux 的 Azure Monitor 依赖项虚拟机扩展
description: 使用虚拟机扩展在 Linux 虚拟机上部署 Azure Monitor 依赖关系代理。
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
ms.openlocfilehash: 46b0ddeeb70e263723b657ef3150dc5548fcc742
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073874"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>适用于 Linux 的 Azure Monitor 依赖项虚拟机扩展

用于 VM 的 Azure Monitor 映射功能从 Microsoft Dependency Agent 获取其数据。 适用于 Linux 的 Azure VM 依赖关系代理虚拟机扩展由 Microsoft 发布和支持。 该扩展在 Azure 虚拟机上安装依赖关系代理。 本文档详细介绍适用于 Linux 的 Azure VM 依赖关系代理虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

适用于 Linux 的 Azure VM 依赖关系代理扩展可以针对用于 VM 的 Azure Monitor 部署一文的 "[支持的操作系统](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)" 部分中列出的受支持操作系统运行。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Azure Linux VM 上的 Azure VM 依赖关系代理扩展的架构。 

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

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| 发布者 | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以使用 Azure 资源管理器模板上一部分中详细介绍的 JSON 架构，在 Azure 资源管理器模板部署期间运行 Azure VM 依赖关系代理扩展。

虚拟机扩展的 JSON 可以嵌套在虚拟机资源内。 也可以将其放置在资源管理器 JSON 模板的根级别或顶层。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/child-resource-name-type.md)。

下面的示例假定依赖关系代理扩展嵌套在虚拟机资源内。 在嵌套扩展资源时，将 JSON 放置在虚拟机的 `"resources": []` 对象中。


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

将扩展 JSON 置于模板的根目录时，资源名称包括对父虚拟机的引用。 类型反映了嵌套配置。 

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

你可以使用 Azure CLI 将依赖关系代理 VM 扩展部署到现有的虚拟机。  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令：

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>支持

如果在本文的任何位置需要更多帮助，请联系 MSDN Azure 上的 Azure 专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。 或者，你可以为 Azure 支持事件提供支持。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关如何使用 Azure 支持的信息，请阅读[Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。
