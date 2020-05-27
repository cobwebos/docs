---
title: 教程 - 创建和管理 Azure 虚拟机规模集
description: 了解如何使用 Azure PowerShell 创建虚拟机规模集以及某些常见的管理任务，例如如何启动和停止实例，或者如何更改规模集容量。
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 938b4e64dd5b67488ae5d061f2ceb29ae4bb7f6e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81011221"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>教程：使用 Azure PowerShell 创建和管理虚拟机规模集

利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 在虚拟机规模集的整个生命周期内，可能需要运行一个或多个管理任务。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建和连接虚拟机规模集
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 实例大小
> * 手动缩放规模集
> * 执行常见的规模集管理任务

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机规模集前创建资源组。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 在此示例中，在“EastUS”区域中创建了名为“myResourceGroup”的资源组。 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
在本教程中，此资源组名称是在创建或修改规模集时指定的。


## <a name="create-a-scale-set"></a>创建规模集
首先，使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 实例的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 创建虚拟机规模集。 若要将流量分配到单独的 VM 实例，则还要创建负载均衡器。 负载均衡器包含的规则可在 TCP 端口 80 上分配流量，并允许 TCP 端口 3389 上的远程桌面流量，以及 TCP 端口 5985 上的 PowerShell 远程流量：

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

创建和配置所有的规模集资源和 VM 实例需要几分钟时间。


## <a name="view-the-vm-instances-in-a-scale-set"></a>查看规模集中的 VM 实例
若要在规模集中查看 VM 实例的列表，请使用 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)，如下所示：

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

以下示例输出显示了规模集中的两个 VM 实例：

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

若要查看特定 VM 实例的其他信息，请将 `-InstanceId` 参数添加到 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)。 以下示例查看 VM 实例 *1* 的相关信息：

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>列出连接信息
系统将公共 IP 地址分配给负载均衡器，由后者将流量路由到各个 VM 实例。 默认情况下，会将网络地址转换 (NAT) 规则添加到 Azure 负载均衡器，由后者将远程连接流量转发给给定端口上的每个 VM。 若要连接到规模集中的 VM 实例，请创建一个可连接到已分配的公共 IP 地址和端口号的远程连接。

若要列出连接到规模集中的 VM 实例所需的 NAT 端口，请先使用 [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer) 获取负载均衡器对象， 然后使用 [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig) 查看入站 NAT 规则：


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

以下示例输出显示了实例名称、负载均衡器的公共 IP 地址，以及可以通过 NAT 规则将流量转发到其中的端口号：

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

此规则的 *Name* 与以前的 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) 命令中显示的 VM 实例的名称相符。 例如，若要连接到 VM 实例 *0*，请使用 *myScaleSet3389.0* 并连接到端口 *50001*。 若要连接到 VM 实例 *1*，请使用 *myScaleSet3389.1* 中的值并连接到端口 *50002*。 若要使用 PowerShell 远程处理，请连接到 *TCP* 端口 *5985* 所对应的 VM 实例规则。

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 查看负载均衡器的公共 IP 地址：


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

示例输出：

```powershell
IpAddress
---------
52.168.121.216
```

创建连接到第一个 VM 实例所需的远程连接。 指定所需 VM 实例的公共 IP 地址和端口号，如前述命令所示。 出现提示时，输入创建规模集时使用的凭据（在示例命令中，默认为 azureuser 和 P\@ssw0rd!） 。 如果使用 Azure Cloud Shell，请从本地 PowerShell 命令提示符或远程桌面客户端执行此步骤。 以下示例连接到 VM 实例 *1*：

```powershell
mstsc /v 52.168.121.216:50001
```

登录到 VM 实例以后，可以根据需要执行一些手动配置更改。 现在，请关闭远程连接。


## <a name="understand-vm-instance-images"></a>了解 VM 实例映像
Azure 市场包括许多可用于创建 VM 实例的映像。 若要查看可用发布者的列表，请使用 [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) 命令。

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

若要查看给定发布者的映像的列表，请使用 [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)。 还可以按 `-PublisherName` 或 `-Offer` 筛选映像列表。 以下示例从列表中筛选出了发布者名称为 *MicrosoftWindowsServer* 且产品/服务与 *WindowsServer* 相匹配的所有映像：

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

以下示例输出演示所有可用的 Windows Server 映像：

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

在教程开头创建规模集时，为 VM 实例提供了默认 VM 映像 *Windows Server 2016 DataCenter*。 可以根据 [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) 的输出指定其他 VM 映像。 以下示例会使用 `-ImageName` 参数创建一个规模集，以便指定 VM 映像 *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*。 由于只需数分钟即可创建和配置所有的规模集资源和 VM 实例，因此不需部署以下规模集：

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>了解 VM 实例大小
VM 实例大小或 *SKU* 决定了可供 VM 实例使用的计算资源（如 CPU、GPU 和内存）的量。 需要根据预期的工作负荷适当调整规模集中 VM 实例的大小。

### <a name="vm-instance-sizes"></a>VM 实例大小
下表将常用 VM 大小按类别分成了多个用例。

| 类型                     | 常见大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](../virtual-machines/windows/sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0-7| CPU 与内存之比均衡。 适用于开发/测试、小到中型应用程序和数据解决方案。  |
| [计算优化](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | 高 CPU 与内存之比。 适用于中等流量的应用程序、网络设备和批处理。        |
| [内存优化](../virtual-machines/windows/sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | 较高的内存核心比。 适用于关系数据库、中到大型缓存和内存分析。                 |
| [存储优化](../virtual-machines/windows/sizes-storage.md)      | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | 专门针对大量图形绘制和视频编辑的 VM。       |
| [高性能](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | 功能极其强大的 CPU VM 具有可选的高吞吐量网络接口 (RDMA)。 

### <a name="find-available-vm-instance-sizes"></a>查找可用的 VM 实例大小
若要查看在特定区域可用的 VM 实例大小的列表，请使用 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 命令。 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

输出类似于以下浓缩版示例，其中显示了分配给每个 VM 大小的资源：

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

在教程开头创建规模集时，为 VM 实例提供了默认 VM SKU *Standard_DS1_v2*。 可以根据 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 的输出指定其他 VM 实例大小。 以下示例会使用 `-VmSize` 参数创建一个规模集，以便指定 VM 实例大小 *Standard_F1*。 由于只需数分钟即可创建和配置所有的规模集资源和 VM 实例，因此不需部署以下规模集：

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>更改规模集的容量
你在创建规模集时，请求了两个 VM 实例。 若要增加或减少规模集中的 VM 实例数，可以手动更改容量。 规模集会创建或删除所需数量的 VM 实例，然后配置分发流量所需的负载均衡器。

首先，使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 创建的规模集对象，然后为 `sku.capacity` 指定新的值。 若要应用容量更改，请使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)。 以下示例将规模集中 VM 实例的数目设置为 *3*：

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

更新规模集容量需要花费数分钟。 若要查看规模集中当前包含的实例数，请使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)：

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

以下示例输出显示规模集的容量现在为 *3*：

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>常见管理任务
现在可以创建规模集、列出连接信息以及连接到 VM 实例。 你已经了解如何对 VM 实例使用其他 OS 映像、如何选择其他 VM 大小，或者如何手动缩放实例数。 在日常管理过程中，可能需要停止、启动或重启规模集中的 VM 实例。

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>停止和解除分配规模集中的 VM 实例
若要停止规模集中的一个或多个 VM，请使用 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss)。 通过 `-InstanceId` 参数，可指定要停止的一个或多个 VM。 若不指定实例 ID，则停止规模集中的所有 VM。 以下示例停止实例 *1*：

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

默认情况下，将取消分配已停止的 VM，这些 VM 不会产生计算费用。 若要在停止 VM 后保持预配状态，请将 `-StayProvisioned` 参数添加到上面的命令中。 保持预配状态的已停止 VM 会产生常规计算费用。

### <a name="start-vm-instances-in-a-scale-set"></a>启动规模集中的 VM 实例
若要启动规模集中的一个或多个 VM，请使用 [Start-AzVmss](/powershell/module/az.compute/start-azvmss)。 通过 `-InstanceId` 参数，可指定要启动的一个或多个 VM。 若不指定实例 ID，则启动规模集中的所有 VM。 以下示例启动实例 *1*：

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>重启规模集中的 VM 实例
若要重启规模集中的一个或多个 VM，请使用 [Retart-AzVmss](/powershell/module/az.compute/restart-azvmss)。 通过 `-InstanceId` 参数，可指定要重启的一个或多个 VM。 若不指定实例 ID，则重启规模集中的所有 VM。 以下示例重启实例 *1*：

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>清理资源
删除资源组时，也会删除其中包含的所有资源，例如 VM 实例、虚拟网络和磁盘。 `-Force` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。 `-AsJob` 参数会使光标返回提示符处，不会等待操作完成。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>后续步骤
本教程介绍了如何使用 Azure PowerShell 执行一些基本的规模集创建和管理任务：

> [!div class="checklist"]
> * 创建和连接虚拟机规模集
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 手动缩放规模集
> * 执行常见的规模集管理任务

请转到下一教程，了解规模集磁盘。

> [!div class="nextstepaction"]
> [将数据磁盘与规模集配合使用](tutorial-use-disks-powershell.md)
