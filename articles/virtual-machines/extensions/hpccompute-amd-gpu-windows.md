---
title: AMD GPU 驱动程序扩展 - Azure Windows VM
description: 用于在运行 Windows 的 NVv4 系列 VM 上安装 AMD GPU 驱动程序的 Microsoft Azure 扩展。
services: virtual-machines-windows
documentationcenter: ''
author: vikancha
manager: jkabat
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 0819eec5275fa9783fff363cabbd8c3ed22b7cd6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119546"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>适用于 Windows 的 AMD GPU 驱动程序扩展

本文概述了用于在 Windows [NVv4 系列](https://docs.microsoft.com/azure/virtual-machines/nvv4-series) VM 上部署 AMD GPU 驱动程序的 VM 扩展。 使用此扩展安装 AMD 驱动程序即表示你接受并同意 [AMD 最终用户许可协议](https://amd.com/radeonsoftwarems)的条款。 在安装过程中，VM 可能会重新启动以完成驱动程序安装。

[此处](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup)提供了有关手动安装驱动程序和当前支持的版本的说明。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

此扩展支持以下 OS：

| 分发 | 版本 |
|---|---|
| Windows 10 EMS | 内部版本 1903 |
| Windows 10 | 内部版本 1809 |
| Windows Server 2016 | 核心 |
| Windows Server 2019 | 核心 |

### <a name="internet-connectivity"></a>Internet 连接

用于 AMD GPU 驱动程序的 Microsoft Azure 扩展要求目标 VM 连接到 Internet 并具有访问权限。

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>属性

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| 发布者 | Microsoft.HpcCompute | 字符串 |
| type | AmdGpuDriverWindows | 字符串 |
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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户以及使用 Azure PowerShell 和 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下目录：

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>错误代码

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 0 | 操作成功 |
| 1 | 操作成功。 需要重新启动。 |
| 100 | 操作不受支持或无法完成。 | 可能的原因：不支持 PowerShell 版本、VM 大小不是 N 系列 VM、下载数据失败。 请检查日志文件，以确定错误原因。 |
| 240、840 | 操作超时。 | 请重试操作。 |
| -1 | 发生异常。 | 请检查日志文件，以确定异常原因。 |
| -5x | 由于重新启动未完成，导致操作中断。 | 重新启动 VM。 重新启动后将继续安装。 应手动调用卸载。 |


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](features-windows.md)。

有关 N 系列 VM 的详细信息，请参阅 [GPU 优化虚拟机大小](../windows/sizes-gpu.md)。
