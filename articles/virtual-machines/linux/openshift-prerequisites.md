---
title: "Azure 中的 OpenShift 的先决条件 | Microsoft Docs"
description: "在 Azure 中部署 OpenShift 的先决条件。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Azure 中的 OpenShift 的常见先决条件

在 Azure 中部署 OpenShift 时，无论是部署 OpenShift Origin 还是 OpenShift Container Platform，都有几个常见的先决条件。

OpenShift 的安装通常是通过 Ansible 攻略完成的。 Ansible 使用 SSH 连接到将成为群集一部分的所有主机来完成安装步骤。
当发起到远程主机的 SSH 连接时，无法输入通行短语。 因此，私钥不能有与之关联的通行短语，否则，部署将失败。
因为所有 VM 都是通过资源管理器模板部署的，所以将使用同一公钥来访问所有 VM。 我们还需要将对应的私钥注入到执行所有攻略的 VM 中。
为安全地执行此操作，我们使用 Azure Key Vault 向 VM 传递私钥。

如果容器需要持久性存储，则需要永久卷 (PV)。 这些 PV 需要由某种形式的持久性存储提供支持。 OpenShift 支持使用 Azure 磁盘 (VHD) 实现此功能，但必须首先将 Azure 配置为云提供程序。 在此模型中，OpenShift 将：

- 在 Azure 存储帐户中创建一个 VHD 对象
- 将 VHD 装载到 VM 中并格式化卷
- 将卷装载到 Pod

要使此方式可行，OpenShift 需要有权在 Azure 中执行前面的任务。 若要实现此目的，需要一个服务主体。 服务主体 (SP) 是 Azure Active Directory 中的一个安全帐户，被授予了对资源的权限。
服务主体需要有权访问构成了群集的存储帐户和 VM。 如果所有 OpenShift 群集资源都部署到单个资源组中，则可以向 SP 授予对该资源组的权限。

本指南介绍了如何创建与先决条件关联的项目。

> [!div class="checklist"]
> * 创建一个 KeyVault 用于管理 OpenShift 群集的 SSH 密钥。
> * 创建一个供 Azure 云提供程序使用的服务主体。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure 
使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，按屏幕说明操作，或者单击“试用”使用 Cloud Shell。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 建议使用专用资源组来托管 Key Vault - 与要将 OpenShift 群集资源部署到的资源组分开。 

以下示例在 *eastus* 位置创建一个名为 *keyvaultrg* 的资源组。

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>创建 key vault
使用 [az keyvault create](/cli/azure/keyvault#create) 命令创建一个 KeyVault 用于管理群集的 SSH 密钥。 Key Vault 名称必须全局唯一。

以下示例在 *keyvaultrg* 资源组中创建一个名为 *keyvault* 的 Key Vault。

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>创建 SSH 密钥 
需要使用 SSH 密钥来保护对 OpenShift Origin 群集的访问。 使用 `ssh-keygen` 命令创建 SSH 密钥对（在 Linux 或 Mac 上）。
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 创建的 SSH 密钥对不能包含通行短语。

有关 Windows 上的 SSH 密钥的详细信息，请参阅[如何在 Windows 上创建 SSH 密钥](/azure/virtual-machines/linux/ssh-from-windows)。

## <a name="store-ssh-private-key-in-key-vault"></a>将 SSH 私钥存储在 Key Vault 中
OpenShift 部署使用创建的 SSH 密钥来保护对 OpenShift 主设备的访问。 为使部署能够安全检索 SSH 密钥，请使用以下命令将密钥存储在 Key Vault 中：

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>创建服务主体 
OpenShift 使用用户名和密码或服务主体来与 Azure 通信。 Azure 服务主体是可用于应用、服务和 OpenShift 等自动化工具的安全标识。 由你控制和定义有关服务主体可以在 Azure 中执行哪些操作的权限。 为了提供比使用用户名和密码更高的安全性，本示例将创建一个基本的服务主体。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 创建服务主体并输出 OpenShift 需要的凭据。

以下示例创建一个服务主体并为其分配对名为 myResourceGroup 的资源组的 Contributor 权限。 如果使用的是 Windows，请单独执行 ```az group show --name myResourceGroup --query id``` 并使用输出来为 --scopes 选项提供输入。

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

记下该命令返回的 appId 属性。
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 请勿创建不安全的密码。  遵循 [Azure AD 密码规则和限制](/azure/active-directory/active-directory-passwords-policy)指导原则。

有关服务主体的详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>后续步骤

本文涵盖了以下主题：
> [!div class="checklist"]
> * 创建一个 KeyVault 用于管理 OpenShift 群集的 SSH 密钥。
> * 创建一个供 Azure 云提供程序使用的服务主体。

现在，去部署 OpenShift 群集

- [部署 OpenShift Origin](./openshift-origin.md)
- [部署 OpenShift Container Platform](./openshift-container-platform.md)

