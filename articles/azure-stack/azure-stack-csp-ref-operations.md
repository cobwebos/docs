---
title: 在 Azure Stack 中注册租户以便进行使用情况跟踪 | Microsoft Docs
description: 详细介绍用于管理租户注册的操作，以及如何在 Azure Stack 中跟踪租户使用情况。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: ef7ca59647a1f8c15d85c809609060a5945bedde
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>在 Azure Stack 中管理租户注册

*适用于：Azure Stack 集成系统*

本文包含有关用于管理租户注册的操作以及如何跟踪租户使用情况的详细信息。 可以找到有关如何添加、列出或删除租户映射的详细信息。 可以使用 PowerShell 或计费 API 终结点来管理使用情况跟踪。

## <a name="add-tenant-to-registration"></a>将租户添加到注册

如果想要将新租户添加到注册，以便在与其 Azure Active Directory (Azure AD) 租户连接的 Azure 订阅下报告其使用情况，可以使用此操作。

如果想要更改与租户关联的订阅，也可以使用此操作，可以再次调用 PUT/New-AzureRMResource。 旧的映射将被覆盖。

请注意，只有一个 Azure 订阅可以与租户相关联。 如果尝试将第二个订阅添加到现有租户，则第一个订阅将被覆盖。 


| 参数                  | 说明 |
|---                         | --- |
| registrationSubscriptionID | 用于初始注册的 Azure 订阅。 |
| customerSubscriptionID     | 属于要注册的客户的 Azure 订阅（非 Azure Stack）。 必须在云服务提供商 (CSP) 产品/服务中创建。 实际上，这意味着通过合作伙伴中心。 如果客户有多个租户，则必须在用于登录 Azure Stack 的租户中创建此订阅。 |
| resourceGroup              | Azure 中用于存储注册的资源组。 |
| registrationName           | Azure Stack 的注册名称。 它是 Azure 中存储的对象。 该名称通常采用格式 azurestack-CloudID，其中 CloudID 是 Azure Stack 部署的云 ID。 |

> [!Note]  
> 租户需要注册到它们使用的每个 Azure Stack。 如果租户使用多个 Azure Stack，则需要使用租户订阅更新每个部署的初始注册。

### <a name="powershell"></a>PowerShell

使用 New-AzureRmResource cmdlet 更新注册资源。 使用用于初始注册的帐户登录到 Azure (`Add-AzureRmAccount`)。 下面是演示如何添加租户的示例：

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 调用

**操作**：PUT  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：201 已创建  
**响应正文**：空  

## <a name="list-all-registered-tenants"></a>列出所有已注册租户

获取已添加到注册的所有租户列表。

 > [!Note]  
 > 如果没有租户已注册，则不会收到响应。

### <a name="parameters"></a>parameters

| 参数                  | 说明          |
|---                         | ---                  |
| registrationSubscriptionId | 用于初始注册的 Azure 订阅。   |
| resourceGroup              | Azure 中用于存储注册的资源组。    |
| registrationName           | Azure Stack 的注册名称。 它是 Azure 中存储的对象。 该名称通常采用格式 **azurestack**-***CloudID***，其中 ***CloudID*** 是 Azure Stack 部署的云 ID。   |

### <a name="powershell"></a>PowerShell

使用 Get-AzureRmResovurce cmdlet 列出所有已注册的租户。 使用用于初始注册的帐户登录到 Azure (`Add-AzureRmAccount`)。 下面是演示如何添加租户的示例：

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

可以使用 GET 操作获取所有租户映射的列表

**操作**：GET  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**响应**：200  
**响应正文**： 

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

可以删除已添加到注册的租户。 如果该租户仍在使用 Azure Stack 上的资源，则会对初始 Azure Stack 注册中使用的订阅收取这些资源的使用费用。

### <a name="parameters"></a>parameters

| 参数                  | 说明          |
|---                         | ---                  |
| registrationSubscriptionId | 注册的订阅 ID。   |
| resourceGroup              | 注册的资源组。   |
| registrationName           | 注册的名称。  |
| customerSubscriptionId     | 客户订阅 ID。  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

可以使用 DELETE 操作删除租户映射。

**操作**：DELETE  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：204 无内容  
**响应正文**：空

## <a name="next-steps"></a>后续步骤

 - 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](/azure-stack-billing-and-chargeback.md)。
