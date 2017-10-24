---
title: "Azure Analysis Services 中的身份验证和用户权限 | Microsoft Docs"
description: "了解 Azure Analysis Services 中的身份验证和用户权限。"
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
ms.date: 10/09/2017
ms.author: owend
ms.openlocfilehash: e9667afff1bdc351e0ed5cd94bb5fcf946b46f3b
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-user-permissions"></a>身份验证和用户权限
Azure Analysis Services 使用 Azure Active Directory (Azure AD) 进行身份管理和用户身份验证。 在相同订阅中，创建、管理或连接到 Azure Analysis Services 服务器的任何用户均需具备 [Azure AD 租户](../active-directory/active-directory-administer.md)中的有效用户标识。

Azure Analysis Services 支持 [Azure AD B2B 协作](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)。 使用 B2B，可邀请组织外的用户作为 Azure AD 目录中的来宾用户。 来宾可来自其他 Azure AD 租户目录或任何有效的电子邮件地址。 用户受邀并接受 Azure 通过电子邮件发送的邀请函后，用户标识就会添加到租户目录中。 可将这些标识添加到安全组，或者作为服务器管理员或数据库角色的成员。

![Azure Analysis Services 身份验证体系结构](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>身份验证
所有客户端应用程序和工具都使用一个或多个 Analysis Services [客户端库](analysis-services-data-providers.md)（AMO、MSOLAP、ADOMD）连接到服务器。 

所有 3 个客户端库均支持 Azure AD 交互流和非交互式身份验证方法。 可在利用 AMOMD 和 MSOLAP 的应用程序中使用两种非交互式方法，即 Active Directory 密码和 Active Directory 集成身份验证方法。 这两种方法绝对不会产生弹出式对话框。

客户端应用程序（如 Excel 和 Power BI Desktop）和工具（如 SSMS 和 SSDT）更新到最新版时，会安装最新版本的库。 Power BI Desktop、SSMS 和 SSDT 每月更新一次。 Excel [随 Office 365 一起更新](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516)。 Office 365 更新频率较低，并且某些组织会使用延期频道将更新延迟 3 个月。

根据使用的客户端应用程序或工具，身份验证类型和登录方式可能有所不同。 每个应用程序可能支持连接到云服务（如 Azure Analysis Services）的不同功能。

Power BI Desktop、SSDT 和 SSMS 支持 Active Directory 通用身份验证，该通用身份验证是一种交互式方法，还支持 Azure 多重身份验证 (MFA)。 Azure MFA 可帮助保护对数据和应用程序的访问，同时提供简单的登录过程。 它利用一些验证选项提供强身份验证，这些选项包括电话、短信、含有 PIN 码的智能卡或移动应用通知。 配合使用 Azure AD 和交互式 MFA 时会出现用于验证的弹出式对话框。 建议使用通用身份验证。

如果通过使用 Windows 帐户登录到 Azure，而未选中通用身份验证或通用身份验证不可用 (Excel)，则需要 [Active Directory 联合身份验证服务 (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md)。 使用联合身份验证，Azure AD 和 Office 365 用户可使用本地凭据进行身份验证，并且可访问 Azure 资源。

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Azure Analysis Services 服务器通过以下方式支持来自 [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 及更高版本的连接：使用 Windows 身份验证、Active Directory 密码验证和 Active Directory 通用身份验证。 通常，建议使用 Active Directory 通用身份验证，原因如下：

*  支持交互式和非交互式身份验证方法。

*  支持邀请 Azure B2B 来宾用户加入 Azure AS 租户。 连接到服务器时，来宾用户必须选择 Active Directory 通用身份验证。

*  支持多重身份验证 (MFA)。 Azure MFA 有助于通过一系列验证选项来保护对数据和应用程序的访问：电话呼叫、短信、含有 PIN 码的智能卡或移动应用通知。 配合使用 Azure AD 和交互式 MFA 时会出现用于验证的弹出式对话框。

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
SSDT 通过使用支持 MFA 的 Active Directory 通用身份验证来连接 Azure Analysis Services。 首次部署时，系统会提示用户登录 Azure。 用户必须使用帐户登录 Azure，该帐户需具备针对部署目标服务器的服务器管理员权限。 首次登录 Azure 时，系统会分配令牌。 SSDT 将令牌缓存在内存中，以便将来重新连接。

### <a name="power-bi-desktop"></a>Power BI Desktop
Power BI Desktop 使用支持 MFA 的 Active Directory 通用身份验证来连接 Azure Analysis Services。 首次连接时，系统会提示用户登录 Azure。 用户必须使用帐户登录 Azure，该帐户包括在服务器管理员或数据库角色中。

### <a name="excel"></a>Excel
Excel 用户可使用 Windows 帐户、组织 ID（电子邮件地址）或外部电子邮件地址连接到服务器。 外部电子邮件标识必须作为来宾用户存在于 Azure AD 中。

## <a name="user-permissions"></a>用户权限

服务器管理员特定于 Azure Analysis Services 服务器实例。 他们通过连接 Azure 门户、SSMS 和 SSDT 等工具，执行诸如添加数据库和管理用户角色等任务。 默认情况下，创建服务器的用户将被自动添加为 Analysis Services 服务器管理员。 可使用 Azure 门户或 SSMS 添加其他管理员。 在相同订阅中，服务器管理员必须具有 Azure AD 租户中的帐户。 若要了解详细信息，请参阅[管理服务器管理员](analysis-services-server-admins.md)。 

数据库用户通过使用 Excel 或 Power BI 等客户端应用程序，连接模型数据库。 必须将用户添加到数据库角色。 数据库角色为数据库确定管理员、进程或读取权限。 具有管理员权限的数据库用户与服务器管理员不同，请务必了解这一点。 但默认情况下，服务器管理员也是数据库管理员。 若要了解详细信息，请参阅[管理数据库角色和用户](analysis-services-database-users.md)。

Azure 资源所有者。 资源所有者管理 Azure 订阅的资源。 资源所有者可通过以下方式在订阅中向“所有者角色”或“参与者角色”添加 Azure AD 用户标识：在 Azure 门户中使用访问控制或使用 Azure Resource Manager 模板。 

![Azure 门户中的访问控制](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

此级别的角色适用于符合以下条件的用户或帐户：需要执行可在门户中完成或使用 Azure Resource Manager 模板完成的任务。 若要了解详细信息，请参阅[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)。 


## <a name="database-roles"></a>数据库角色

 为表格模型定义的角色就是数据库角色。 也就是说，此类角色包含由 Azure AD 用户和安全组构成的成员，并拥有特定权限来定义这些成员可以对模型数据库执行的操作。 数据库角色作为数据库中的单独对象创建，并且仅适用于创建该角色的数据库。   
  
 默认情况下，创建新的表格模型项目时，该模型项目没有任何角色。 可使用 SSDT 中的“角色管理器”对话框定义角色。 如果在模型项目设计期间定义了角色，则这些角色仅适用于模型工作区数据库。 部署模型时，应对已部署模型使用相同的角色。 部署模型后，服务器和数据库管理员就可以使用 SSMS 管理角色和成员。 若要了解详细信息，请参阅[管理数据库角色和用户](analysis-services-database-users.md)。
  


## <a name="next-steps"></a>后续步骤

[使用 Azure Active Directory 组管理对资源的访问权限](../active-directory/active-directory-manage-groups.md)   
[管理数据库角色和用户](analysis-services-database-users.md)  
[管理服务器管理员](analysis-services-server-admins.md)  
[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)  