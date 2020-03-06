---
title: 使用 Azure Red Hat OpenShift 3.11 创建专用群集 |Microsoft Docs
description: 使用 Azure Red Hat OpenShift 3.11 创建专用群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro，openshift，专用群集，red hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399414"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>使用 Azure Red Hat OpenShift 3.11 创建专用群集

> [!IMPORTANT]
> Azure Red Hat OpenShift （ARO）专用群集目前仅在美国东部2个人预览版中提供。 仅邀请接受个人预览版。 请确保在尝试启用此功能之前注册订阅。

专用群集具有以下优势：

* 专用群集不会公开公共 IP 地址上的群集控制平面组件（如 API 服务器）。
* 专用群集的虚拟网络可由客户进行配置，使你可以设置网络以允许与其他虚拟网络（包括 ExpressRoute 环境）的对等互连。 你还可以在虚拟网络上配置自定义 DNS 以与内部服务集成。

## <a name="before-you-begin"></a>开始之前

> [!NOTE]
> 此功能要求在版本为2019-10-27 的情况下预览 ARO HTTP API。 此 Azure CLI 尚不受支持。

以下配置代码段中的字段是新的，必须包含在群集配置中。 `managementSubnetCidr` 必须在群集虚拟网络内，并由 Azure 用于管理群集。

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

可以使用下面提供的示例脚本来部署专用群集。 部署群集后，请执行 `cluster get` 命令并查看 `properties.FQDN` 属性，以确定 OpenShift API 服务器的专用 IP 地址。

将使用权限创建群集虚拟网络，以便可以对其进行修改。 然后，可以根据需要设置网络，以便访问虚拟网络（ExpressRoute、VPN、虚拟网络对等互连）。

如果更改群集虚拟网络上的 DNS 名称服务器，则需要在群集上发出更新，并将 `properties.RefreshCluster` 属性设置为 "`true`，以便 Vm 可以重置映像。 此更新将允许它们选取新名称服务器。

## <a name="sample-configuration-scripts"></a>示例配置脚本

使用本部分中的示例脚本来设置和部署专用群集。

### <a name="environment"></a>环境

使用您自己的值填充下面的环境变量。

> [!NOTE]
> 此位置必须设置为 `eastus2`，因为这是当前唯一支持的专用群集位置。

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>私有-cluster

使用上面定义的环境变量，此处是启用了专用群集的示例群集配置。

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>部署专用群集

通过上述示例脚本配置专用群集后，请运行以下命令来部署专用群集。

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>后续步骤

若要了解如何访问 OpenShift 控制台，请参阅[Web 控制台演练](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)。
