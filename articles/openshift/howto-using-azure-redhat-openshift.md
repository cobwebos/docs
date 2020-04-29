---
title: 创建 Azure Red Hat OpenShift 4.3 群集 |Microsoft Docs
description: 使用 Azure Red Hat OpenShift 4.3 创建群集
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro，openshift，az aro，red hat，cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398898"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>创建、访问和管理 Azure Red Hat OpenShift 4.3 群集

> [!IMPORTANT]
> 请注意，Azure Red Hat OpenShift 4.3 目前仅在美国东部和美国东部2个人预览版中可用。 仅邀请接受个人预览版。 请确保在尝试启用此功能之前注册订阅： [Azure Red Hat OpenShift 个人预览版](https://aka.ms/aro-preview-register)

> [!NOTE]
> 预览功能是自助服务，按原样提供，并在服务级别协议（SLA）和有限担保中排除。 因此，这些功能并不用于生产。

## <a name="prerequisites"></a>必备条件

若要创建 Azure Red Hat OpenShift 4.3 群集，需要以下各项：

- Azure CLI 版本2.0.72 或更高版本
  
- "Az aro" 扩展

- 一个虚拟网络，其中包含两个空子网，每个子网均附加有网络安全组。  群集将部署到这些子网中。

- 群集 AAD 应用程序（客户端 ID 和机密）和服务主体，或用于自动为`az aro create`你创建 aad 应用程序和服务主体的足够 AAD 权限。

- RP 服务主体和群集服务主体每个都必须具有群集虚拟网络上的 "参与者" 角色。  如果在虚拟网络上具有 "用户访问管理员" 角色， `az aro create`将自动为你设置角色分配。

### <a name="install-the-az-aro-extension"></a>安装 "az aro" 扩展
使用`az aro`此扩展，可以使用 Azure CLI 从命令行直接创建、访问和删除 Azure Red Hat OpenShift 群集。

> [!Note] 
> 此`az aro`扩展在预览中为当前。 将来的版本中可能会更改或删除该版本。
> 若要选择启用`az aro`扩展预览，需要注册`Microsoft.RedHatOpenShift`资源提供程序。
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

3. 验证是否已注册 ARO 扩展。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>获取 Red Hat 请求机密（可选）

Red Hat 获取密钥使群集能够访问 Red Hat 容器注册表和其他内容。 使用拉取密钥是可选的，但建议使用。

获取你的请求机密：

1. 转到 https://cloud.redhat.com/openshift/install/azure/aro-provisioned。
1. 使用你的企业电子邮件登录 Red Hat 帐户，或创建新的 Red Hat 帐户;接受条款和条件。
1. 选择 "**下载请求机密**"。

将*pull-secret*文件保存在一个安全的位置;每次创建群集时，都将使用该文件。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>创建包含两个子网的虚拟网络

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

5. 在虚拟网络和子网上禁用专用链接服务的网络策略。 这是 ARO 服务访问和管理群集的必要条件。

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
> 创建群集通常需要大约35分钟。

## <a name="access-the-cluster-console"></a>访问群集控制台

可以在 Azure Red Hat OpenShift 4.3 群集资源下找到`https://console-openshift-console.apps.<random>.<location>.aroapp.io/`群集控制台 URL （格式为）。 运行以下命令以查看资源：

```console
az aro list -o table
```

您可以使用`kubeadmin`用户登录到群集。  运行以下命令以查找`kubeadmin`用户的密码：

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>删除群集

运行以下命令来删除群集。

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
