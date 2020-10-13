---
title: 无限驱动程序扩展-Azure Linux Vm
description: 用于在运行 Linux 的 H 系列和 N 系列计算 Vm 上安装不适合的驱动程序的 Microsoft Azure 扩展。
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: eb06f98d1a6e9b76c321e3d202043d656a2d94eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87829044"
---
# <a name="infiniband-driver-extension-for-linux"></a>适用于 Linux 的无限驱动程序扩展

此扩展在运行 Linux) H 系列和 N 系列 vm [H 系列](../sizes-hpc.md) 和 [N 系列](../sizes-gpu.md) 虚拟机上安装 OFED 驱动程序，并在启用了 sr-iov 的、启用了 sr-iov 的 ( "r" 大小。 根据 VM 系列的不同，扩展将为 x-blade NIC 安装适当的驱动程序。

[此处](../workloads/hpc/enable-infiniband.md#manual-installation)提供有关手动安装 OFED 驱动程序的说明。

还可使用扩展来安装 [Windows vm](hpc-compute-infiniband-windows.md)的无窗口驱动程序。

## <a name="prerequisites"></a>必备条件

### <a name="operating-system"></a>操作系统

此扩展支持以下 OS 发行版，具体取决于特定 OS 版本对驱动程序的支持。

| 分发 | 版本 |
|---|---|
| Linux：Ubuntu | 16.04 LTS、18.04 LTS、20.04 LTS |
| Linux：CentOS | 7.4、7.5、7.6、7.7、8.1、8、2 |
| Linux：Red Hat Enterprise Linux | 7.4、7.5、7.6、7.7、8.1、8、2 |

### <a name="internet-connectivity"></a>Internet 连接

用于不使用的驱动程序的 Microsoft Azure 扩展要求目标 VM 已连接到并且有权访问 internet。

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
| publisher | Microsoft.HpcCompute | 字符串 |
| type | InfiniBandDriverLinux | 字符串 |
| typeHandlerVersion | 1.1 | int |



## <a name="deployment"></a>部署


### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板 

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 部署需要部署后配置的一个或多个虚拟机时，模板是理想选择。

虚拟机扩展的 JSON 配置可以嵌套在虚拟机资源内，或放置在资源管理器 JSON 模板的根级别或顶级别。 JSON 的位置会影响资源名称和类型的值。 有关详细信息，请参阅[设置子资源的名称和类型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>将扩展添加到虚拟机规模集

以下示例在名为*myResourceGroup*的资源组中部署的名为*myVMSS*的现有虚拟机规模集中的所有支持 RDMA 的 vm 上安装最新版本 1.1 InfiniBandDriverLinux 扩展。

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
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

扩展执行输出将记录到以下文件。 请参阅此文件，以跟踪安装的状态以及排查任何故障。

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>退出代码

下表介绍了基于扩展安装过程的退出代码的含义和建议的操作。

| 退出代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 0 | 操作成功 |
| 1 | 扩展的用法不正确 | 检查执行输出日志 |
| 10 | 用于 Hyper-V 和 Azure 的 Linux Integration Services 不可用或未安装 | 检查 lspci 的输出 |
| 11 | 在此 VM 大小上找不到 Mellanox 无空间 | 使用[受支持的 VM 大小和 OS](../sizes-hpc.md) |
| 12 | 不支持的映像产品/服务 |
| 13 | 不支持的 VM 大小 | 使用启用了不支持的 ( "r" 大小) [H 系列](../sizes-hpc.md) 和 [N 系列](../sizes-gpu.md)n 系列 VM 进行部署 |
| 14 | 操作失败 | 检查执行输出日志 |


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你可以通过 [Azure 支持站点](https://azure.microsoft.com/support/options/)提供支持事件。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关启用了未支持 ( "r" 大小) 的详细信息，请参阅 [H 系列](../sizes-hpc.md) 和 [N 系列](../sizes-gpu.md) vm。

> [!div class="nextstepaction"]
> [详细了解 Linux Vm 扩展和功能](features-linux.md)