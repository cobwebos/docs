---
title: 适用于 Windows 的 Azure Monitor 依赖项虚拟机扩展 |Microsoft Docs
description: 使用虚拟机扩展在 Windows 虚拟机上部署 Azure Monitor 依赖关系代理。
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 5913e33bc29541adfa1599ad5413ad3702635740
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775429"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>适用于 Windows 的 Azure Monitor 依赖项虚拟机扩展

用于 VM 的 Azure Monitor 映射功能从 Microsoft Dependency Agent 获取其数据。 适用于 Windows 的 Azure VM 依赖关系代理虚拟机扩展由 Microsoft 发布和支持。 该扩展在 Azure 虚拟机上安装依赖关系代理。 本文档详细介绍适用于 Windows 的 Azure VM 依赖关系代理虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>操作系统

适用于 Windows 的 Azure VM 依赖关系代理扩展可以针对用于 VM 的 Azure Monitor 部署一文的 "[支持的操作系统](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)" 部分中列出的受支持操作系统运行。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Azure Windows VM 上的 Azure VM 依赖关系代理扩展的架构。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
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
        "type": "DependencyAgentWindows",
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

| 姓名 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>模板部署

可以通过 Azure 资源管理器模板部署 Azure VM 扩展。 可以使用 Azure 资源管理器模板上一部分中详细介绍的 JSON 架构, 在 Azure 资源管理器模板部署期间运行 Azure VM 依赖关系代理扩展。

虚拟机扩展的 JSON 可以嵌套在虚拟机资源内。 也可以将其放置在资源管理器 JSON 模板的根级别或顶层。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/child-resource-name-type.md)。

下面的示例假定依赖关系代理扩展嵌套在虚拟机资源内。 在嵌套扩展资源时, 将 JSON 放置在虚拟机的`"resources": []`对象中。


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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

将扩展 JSON 置于模板的根目录时, 资源名称包括对父虚拟机的引用。 类型反映了嵌套配置。

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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

你可以使用`Set-AzVMExtension`命令将依赖关系代理虚拟机扩展部署到现有的虚拟机。 在运行该命令之前, 需要将公共和专用配置存储在 PowerShell 哈希表中。

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块检索。 若要查看给定 VM 的扩展部署状态, 请使用 Azure PowerShell 模块运行以下命令:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

扩展执行输出将记录到在以下目录中发现的文件：

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者, 你可以为 Azure 支持事件提供支持。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关如何使用 Azure 支持的信息, 请阅读[Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。
