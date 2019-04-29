---
title: 创建、更改或删除虚拟网络 TAP - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 创建、更改或删除虚拟网络 TAP。
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 3d95a9ea555cceda82530eb5c487eeb993c1a678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743184"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>通过 Azure CLI 使用虚拟网络 TAP

通过 Azure 虚拟网络 TAP（终端接入点），可让你持续将虚拟机网络流量流式传输到网络数据包收集器或分析工具。 收集器或分析工具由[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)合作伙伴提供。 有关经验证可与虚拟网络 TAP 一起使用的合作伙伴解决方案列表，请参阅[合作伙伴解决方案](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)。 

## <a name="create-a-virtual-network-tap-resource"></a>创建虚拟网络 TAP 资源

在创建虚拟网络 TAP 资源之前，请先阅读[先决条件](virtual-network-tap-overview.md#prerequisites)。 可以在 [Azure Cloud Shell](https://shell.azure.com/bash) 中运行以下命令，或者在计算机上运行 Azure 命令行接口 (CLI)。 Azure Cloud Shell 是免费的交互式 shell，不需要在计算机上安装 Azure CLI。 必须使用具有相应[权限](virtual-network-tap-overview.md#permissions)的帐户登录 Azure。 本文需要 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 虚拟网络点击是目前以扩展形式提供。 若要安装的扩展需要运行`az extension add -n virtual-network-tap`。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

1. 将订阅 ID 检索到后续步骤中使用的变量中：

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. 设置用于创建虚拟网络 TAP 资源的订阅 ID。

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. 重新注册用于创建虚拟网络 TAP 资源的订阅 ID。 如果在创建 TAP 资源时出现注册错误，请运行以下命令：

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. 如果虚拟网络 TAP 的目标是收集器或分析工具的网络虚拟设备上的网络接口 -

   - 将网络虚拟设备的网络接口的 IP 配置检索到后续步骤中使用的变量中。 ID 是用于聚合 TAP 流量的终结点。 以下示例在名为 *myResourceGroup* 的资源组中检索名为 *myNetworkInterface* 的网络接口的 *ipconfig1* IP 配置的 ID：

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - 将 IP 配置的 ID 用作目标和可选的端口属性，在 westcentralus Azure 区域中创建虚拟网络 TAP。 该端口指定了网络接口 IP 配置上用于接收 TAP 流量的目标端口：  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. 如果虚拟网络 TAP 的目标是 Azure 内部负载均衡器：
  
   - 将 Azure 内部负载均衡器的前端 IP 配置检索到后续步骤中使用的变量中。 ID 是用于聚合 TAP 流量的终结点。 以下示例在名为 *myResourceGroup* 的资源组中检索名为 *myInternalLoadBalancer* 的负载均衡器的 *frontendipconfig1* 前端 IP 配置的 ID：

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - 将前端 IP 配置的 ID 用作目标和可选的端口属性，创建虚拟网络 TAP。 该端口指定了前端 IP 配置上用于接收 TAP 流量的目标端口：  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. 确认创建虚拟网络 TAP：

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>将 TAP 配置添加到网络接口

1. 检索现有虚拟网络 TAP 资源的 ID。 以下示例在名为 *myResourceGroup* 的资源组中检索名为 *myTap* 的虚拟网络 TAP：

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. 在受监视的虚拟机的网络接口上创建 TAP 配置。 以下示例为名为 *myNetworkInterface* 的网络接口创建 TAP 配置：

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. 确认创建 TAP 配置：

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>删除网络接口上的 TAP 配置

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>列出订阅中的虚拟网络 TAP

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>删除资源组中的虚拟网络 TAP

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
