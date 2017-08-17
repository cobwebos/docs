---
title: "将 OpenShift Origin 部署到 Azure | Microsoft Docs"
description: "了解如何将 OpenShift Origin 部署到 Azure 虚拟机。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.contentlocale: zh-cn
ms.lasthandoff: 06/21/2017

---

# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>将 OpenShift Origin 部署到 Azure 虚拟机 

[OpenShift Origin](https://www.openshift.org/) 是构建在 [Kubernetes](https://kubernetes.io/) 基础之上的开源容器平台。 它可以简化部署、缩放和操作多租户应用程序的过程。 

本指南介绍如何使用 Azure CLI 和 Azure Resource Manager 模板在 Azure 虚拟机上部署 OpenShift Origin。 本教程将介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建一个 KeyVault 用于管理 OpenShift 群集的 SSH 密钥。
> * 在 Azure VM 上部署 OpenShift 群集。 
> * 安装并配置 [OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) 来管理群集。
> * 自定义 OpenShift 部署。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本快速入门教程需要 Azure CLI 2.0.8 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>登录 Azure 
使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，按屏幕说明操作，或者单击“试用”使用 Cloud Shell。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>创建密钥保管库
使用 [az keyvault create](/cli/azure/keyvault#create) 命令创建一个 KeyVault 用于管理群集的 SSH 密钥。  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>创建 SSH 密钥 
需要使用 SSH 密钥来保护对 OpenShift Origin 群集的访问。 使用 `ssh-keygen` 命令创建 SSH 密钥对。 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 创建的 SSH 密钥对不能包含通行短语。

有关 Windows 上的 SSH 密钥的详细信息，请参阅[如何在 Windows 上创建 SSH 密钥](/azure/virtual-machines/linux/ssh-from-windows)。

## <a name="store-ssh-private-key-in-key-vault"></a>在 Key Vault 中存储 SSH 私钥
OpenShift 部署使用创建的 SSH 密钥来保护对 OpenShift 主设备的访问。 为使部署能够安全检索 SSH 密钥，请使用以下命令将密钥存储在 Key Vault 中。

# <a name="enabled-for-template-deployment"></a>为模板部署启用
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>创建服务主体 
OpenShift 使用用户名和密码或服务主体来与 Azure 通信。 Azure 服务主体是可用于应用、服务和 OpenShift 等自动化工具的安全标识。 由你控制和定义有关服务主体可以在 Azure 中执行哪些操作的权限。 为了提供比使用用户名和密码更高的安全性，本示例将创建一个基本的服务主体。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 创建服务主体并输出 OpenShift 所需的凭据：

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
记下该命令返回的 appId 属性。
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 请勿创建不安全的密码。  遵循 [Azure AD 密码规则和限制](/azure/active-directory/active-directory-passwords-policy)指导原则。

有关服务主体的详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>部署 OpenShift Origin 模板
接下来，使用 Azure Resource Manager 模板部署 OpenShift Origin。 

> [!NOTE] 
> 以下命令需要 az CLI 2.0.8 或更高版本。 可以使用 `az --version` 命令检查 az CLI 版本。 若要更新 CLI 版本，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

为 `aadClientId` 参数使用前面创建的服务主体中的 `appId` 值。

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
部署最长可能需要 20 分钟才能完成。 部署完成后，终端中会列显 OpenShift 控制台的 URL，以及 OpenShift 主设备的 DNS 名称。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集
部署完成后，可在浏览器中使用 `OpenShift Console Uri` 连接到OpenShift 控制台。 或者，可使用以下命令连接到 OpenShift 主设备。

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源
如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group#delete) 命令将其删除。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：
> [!div class="checklist"]
> * 创建一个 KeyVault 用于管理 OpenShift 群集的 SSH 密钥。
> * 在 Azure VM 上部署 OpenShift 群集。 
> * 安装并配置 [OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) 来管理群集。

部署 OpenShift Origin 群集后， 可以遵照 OpenShift 教程了解如何部署第一个应用程序和使用 OpenShift 工具。 请参阅 [OpenShift Origin 入门](https://docs.openshift.org/latest/getting_started/index.html)开始操作。 

