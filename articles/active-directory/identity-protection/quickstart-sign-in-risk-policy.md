---
title: 快速入门 - 使用 Azure Active Directory Identity Protection 检测到会话风险后阻止访问 | Microsoft Docs
description: 本快速入门介绍如何配置 Azure Active Directory (Azure AD) Identity Protection 登录风险条件访问策略，以基于会话风险阻止登录。
services: active-directory
keywords: 身份保护, 应用的条件访问, Azure AD 的条件访问, 公司资源的安全访问, 条件访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 6879822e8451e6170bf17fff4ab66b672f711a93
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632381"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>快速入门：使用 Azure Active Directory Identity Protection 检测到会话风险后阻止访问  

若要使环境保持受保护状态，可能需要阻止可疑用户登录。 Azure Active Directory (Azure AD) Identity Protection 会分析每次登录，并计算不是由用户帐户合法所有者执行的登录尝试的可能性。 这种可能性（低、中、高）以称作登录风险级别的计算值来表示。 通过设置登录风险条件，可以配置一个登录风险条件访问策略对特定的登录风险级别做出响应。 

本快速入门介绍如何配置登录风险条件访问策略，以便在检测到中等及更高登录风险级别时阻止登录。 

![创建策略](./media/quickstart-sign-in-risk-policy/1004.png)


如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。



## <a name="prerequisites"></a>先决条件 

若要完成本教程中的方案，需要：

- 对 Azure AD Premium P2 版本的访问权限 - Azure AD Identity Protection 是一项 Azure AD Premium P2 功能。 

- **Identity Protection** - 本快速入门中的方案需要启用 Identity Protection。 如果不知道如何启用 Identity Protection，请参阅[启用 Azure Active Directory Identity Protection](../identity-protection/enable.md)。

- **Tor 浏览器** - [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)可帮助保护你在网络中的隐私。 Identity Protection 将来自 Tor 浏览器的登录检测为“从匿名 IP 地址登录”，这种登录存在中等风险级别。 有关详细信息，请参阅 [Azure Active Directory 风险事件](../reports-monitoring/concept-risk-events.md)。  

- 名为 Alain Charon 的测试帐户：如果不知道如何创建测试帐户，请参阅[添加新用户](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)。


## <a name="test-your-sign-in"></a>测试登录 

此步骤旨在确保测试帐户可以使用 Tor 浏览器访问你的租户。

**测试登录：**

1. 以 **Alain Charon** 的身份登录到 [Azure 门户](https://portal.azure.com)。

2. 注销。 


## <a name="create-your-conditional-access-policy"></a>创建条件访问策略 

本快速入门中的方案使用 Tor 浏览器中的登录名生成检测到的“从匿名 IP 地址登录”风险事件。 此风险事件的风险级别为中等。 若要响应此风险事件，请将登录风险条件设置为中。 

此部分介绍如何创建所需的登录风险条件访问策略。 在策略中，设置：

|设置 |值|
|---     | --- |
| 用户  | Alain Charon  |
| 条件 | 登录风险，中等及以上 |
| 控制 | 阻止访问 |
 

![创建策略](./media/quickstart-sign-in-risk-policy/201.png)

 


**若要配置条件访问策略，请执行以下操作：**

1. 以全局管理员身份登录 [Azure 门户](https://portal.azure.com)。

2. 转到 [Azure AD Identity Protection 页](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview)。
 
3. 在“Azure AD Identity Protection”页上的“配置”部分中，单击“登录风险策略”。
 
4. 在策略页上的“分配”部分，单击“用户”。

5. 在“用户”页上，单击“选择用户”。

6. 在“选择用户”页上，选择“Alain Charon”，然后单击“选择”。

7. 在“用户”页上，单击“完成”。 

8. 在策略页上的“分配”部分，单击“条件”。

9. 在“条件”页上，单击“登录风险”。

10. 在“登录风险”页上，选择“中等及以上”，然后单击“选择”。 

11. 在“条件”页面上，单击“完成”。

12. 在策略页上的“控件”部分，单击“访问”。

13. 在“访问”页上，单击“允许访问”，选择“需要多重身份验证”，然后单击“选择”。

14. 在策略页上，单击“保存”。  


## <a name="test-your-conditional-access-policy"></a>测试条件访问策略

若要测试策略，请尝试使用 Tor 浏览器以 **Alan Charon** 身份登录到 [Azure 门户](https://portal.azure.com)。 条件访问策略应会阻止你的登录尝试。

![多重身份验证](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>清理资源

不再需要时，请删除测试用户、Tor 浏览器并禁用登录风险条件访问策略：

- 如果不知道如何删除 Azure AD 用户，请参阅[如何添加或删除用户](../fundamentals/add-users-azure-active-directory.md#delete-a-user)。

- 有关删除 Tor 浏览器的说明，请参阅[卸载](https://tb-manual.torproject.org/en-US/uninstalling.html)。


