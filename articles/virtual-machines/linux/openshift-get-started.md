---
title: "将 OpenShift 源部署到 Azure |Microsoft 文档"
description: "了解如何将 OpenShift 源部署到 Azure 虚拟机。"
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
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>将 OpenShift 源部署到 Azure 虚拟机 

[OpenShift 原点](https://www.openshift.org/)是基于开放源容器平台[Kubernetes](https://kubernetes.io/)。 简化的部署、 缩放和操作多租户应用程序的过程。 

本指南介绍如何部署 OpenShift 源 Azure 虚拟机上使用 Azure CLI 和 Azure 资源管理器模板。 在本教程中，你将学习如何：

> [!div class="checklist"]
> * 创建 KeyVault OpenShift 群集的 SSH 密钥进行管理。
> * 部署 Azure Vm 上的 OpenShift 群集。 
> * 安装和配置[OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index)将群集进行管理。
> * 自定义 OpenShift 部署。

如果你没有 Azure 订阅，创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)在开始之前。

本快速入门需要 Azure CLI 版本 2.0.8 或更高版本。 若要查找版本，运行`az --version`。 如果你需要安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>登录到 Azure 
登录到你的 Azure 订阅与[az 登录](/cli/azure/#login)命令并按照屏幕上指导性内容或单击**动手**使用云 Shell。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>创建资源组

创建资源组与[az 组创建](/cli/azure/group#create)命令。 Azure 资源组是到哪个 Azure 部署和管理资源的逻辑容器。 

下面的示例创建一个名为的资源组*myResourceGroup*中*eastus*位置。

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>创建密钥保管库
创建用于存储与群集的 SSH 密钥 KeyVault [az keyvault 创建](/cli/azure/keyvault#create)命令。  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>创建 SSH 密钥 
SSH 密钥需要安全地访问 OpenShift 源群集。 创建 SSH 密钥对使用`ssh-keygen`命令。 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 你创建的 SSH 密钥对不能有一个通行短语。

有关详细信息在 Windows 上，SSH 密钥[如何创建 SSH 密钥在 Windows 上](/azure/virtual-machines/linux/ssh-from-windows)。

## <a name="store-ssh-private-key-in-key-vault"></a>密钥保管库中存储的 SSH 私钥
OpenShift 部署使用帮助确保安全访问 OpenShift 母版创建的 SSH 密钥。 若要启用该部署才能安全地检索 SSH 密钥，请使用以下命令的密钥保管库中存储密钥。

# <a name="enabled-for-template-deployment"></a>已为模板部署启用
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>创建服务主体 
OpenShift 与 Azure 中使用用户名和密码或服务主体进行通信。 Azure 服务主体是可用于应用程序、 服务和等 OpenShift 自动化工具的安全标识。 你控制和定义有关服务主体可以在 Azure 中执行哪些操作的权限。 为了提高安全性通过仅提供用户名和密码，此示例创建一个基本的服务主体。

创建服务主体与[az ad sp 创建-对于-rbac](/cli/azure/ad/sp#create-for-rbac)和输出 OpenShift 需要的凭据：

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
 > 不创建不安全的密码。  请按照[Azure AD 密码规则和限制，](/azure/active-directory/active-directory-passwords-policy)指南。

对服务主体的详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>部署 OpenShift 源模板
接下来部署 OpenShift 源使用 Azure 资源管理器模板。 

> [!NOTE] 
> 以下命令需要 az CLI 2.0.8 或更高版本。 你可以验证 az CLI 版本与`az --version`命令。 若要更新的 CLI 版本，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

使用`appId`从以前为创建的服务主体的值`aadClientId`参数。

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
部署可能需要 20 分钟才能完成。 部署完成后，将到终端打印 OpenShift 控制台 OpenShift 主机的 DNS 名称的 URL。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集
部署完成后，连接到使用浏览器使用 OpenShift 控制台`OpenShift Console Uri`。 或者，你可以连接到 OpenShift 主要使用以下命令。

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源
当不再需要可以使用[az 组删除](/cli/azure/group#delete)命令删除资源组、 OpenShift 群集，以及所有相关资源。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在此教程，学习到如何：
> [!div class="checklist"]
> * 创建 KeyVault OpenShift 群集的 SSH 密钥进行管理。
> * 部署 Azure Vm 上的 OpenShift 群集。 
> * 安装和配置[OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index)将群集进行管理。

现在部署该 OpenShift 源群集。 你可以按照 OpenShift 教程，若要了解如何部署第一个应用程序并使用 OpenShift 工具。 请参阅[Getting Started with OpenShift 原点](https://docs.openshift.org/latest/getting_started/index.html)吧。 
