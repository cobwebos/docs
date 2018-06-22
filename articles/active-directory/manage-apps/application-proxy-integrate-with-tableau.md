---
title: Azure Active Directory 应用程序代理和 Tableau | Microsoft 文档
description: 了解如何使用 Azure Active Directory (Azure AD) 应用程序代理为 Tableau 部署提供远程访问。  。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 832d37e6c168a7b25adecee967b6523f6cea5554
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302040"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory 应用程序代理和 Tableau 

Azure Active Directory 应用程序代理和 Tableau 已展开合作，以确保你可以轻松使用应用程序代理为 Tableau 部署提供远程访问。 本文介绍如何配置此方案。  

## <a name="prerequisites"></a>先决条件 

本文中的方案假定你：

- 已配置 [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure)。 

- 已安装[应用程序代理连接器](application-proxy-enable.md)。 

 

## <a name="enabling-application-proxy-for-tableau"></a>为 Tableau 启用应用程序代理 

如果想要为 Tableau 使用应用程序代理，则需要将电子邮件发送至 [aadapfeedback@microsoft.com](mailto:aadapfeedback@microsoft.com) 才能启用此方案。
在你的电子邮件中：

-   将“为 Tableau 启用应用程序代理”作为主题
-   在正文中包括你的租户 ID    

准备好使用应用程序时，将收到确认消息。 可以在等待确认时完成配置。


 

## <a name="publish-your-applications-in-azure"></a>在 Azure 中发布应用程序 

要发布 Tableau，你需要在 Azure 门户中发布应用程序。

对于：

- 步骤 1-8 的详细说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)。 
- 如何查找应用程序代理字段的 Tableau 值的信息，请参阅 Tableau 文档。  

**要发布应用程序**： 


1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。 

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

