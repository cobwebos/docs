---
title: 适用于 Windows 的 Azure Monitor 虚拟机扩展
description: 使用虚拟机扩展在 Windows 虚拟机上部署 Log Analytics 代理。
services: virtual-machines-windows
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 604be42ec74f75e3aa9c790092ed83aee4ad25e1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907024"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>适用于 Windows 的 Azure Monitor 虚拟机扩展

Azure Monitor 日志提供跨云和本地资产的监视功能。 适用于 Windows 的 Log Analytics 代理虚拟机扩展由 Microsoft 发布和支持。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有 Log Analytics 工作区。 本文档详细介绍适用于 Windows 的 Azure Monitor 虚拟机扩展的支持平台、配置和部署选项。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>操作系统

有关受支持的 Windows 操作系统的详细信息，请参阅[Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)文章。

### <a name="agent-and-vm-extension-version"></a>代理和 VM 扩展版本
下表提供了 Windows Azure Monitor VM 扩展版本和每个版本 Log Analytics 代理捆绑包的映射。 

| Log Analytics Windows 代理捆绑版本 | Azure Monitor Windows VM 扩展版本 | 发布日期 | 发行说明 |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18018 | 1.0.18018 | 2019 年 10 月 | <ul><li> 次要 bug 修复和稳定性改进 </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019 年 7 月 | <ul><li> 次要 bug 修复和稳定性改进 </li><li> MaxExpressionDepth 已增加到10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019 年 7 月 | <ul><li> 次要 bug 修复和稳定性改进 </li><li> 添加了在建立代理连接时禁用默认凭据的功能（WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH 的支持） </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019 年 3 月 | <ul><li>小稳定修补程序 </li></ul> |
| 10.19.10006 | 不适用 | Dec 2018 | <ul><li> 小稳定修补程序 </li></ul> | 
| 8.0.11136 | 不适用 | 9月2018 |  <ul><li> 添加了对在 VM 移动上检测资源 ID 更改的支持 </li><li> 添加了使用非扩展安装时对报表资源 ID 的支持 </li></ul>| 
| 8.0.11103 | 不适用 |  2018年4月 | |
| 8.0.11081 | 1.0.11081 | 11月2017 | | 
| 8.0.11072 | 1.0.11072 | 9月2017 | |
| 8.0.11049 | 1.0.11049 | 2017年2月 | |

### <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心会自动预配 Log Analytics 代理，并将其与 Azure 订阅的默认 Log Analytics 工作区连接。 如果你使用的是 Azure 安全中心，请不要通过本文档中的步骤运行。 这样做会覆盖已配置的工作区并断开与 Azure 安全中心的连接。

### <a name="internet-connectivity"></a>Internet 连接
适用于 Windows 的 Log Analytics 代理扩展要求目标虚拟机已连接到 internet。 

## <a name="extension-schema"></a>扩展架构

下面的 JSON 显示了 Log Analytics 代理扩展的架构。 此扩展需要目标 Log Analytics 工作区中的 "工作区 ID" 和 "工作区密钥"。 可以在 Azure 门户的工作区设置中找到这些设置。 由于工作区密钥应视为敏感数据，因此应将其存储在受保护的设置配置中。 Azure VM 扩展保护的设置数据已加密，并且仅在目标虚拟机上解密。 请注意， **workspaceId**和**workspaceKey**区分大小写。

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>属性值

| 姓名 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | EnterpriseCloud。监视 |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId （例如） * | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey （例如） | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |

\* 在 Log Analytics API 中称为 consumerId。

> [NOTE！]有关其他属性，请参阅 Azure [Connect Windows 计算为 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)。

## <a name="template-deployment"></a>模板部署

可以通过 Azure 资源管理器模板部署 azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行 Log Analytics 代理扩展。 可在[Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)中找到包含 LOG ANALYTICS 代理 VM 扩展的示例模板。 

>[!NOTE]
>如果要将代理配置为向多个工作区报告，则模板不支持指定多个工作区 ID 和工作区密钥。 若要将代理配置为向多个工作区报告，请参阅[添加或删除工作区](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)。  

虚拟机扩展的 JSON 可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶层。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

下面的示例假定 Azure Monitor 扩展嵌套在虚拟机资源内。 嵌套扩展资源时，将 JSON 放置在虚拟机的 `"resources": []` 对象中。


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

在模板的根位置放置扩展 JSON 时，资源名称包括对父虚拟机的引用，而类型反映了嵌套配置。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzVMExtension` 命令可用于将 Log Analytics 代理虚拟机扩展部署到现有的虚拟机。 在运行该命令之前，需要将公共和专用配置存储在 PowerShell 哈希表中。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户，以及使用 Azure PowerShell 模块来检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure PowerShell 模块运行以下命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

扩展执行输出将记录到在以下目录中找到的文件：

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>支持

如果在本文的任何位置需要更多帮助，可以联系 MSDN Azure 上的 Azure 专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。 或者，你可以提供 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "获取支持"。 有关使用 Azure 支持的信息，请阅读[Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。
