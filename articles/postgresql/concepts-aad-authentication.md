---
title: 活动目录身份验证 - Azure 数据库，用于 PostgreSQL - 单个服务器
description: 了解 Azure 活动目录的概念，以便使用 Azure 数据库进行身份验证，用于 PostgreSQL - 单服务器
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ec853657d6dd1f3b019d8a414cfa28edc1083b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769908"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure 活动目录使用 PostgreSQL 进行身份验证

Microsoft Azure 活动目录 （Azure AD） 身份验证是使用 Azure AD 中定义的标识连接到 Azure 数据库的机制。
使用 Azure AD 身份验证，您可以在中心位置管理数据库用户标识和其他 Microsoft 服务，从而简化权限管理。

> [!IMPORTANT]
> 用于 PostgreSQL 的 Azure 数据库的 Azure AD 身份验证当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 的好处包括：

- 以统一的方式跨 Azure 服务对用户进行身份验证
- 在单个位置管理密码策略和密码轮换
- Azure 活动目录支持的多种形式的身份验证，无需存储密码
- 客户可以使用外部 (Azure AD) 组管理数据库权限。
- Azure AD 身份验证使用 PostgreSQL 数据库角色在数据库级别验证标识
- 支持基于令牌的身份验证，适用于连接到 Azure 数据库的应用程序，用于 PostgreSQL

要配置和使用 Azure 活动目录身份验证，请使用以下过程：

1. 根据需要使用用户标识创建和填充 Azure 活动目录。
2. 可以选择关联或更改当前与 Azure 订阅关联的活动目录。
3. 为 PostgreSQL 服务器创建 Azure 数据库的 Azure AD 管理员。
4. 在数据库中创建映射到 Azure AD 标识的数据库用户。
5. 通过检索 Azure AD 标识的令牌并登录连接到数据库。

> [!NOTE]
> 要了解如何创建和填充 Azure AD，然后使用 Azure 数据库为 PostgreSQL 配置 Azure AD，请参阅[为 PostgreSQL 配置 Azure 数据库并登录 Azure 数据库](howto-configure-sign-in-aad-authentication.md)。

## <a name="architecture"></a>体系结构

以下高级关系图总结了身份验证如何使用 Azure AD 身份验证与 Azure 数据库进行 PostgreSQL。 箭头表示通信路径。

![身份验证流][1]

## <a name="administrator-structure"></a>管理员结构

使用 Azure AD 身份验证时，PostgreSQL 服务器有两个管理员帐户;原始 PostgreSQL 管理员和 Azure AD 管理员。 只有基于 Azure AD 帐户的管理员可以在用户数据库中创建第一个 Azure AD 包含的数据库用户。 Azure AD 管理员登录名可以是 Azure AD 用户，也可以是 Azure AD 组。 当管理员是组帐户时，任何组成员都可以使用它，为 PostgreSQL 服务器启用多个 Azure AD 管理员。 使用组帐户作为管理员，允许您在 Azure AD 中集中添加和删除组成员，而无需更改 PostgreSQL 服务器中的用户或权限，从而增强可管理性。 无论何时都仅可配置一个 Azure AD 管理员（一个用户或组）。

![管理结构][2]

## <a name="permissions"></a>权限

要创建可以使用 Azure AD 进行身份验证的新用户，必须在数据库中`azure_ad_admin`具有该角色。 此角色是通过为 PostgreSQL 服务器配置特定 Azure 数据库的 Azure AD 管理员帐户来分配的。

要创建新的 Azure AD 数据库用户，必须作为 Azure AD 管理员进行连接。 这表现在[为 PostgreSQL 的 Azure 数据库配置和登录 Azure AD](howto-configure-sign-in-aad-authentication.md)中。

仅当为 PostgreSQL 为 Azure 数据库创建 Azure AD 管理员时，才可能进行任何 Azure AD 身份验证。 如果 Azure 活动目录管理员从服务器中删除，则以前创建的现有 Azure 活动目录用户无法再使用其 Azure 活动目录凭据连接到数据库。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 标识进行连接

Azure Active Directory 身份验证支持使用 Azure AD 标识连接到数据库的以下方法：

- Azure Active Directory 密码
- 集成式 Azure Active Directory
- 采用了 MFA 的通用 Azure Active Directory
- 使用活动目录应用程序证书或客户端机密

对活动目录进行身份验证后，然后检索令牌。 此令牌是您登录的密码。

> [!NOTE]
> 有关如何使用活动目录令牌进行连接的更多详细信息，请参阅[为 PostgreSQL 配置 Azure 数据库的 Azure AD](howto-configure-sign-in-aad-authentication.md)并登录。

## <a name="additional-considerations"></a>其他注意事项

- 为了增强可管理性，建议将一个专用 Azure AD 组预配为管理员。
- 任何时候都只能为 PostgreSQL 服务器配置一个 Azure AD 管理员（用户或组）。
- 只有 PostgreSQL 的 Azure AD 管理员才能最初使用 Azure 活动目录帐户连接到 PostgreSQL 的 Azure 数据库。 Active Directory 管理员可以配置后续的 Azure AD 数据库用户。
- 如果从 Azure AD 中删除用户，该用户将不再能够使用 Azure AD 进行身份验证，因此将无法再获取该用户的访问令牌。 在这种情况下，虽然匹配角色仍将在数据库中，但无法使用该角色连接到服务器。
> [!NOTE]
> 使用已删除的 Azure AD 用户登录仍可在令牌过期之前完成（令牌颁发最多 60 分钟）。  如果还从 Azure 数据库中删除用户 PostgreSQL，则此访问权限将立即被吊销。
- 如果 Azure AD 管理员从服务器中删除，则服务器将不再与 Azure AD 租户关联，因此服务器将禁用所有 Azure AD 登录名。 从同一租户添加新的 Azure AD 管理员将重新启用 Azure AD 登录名。
- PostgreSQL 的 Azure 数据库使用用户的唯一 Azure AD 用户 ID（而不是使用用户名）匹配对 PostgreSQL 角色的 Azure 数据库的访问令牌。 这意味着，如果在 Azure AD 中删除 Azure AD 用户，并且使用相同名称创建新用户，则 PostgreSQL 的 Azure 数据库将考虑为其他用户。 因此，如果从 Azure AD 中删除用户，然后添加了同名的新用户，则新用户将无法与现有角色连接。 为此，Azure 数据库的 PostgreSQL Azure AD 管理员必须撤消，然后授予用户"azure_ad_user"以刷新 Azure AD 用户 ID 的角色。

## <a name="next-steps"></a>后续步骤

- 要了解如何创建和填充 Azure AD，然后使用 Azure 数据库为 PostgreSQL 配置 Azure AD，请参阅[为 PostgreSQL 配置 Azure 数据库并登录 Azure 数据库](howto-configure-sign-in-aad-authentication.md)。
- 有关登录名、用户和数据库角色的概述，请参阅[在 Azure 数据库中为 PostgreSQL - 单服务器创建用户](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
