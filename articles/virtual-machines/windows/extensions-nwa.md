---
title: 适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展 | Microsoft 文档
description: 使用虚拟机扩展在 Windows 虚拟机上部署网络观察程序代理。
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: ed5795b3ec6cf31feea9429914b04f11efeffe6a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776000"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>适用于 Windows 的网络观察程序代理虚拟机扩展

## <a name="overview"></a>概述

[Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)是一项网络性能监视、诊断和分析服务，可以对 Azure 网络进行监视。 网络观察程序代理虚拟机扩展是按需捕获网络流量和运行 Azure 虚拟机上的其他高级功能所必需的。


本文档详细介绍适用于 Windows 的网络观察程序代理虚拟机扩展支持的平台和部署选项。 安装代理时不会中断，也不会需要重新启动虚拟机。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

可以在 Windows Server 2008 R2、2012、2012 R2 和 2016 版本中运行适用于 Windows 的网络观察程序代理扩展。 不支持 Nano Server。

### <a name="internet-connectivity"></a>Internet 连接

某些网络观察程序代理功能要求将目标虚拟机连接到 Internet。 如果不能建立传出连接，网络观察程序代理将不能将数据包捕获上传到存储帐户。 有关更多详细信息，请参阅[网络观察程序文档](../../network-watcher/network-watcher-monitoring-overview.md)。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示网络观察程序代理扩展的架构。 该扩展既不需要也不支持任何用户提供的设置，而是依赖于其默认配置。

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| 发布者 | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行网络观察程序代理扩展。

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureRmVMExtension` 命令将网络观察程序代理虚拟机扩展部署到现有的虚拟机：

```powershell
Set-AzureRmVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>故障排除和支持

### <a name="troubleshooting"></a>故障排除

可以从 Azure 门户和 PowerShell 检索有关扩展部署状态的数据。 若要查看给定 VM 的扩展部署状态，请使用 Azure PowerShell 模块运行以下命令：

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

扩展执行输出将记录到在以下目录中发现的文件：

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>支持

如果对本文中的任何内容不了解，可以参阅网络观察程序用户指南文档或联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
