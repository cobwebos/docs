---
title: "在 Azure 中创建 Linux Service Fabric 群集 | Microsoft Docs"
description: "了解如何使用 Azure CLI 将 Linux Service Fabric 群集部署到现有 Azure 虚拟网络。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 84b219d31635af6fbdb6bd618e3a9bb4e4848809
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>将 Service Fabric Linux 群集部署到 Azure 虚拟网络
本教程是一个系列中的第一部分。 可以了解到如何使用 Azure CLI 将 Linux Service Fabric 群集部署到现有 Azure 虚拟网络 (VNET) 及子网。 完成本教程后，云中会运行一个可在其中部署应用程序的群集。 若要使用 PowerShell 创建 Windows 群集，请参阅[在 Azure 上创建安全的 Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Azure CLI 在 Azure 中创建 VNET
> * 使用 Azure CLI 在 Azure 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 Service Fabric CLI 连接到群集
> * 删除群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 在 Azure 上创建安全群集
> * [缩小或扩大群集](/service-fabric-tutorial-scale-cluster.md)
> * [部署 API 管理与 Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安装 [Service Fabric CLI](service-fabric-cli.md)
- 安装 [Azure CLI 2.0](/cli/azure/install-azure-cli)

以下步骤将创建一个五节点 Service Fabric 群集。 若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>登录到 Azure，然后选择订阅
本指南使用 Azure CLI。 开始新的会话时，请登录到 Azure 帐户并选择订阅，然后执行 Azure 命令。
 
运行以下脚本以登录到 Azure 帐户并选择订阅：

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>创建资源组
针对部署创建新的资源组，并为其提供名称和位置。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>部署网络拓扑
接下来，设置将向其部署 API 管理和 Service Fabric 群集的网络拓扑。 [network.json][network-arm] 资源管理器模板会配置为针对 Service Fabric 创建虚拟网络 (VNET) 以及一个子网和网络安全组 (NSG)，并配置为针对 API 管理创建一个子网和 NSG。 在[此处](../virtual-network/virtual-networks-overview.md)了解有关 VNET、子网和 NSG 的详细信息。

[network.parameters.json][network-parameters-arm] 参数文件包含子网名称以及向其部署 Service Fabric 和 API 管理的 NSG。  API 管理将在[以下教程](service-fabric-tutorial-deploy-api-management.md)中进行部署。 本指南中，不需要更改参数值。 Service Fabric 资源管理器模板使用这些值。  如果在此处修改这些值，则必须在本教程和[部署 API 管理教程](service-fabric-tutorial-deploy-api-management.md)中所使用的其他资源管理器模板中对其进行修改。 

下载以下 Resource Manager 模板和参数文件：
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

使用以下脚本为网络设置部署资源管理器模板和参数文件：

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>部署 Service Fabric 群集
网络资源部署完成后，下一步是将 Service Fabric 群集部署到子网中的 VNET 以及为 Service Fabric 群集指定的 NSG。 将群集部署到现有 VNET 和子网（在本文前面已部署）需要资源管理器模板。  有关详细信息，请参阅[使用 Azure 资源管理器创建群集](service-fabric-cluster-creation-via-arm.md)。 在本教程系列中，模板预配置为使用上一步中设置的 VNET、子网和 NSG 的名称。  

下载以下 Resource Manager 模板和参数文件：
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

使用此模板创建安全群集。  群集证书是一种 X.509 证书，用于保护节点到节点的通信，并对指向管理客户端的群集管理终结点进行验证。  群集证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 SSL。 Azure 密钥保管库用于管理 Azure 中 Service Fabric 群集的证书。  在 Azure 中部署群集时，负责创建 Service Fabric 群集的 Azure 资源提供程序将从密钥保管库提取证书，并将其安装在群集 VM 上。 

可将来自证书颁发机构 (CA) 的证书用作群集证书，或创建自签名证书用于测试。 群集证书必须具备以下条件：

- 包含私钥。
- 专为密钥交换而创建，且证书可导出到个人信息交换 (.pfx) 文件。
- 证书的使用者名称必须与用于访问 Service Fabric 群集的域匹配。 只有满足此匹配，才能为群集的 HTTPS 管理终结点和 Service Fabric Explorer 提供 SSL。 无法从证书颁发机构 (CA) 处获取针对 cloudapp.azure.com 域的 SSL 证书。 必须获取群集的自定义域名。 从 CA 请求证书时，该证书的使用者名称必须与用于群集的自定义域名匹配。

填写 linuxcluster.parameters.json 文件中用于部署的空参数：

|参数|值|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

若要创建自签名证书，请将 certificateThumbprint、certificateUrlValue 和 sourceVaultValue 参数留空。  如果要使用之前上传到密钥保管库的现有证书，请填写这些参数值。

以下脚本使用 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 命令和模板在 Azure 中部署新群集。 该 cmdlet 还会在 Azure 中创建新的密钥保管库、向密钥保管库添加新的自签名证书，并将证书文件下载到本地。 可使用 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 命令的其他参数指定现有的证书和/或密钥保管库。

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>连接到安全群集
使用密钥通过 Service Fabric CLI `sfctl cluster select` 命令连接到群集。  请注意仅针对自签名证书使用 **--no-verify** 选项。

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

检查是否已连接并使用 `sfctl cluster health` 命令检查群集是否处于正常状态。

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>清理资源
本教程系列中的其他文章将使用刚才创建的群集。 如果没有立即转到下一篇文章，可能需要删除该群集，避免产生费用。 若要删除群集及其占用的所有资源，最简单的方式是删除资源组。

登录到 Azure，选择要删除的群集的订阅 ID。  可通过登录到 [Azure 门户](http://portal.azure.com)查找订阅 ID。 使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) 命令删除资源组和所有群集资源。

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Azure CLI 在 Azure 中创建 VNET
> * 使用 Azure CLI 在 Azure 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 Service Fabric CLI 连接到群集
> * 删除群集

接下来，请转到以下教程了解如何缩放群集。
> [!div class="nextstepaction"]
> [缩放群集](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
