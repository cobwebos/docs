---
title: Stackify Retrace Azure Linux 代理扩展 | Microsoft Docs
description: 在 Linux 虚拟机上部署 Stackify Retrace Linux 代理。
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: b286ebc2e50166e8491b45346a81b161227f8d21
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415960"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux 代理扩展

## <a name="overview"></a>概述
Stackify 提供跟踪应用程序详细信息的产品，帮助快速查找和修复问题。 对于开发人员团队来说，Retrace 是一款完全集成、多环境且应用性能超级强大的工具。 它可与每个开发团队所需的一系列工具相结合。

Retrace 是唯一可以在单一平台中的所有环境下提供以下所有功能的工具。

* 应用程序性能管理 (APM)
* 应用程序和服务器日志记录
* 跟踪和监视错误
* 服务器、应用程序和自定义指标

**关于 Stackify Linux 代理扩展**

此扩展提供了适用于 Retrace 的 Linux 代理的安装路径。 

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统 
Retrace 代理可以针对这些 Linux 分发运行

| 分发 | 版本 |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS、16.10 和 17.04 |
| Debian | 7.9+ 和 8.2+、9 |
| Red Hat | 6.7+、7.1+ |
| CentOS | 6.3+、7.0+ |

### <a name="internet-connectivity"></a>Internet 连接
适用于 Linux 的 Stackify 代理扩展要求目标虚拟机已连接到 Internet。 

可能需要调整网络配置才可连接到 Stackify，请参阅 https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall。 


## <a name="extension-schema"></a>扩展架构
---

以下 JSON 显示 Stackify Retrace 代理扩展的架构。 该扩展需要 `environment` 和 `activationKey`。

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>模板部署 

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行 Stackify Retrace Linux 代理扩展。  

虚拟机扩展的 JSON 可以嵌套在虚拟机资源内，或放置在 Resource Manager JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅“设置子资源的名称和类型”。

以下示例假定 Stackify Retrace Linux 扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 "resources": [] 对象中。

该扩展需要 `environment` 和 `activationKey`。

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

将扩展 JSON 放置在模板的根部时，资源名称包括对父虚拟机的引用，并且类型反映了嵌套的配置。

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzureRmVMExtension` 命令将 Stackify Retrace Linux 代理虚拟机扩展部署到现有的虚拟机。 运行命令之前，需将公共和专用配置存储在 PowerShell 哈希表中。

该扩展需要 `environment` 和 `activationKey`。

```
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzureRmVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署 

可以使用 Azure CLI 工具将 Stackify Retrace Linux 代理虚拟机扩展部署到现有的虚拟机。  

该扩展需要 `environment` 和 `activationKey`。

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="error-codes"></a>错误代码

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 10 | 安装错误 | 必需 wget |
| 20 | 安装错误 | 必需 python |
| 30 | 安装错误 | 必需 sudo |
| 40 | 安装错误 | 必需 activationKey |
| 51 | 安装错误 | 不支持 OS 发行版 |
| 60 | 安装错误 | 必需环境 |
| 70 | 安装错误 | Unknown |
| 80 | 启用错误 | 服务安装失败 |
| 90 | 启用错误 | 服务启动失败 |
| 100 | 禁用错误 | 服务停止失败 |
| 110 | 禁用错误 | 服务删除失败 |
| 120 | 卸载错误 | 服务停止失败 |

如果需要更多帮助，可以在 https://support.stackify.com 联系 Stackify 支持。