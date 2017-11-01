---
title: "创建具有多个 NIC 的 VM（经典）- Azure CLI 1.0 | Microsoft 文档"
description: "了解如何使用 Azure 命令行接口 (CLI) 1.0 创建具有多个 NIC 的 VM（经典）。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8555bd830583f51164d39ca0e7b95813b7d35965
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 创建具有多个 NIC 的 VM（经典）

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

可以在 Azure 中创建虚拟机 (VM)，然后将多个网络接口 (NIC) 附加到每个 VM。 通过多个 NIC 可分离跨 NIC 的流量类型。 例如，一个 NIC 可与 Internet 通信，而另一个 NIC 仅与未连接到 Internet 的内部资源通信。 许多网络虚拟设备（例如应用程序交付和 WAN 优化解决方案）都需要具备跨多个 NIC 分离网络流量的能力。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 了解如何使用 [Resource Manager 部署模型](../virtual-machines/linux/multiple-nics.md)执行这些步骤。

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

以下步骤使用名为 *IaaSStory* 的资源组作为主资源组，并在名为 *IaaSStory-BackEnd* 的资源组中实现后端服务器。

## <a name="prerequisites"></a>先决条件
创建数据库服务器之前，需要先使用此方案的所有必需资源创建 *IaaSStory* 资源组。 若要创建这些资源，请完成如下步骤。 若要创建虚拟网络，请完成[创建虚拟网络](virtual-networks-create-vnet-classic-cli.md)一文中的步骤。

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>部署后端 VM
后端 VM 取决于以下资源的创建：

* **数据磁盘的存储帐户**。 为了提高性能，数据库服务器上的数据磁盘将使用固态驱动器 (SSD) 技术，这需要高级存储帐户。 请确保部署到的 Azure 位置支持高级存储。
* **NIC**。 每个 VM 都将具有两个 NIC，一个用于数据库访问，另一个用于管理。
* **可用性集**。 所有数据库服务器都将添加到单个可用性集，以确保在维护期间至少有一个 VM 已启动且正在运行。

### <a name="step-1---start-your-script"></a>步骤 1 - 启动脚本
可以在[此处](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)下载所用的完整 bash 脚本。 完成以下步骤，以更改要在环境中使用的脚本：

1. 基于在上面[先决条件](#Prerequisites)中部署的现有资源组更改以下变量的值。

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. 基于要用于后端部署的值更改以下变量的值。

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>步骤 2 - 为 VM 创建必要资源
1. 为所有后端 VM 创建新的云服务。 请注意使用 `$backendCSName` 变量表示资源组名称，使用 `$location` 表示 Azure 区域。

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. 为要由 VM 使用的 OS 和数据磁盘创建高级存储帐户。

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>步骤 3 - 创建具有多个 NIC 的 VM
1. 基于 `numberOfVMs` 变量启动一个循环来创建多个 VM。

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. 对于每个 VM，指定两个 NIC 中的每一个的名称和 IP 地址。

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. 创建 VM。 请注意 `--nic-config` 参数的用法，其中包含所有 NIC 的列表（包含名称、子网和 IP 地址）。

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. 为每个 VM 创建两个数据磁盘。

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>步骤 4 - 运行脚本
现在，已根据需要下载并更改了脚本，请运行该脚本以创建具有多个 NIC 的后端数据库 VM。

1. 保存该脚本并从 **Bash** 终端运行它。 将看到最初的输出，如下所示。

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. 几分钟后，执行将结束，会看到输出的其余部分，如下所示。

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>步骤 5 - 在 VM 的操作系统中配置路由

Azure DHCP 会将默认网关分配给附加到虚拟机的第一个（主）网络接口。 Azure 不会将默认网关分配给附加到虚拟机的其他（辅助）网络接口。 因此，默认情况下你无法与辅助网络接口所在的子网外部的资源进行通信。 但是，辅助网络接口可以与其子网之外的资源进行通信。 若要为辅助网络接口配置路由，请参阅[在具有多个网络接口的虚拟机操作系统中进行路由选择](virtual-network-network-interface-vm.md#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces)。
