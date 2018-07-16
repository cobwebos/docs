---
title: NVIDIA GPU 驱动程序扩展 - Azure Linux VM | Microsoft Docs
description: 用于在运行 Linux 的 N 系列计算 VM 上安装 NVIDIA GPU 驱动程序的 Microsoft Azure 扩展。
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: danis
ms.openlocfilehash: 1febf81745ed804de59113da4f48376e9564b68f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448937"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>适用于 Linux 的 NVIDIA GPU 驱动程序扩展

## <a name="overview"></a>概述

此扩展在 Linux N 系列 VM 上安装 NVIDIA GPU 驱动程序。 根据 VM 系列，此扩展安装 CUDA 或 GRID 驱动程序。 使用此扩展安装 NVIDIA 驱动程序时，即表示你接受并同意 NVIDIA 最终用户许可协议的条款。 在安装过程中，虚拟机可能会重新启动以完成驱动程序安装。

此扩展也可用于在 [Windows N 系列 VM](hpccompute-gpu-windows.md) 上安装 NVIDIA GPU 驱动程序。

NVIDIA 最终用户许可协议条款位于此处 - https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

此扩展支持以下 OS：

| 分发 | 版本 |
|---|---|
| Linux：Ubuntu | 16.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.3, 7.4 |
| Linux：CentOS | 7.3, 7.4 |

### <a name="internet-connectivity"></a>Internet 连接

用于 NVIDIA GPU 驱动程序的 Microsoft Azure 扩展要求目标虚拟机连接到 Internet 并具有访问权限。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示此扩展的架构。

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | 日期 |
| 发布者 | Microsoft.HpcCompute | 字符串 |
| type | NvidiaGpuDriverLinux | 字符串 |
| typeHandlerVersion | 1.0 | int |


## <a name="deployment"></a>部署


### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板 

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 部署需要部署后配置的一个或多个虚拟机时，模板是理想选择。

虚拟机扩展的 JSON 配置可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/resource-manager-template-child-resource.md)。 

以下示例假定扩展嵌套在虚拟机资源内。 嵌套扩展资源时，JSON 放置在虚拟机的 `"resources": []` 对象中。

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户以及使用 Azure PowerShell 和 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>退出代码

| 退出代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 0 | 操作成功 |
| 1 | 扩展的用法不正确。 | 请带着执行输出日志联系支持人员。 |
| 10 | 用于 Hyper-V 和 Azure 的 Linux Integration Services 不可用或未安装。 | 请检查 lspci 输出。 |
| 11 | 在此 VM 大小上找不到 NVIDIA GPU。 | 请使用[支持的 VM 大小和 OS](../linux/n-series-driver-setup.md)。 |
| 14 | 操作失败 | |
| 21 | 在 Ubuntu 上更新失败 | 请检查“sudo apt-get update”的输出 |


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关扩展的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](features-linux.md)。

有关 N 系列 VM 的详细信息，请参阅 [GPU 优化虚拟机大小](../linux/sizes-gpu.md)。