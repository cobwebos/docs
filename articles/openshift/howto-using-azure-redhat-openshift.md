---
title: 创建 Azure Red Hat OpenShift 4.3 群集 |Microsoft Docs
description: 使用 Azure Red Hat OpenShift 3.11 创建群集
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro，openshift，az aro，red hat，cli
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899303"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>创建、访问和管理 Azure Red Hat OpenShift 4.3 群集

> [!IMPORTANT]
> 预览版中提供了 Azure Red Hat OpenShift （ARO）4.3。 预览功能是自助服务，按原样提供，并在服务级别协议（SLA）和有限担保中排除。 因此，这些功能并不用于生产。

## <a name="prerequisites"></a>必备条件

若要创建 Azure Red Hat OpenShift 4.3 群集，需要以下各项：

- Azure CLI 版本2.0.72 或更高版本
  
- "Az aro" 扩展

- 一个虚拟网络，其中包含两个空子网，每个子网均附加有网络安全组。  群集将部署到这些子网中。

- 群集 AAD 应用程序（客户端 ID 和机密）和服务主体，或足够的 AAD 权限，`az aro create` 自动为你创建 AAD 应用程序和服务主体。

- RP 服务主体和群集服务主体每个都必须具有群集虚拟网络上的 "参与者" 角色。  如果在虚拟网络上具有 "用户访问管理员" 角色，`az aro create` 将自动为你设置角色分配。

### <a name="install-the-az-aro-extension"></a>安装 "az aro" 扩展
`az aro` 扩展允许你使用 Azure CLI 从命令行直接创建、访问和删除 Azure Red Hat OpenShift 群集。

> [!Note] 
> `az aro` 扩展在预览中为当前。 将来的版本中可能会更改或删除该版本。
> 若要选择启用 `az aro` 扩展预览，需注册 `Microsoft.RedHatOpenShift` 资源提供程序。
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. 登录 Azure。

   ```console
   az login
   ```

2. 运行以下命令以安装 `az aro` 扩展：

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. 验证是否已注册 ARO 扩展。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
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
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> 创建群集通常需要大约35分钟。

## <a name="access-the-cluster-console"></a>访问群集控制台

可以在 Azure Red Hat OpenShift 4.3 群集资源下找到群集控制台 URL （格式为 `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`）。 运行以下命令以查看资源：

```console
az aro list -o table
```

你可以使用 `kubeadmin` 用户登录到群集。  运行以下命令以查找 `kubeadmin` 用户的密码：

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