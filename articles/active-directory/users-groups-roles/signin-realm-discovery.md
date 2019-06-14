---
title: 登录身份验证期间的用户名查找 - Azure Active Directory | Microsoft Docs
description: 屏幕上的消息如何反映登录期间的用户名查找
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db627359b75aa0ea19e30a8d22bcacaa3409cb4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418222"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 登录页的主领域发现

我们正在更改 Azure Active Directory (Azure AD) 登录行为，以便为新的身份验证方法腾出空间，同时提高可用性。 登录期间，Azure AD 确定用户需要进行身份验证的位置。 Azure AD 读取登录页上输入的用户名的组织和用户设置，以做出明智决策。 此技术为将来支持 FIDO 2.0 等附加凭据的无密码登录进一步打下了基础。

## <a name="home-realm-discovery-behavior"></a>主领域发现的行为

一直以来，主领域发现都是由登录时提供的域调控的，或者由某些传统应用程序的主领域发现策略调控的。 例如，在采用我们的发现行为时，Azure Active Directory 用户可能在键入错误的用户名后，仍能打开其组织的凭据收集屏幕。 当用户正确提供组织的域名“contoso.com”时，就会发生这种情况。 此行为不允许对个人用户的体验进行精细的自定义。

为了支持更多样化的凭据并提高可用性，现已更新登录期间的 Azure Active Directory 用户名查找行为。 新的行为可以根据登录页上输入的用户名，通过读取租户和用户级别的设置来做出明智的决策。 为了实现此目的，Azure Active Directory 将检查登录页上输入的用户名是否在用户的指定域中存在，或者将用户重定向到可输入凭据的位置。

此项工作的另一个好处是改进了错误消息。 下面是登录到仅支持 Azure Active Directory 用户的应用程序时，显示的一些经过改进的错误消息示例。

- 键入错误的用户名，或者用户名尚未同步到 Azure AD：
  
    ![键入错误的用户名，或者找不到该用户名](./media/signin-realm-discovery/typo-username.png)
  
- 键入错误的域名：
  
    ![键入错误的域名，或者找不到该域名](./media/signin-realm-discovery/typo-domain.png)
  
- 用户尝试使用已知的使用者域登录：
  
    ![使用已知的使用者域登录](./media/signin-realm-discovery/consumer-domain.png)
  
- 键入错误的密码，但用户名正确：  
  
    ![键入错误的密码，但用户名正确](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 此功能可能对依赖于使用旧式域级别主领域发现来强制联合的联合域造成影响。 有关何时将添加联合的域支持的更新，请参阅[期间登录 Microsoft 365 服务的主领域发现](https://azure.microsoft.com/updates/signin-hrd/)。 同时，某些组织已经为其员工提供了培训，让他们使用 Azure Active Directory 中不存在的、但包含正确域名的用户名登录，因为域名目前会将用户路由到其组织的域终结点。 新的登录行为不允许此操作。 系统会告知用户更正用户名，且不允许他们使用 Azure Active Directory 中不存在的用户名登录。
>
> 如果你或你的组织在实践中依赖于旧行为，则组织管理员必须更新员工登录与身份验证文档，并为员工提供培训，让他们使用其 Azure Active Directory 用户名登录。
  
如果你对新的行为存有疑虑，请在本文的“反馈”部分留言。   

## <a name="next-steps"></a>后续步骤

[自定义登录品牌](../fundamentals/add-custom-domain.md)
