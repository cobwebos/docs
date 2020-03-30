---
title: 使用 Azure 红帽开放Shift 3.11 创建专用群集 |微软文档
description: 使用 Azure 红帽开放Shift 3.11 创建专用群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro， 开放式移位， 私人集群， 红帽子
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399414"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>使用 Azure 红帽开放Shift 3.11 创建专用群集

> [!IMPORTANT]
> Azure 红帽 OpenShift （ARO） 专用群集目前仅在美国东部 2 的专用预览版中可用。 专用预览接受仅通过邀请。 在尝试启用此功能之前，请确保注册订阅。

专用群集提供以下好处：

* 专用群集不会在公共 IP 地址上公开群集控制平面组件（如 API 服务器）。
* 专用群集的虚拟网络可由客户配置，允许您设置网络以允许与其他虚拟网络（包括 ExpressRoute 环境）对等互连。 您还可以在虚拟网络上配置自定义 DNS 以与内部服务集成。

## <a name="before-you-begin"></a>开始之前

> [!NOTE]
> 此功能需要 ARO HTTP API 的版本 2019-10-27 预览。 Azure CLI 中尚不支持它。

以下配置代码段中的字段是新的，必须包含在群集配置中。 `managementSubnetCidr`必须在群集虚拟网络中，并且 Azure 使用 它来管理群集。

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

可以使用下面提供的示例脚本部署专用群集。 部署群集后，`cluster get`执行命令并查看`properties.FQDN`属性以确定 OpenShift API 服务器的专用 IP 地址。

群集虚拟网络将具有权限创建，以便您可以对其进行修改。 然后，您可以根据需要设置网络以访问虚拟网络（ExpressRoute、VPN、虚拟网络对等互连）。

如果更改群集虚拟网络上的 DNS 名称服务器，则需要在群集上发出更新，`properties.RefreshCluster`并将属性设置为，`true`以便可以重新映像 VM。 此更新将允许他们拿起新的名称服务器。

## <a name="sample-configuration-scripts"></a>示例配置脚本

使用本节中的示例脚本设置和部署专用群集。

### <a name="environment"></a>环境

将下面的环境变量填报为使用您自己的值。

> [!NOTE]
> 必须将位置设置为`eastus2`，因为当前这是专用群集的唯一受支持的位置。

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

### <a name="private-clusterjson"></a>私有群集.json

使用上面定义的环境变量，下面是启用专用群集的示例群集配置。

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

使用上面的示例脚本配置专用群集后，运行以下命令以部署专用群集。

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>后续步骤

要了解如何访问 OpenShift 控制台，请参阅[Web 控制台演练](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)。
