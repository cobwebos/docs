---
title: 管理 Azure 中的专用终结点连接
description: 了解如何管理 Azure 中的专用终结点连接
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452967"
---
# <a name="manage-a-private-endpoint-connection"></a>管理专用终结点连接
Azure 专用链接适用于审批调用流模型，其中，专用链接服务使用者可以请求连接到服务提供商以使用该服务。 然后，服务提供商可以决定是否允许使用者进行连接。 通过 Azure 专用链接，服务提供商可以管理其资源上的专用终结点连接。 本文提供了有关如何管理专用终结点连接的说明。

![管理专用终结点](media/manage-private-endpoint/manage-private-endpoint.png)

专用链接服务使用者可以选择两种连接批准方法：
- **自动**：如果服务使用者对服务提供者资源具有 RBAC 权限，则使用者可以选择自动批准方法。 在这种情况下，当请求到达服务提供商资源时，服务提供商不需要执行任何操作，并且将自动批准连接。 
- **手动**：相反，如果服务使用者没有对服务提供者资源的 RBAC 权限，则使用者可以选择手动批准方法。 在这种情况下，连接请求在服务资源上显示为**挂起**。 在建立连接之前，服务提供商必须手动批准请求。 在手动情况下，服务使用者还可以使用请求指定消息，以便向服务提供商提供更多上下文。 服务提供程序有以下选项可供选择用于所有专用终结点连接：**已批准**、**拒绝**、**删除**。

下表显示了不同的服务提供程序操作以及专用终结点的生成的连接状态。  服务提供商还可以在以后无需用户干预的情况下更改专用终结点连接的连接状态。 操作将更新使用者端上终结点的状态。 


|服务提供商操作   |服务使用者专用终结点状态   |Description   |
|---------|---------|---------|
|无    |    Pending     |    手动创建连接，并等待专用链接资源所有者批准。       |
|审批    |  已批准       |  连接已自动或已手动批准，并已准备好使用。     |
|拒绝     | 已拒绝        | 专用链接资源所有者拒绝了连接。        |
|移除    |  已断开连接       | 连接已被专用链接资源所有者删除，专用终结点将变为信息性，应删除进行清理。        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>管理 Azure PaaS 资源上的专用终结点连接
门户是管理 Azure PaaS 资源上的专用终结点连接的首选方法。 目前，我们没有 PowerShell/CLI 支持来管理 Azure PaaS 资源上的连接。
1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 导航到 "专用链接中心"。
3. 在 "**资源**" 下，选择要管理专用终结点连接的资源类型。
4. 对于每种资源类型，可以查看与其关联的专用终结点连接数。 你可以根据需要筛选资源。
5. 选择专用终结点连接。  在列出的连接下，选择要管理的连接。 
6. 可以通过从顶部的选项中进行选择来更改连接的状态。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>在客户/合作伙伴拥有的专用链接服务上管理专用终结点连接

Azure PowerShell 和 Azure CLI 是在 Microsoft 合作伙伴服务或客户拥有的服务上管理专用终结点连接的首选方法。 目前，我们没有任何门户支持来管理专用链接服务上的连接。  
 
### <a name="powershell"></a>PowerShell 
  
使用以下 PowerShell 命令管理专用终结点连接。  
#### <a name="get-private-link-connection-states"></a>获取专用链接连接状态 
使用 `Get-AzPrivateLinkService` cmdlet 可获取专用终结点连接及其状态。  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接 
 
使用 `Approve-AzPrivateEndpointConnection` cmdlet 批准专用终结点连接。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>拒绝专用终结点连接 
 
使用 `Deny-AzPrivateEndpointConnection` cmdlet 拒绝专用终结点连接。 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>删除专用终结点连接 
 
使用 `Remove-AzPrivateEndpointConnection` cmdlet 可删除专用终结点连接。 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
使用 `az network private-link-service update` 管理专用终结点连接。 在 ```azurecli connection-status``` 参数中指定连接状态。 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>后续步骤
- [了解专用终结点](private-endpoint-overview.md)
 
