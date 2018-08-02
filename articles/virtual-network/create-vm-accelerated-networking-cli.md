---
title: 创建具有加速网络的 Azure 虚拟机 | Microsoft Docs
description: 了解如何创建具有加速网络的 Linux 虚拟机。
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 9ea843df4cf437b97f7fe1d62636a51f8201376e
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414566"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>创建具有加速网络的 Linux 虚拟机

本教程介绍如何创建具有加速网络的 Linux 虚拟机 (VM)。 若要创建具有加速网络的 Windows VM，请参阅[创建具有加速网络的 Windows VM](create-vm-accelerated-networking-powershell.md)。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 下图显示了具有和没有加速网络的两个 VM 之间的通信：

![比较](./media/create-vm-accelerated-networking/accelerated-networking.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要详细了解虚拟交换机，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达 VM 的网络接口 (NIC)，然后转发到 VM。 由虚拟交换机应用的所有网络策略现在都会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 Azure 虚拟网络 (VNet) 的最少两个 VM 上启用此功能。 跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：** 从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：** 虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：** 绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="supported-operating-systems"></a>支持的操作系统
从 Azure 库即可支持以下分发： 
* **Ubuntu 16.04** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian“Stretch”（backport 内核）**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>限制和约束

### <a name="supported-vm-instances"></a>支持的 VM 实例
大多数常规用途实例以及具有 2 个或更多 vCPU 的计算优化实例都支持加速网络。  这些受支持的系列包括 D/DSv2 和 F/Fs

在支持超线程的实例上，具有 4 个或更多 vCPU 的 VM 实例支持加速网络。 受到支持的系列包括 D/DSv3、E/ESv3、Fsv2 和 Ms/Mms。

有关 VM 实例的详细信息，请参阅[Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="regions"></a>区域
在所有公共 Azure 区域和 Azure 政府云中均可用。

### <a name="network-interface-creation"></a>网络接口创建 
只能为新 NIC 启用加速网络。 不能为现有 NIC 启用。
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>在正在运行的 VM 上启用加速网络
未启用加速网络的受支持 VM 大小只有在停止和解除分配时才能启用该功能。  
### <a name="deployment-through-azure-resource-manager"></a>通过 Azure 资源管理器部署
虚拟机（经典）无法部署加速网络。

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>创建具有 Azure 加速网络的 Linux VM

尽管本文提供了使用 Azure CLI 创建具有加速网络的虚拟机的步骤，但也可以[使用 Azure 门户创建具有加速网络的虚拟机](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 在门户中创建虚拟机时，请在“设置”的“加速网络”下选择“启用”。 除非选择了[支持的操作系统](#supported-operating-systems)和 [VM 大小](#supported-vm-instances)，否则启用加速网络的选项不会显示在门户中。 创建虚拟机后，需要按照[确认已启用加速网络](#confirm-that-accelerated-networking-is-enabled)中的说明完成操作。

### <a name="create-a-virtual-network"></a>创建虚拟网络

安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。 在以下示例中，请将示例参数名称替换为自己的值。 参数名称示例包括 myResourceGroup、myNic 和 myVm。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 centralus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location centralus
```

选择 [Linux 加速网络](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)中列出的受支持 Linux 区域。

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例创建名为 myVnet 且具有一个子网的虚拟网络：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>创建网络安全组
使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建网络安全组。 以下示例创建名为“myNetworkSecurityGroup”的网络安全组：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

网络安全组包含多个默认规则，其中一个规则禁用了来自 Internet 的所有入站访问。 打开端口，以允许使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 对虚拟机进行 SSH 访问：

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>创建具有加速网络的网络接口

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 创建公共 IP 地址。 如果不打算从 Internet 访问虚拟机，则不需要公共 IP 地址，但必须完成本文中的步骤。

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建启用加速网络的网络接口。 以下示例在 myVnet 虚拟网络的 mySubnet 子网中创建名为 myNic 的网络接口，并将 myNetworkSecurityGroup 网络安全组关联到该网络接口：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>创建 VM 并附加 NIC
创建 VM 时，指定使用 `--nics` 创建的 NIC。 选择 [Linux 加速网络](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)中列出的大小和分发版本。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例创建名为 myVM 的 VM，其具有 UbuntuLTS 映像，并且大小支持加速网络 (*Standard_DS4_v2*) ：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

若要获取所有 VM 大小和特性列表，请参阅 [Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

创建 VM 后，将返回以下类似输出。 记下 publicIpAddress。 在后续步骤中，将使用此地址访问 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>确认已启用加速网络

使用以下命令来与 VM 建立 SSH 会话。 将 `<your-public-ip-address>` 替换为分配给所创建虚拟机的公共 IP 地址，并替换 azureuser（如果在创建 VM 时使用了 `--admin-username` 以外的值）。

```bash
ssh azureuser@<your-public-ip-address>
```

从 Bash shell 中，输入 `uname -r` 并确认内核版本为以下版本之一或更高版本：

* **Ubuntu 16.04**：4.11.0-1013
* **SLES SP3**：4.4.92-6.18
* **RHEL**：7.4.2017120423
* **CentOS**：7.4.20171206


使用 `lspci` 命令确认向 VM 公开了 Mellanox VF 设备。 返回的输出与以下输出类似：

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

使用 `ethtool -S eth0 | grep vf_` 命令检查 VF（虚拟函数）的活动。 如果获得的输出类似以下示例，则表示加速网络已启用并正常工作。

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
现在已为 VM 启用加速网络。

## <a name="enable-accelerated-networking-on-existing-vms"></a>在现有 VM 上启用加速网络
如果创建的 VM 没有加速网络，则可在现有 VM 上启用此功能。  VM 必须支持加速网络，前提是满足以下先决条件（上文亦有列出）：

* VM 必须是加速网络支持的大小
* VM 必须是受支持的 Azure 库映像（以及适用于 Linux 的内核版本）
* 在任何 NIC 上启用加速网络前，必须停止/解除分配可用性集或 VMSS 中的所有 VM

### <a name="individual-vms--vms-in-an-availability-set"></a>可用性集中的单个 VM 和多个 VM
首先停止/解除分配 VM，或集合中的所有 VM（如果是可用性集）：

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

请务必注意，如果 VM 是单独创建的并且没有可用性集，则只需停止/解除分配单个 VM 即可启用加速网络。  如果 VM 是随可用性集创建的，则在任何 NIC 上启用加速网络前，必须停止/解除分配可用性集中包含的所有 VM。 

一旦停止，即可在 VM 的 NIC 上启用加速网络：

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

重启 VM，或集合中的所有 VM（如果在可用性集中），并确认已启用加速网络： 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS 略有不同，但遵循相同的工作流。  首先，停止 VM：

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

VM 停止后，更新网络接口下的加速网络属性：

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

请注意，VMSS 的 VM 升级功能可使用三种不同的设置（自动、滚动和手动）应用更新。  在这些说明中，策略设置为自动，以便 VMSS 在可重启后立即收到更改。  若要将其设置为自动以便立即收到更改，请执行以下操作： 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

最后，重启 VMSS：

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

请在重启后等待升级完成，但一旦完成，VF 将在 VM 内部出现。  （请确保使用的是支持的操作系统和 VM 大小。）

### <a name="resizing-existing-vms-with-accelerated-networking"></a>调整具有加速网络的现有 VM 的大小

启用加速网络的 VM 只能调整为支持加速网络的 VM 的大小。  

启用加速网络的 VM 不能使用调整大小操作调整为不支持加速网络的 VM 实例的大小。  相反，若要调整其中一个 VM 的大小，请执行以下操作： 

* 停止/解除分配 VM，或如果在可用性集/VMSS 中，则停止/解除分配集合/VMSS 中的所有 VM。
* 必须在 VM 的 NIC 上禁用加速网络，或者如果在可用性集/VMSS 中，则必须在集合/​​VMSS 中的所有 VM 上禁用。
* 一旦加速网络被禁用，VM/可用性集/VMSS 即可移至不支持加速网络的新大小并重启。  

