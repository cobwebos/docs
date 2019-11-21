---
title: Create an Azure Private Link service using Azure CLI
description: Learn how to create an Azure Private Link service using Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3cc171ddabbe8241622d4e599b4b3cd281558976
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229361"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Create a Private Link service using Azure CLI
This article shows you how to create a Private Link service in Azure using Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you decide to install and use Azure CLI locally instead, this quickstart requires you to use the latest Azure CLI version. 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
## <a name="create-a-private-link-service"></a>创建专用链接服务
### <a name="create-a-resource-group"></a>创建资源组

在创建虚拟网络之前，必须创建一个资源组用于托管该虚拟网络。 使用 [az group create](/cli/azure/group) 创建资源组。 This example creates a resource group named *myResourceGroup* in the *westcentralus* location:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>创建虚拟网络
使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络。 This example creates a default virtual network named *myVirtualNetwork* with one subnet named *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>创建子网
Create a subnet for the virtual network with [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). This example creates a subnet named *mySubnet* in the *myVirtualNetwork* virtual network:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Create a Internal Load Balancer 
Create a internal load balancer with [az network lb create](/cli/azure/network/lb#az-network-lb-create). This example creates a internal load balancer named *myILB* in resource group named *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>创建负载均衡器运行状况探测

运行状况探测会检查所有虚拟机实例，以确保它们可以接收网络流量。 探测器检查失败的虚拟机实例将从负载均衡器中删除，直到它恢复联机状态并且探测器检查确定它运行正常。 使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) 创建运行状况探测，以监视虚拟机的运行状况。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义传入流量的前端 IP 配置和用于接收流量的后端 IP 池，以及所需源和目标端口。 使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) 创建负载均衡器规则 *myHTTPRule*，以便侦听前端池 *myFrontEnd* 中的端口 80，并且将经过负载均衡的网络流量发送到也使用端口 80 的后端地址池 *myBackEndPool*。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>创建后端服务器

In this example, we don't cover virtual machine creation. You can follow the steps in [Create an internal load balancer to load balance VMs using Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) to create two virtual machines to be used as backend servers for the load balancer. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Disable Private Link service network policies on subnet 
Private Link service requires an IP from any subnet of your choice  within a virtual network. Currently, we don’t support Network Policies on these IPs.  Hence, we have to disable the network policies on the subnet. Update the subnet to disable Private Link service network policies with [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>创建专用链接服务  
 
Create a Private Link service using Standard Load Balancer frontend IP configuration with [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create). This example creates a Private Link service named *myPLS* using Standard Load Balancer named *myLoadBalancer* in resource group named *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Once created, take note of the Private Link Service ID. You will need that later for requesting connection to this service.  
 
At this stage, your Private Link service is successfully created and is ready to receive the traffic. Note that above example is only to demonstrate creating Private Link service using Azure CLI.  We haven't configured the load balancer backend pools or any application on the backend pools to listen to the traffic. If you want to see end-to-end traffic flows, you are strongly advised to configure your application behind your Standard Load Balancer.  
 
Next, we will demonstrate how to map this service to a private endpoint in different virtual network using Azure CLI. Again, the example is limited to creating the private endpoint and connecting to Private Link service created above using Azure CLI. Additionally, you can create virtual machines in the virtual network to send/receive traffic to the private endpoint.        
 
## <a name="private-endpoints"></a>Private endpoints

### <a name="create-the-virtual-network"></a>创建虚拟网络 
Create a virtual network with [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). This example creates a virtual network named *myPEVNet* in resource group named *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Create the subnet 
Create a subnet in virtual network with [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). This example creates a subnet named *mySubnet* in virtual network named *myPEVnet* in resource group named *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Disable private endpoint network policies on subnet 
Private endpoint can be created in any subnet of your choice within a virtual network. Currently, we don’t support network policies on private endpoints.  Hence, we have to disable the network policies on the subnet. Update the subnet to disable private endpoint network policies with [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Create private endpoint and connect to private link service 
Create a private endpoint for consuming Private Link service created above in your virtual network:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
You can get the *private-connection-resource-id* with `az network private-link-service show` on Private Link service. The ID will look like:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Show Private Link service connections 
 
See connection requests on your Private Link service  using [az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>后续步骤
- Learn more about [Azure Private Link service](private-link-service-overview.md)
 
