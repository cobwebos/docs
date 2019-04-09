---
title: 登录身份验证期间的 Azure Active Directory 用户名查找 |Microsoft Docs
description: 如何屏幕消息传送在登录期间反映用户名查找
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007493"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>登录过程中 Microsoft 365 服务的主领域发现

我们正在更改我们 Azure Active Directory (Azure AD) 单一登录行为以留出空间的新身份验证方法和提高可用性。 在登录期间，Azure AD 确定用户需要进行身份验证。 Azure AD 可让通过阅读在登录页上输入的用户名的组织和用户设置的明智决策。 这是更好地无密码的将来，使其他凭据，例如 FIDO 2.0。

## <a name="home-realm-discovery-behavior"></a>主领域发现行为

从历史上看，通过提供在登录的域或通过某些旧版应用程序的主领域发现策略被控制主领域发现。 例如，在旧的发现行为的 Azure Active Directory 用户可能错误键入其用户名，但仍会在其组织的凭据集合屏幕在到达。 当用户正确提供组织的域名"contoso.com"时，将发生这种情况。 此行为不允许进行精细的控制自定义为单个用户的体验。

若要支持更广范围的凭据，并提高可用性，在登录过程中的 Azure Active Directory 用户名查找行为现已更新。 新行为可通过明智的决策读取租户和用户级别设置基于在登录页上输入的用户名。 若要实现此目的，Azure Active Directory 将检查以查看登录页输入的用户名是否存在指定的域中或提供其凭据将用户重定向。 

这项工作的另一个好处是改进的错误消息。 以下是错误的一些示例改进消息传送支持仅限 Azure Active Directory 用户的应用程序中登录时。

1. 输入错误的用户名或用户名具有尚未同步到 Azure AD:
  
    ![键入了错误或找不到用户名](./media/signin-realm-discovery/typo-username.png)
  
2. 拼错的域名：
  
    ![键入了错误或找不到的域名](./media/signin-realm-discovery/typo-domain.png)
  
3. 用户尝试使用已知的使用者域登录：
  
    ![使用已知的使用者域登录](./media/signin-realm-discovery/consumer-domain.png)
  
4. 输入错误密码，但是准确的用户名：  
  
    ![密码是使用适当的用户名输入错误](./media/signin-realm-discovery/incorrect-password.png)
  
## <a name="additional-info"></a>其他信息

改进了登录用户体验，除了此更改还包括可帮助减轻大规模用户名枚举滥用的机制。

此更改对托管域最初的目标和开始推出在 2019 年 5，但不会开始实施到联盟域 2019年结束。 联合域的确切推出日期取决于客户的反馈。

> [!IMPORTANT]
> 此功能将依赖于旧域级别主领域发现来强制执行联合身份验证的联合域对产生重大影响。 某些组织具有经过培训员工登录的用户名的 Azure Active Directory 中不存在，但包含正确的域名，因为域名将路由用户当前到其组织的域终结点。 新的登录行为不允许此操作。 通知用户若要更正的用户名称，并不允许使用 Azure Active Directory 中不存在的用户名登录。
>
> 如果你或你的组织有依赖旧行为的做法，务必为组织管理员若要更新员工在登录和身份验证文档和培训员工使用其 Azure Active Directory 用户名进行登录。
  
如果必须使用新行为的问题，请将中的备注**反馈**本文的部分。  

## <a name="next-steps"></a>后续步骤

[自定义登录品牌](../fundamentals/add-custom-domain.md)