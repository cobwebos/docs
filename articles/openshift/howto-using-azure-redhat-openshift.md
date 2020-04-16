---
title: 创建 Azure 红帽开放移位 4.3 群集 |微软文档
description: 使用 Azure 红帽开放Shift 4.3 创建群集
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro， 开移， 阿兹阿罗， 红帽子， cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398898"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>创建、访问和管理 Azure 红帽 OpenShift 4.3 群集

> [!IMPORTANT]
> 请注意，Azure 红帽 OpenShift 4.3 目前仅在美国东部和美国东部 2 的专用预览版中可用。 专用预览接受仅通过邀请。 在尝试启用此功能之前，请务必注册订阅[：Azure 红帽开放移位专用预览注册](https://aka.ms/aro-preview-register)

> [!NOTE]
> 预览功能是自助服务，提供时和可用，不在服务级别协议 （SLA） 和有限保修中。 因此，这些功能不适合生产用途。

## <a name="prerequisites"></a>先决条件

创建 Azure 红帽 OpenShift 4.3 群集需要以下内容：

- Azure CLI 版本 2.0.72 或更高版本
  
- "az aro"扩展

- 包含两个空子网的虚拟网络，每个子网没有附加网络安全组。  群集将部署到这些子网中。

- 群集 AAD 应用程序（客户端 ID 和机密）和服务主体，或用于`az aro create`自动为您创建 AAD 应用程序和服务主体的足够 AAD 权限。

- RP 服务主体和群集服务主体必须在群集虚拟网络上具有"参与者"角色。  如果在虚拟网络上具有"用户访问管理员"角色，`az aro create`将自动为您设置角色分配。

### <a name="install-the-az-aro-extension"></a>安装"az aro"扩展
扩展`az aro`允许您使用 Azure CLI 直接从命令行创建、访问和删除 Azure 红帽 OpenShift 群集。

> [!Note] 
> 扩展`az aro`在预览中是最新的。 它可能在将来的版本中更改或删除它。
> 要选择加入扩展预览，`az aro`您需要注册`Microsoft.RedHatOpenShift`资源提供程序。
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. 登录 Azure。

   ```console
   az login
   ```

2. 运行以下命令以安装`az aro`扩展：

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. 验证 ARO 扩展已注册。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>获取红帽拉取机密（可选）

红帽拉取密钥使群集可以访问红帽容器注册表和其他内容。 使用拉取密钥是可选的，但建议使用。

要获取您的拉取秘密：

1. 转到  https://cloud.redhat.com/openshift/install/azure/aro-provisioned 。
1. 登录到您的红帽帐户，或使用您的业务电子邮件创建新的红帽帐户;接受条款和条件。
1. 选择 **"下载拉取"机密**。

将*拉-机密.txt*文件保存在安全的地方;每次创建群集时，都将使用该文件。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>创建包含两个空子网的虚拟网络

按照以下步骤创建包含两个空子网的虚拟网络。

1. 设置以下变量。

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. 为群集创建资源组。

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. 创建虚拟网络。

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. 向虚拟网络添加两个空子网。

   ```console
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. 禁用虚拟网络和子网上的专用链路服务的网络策略。 这是 ARO 服务访问和管理群集的要求。

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>创建群集

运行以下命令以创建群集。

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> 创建群集通常需要大约 35 分钟。

## <a name="access-the-cluster-console"></a>访问群集控制台

您可以在 Azure 红帽 OpenShift 4.3 群集资源下找到群集控制台 URL（窗体`https://console-openshift-console.apps.<random>.<location>.aroapp.io/`）。 运行以下命令以查看资源：

```console
az aro list -o table
```

您可以使用`kubeadmin`用户登录到群集。  运行以下命令以查找`kubeadmin`用户的密码：

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>删除群集

运行以下命令以删除群集。

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
