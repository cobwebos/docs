---
title: Azure Active Directory 应用程序代理和 Tableau | Microsoft 文档
description: 了解如何使用 Azure Active Directory (Azure AD) 应用程序代理为 Tableau 部署提供远程访问。
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d80381815524658d91067ee880eba47c394058
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292888"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory 应用程序代理和 Tableau 

Azure Active Directory 应用程序代理和 Tableau 已展开合作，以确保你可以轻松使用应用程序代理为 Tableau 部署提供远程访问。 本文介绍如何配置此方案。  

## <a name="prerequisites"></a>必备组件 

本文中的方案假定你：

- 已配置 [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure)。 

- 已安装[应用程序代理连接器](application-proxy-add-on-premises-application.md)。 

 
## <a name="enabling-application-proxy-for-tableau"></a>为 Tableau 启用应用程序代理 

应用程序代理支持 OAuth 2.0 授权流，这是 Tableau 正常工作所必需的。 这意味着启用此应用程序不再需要任何特殊步骤，只需按照以下发布步骤进行配置即可。


## <a name="publish-your-applications-in-azure"></a>在 Azure 中发布应用程序 

要发布 Tableau，你需要在 Azure 门户中发布应用程序。

对于：

- 步骤 1-8 的详细说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。 
- 如何查找应用程序代理字段的 Tableau 值的信息，请参阅 Tableau 文档。  

**要发布应用程序**： 


1. 登录到[Azure 门户](https://portal.azure.com)作为应用程序管理员。 

2. 选择“Azure Active Directory”>“企业应用程序”。 

3. 单击边栏选项卡顶部的“添加”。 

4. 选择“本地应用程序”。 

5. 在必填的字段中填写有关新应用的信息。 参考以下指导完成设置： 

    - **内部 URL**：此应用程序具有的内部 URL，本身应为 Tableau URL。 例如，`https://adventure-works.tableau.com`。 

    - **预身份验证方法**：Azure Active Directory（推荐使用但并非必需项）。 

6. 单击边栏选项卡顶部的“添加”。 添加应用程序后，将打开快速启动菜单。 

7. 在快速启动菜单中选择“分配用于测试的用户”，并将至少一个用户添加到应用程序。 确保此测试帐户有权访问本地应用程序。 

8. 选择“分配”，保存测试用户分配。 

9. （可选）在应用管理页面中选择“单一登录”。 从下拉菜单中选择“集成 Windows 身份验证”，然后根据 Tableau 配置填写必填字段。 选择“保存”。 

 

## <a name="testing"></a>测试 

现在已准备好测试应用程序。 访问用来发布 Tableau 的外部 URL，并以分配到两个应用程序的用户身份登录。



## <a name="next-steps"></a>后续步骤

有关 Azure AD 应用程序代理的详细信息，请参阅[如何提供对本地应用程序的安全远程访问](application-proxy.md)。

