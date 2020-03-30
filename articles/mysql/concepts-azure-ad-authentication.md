---
title: 活动目录身份验证 - MySQL 的 Azure 数据库
description: 了解 Azure 活动目录的概念，以便使用 MySQL 的 Azure 数据库进行身份验证
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299016"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure 活动目录使用 MySQL 进行身份验证

Microsoft Azure 活动目录 （Azure AD） 身份验证是使用 Azure AD 中定义的身份连接到 MySQL 的 Azure 数据库的机制。
使用 Azure AD 身份验证，您可以在中心位置管理数据库用户标识和其他 Microsoft 服务，从而简化权限管理。

> [!IMPORTANT]
> MySQL Azure 数据库的 Azure AD 身份验证当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 的好处包括：

- 以统一的方式跨 Azure 服务对用户进行身份验证
- 在单个位置管理密码策略和密码轮换
- Azure 活动目录支持的多种形式的身份验证，无需存储密码
- 客户可以使用外部 (Azure AD) 组管理数据库权限。
- Azure AD 身份验证使用 MySQL 数据库用户在数据库级别验证标识
- 支持为 MySQL 连接到 Azure 数据库的应用程序提供基于令牌的身份验证

要配置和使用 Azure 活动目录身份验证，请使用以下过程：

1. 根据需要使用用户标识创建和填充 Azure 活动目录。
2. 可以选择关联或更改当前与 Azure 订阅关联的活动目录。
3. 为 MySQL 服务器的 Azure 数据库创建 Azure AD 管理员。
4. 在数据库中创建映射到 Azure AD 标识的数据库用户。
5. 通过检索 Azure AD 标识的令牌并登录连接到数据库。

> [!NOTE]
> 要了解如何创建和填充 Azure AD，然后使用 MySQL 的 Azure 数据库配置 Azure AD，请参阅[为 MySQL 配置 Azure 数据库并与 Azure AD 登录](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="architecture"></a>体系结构

以下高级关系图总结了身份验证如何使用 Azure AD 身份验证与 MySQL 的 Azure 数据库一起使用。 箭头表示通信路径。

![身份验证流][1]

## <a name="administrator-structure"></a>管理员结构

使用 Azure AD 身份验证时，MySQL 服务器有两个管理员帐户;因此，使用 Azure AD 身份验证时，MySQL 服务器有两个管理员帐户。原始 MySQL 管理员和 Azure AD 管理员。 只有基于 Azure AD 帐户的管理员可以在用户数据库中创建第一个 Azure AD 包含的数据库用户。 Azure AD 管理员登录名可以是 Azure AD 用户，也可以是 Azure AD 组。 当管理员是组帐户时，任何组成员都可以使用它，为 MySQL 服务器启用多个 Azure AD 管理员。 使用组帐户作为管理员，允许您在 Azure AD 中集中添加和删除组成员，而无需更改 MySQL 服务器中的用户或权限，从而增强可管理性。 无论何时都仅可配置一个 Azure AD 管理员（一个用户或组）。

![管理结构][2]

## <a name="permissions"></a>权限

要创建新用户，可以使用 Azure AD 进行身份验证，您必须是设计的 Azure AD 管理员。 此用户是通过为 MySQL 服务器配置特定 Azure 数据库的 Azure AD 管理员帐户来分配的。

要创建新的 Azure AD 数据库用户，必须作为 Azure AD 管理员进行连接。 这表现在[为 MySQL 的 Azure 数据库配置和登录 Azure 数据库](howto-configure-sign-in-azure-ad-authentication.md)。

仅当为 MySQL 为 Azure 数据库创建 Azure AD 管理员时，才可能进行任何 Azure AD 身份验证。 如果 Azure 活动目录管理员从服务器中删除，则以前创建的现有 Azure 活动目录用户无法再使用其 Azure 活动目录凭据连接到数据库。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 标识进行连接

Azure Active Directory 身份验证支持使用 Azure AD 标识连接到数据库的以下方法：

- Azure Active Directory 密码
- 集成式 Azure Active Directory
- 采用了 MFA 的通用 Azure Active Directory
- 使用活动目录应用程序证书或客户端机密

对活动目录进行身份验证后，然后检索令牌。 此令牌是您登录的密码。

> [!NOTE]
> 有关如何使用活动目录令牌进行连接的更多详细信息，请参阅[为 MySQL 配置 Azure 数据库的 Azure AD](howto-configure-sign-in-azure-ad-authentication.md)并登录。

## <a name="additional-considerations"></a>其他注意事项

- 任何时候都只能为 MySQL 服务器配置一个 Azure 数据库管理员。
- 只有 MySQL 的 Azure AD 管理员才能最初使用 Azure 活动目录帐户连接到 MySQL 的 Azure 数据库。 Active Directory 管理员可以配置后续的 Azure AD 数据库用户。
- 如果从 Azure AD 中删除用户，该用户将不再能够使用 Azure AD 进行身份验证，因此将无法再获取该用户的访问令牌。 在这种情况下，尽管匹配的用户仍将在数据库中，但无法与该用户连接到服务器。
> [!NOTE]
> 使用已删除的 Azure AD 用户登录仍可在令牌过期之前完成（令牌颁发最多 60 分钟）。  如果同时从 Azure 数据库中删除 MySQL 的用户，此访问权限将立即被吊销。
- 如果 Azure AD 管理员从服务器中删除，则服务器将不再与 Azure AD 租户关联，因此服务器将禁用所有 Azure AD 登录名。 从同一租户添加新的 Azure AD 管理员将重新启用 Azure AD 登录名。
- MySQL 的 Azure 数据库使用用户的唯一 Azure AD 用户 ID（而不是使用用户名）匹配 MySQL 用户对 Azure 数据库的访问令牌。 这意味着，如果在 Azure AD 中删除 Azure AD 用户，并且使用相同名称创建新用户，则 MySQL 的 Azure 数据库将认为该用户为其他用户。 因此，如果从 Azure AD 中删除用户，然后添加了同名的新用户，则新用户将无法与现有用户连接。

## <a name="next-steps"></a>后续步骤

- 要了解如何创建和填充 Azure AD，然后使用 MySQL 的 Azure 数据库配置 Azure AD，请参阅[为 MySQL 配置 Azure 数据库并与 Azure AD 登录](howto-configure-sign-in-azure-ad-authentication.md)。
- 有关 MySQL 的登录名和数据库用户的概述，请参阅[为 MySQL 在 Azure 数据库中创建用户](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
