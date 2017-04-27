---
title: "将 Azure VM 转换为一个规模集 | Microsoft Docs"
description: "使用 Azure CLI 创建和部署 Linux Azure 虚拟机规模集。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>将现有的 Azure 虚拟机转换为一个规模集

本教程演示如何使用 Azure CLI 2.0 将虚拟机转换为虚拟机规模集。 还介绍如何在规模集中自动进行虚拟机配置。 有关如何安装 Azure CLI 2.0 的详细信息，请参阅 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli.md)。 有关规模集的详细信息，请参阅[虚拟机规模集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

## <a name="step-1---deprovision-the-vm"></a>步骤 1 - 取消设置 VM

使用 SSH 连接到 VM。

使用 Azure VM 代理取消设置 VM 以删除文件和数据。 有关取消设置的详细概述，请参阅[捕获 Linux 虚拟机](capture-image.md)。

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>步骤 2 - 捕获 VM 的映像

有关捕获的详细概述，请参阅[捕获 Linux 虚拟机](capture-image.md)。

使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM：

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

使用 [az vm generalize](/cli/azure/vm#generalize) 通用化 VM：

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

使用 [az image create](/cli/azure/image#create) 从 VM 资源创建映像：

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>步骤 3 - 创建规模集

获取映像的 **ID**。

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

使用 [az vmss create](/cli/azure/vmss#create) 从映像资源创建 VM：

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

此命令还附加一个 10GB 的数据磁盘。 请记住，所选的 VM 大小不同（我们使用 **Standard_DS1_v2**），允许的数据磁盘数量不同。 有关详细信息，请参阅[虚拟机大小](sizes.md)。

规模集完成创建后，连接它。 使用 [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) 获取 SSH 实例的 IP 地址列表：

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

现在，你可以连接到虚拟机实例以初始化数据磁盘

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>步骤 4 - 初始化数据磁盘

连接到虚拟机时，使用 `fdisk` 对磁盘进行分区：

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

使用 `mkfs` 命令将文件系统写入分区：

```bash
sudo mkfs -t ext4 /dev/sdc1
```

装载新磁盘以使其在操作系统中可访问：

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

现在可以通过 datadrive 装载点访问磁盘，可使用 `ls /datadrive/` 对此进行验证。

结束 SSH 会话。


## <a name="step-5---configure-firewall"></a>步骤 5 - 配置防火墙

在防火墙上打一个洞以连接规模集托管的 Web 服务器。 创建规模集时，也会创建负载均衡器，用于 **SSH** 到单个虚拟机。 要打开端口，需要两条信息（可以使用 Azure CLI 获取）。

* **前端 IP 地址池**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **后端 IP 地址池**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

使用这两个名称，可以打开端口 **80**。

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>步骤 6 - 自动配置

数据磁盘需要在每个虚拟机实例上进行配置。 可以使用 **CustomScript** 扩展自动配置虚拟机。

首先，创建包括磁盘格式命令的 .sh 脚本。

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

接下来，将该脚本文件上传到 **CustomScript** 扩展可访问的位置。 [此处](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4)提供了一个副本。

创建一个名为 **settings.json** 的本地文件，并将下面的 JSON 块放入该文件。 `flieUris` 属性应设置为上传脚本文件的位置。

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

引用刚刚创建的 **settings.json** 文件，使用 **CustomScript** 扩展将此命令部署到规模集。

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

此扩展在所有当前实例和后续通过缩放创建的任何实例上自动运行。

## <a name="step-7---configure-autoscale-rules"></a>步骤 7 - 配置自动缩放规则

目前，不能在 Azure CLI 中设置自动缩放规则。 使用 [Azure 门户](https://portal.azure.com)配置自动缩放。

## <a name="step-8---management-tasks"></a>步骤 8 - 管理任务

在规模集的整个生命周期内，可能需要运行一个或多个管理任务。 此外，你可能想要创建可自动执行不同的生命周期任务的脚本，Azure CLI 可提供执行这些任务的快速方法。 以下是一些常见任务。

### <a name="get-connection-info"></a>获取连接信息

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>设置实例计数（手动缩放）

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>删除资源组

删除资源组会删除其包含的所有资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤
若要详细了解本教程中介绍的一些虚拟机规模集功能，请参阅以下信息：

- [Azure 虚拟机规模集概述](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure 负载均衡器概述](../../load-balancer/load-balancer-overview.md)
- [使用网络安全组控制网络流量](../../virtual-network/virtual-networks-nsg.md)
