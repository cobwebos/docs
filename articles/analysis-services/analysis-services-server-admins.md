---
title: "管理 Azure Analysis Services 中的服务器管理员 | Microsoft Docs"
description: "了解如何在 Azure 中管理 Analysis Services 服务器的服务器管理员。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a1b58125dafdf73f245b6a8cd0f4917513b22ea9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="manage-server-administrators"></a>管理服务器管理员
服务器管理员必须是 Azure Active Directory (Azure AD) 中服务器所在租户的有效用户或组。 可以为 Azure 门户中的服务器使用控制边栏选项卡中的“Analysis Services 管理员”，或使用 SSMS 中的“服务器属性”来管理服务器管理员。 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>使用 Azure 门户添加服务器管理员
1. 在服务器的控制边栏选项卡中，单击“Analysis Services 管理员”。
2. 在“\<servername> - Analysis Services 管理员”边栏选项卡中，单击“添加”。
3. 在“添加服务器管理员”边栏选项卡中，从 Azure AD 选择用户帐户，或使用电子邮件地址邀请外部用户。

    ![Azure 门户中的服务器管理员](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>使用 SSMS 添加服务器管理员
1. 右键单击服务器 >“属性”。
2. 在“Analysis Server 属性”中，单击“安全性”。
3. 单击“添加”，然后输入 Azure AD 中用户或组的电子邮件地址。
   
    ![在 SSMS 中添加服务器管理员](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>后续步骤 
[身份验证和用户权限](analysis-services-manage-users.md)  
[管理数据库角色和用户](analysis-services-database-users.md)  
[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)  

