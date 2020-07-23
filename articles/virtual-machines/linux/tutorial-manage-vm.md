---
title: 教程 - 使用 Azure CLI 创建和管理 Linux VM
description: 本教程介绍如何使用 Azure CLI 在 Azure 中创建和管理 Linux VM
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8e559be82fc7c3d6b25b99319785d49e30f56287
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459995"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>教程：使用 Azure CLI 创建和管理 Linux VM

Azure 虚拟机提供完全可配置的灵活计算环境。 本教程介绍 Azure 虚拟机的基本部署项目，例如选择 VM 大小、选择 VM 映像和部署 VM。 学习如何：

> [!div class="checklist"]
> * 创建并连接到 VM
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 调整 VM 的大小
> * 查看并了解 VM 状态

本教程在 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 中使用 CLI，后者已不断更新到最新版本。 若要打开 Cloud Shell，请从任何代码块的顶部选择“试一试”  。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-resource-group"></a>创建资源组

使用“[az group create](https://docs.microsoft.com/cli/azure/group)”命令创建资源组。 

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。 在此示例中，在“eastus”区域中创建了名为“myResourceGroupVM”的资源组。 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

创建或修改 VM 时指定资源组，本教程会对此进行演示。

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm) 命令创建虚拟机。 

创建虚拟机时，可使用多个选项，例如操作系统映像、磁盘大小调整和管理凭据。 下面的示例创建一个名为 *myVM*、运行 Ubuntu Server 的 VM。 将在该 VM 上创建名为 *azureuser* 的用户帐户，并生成 SSH 密钥（如果这些密钥在默认密钥位置 ( *~/.ssh*) 中不存在）：

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 可能需要几分钟。 创建 VM 后，Azure CLI 会输出有关 VM 的信息。 请记下 `publicIpAddress`，可以使用此地址访问虚拟机。 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>连接到 VM

现在可以在 Azure Cloud Shell 中使用 SSH 或从本地计算机连接到 VM。 将示例 IP 地址替换为上一步骤中记下的 `publicIpAddress`。

```bash
ssh azureuser@52.174.34.95
```

登录 VM 后，可以安装和配置应用程序。 完成后，可按正常方式关闭 SSH 会话：

```bash
exit
```

## <a name="understand-vm-images"></a>了解 VM 映像

Azure 市场包括许多可用于创建 VM 的映像。 在之前的步骤中，使用 Ubuntu 映像创建了虚拟机。 在此步骤中，Azure CLI 用于在市场中搜索 CentOS 映像，此映像稍后用于部署第二个虚拟机。 

若要查看最常用的映像列表，请使用 [az vm image list](/cli/azure/vm/image) 命令。

```azurecli-interactive 
az vm image list --output table
```

命令输出返回 Azure 上最常用的 VM 映像。

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

可以通过添加 `--all` 参数查看完整列表。 还可以按 `--publisher` 或 `–-offer` 筛选映像列表。 在此示例中，已在列表中筛选出其产品与“CentOS”  匹配的所有映像。 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

部分输出：

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

若要使用特定的映像部署 VM，请记下“Urn”列中的值，包括发布者、产品/服务、SKU，以及用于[标识](cli-ps-findimage.md#terminology)映像的版本号（可选）。 指定映像时，可将映像版本号替换为“latest”，这会选择最新的发行版。 在此示例中，`--image` 参数用于指定最新版本的 CentOS 6.5 映像。  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>了解 VM 大小

虚拟机大小决定虚拟机可用计算资源（如 CPU、GPU 和内存）的数量。 需要根据预期的工作负载适当调整虚拟机的大小。 如果工作负荷增加，则可调整现有虚拟机的大小。

### <a name="vm-sizes"></a>VM 大小

下表将大小分类成了多个用例。  

| 类型                     | 常见大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| CPU 与内存之比均衡。 适用于开发/测试、小到中型应用程序和数据解决方案。  |
| [计算优化](sizes-compute.md)   | Fsv2          | 高 CPU 与内存之比。 适用于中等流量的应用程序、网络设备和批处理。        |
| [内存优化](sizes-memory.md)    | Esv3、Ev3、M、DSv2、Dv2  | 较高的内存核心比。 适用于关系数据库、中到大型缓存和内存分析。                 |
| [存储优化](sizes-storage.md)      | Lsv2、Ls              | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](sizes-gpu.md)          | NV、NVv2、NC、NCv2、NCv3、ND            | 专门针对大量图形绘制和视频编辑的 VM。       |
| [高性能](sizes-hpc.md) | H        | 功能极其强大的 CPU VM 具有可选的高吞吐量网络接口 (RDMA)。 |


### <a name="find-available-vm-sizes"></a>查找可用的 VM 大小

若要查看在特定区域可用的 VM 大小的列表，请使用 [az vm list-sizes](/cli/azure/vm) 命令。 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

部分输出：

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>创建具有特定大小的 VM

在前面的 VM 创建示例中未提供大小，因此会使用默认大小。 可以在创建时使用 [az vm create](/cli/azure/vm) 和 `--size` 参数选择 VM 大小。 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>调整 VM 的大小

部署 VM 后，可调整其大小以增加或减少资源分配。 可通过 [az vm show](/cli/azure/vm) 查看 VM 的当前大小：

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

调整 VM 大小之前，请检查所需的大小在当前 Azure 群集上是否可用。 [az vm list-vm-resize-options](/cli/azure/vm) 命令返回大小列表。 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

如果所需大小可用，则可从开机状态调整 VM 大小，但需在此操作期间重启 VM。 使用 [az vm resize]( /cli/azure/vm) 命令执行大小调整。

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

如果所需大小在当前群集上不可用，则需解除分配 VM，才能执行调整大小操作。 使用 [az vm deallocate]( /cli/azure/vm) 命令停止和解除分配 VM。 请注意，重新打开 VM 的电源时，可能会删除临时磁盘上的所有数据。 除非使用静态 IP 地址，否则公共 IP 地址也会更改。 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

解除分配 VM 后，可能会发生大小调整。 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

调整大小后，可以启动 VM。

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>VM 电源状态

Azure VM 可能会处于多种电源状态之一。 从虚拟机监控程序的角度来看，此状态表示 VM 的当前状态。 

### <a name="power-states"></a>电源状态

| 电源状态 | 说明
|----|----|
| 正在启动 | 指示正在启动虚拟机。 |
| 运行 | 指示虚拟机正在运行。 |
| 正在停止 | 指示正在停止虚拟机。 | 
| 已停止 | 指示虚拟机已停止。 虚拟机处于停止状态时仍会产生计算费用。  |
| 正在解除分配 | 指示正在解除分配虚拟机。 |
| 已解除分配 | 指示虚拟机已从监控程序中删除，但仍可在控制面板中使用。 处于“已解除分配”状态的虚拟机不会产生计算费用。 |
| - | 指示虚拟机的电源状态未知。 |

### <a name="find-the-power-state"></a>查找电源状态

若要检索特定 VM 的状态，请使用 [az vm get-instance-view](/cli/azure/vm) 命令。 请确保为虚拟机和资源组指定有效的名称。 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

输出：

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>管理任务

在虚拟机生命周期中，可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，可能还需要创建脚本来自动执行重复或复杂的任务。 使用 Azure CLI，可从命令行或脚本运行许多常见的管理任务。 

### <a name="get-ip-address"></a>获取 IP 地址

此命令返回虚拟机的私有 IP 地址和公共 IP 地址。  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>停止虚拟机

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>启动虚拟机

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>删除资源组

删除资源组还会删除其包含的所有资源，例如 VM、虚拟网络和磁盘。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>后续步骤

在本教程中，已学习 VM 创建和管理的基本知识，例如如何：

> [!div class="checklist"]
> * 创建并连接到 VM
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 调整 VM 的大小
> * 查看并了解 VM 状态

请转到下一教程，了解 VM 磁盘。  

> [!div class="nextstepaction"]
> [创建和管理 VM 磁盘](./tutorial-manage-disks.md)
