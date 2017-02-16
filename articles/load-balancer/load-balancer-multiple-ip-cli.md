---
title: "使用 Azure CLI 在多个 IP 配置上进行负载平衡 | Microsoft 文档"
description: "了解如何使用 Azure CLI | Resource Manager 为虚拟机分配多个 IP 地址。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 5c4e6fd9b560d3005294a02a5ec52c140690c819
ms.openlocfilehash: 79bb6d74c4eba99386b44e8464214d84abf882c6


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>在多个 IP 配置上进行负载平衡

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
>

本文介绍如何将 Azure Load Balancer 用于每个虚拟网络接口 (NIC) 的多个 IP 地址。 目前，对一个 NIC 的多个 IP 地址的支持是预览版的功能。 有关详细信息，请参阅本文中的[限制](#limitations)部分。 以下场景说明了如何通过 Azure Load Balancer 使用此功能。

在本场景中有两个运行 Windows 的 VM，每个 VM 有一个 NIC。 每个 NIC 具有多个 IP 配置。 每个 VM 都托管了 contoso.com 和 fabrikam.com 这两个网站。 每个网站都绑定到 NIC 的一个 IP 配置。 我们使用负载平衡器来公开两个前端 IP 地址，每个地址分别对应于一个网站，从而将流量分发到各个网站的 IP 配置。 此场景中两个前端以及两个后端池 IP 地址都使用相同的端口号。

![负载平衡应用场景图像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>限制

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。


## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>在多个 IP 配置上进行负载平衡的步骤

按照以下步骤来实现本文所概述的场景：

1. 按照所链接的文章中的步骤[安装和配置 Azure CLI](../xplat-cli-install.md)，然后登录到你的 Azure 帐户。
2. 如下所述[创建一个资源组](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations)并将其命名为 *contosofabrikam*。
 
    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. 为两个 VM [创建可用性集](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set)。 对于此场景，请使用以下命令：

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [创建一个虚拟网络](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)并将其命名为 *myVNet*，并创建一个名为 *mySubnet* 的子网： 

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [创建负载平衡器](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools)并将其命名为 *mylb*：

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. 为负载平衡器的前端 IP 配置创建两个动态公共 IP 地址：

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. 创建两个分别名为 *contosofe* 和 *fabrikamfe* 的前端 IP 配置：

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. 创建后端地址池 - *contosopool* 和 *fabrikampool*、[探测器](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe) - *HTTP* 以及负载平衡规则 - *HTTPc* 和 *HTTPf*：

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. 运行以下命令，然后检查输入以[验证是否正确创建了负载平衡器](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer)：

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. 为第一个虚拟机 VM1 [创建公共 IP](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address-pip) *myPublicIp* 和[存储帐户](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) *mystorageaccont1*，如下所示：

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. 为 VM1 [创建网络接口](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm) *VM1-NIC*，添加另一个 IP 配置 *VM1-ipconfig2*，然后[创建 VM](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms)，如下所示：

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-name myNic --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. 为第二个 VM 重复步骤 10-11：

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount3426
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-name myNic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. 最后，必须将 DNS 资源记录配置为指向各自的负载平衡器的前端 IP 地址。 可以在 Azure DNS 中托管域。 有关将 Azure DNS 与负载平衡器配合使用的详细信息，请参阅[将 Azure DNS 与其他 Azure 服务配合使用](../dns/dns-for-azure-services.md)。


<!--HONumber=Nov16_HO5-->


