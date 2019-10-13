---
title: 管理 Azure Analysis Services 中的服务器管理员 | Microsoft Docs
description: 了解如何在 Azure 中管理 Analysis Services 服务器的服务器管理员。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fbb7d339c5c3c12990a49d6ebd53760e101f4eb7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301094"
---
# <a name="manage-server-administrators"></a>管理服务器管理员

服务器管理员必须是 Azure Active Directory (Azure AD) 中服务器所在租户的有效用户或安全组。 可以对 Azure 门户中的服务器使用“Analysis Services 管理员”，或使用 SSMS、PowerShell 或 REST API 中的“服务器属性”来管理服务器管理员。 

**安全组**必须[已启用邮件](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups)且 `MailEnabled` 属性设置为 `True`。 按电子邮件地址指定组时，请使用 `obj:groupid@tenantid`。

## <a name="to-add-server-administrators-by-using-azure-portal"></a>使用 Azure 门户添加服务器管理员

1. 在门户中，对于服务器，单击“Analysis Services 管理员”。
2. 在“\<servername> - Analysis Services 管理员”中，单击“添加”。
3. 在“添加服务器管理员”中，从 Azure AD 选择用户帐户，或使用电子邮件地址邀请外部用户。

    ![Azure 门户中的服务器管理员](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>使用 SSMS 添加服务器管理员

1. 右键单击服务器 >“属性”。
2. 在“Analysis Server 属性”中，单击“安全性”。
3. 单击“添加”，然后输入 Azure AD 中用户或组的电子邮件地址。
   
    ![在 SSMS 中添加服务器管理员](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

创建新服务器时，使用 [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) cmdlet 指定 Administrator 参数。 <br>
使用 [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdlet 修改现有服务器的 Administrator 参数。

## <a name="rest-api"></a>REST API

使用 [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) 在创建新服务器时指定 asAdministrator 属性。 <br>
使用 [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) 在修改现有服务器时指定 asAdministrator 属性。 <br>



## <a name="next-steps"></a>后续步骤 

[身份验证和用户权限](analysis-services-manage-users.md)  
[管理数据库角色和用户](analysis-services-database-users.md)  
[基于角色的访问控制](../role-based-access-control/overview.md)  

