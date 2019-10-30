---
title: '禁用 Azure 专用链接服务源 IP 地址的网络策略 '
description: 了解如何禁用 Azure 专用链接的网络策略
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 53df209d080cf91be9c558b43edaa618c0748fc5
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101539"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>为专用链接服务源 IP 禁用网络策略

若要为专用链接服务选择源 IP 地址，子网需要 `privateLinkServiceNetworkPolicies` 显式禁用设置。 此设置仅适用于你选择的特定专用 IP 地址作为专用链接服务的源 IP 地址。 对于子网中的其他资源，访问权限基于网络安全组（NSG）安全规则定义进行控制。 
 
使用任何 Azure 客户端（PowerShell、CLI 或模板）时，需要额外的步骤来更改此属性。 你可以从 Azure 门户中使用 cloud shell，或者 Azure PowerShell、Azure CLI 的本地安装或使用 Azure 资源管理器模板来禁用策略。  
 
按照以下步骤为名为*myVirtualNetwork*的虚拟网络禁用专用链接服务网络策略，并在名为*myResourceGroup*的资源组中托管*默认*子网。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>后续步骤
- 了解有关[Azure 专用终结点](private-endpoint-overview.md)的详细信息
 
