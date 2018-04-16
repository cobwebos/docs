---
title: 适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展 | Microsoft 文档
description: 使用虚拟机扩展在 Linux 虚拟机上部署网络观察程序代理。
services: virtual-machines-linux
documentationcenter: ''
author: dennisg
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 487eca98b9be20faaa52c0a8952e84c6027ee1f0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>适用于 Linux 的网络观察程序代理虚拟机扩展

## <a name="overview"></a>概述

[Azure 网络观察程序](https://docs.microsoft.com/azure/network-watcher/)是一项网络性能监视、诊断和分析服务，适用于对 Azure 网络进行监视。 网络观察程序代理虚拟机扩展是在 Azure 虚拟机上使用某些网络观察程序功能所必需的。 其中包括根据需要捕获网络流量等高级功能。

本文档详细介绍适用于 Linux 的网络观察程序代理虚拟机扩展支持的平台和部署选项。 安装代理时不会中断，也不会需要重新启动虚拟机。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

网络观察程序代理扩展可以针对这些 Linux 分发运行：

| 分发 | 版本 |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS 和 12.04 LTS |
| Debian | 7 和 8 |
| RedHat | 6.x 和 7.x |
| Oracle Linux | 7x |
| Suse | 11 和 12 |
| OpenSuse | 7.0 |
| CentOS | 7.0 |

请注意，目前不支持 CoreOS。

### <a name="internet-connectivity"></a>Internet 连接

某些网络观察程序代理功能要求将目标虚拟机连接到 Internet。 如果无法建立传出连接，某些网络观察程序代理功能可能无法正常使用，或者会变得不可使用。 有关更多详细信息，请参阅[网络观察程序文档](https://review.docs.microsoft.com/azure/network-watcher/)。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示网络观察程序代理扩展的架构。 该扩展目前既不需要也不支持任何用户提供的设置，而是依赖于默认的配置。

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
        "type": "NetworkWatcherAgentLinux",
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
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行网络观察程序代理扩展。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将网络观察程序代理 VM 扩展部署到现有的虚拟机。

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>故障排除和支持

### <a name="troubleshooting"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

扩展执行输出将记录到在以下目录中发现的文件：

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>支持

如果对本文中的任何内容不了解，可以参阅网络观察程序文档或联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/en-us/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/en-us/support/faq/)。
