---
title: 教程 - 创建和管理 Azure 虚拟机规模集
description: 了解如何使用 Azure CLI 创建虚拟机规模集以及某些常见的管理任务，例如如何启动和停止实例，或者如何更改规模集容量。
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 882ace506ee55f09d1b0eb5227892398a7dd9237
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011251"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>教程：使用 Azure CLI 创建和管理虚拟机规模集
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 在虚拟机规模集的整个生命周期内，可能需要运行一个或多个管理任务。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建和连接虚拟机规模集
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 实例大小
> * 手动缩放规模集
> * 执行常见的规模集管理任务

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，本教程要求运行 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 


## <a name="create-a-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机规模集前创建资源组。 使用“[az group create](/cli/azure/group)”命令创建资源组。 在此示例中，在“eastus”区域中创建了名为“myResourceGroup”的资源组。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

在本教程中，此资源组名称是在创建或修改规模集时指定的。


## <a name="create-a-scale-set"></a>创建规模集
请使用 [az vmss create](/cli/azure/vmss) 命令创建虚拟机规模集。 以下示例创建名为“myScaleSet”  的规模集，并生成 SSH 密钥（如果不存在）：

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建和配置所有的规模集资源和 VM 实例需要几分钟时间。 若要将流量分配到单独的 VM 实例，则还要创建负载均衡器。


## <a name="view-the-vm-instances-in-a-scale-set"></a>查看规模集中的 VM 实例
若要查看规模集中的 VM 实例的列表，请使用 [az vmss list-instances](/cli/azure/vmss)，如下所示：

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

以下示例输出显示了规模集中的两个 VM 实例：

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


输出中的第一个列显示 *InstanceId*。 若要查看特定 VM 实例的其他信息，请将 `--instance-id` 参数添加到 [az vmss get-instance-view](/cli/azure/vmss)。 以下示例查看 VM 实例 *1* 的相关信息：

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>列出连接信息
系统将公共 IP 地址分配给负载均衡器，由后者将流量路由到各个 VM 实例。 默认情况下，会将网络地址转换 (NAT) 规则添加到 Azure 负载均衡器，由后者将远程连接流量转发给给定端口上的每个 VM。 若要连接到规模集中的 VM 实例，请创建一个可连接到已分配的公共 IP 地址和端口号的远程连接。

若要列出规模集中连接到 VM 实例的地址和端口，请使用 [az vmss list-instance-connection-info](/cli/azure/vmss)：

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

以下示例输出显示了实例名称、负载均衡器的公共 IP 地址，以及可以通过 NAT 规则将流量转发到其中的端口号：

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

通过 SSH 连接到第一个 VM 实例。 使用 `-p` 参数指定公共 IP 地址和端口号，如前述命令所示：

```console
ssh azureuser@13.92.224.66 -p 50001
```

登录到 VM 实例以后，可以根据需要执行一些手动配置更改。 现在，请按正常方式关闭 SSH 会话：

```console
exit
```


## <a name="understand-vm-instance-images"></a>了解 VM 实例映像
在教程开头创建规模集时，为 VM 实例指定的 `--image` 为 *UbuntuLTS*。 Azure 市场包括许多可用于创建 VM 实例的映像。 若要查看最常用的映像列表，请使用 [az vm image list](/cli/azure/vm/image) 命令。

```azurecli-interactive
az vm image list --output table
```

以下示例输出显示 Azure 上的最常用 VM 映像。 可以使用 *UrnAlias* 在创建规模集时指定一个最常用的映像。

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

若要查看完整列表，请添加 `--all` 参数。 还可以按 `--publisher` 或 `–-offer` 筛选映像列表。 以下示例从列表中筛选出了产品/服务与“CentOS”  相匹配的所有映像：

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

以下浓缩版输出显示了一些可用的 CentOS 7.3 映像：

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

若要部署使用特定映像的规模集，请使用“Urn”  列中的值。 指定映像时，可将映像版本号替换为 *latest*，以便选择最新的发行版。 在以下示例中，`--image` 参数用于指定最新版本的 CentOS 7.3 映像。

由于只需数分钟即可创建和配置所有的规模集资源和 VM 实例，因此不需部署以下规模集：

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>了解 VM 实例大小
VM 实例大小或 *SKU* 决定了可供 VM 实例使用的计算资源（如 CPU、GPU 和内存）的量。 需要根据预期的工作负荷适当调整规模集中 VM 实例的大小。

### <a name="vm-instance-sizes"></a>VM 实例大小
下表将常用 VM 大小按类别分成了多个用例。

| 类型                     | 常见大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](../virtual-machines/linux/sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0-7| CPU 与内存之比均衡。 适用于开发/测试、小到中型应用程序和数据解决方案。  |
| [计算优化](../virtual-machines/linux/sizes-compute.md)   | Fs, F             | 高 CPU 与内存之比。 适用于中等流量的应用程序、网络设备和批处理。        |
| [内存优化](../virtual-machines/linux/sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | 较高的内存核心比。 适用于关系数据库、中到大型缓存和内存分析。                 |
| [存储优化](../virtual-machines/linux/sizes-storage.md)      | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](../virtual-machines/linux/sizes-gpu.md)          | NV, NC            | 专门针对大量图形绘制和视频编辑的 VM。       |
| [高性能](../virtual-machines/linux/sizes-hpc.md) | H, A8-11          | 功能极其强大的 CPU VM 具有可选的高吞吐量网络接口 (RDMA)。 

### <a name="find-available-vm-instance-sizes"></a>查找可用的 VM 实例大小
若要查看在特定区域可用的 VM 实例大小的列表，请使用 [az vm list-sizes](/cli/azure/vm) 命令。

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

输出类似于以下浓缩版示例，其中显示了分配给每个 VM 大小的资源：

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>创建特定 VM 实例大小的规模集
在教程开头创建规模集时，为 VM 实例提供了默认 VM SKU *Standard_D1_v2*。 可以根据 [az vm list-sizes](/cli/azure/vm) 的输出指定其他 VM 实例大小。 以下示例会使用 `--vm-sku` 参数创建一个规模集，以便指定 VM 实例大小 *Standard_F1*。 由于只需数分钟即可创建和配置所有的规模集资源和 VM 实例，因此不需部署以下规模集：

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>更改规模集的容量
在教程开头创建规模集时，默认部署了两个 VM 实例。 可以使用 [az vmss create](/cli/azure/vmss) 来指定 `--instance-count` 参数，以便更改通过规模集创建的实例数。 若要增加或减少现有规模集中的 VM 实例数，可以手动更改容量。 规模集会创建或删除所需数量的 VM 实例，然后配置分发流量所需的负载均衡器。

若要手动增加或减少规模集中 VM 实例的数目，请使用 [az vmss scale](/cli/azure/vmss)。 以下示例将规模集中 VM 实例的数目设置为 *3*：

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

更新规模集容量需要花费数分钟。 若要查看规模集中目前包含的实例数，请使用 [az vmss show](/cli/azure/vmss) 并对 sku.capacity  进行查询：

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>常见管理任务
现在可以创建规模集、列出连接信息以及连接到 VM 实例。 你已经了解如何对 VM 实例使用其他 OS 映像、如何选择其他 VM 大小，或者如何手动缩放实例数。 在日常管理过程中，可能需要停止、启动或重启规模集中的 VM 实例。

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>停止和解除分配规模集中的 VM 实例
若要在规模集中停止一个或多个 VM 实例，请使用 [az vmss stop](/cli/azure/vmss)。 可以通过 `--instance-ids` 参数指定一个或多个需要停止的 VM 实例。 若不指定实例 ID，则会停止规模集中的所有 VM 实例。 以下示例停止实例 *1*：

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

已停止的 VM 实例会保留已分配状态，且会继续产生计算费用。 如果希望 VM 实例保持已解除分配状态，且仅会产生存储费用，请使用 [az vmss deallocate](/cli/azure/vmss)。 以下示例停止和解除分配实例 *1*：

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>启动规模集中的 VM 实例
若要在规模集中启动一个或多个 VM 实例，请使用 [az vmss start](/cli/azure/vmss)。 可以通过 `--instance-ids` 参数指定一个或多个需要启动的 VM 实例。 若不指定实例 ID，则会启动规模集中的所有 VM 实例。 以下示例启动实例 *1*：

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>重启规模集中的 VM 实例
若要在规模集中重启一个或多个 VM 实例，请使用 [az vmss restart](/cli/azure/vmss)。 可以通过 `--instance-ids` 参数指定一个或多个需要重启的 VM 实例。 若不指定实例 ID，则会重启规模集中的所有 VM 实例。 以下示例重启实例 *1*：

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>清理资源
删除资源组时，也会删除其中包含的所有资源，例如 VM 实例、虚拟网络和磁盘。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>后续步骤
本教程介绍了如何使用 Azure CLI 执行一些基本的规模集创建和管理任务：

> [!div class="checklist"]
> * 创建和连接虚拟机规模集
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 手动缩放规模集
> * 执行常见的规模集管理任务

请转到下一教程，了解规模集磁盘。

> [!div class="nextstepaction"]
> [将数据磁盘与规模集配合使用](tutorial-use-disks-cli.md)
