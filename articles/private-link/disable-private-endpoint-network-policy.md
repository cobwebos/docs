---
title: 在 Azure 中禁用专用终结点的网络策略
description: 了解如何禁用专用终结点的网络策略。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef9dafd97b3d9889714a321ad00d98a87c3665d6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224807"
---
# <a name="disable-network-policies-for-private-endpoints"></a>禁用专用终结点的网络策略

专用终结点不支持网络安全组（NSG）等网络策略。 若要在给定的子网上部署专用终结点，需要在该子网上进行显式禁用设置。 此设置仅适用于专用终结点。 对于子网中的其他资源，访问权限基于网络安全组（NSG）安全规则定义进行控制。 
 
使用门户创建专用终结点时，会在创建过程中自动禁用此设置。 使用其他客户端进行的部署需要额外的步骤来更改此设置。 你可以从 Azure 门户中使用 cloud shell，或者 Azure PowerShell、Azure CLI 的本地安装或使用 Azure 资源管理器模板来禁用此设置。  
 
以下示例介绍如何使用名为*myResourceGroup*的资源组中托管的*默认*子网来禁用名为*myVirtualNetwork*的虚拟网络的 `PrivateEndpointNetworkPolicies`。

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>使用模板
本部分介绍如何使用 Azure 资源管理器模板禁用子网专用终结点策略。
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>后续步骤
- 了解有关[Azure 专用终结点](private-endpoint-overview.md)的详细信息
 
