---
title: "创建具有加速网络的 Azure 虚拟机 | Microsoft Docs"
description: "了解如何创建具有加速网络的 Linux 虚拟机。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: e4c875d07905b56c0d3eb346c839f7a4917531de
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>创建具有加速网络的 Linux 虚拟机

> [!IMPORTANT] 
> 必须创建启用加速网络的虚拟机。 现有虚拟机不能启用此功能。 可按照以下步骤启用加速网络：
>   1. 删除虚拟机。
>   2. 重新创建启用加速网络的虚拟机。
>

本教程介绍如何创建具有加速网络的 Linux 虚拟机 (VM)。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 下图显示了具有和没有加速网络的两个 VM 之间的通信：

![比较](./media/create-vm-accelerated-networking/accelerated-networking.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要详细了解虚拟交换机，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达 VM 的网络接口 (NIC)，然后转发到 VM。 由虚拟交换机应用的所有网络策略现在都会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 Azure 虚拟网络 (VNet) 的最少两个 VM 上启用此功能。 跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：**从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：**虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：**绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="supported-operating-systems"></a>支持的操作系统
* **Ubuntu 16.04**：4.11.0-1013 或更高内核版本
* **SLES SP3**：4.4.92-6.18 或更高内核版本
* **RHEL 7.4**：7.4.2017120423 或更高内核版本
* **CentOS 7.4**：7.4.20171206 或更高内核版本

## <a name="supported-vm-instances"></a>支持的 VM 实例
大多数常规用途实例以及具有 4 个或更多 vCPU 的计算优化实例都支持加速网络。 在支持超线程的实例上（如 D/DSv3 或 E/ESv3），具有 8 个或更多 vCPU 的 VM 实例支持加速网络。  支持的系列包含 D/DSv2、D/DSv3、E/ESv3、F/Fs/Fsv2 和 Ms/Mms。 

有关 VM 实例的详细信息，请参阅[Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="regions"></a>区域
除东亚外的所有公共 Azure 区域可用。   尚不支持 Azure 政府云。

## <a name="limitations"></a>限制
此功能存在以下限制：

* **网络接口创建：**只能为新 NIC 启用加速网络。 不能为现有 NIC 启用。
* **VM 创建：**已启用加速网络的 NIC 只能在创建 VM 时附加到该 VM。 该 NIC 无法附加到现有 VM。 如果将 VM 添加到现有可用性集，可用性集中的所有 VM 也必须启用加速网络。
* **仅通过 Azure 资源管理器部署：**不能部署具有加速网络的虚拟机（经典）。

## <a name="create-a-virtual-network"></a>创建虚拟网络

安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/#az_login) 登录到 Azure 帐户。 在以下示例中，请将示例参数名称替换为自己的值。 参数名称示例包括 myResourceGroup、myNic 和 myVm。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 centralus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location centralus
```

必须选择 [Linux 加速网络](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)中列出的受支持 Linux 区域。

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例创建名为 myVnet 且具有一个子网的虚拟网络：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>创建网络安全组
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

## <a name="create-a-network-interface-with-accelerated-networking"></a>创建具有加速网络的网络接口

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

## <a name="create-a-vm-and-attach-the-nic"></a>创建 VM 并附加 NIC
创建 VM 时，指定使用 `--nics` 创建的 NIC。 必须选择 [Linux 加速网络](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)中列出的大小和分发。 

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

## <a name="confirm-that-accelerated-networking-is-enabled"></a>确认已启用加速网络

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
