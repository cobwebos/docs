---
title: "注册租户为在 Azure 堆栈中跟踪的使用量 |Microsoft 文档"
description: "有关用于管理的租户注册和如何在 Azure 堆栈跟踪租户使用的操作的详细信息。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: be15fbc5fad79f1079b901b3d6cb4948c45a0ab4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>管理 Azure 堆栈中的租户注册

*适用于：Azure Stack 集成系统*

本文包含有关可用于管理你的租户注册，和如何跟踪租户使用的操作的详细信息。 你可以找到有关如何添加列表中，或删除租户映射详细信息。 你可以使用 PowerShell 或计费 API 终结点来管理你使用跟踪。

## <a name="add-tenant-to-registration"></a>将租户添加到注册

当你想要将新租户添加到你的注册，以便其使用情况报告使用其 Azure Active Directory (Azure AD) 租户连接使用 Azure 订阅，你可以使用此操作。

如果你想要更改与租户关联的订阅，也可以使用此操作，你可以再次调用 PUT/New-azurermresource。 旧的映射将被覆盖。

请注意，只有一个 Azure 订阅可以是与租户相关联。 如果你尝试将第二个订阅添加到现有租户，则第一个订阅是被覆盖。 


| 参数                  | 说明 |
|---                         | --- |
| registrationSubscriptionID | 用于在初始注册 Azure 订阅。 |
| customerSubscriptionID     | 属于客户要注册 Azure 订阅 （而不是 Azure 堆栈）。 必须创建云服务提供程序 (CSP) 产品/服务中。 在实践中，这意味着通过合作伙伴中心。 如果客户有多个租户，则必须在将用于登录到 Azure 堆栈的租户中创建此订阅。 |
| resourceGroup              | 在其中存储你的注册的 Azure 中的资源组。 |
| registrationName           | Azure 堆栈的注册的名称。 它是在 Azure 中存储的对象。 名称通常是部署的在窗体 azurestack-CloudID，其中 CloudID 是部署的你 Azure 堆栈的云 ID。 |

> [!Note]  
> 租户需要使用它们使用每个 Azure 堆栈进行注册。 如果租户使用多个 Azure 堆栈，你需要更新每个部署的初始注册信息与租户订阅。

### <a name="powershell"></a>PowerShell

New-azurermresource cmdlet 用于更新的注册资源。 登录到 Azure (`Login-AzureRMAccount`) 使用用于在初始注册的帐户。 下面是如何添加租户的一个示例：

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 调用

**操作**： 放  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**: 201 创建  
**响应正文**： 空  

## <a name="list-all-registered-tenants"></a>列出所有已注册的租户

获取已添加到注册的所有租户的列表。

 > [!Note]  
 > 如果没有租户已注册，你不会收到响应。

### <a name="parameters"></a>parameters

| 参数                  | 说明          |
|---                         | ---                  |
| registrationSubscriptionId | 用于在初始注册 Azure 订阅。   |
| resourceGroup              | 在其中存储你的注册的 Azure 中的资源组。    |
| registrationName           | Azure 堆栈的注册的名称。 它是在 Azure 中存储的对象。 名称通常所采用的形式**azurestack**-***CloudID***，其中***CloudID***是你 Azure 堆栈的部署的云 ID。   |

### <a name="powershell"></a>PowerShell

使用 Get AzureRmResovurce cmdlet 列出所有已注册的租户。 登录到 Azure (`Login-AzureRMAccount`) 使用用于在初始注册的帐户。 下面是如何添加租户的一个示例：

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

你可以使用 GET 操作的所有租户映射的列表

**操作**： 获取  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**响应**: 200  
**响应正文**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>删除租户映射

你可以删除已添加到注册租户。 如果该租户仍使用 Azure 堆栈上的资源，其使用情况收取初始 Azure 堆栈注册中使用的订阅。

### <a name="parameters"></a>parameters

| 参数                  | 说明          |
|---                         | ---                  |
| registrationSubscriptionId | 注册的订阅 ID。   |
| resourceGroup              | 注册资源组。   |
| registrationName           | 注册的名称。  |
| customerSubscriptionId     | 客户订阅 id。  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

你可以删除使用删除操作的租户映射。

**操作**： 删除  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**: 204 无内容  
**响应正文**： 空

## <a name="next-steps"></a>后续步骤

 - 若要了解有关如何从 Azure 堆栈中检索资源使用情况信息的详细信息，请参阅[使用情况和 Azure 堆栈中的计费](/azure-stack-billing-and-chargeback.md)。
