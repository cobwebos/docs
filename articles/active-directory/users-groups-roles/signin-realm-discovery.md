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
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b6846c5f907c41db16e99883be7041a68357586
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608769"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 登录页的主领域发现

我们正在更改 Azure Active Directory (Azure AD) 登录行为，以便为新的身份验证方法腾出空间，同时提高可用性。 登录期间，Azure AD 确定用户需要进行身份验证的位置。 Azure AD 读取登录页上输入的用户名的组织和用户设置，以做出明智决策。 这向可支持 FIDO 2.0 等其他凭据免密码登陆的未来迈进了一步。

## <a name="home-realm-discovery-behavior"></a>主领域发现行为

从历史上看，通过提供在登录的域或通过某些旧版应用程序的主领域发现策略被控制主领域发现。 例如，在我们发现行为的 Azure Active Directory 用户可能错误键入其用户名，但仍会在其组织的凭据集合屏幕在到达。 当用户正确提供组织的域名"contoso.com"时，将发生这种情况。 此行为不允许进一步为单个用户自定义体验。

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
  
> [!IMPORTANT]
> 此功能可能会影响依赖于旧域级别主领域发现来强制执行联合身份验证的联合域。 有关何时将添加联合的域支持的更新，请参阅[期间登录 Microsoft 365 服务的主领域发现](https://azure.microsoft.com/en-us/updates/signin-hrd/)。 在此期间，某些组织具有经过培训员工登录的用户名的 Azure Active Directory 中不存在，但包含正确的域名，因为域名将路由用户当前到其组织的域终结点。 新的登录行为不允许此操作。 通知用户若要更正的用户名称，并不允许使用 Azure Active Directory 中不存在的用户名登录。
>
> 如果你或你的组织有依赖旧行为的做法，务必为组织管理员若要更新员工在登录和身份验证文档和培训员工使用其 Azure Active Directory 用户名进行登录。
  
如果必须使用新行为的问题，请将保留在您的意见**反馈**本文的部分。  

## <a name="next-steps"></a>后续步骤

[自定义登录品牌](../fundamentals/add-custom-domain.md)
