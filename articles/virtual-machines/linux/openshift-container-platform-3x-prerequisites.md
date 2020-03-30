---
title: 在 Azure 先决条件中打开Shift容器平台 3.11
description: 在 Azure 中部署 OpenShift 容器平台 3.11 的先决条件。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: b2b34a6fdf96613c5bc372e585598fabbe43d53d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066611"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平台 3.11 的常见先决条件

本文介绍了在 Azure 中部署 OpenShift 容器平台或 OKD 所要满足的常见先决条件。

OpenShift 的安装使用 Ansible 攻略。 Ansible 使用安全外壳 (SSH) 连接到所有群集主机来完成安装步骤。

当 Aible 使 SSH 连接到远程主机时，它无法输入密码。 因此，私钥不能有与之关联的密码（通行短语），否则，部署将失败。

因为虚拟机 (VM) 是通过 Azure 资源管理器模板部署的，所以将使用同一公钥来访问所有 VM。 相应的私钥必须位于执行所有行动手册的 VM 上。 要安全地执行此操作，使用 Azure 密钥保管库将私钥传递到 VM 中。

如果容器需要持久性存储，则需要永久卷。 OpenShift 支持持久卷的 Azure 虚拟硬盘 （VHD），但必须先将 Azure 配置为云提供程序。

在此模型中，OpenShift 将会：

- 在 Azure 存储帐户或托管磁盘中创建 VHD 对象。
- 将 VHD 装载到 VM 中并格式化卷。
- 将卷装载到 Pod。

要使此配置可行，OpenShift 需要有权在 Azure 中执行这些任务。 为此，使用服务主体。 服务主体是 Azure Active Directory 中的一个安全帐户，被授予了对资源的权限。

服务主体需要有权访问构成了群集的存储帐户和 VM。 如果所有 OpenShift 群集资源都部署到单个资源组中，则可以向服务主体授予对该资源组的权限。

本指南介绍了如何创建与先决条件关联的项目。

> [!div class="checklist"]
> * 创建一个 Key Vault 用于管理 OpenShift 群集的 SSH 密钥。
> * 创建一个供 Azure 云提供程序使用的服务主体。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure 
使用 [az login](/cli/azure/reference-index) 命令登录到 Azure 订阅，按屏幕说明操作，或者单击“试用”使用 Cloud Shell。****

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 应使用专用资源组来承载密钥保管库。 此组与要将 OpenShift 群集资源部署到的资源组分开。

以下示例在 *eastus* 位置创建一个名为 *keyvaultrg* 的资源组：

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>创建 key vault
使用 [az keyvault create](/cli/azure/keyvault) 命令创建一个 Key Vault 用于管理群集的 SSH 密钥。 密钥保管库名称必须全局唯一，并且必须为模板部署启用，否则部署将失败，出现"KeyVault 参数引用秘密检索失败"错误。

以下示例在 *keyvaultrg* 资源组中创建一个名为 *keyvault* 的 Key Vault：

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>创建 SSH 密钥 
需要使用 SSH 密钥来保护对 OpenShift 群集的访问。 使用 `ssh-keygen` 命令创建 SSH 密钥对（在 Linux 或 macOS 上）：
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH 密钥对不能包含密码/通行短语。

有关 Windows 上的 SSH 密钥的详细信息，请参阅[如何在 Windows 上创建 SSH 密钥](/azure/virtual-machines/linux/ssh-from-windows)。 请务必以 OpenSSH 格式导出私钥。

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>将 SSH 私钥存储在 Azure Key Vault 中
OpenShift 部署使用创建的 SSH 密钥来保护对 OpenShift 主设备的访问。 为使部署能够安全检索 SSH 密钥，请使用以下命令将密钥存储在 Key Vault 中：

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>创建服务主体 
OpenShift 使用用户名和密码或服务主体来与 Azure 通信。 Azure 服务主体是可用于应用、服务和 OpenShift 等自动化工具的安全标识。 控制和定义服务主体可在 Azure 中执行哪些操作的权限。 最好将服务主体的权限范围限定为特定资源组，而不是整个订阅。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp) 创建服务主体并输出 OpenShift 需要的凭据。

下面的示例创建一个服务主体，并将其参与者权限分配给名为*openshiftrg*的资源组。

首先，创建名为*openshiftrg*的资源组 ：

```azurecli
az group create -l eastus -n openshiftrg
```

创建服务主体：

```azurecli
az group show --name openshiftrg --query id
```

保存命令的输出，并代替下一个命令中的$scope

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

请注意从命令返回的 appId 属性和密码：

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
 > 请务必记下安全密码，因为无法再次检索此密码。

有关服务主体的详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>仅适用于资源管理器模板的先决条件

需要为 SSH 私**钥 （sshPrivateKey）、** Azure AD 客户端密钥 （**aadClientSecret）、** OpenShift 管理员密码 （**开放移位密码**） 和红帽订阅管理器密码或激活密钥 **（rhsmPasswordOr 激活密钥**） 创建秘密。  此外，如果使用自定义 SSL 证书，则需要创建另外六个机密 -**路由缓存文件**、**路由文件**、**路由密钥文件**、**母版文件**、**母版文件**和**主密钥文件**。  这些参数将更详细地解释。

模板引用特定的机密名称，因此**必须**使用上面列出的粗体名称（区分大小写）。

### <a name="custom-certificates"></a>自定义证书

默认情况下，模板将使用 OpenShift Web 控制台和路由域的自签名证书部署 OpenShift 群集。 如果要使用自定义 SSL 证书，可以将"路由证书类型"设置为"自定义"，将"主证书类型"设置为"自定义"。  证书需要以 .pem 格式的 CA、Cert 和 Key 文件。  可以将自定义证书用于其中一个，但不能对另一个证书使用。

您需要将这些文件存储在密钥保管库机密中。  使用与私钥相同的密钥保管库。  模板无需为机密名称增加 6 个输入，而是硬编码，可为每个 SSL 证书文件使用特定机密名称。  使用下表中的信息存储证书数据。

| 秘密名称      | 证书文件   |
|------------------|--------------------|
| 马斯特卡菲菲     | 主 CA 文件     |
| 主文件   | 主 CERT 文件   |
| 主键文件    | 主密钥文件    |
| 路由缓存文件    | 路由 CA 文件    |
| 路由文件  | 路由 CERT 文件  |
| 路由密钥文件   | 路由密钥文件   |

使用 Azure CLI 创建机密。 下面是一个示例。

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>后续步骤

本文涵盖了以下主题：
> [!div class="checklist"]
> * 创建一个 Key Vault 用于管理 OpenShift 群集的 SSH 密钥。
> * 创建一个供 Azure 云解决方案提供程序使用的服务主体。

接下来，可部署 OpenShift 群集：

- [部署 OpenShift Container Platform](./openshift-container-platform-3x.md)
- [部署 OpenShift 容器平台自管理市场产品/服务](./openshift-container-platform-3x-marketplace-self-managed.md)
