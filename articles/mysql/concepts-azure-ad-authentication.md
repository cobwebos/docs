---
title: Active Directory 身份验证-Azure Database for MySQL
description: 了解 Azure Active Directory 用于身份验证的概念 Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299016"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 通过 MySQL 进行身份验证

Microsoft Azure Active Directory （Azure AD）身份验证是使用 Azure AD 中定义的标识连接到 Azure Database for MySQL 的机制。
使用 Azure AD 身份验证，可以在一个中心位置管理数据库用户标识和其他 Microsoft 服务，从而简化权限管理。

> [!IMPORTANT]
> Azure Database for MySQL 的 Azure AD 身份验证目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 的好处包括：

- 以统一的方式跨 Azure 服务对用户进行身份验证
- 在单一位置管理密码策略和密码轮换
- Azure Active Directory 支持多种形式的身份验证，这样就无需存储密码
- 客户可以使用外部 (Azure AD) 组管理数据库权限。
- Azure AD authentication 使用 MySQL 数据库用户在数据库级别对标识进行身份验证
- 支持对连接到 Azure Database for MySQL 的应用程序进行基于令牌的身份验证

若要配置和使用 Azure Active Directory 身份验证，请使用以下过程：

1. 根据需要创建并填充用户标识 Azure Active Directory。
2. 可以选择关联或更改当前与 Azure 订阅关联的 Active Directory。
3. 为 Azure Database for MySQL 服务器创建 Azure AD 管理员。
4. 在您的数据库中创建映射到 Azure AD 标识的数据库用户。
5. 通过检索 Azure AD 标识的令牌并登录来连接到数据库。

> [!NOTE]
> 若要了解如何创建和填充 Azure AD，然后用 Azure Database for MySQL 配置 Azure AD，请参阅[Azure Database for MySQL 的 Azure AD 配置和登录](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="architecture"></a>体系结构

以下高级别关系图概述了使用 Azure AD 身份验证和 Azure Database for MySQL 进行身份验证的方式。 箭头表示通信路径。

![身份验证流][1]

## <a name="administrator-structure"></a>管理员结构

使用 Azure AD 身份验证时，MySQL 服务器有两个管理员帐户;原始 MySQL 管理员和 Azure AD 管理员。 只有基于 Azure AD 帐户的管理员可以在用户数据库中创建第一个 Azure AD 包含的数据库用户。 Azure AD 管理员登录名可以是 Azure AD 用户，也可以是 Azure AD 组。 当管理员为组帐户时，可以由任何组成员使用，从而为 MySQL 服务器启用多个 Azure AD 管理员。 使用组帐户作为管理员可通过允许在 Azure AD 中集中添加和删除组成员而无需更改 MySQL 服务器中的用户或权限，从而提高可管理性。 无论何时都仅可配置一个 Azure AD 管理员（一个用户或组）。

![管理结构][2]

## <a name="permissions"></a>权限

若要创建可通过 Azure AD 进行身份验证的新用户，您必须是设计 Azure AD 管理员。 此用户通过配置特定 Azure Database for MySQL 服务器的 Azure AD 管理员帐户进行分配。

若要创建新的 Azure AD 数据库用户，必须以 Azure AD 管理员身份连接。 在[配置和登录](howto-configure-sign-in-azure-ad-authentication.md)时，将为 Azure Database for MySQL Azure AD。

仅当为 Azure Database for MySQL 创建 Azure AD 管理员时，才可以进行 Azure AD 身份验证。 如果从服务器中删除了 Azure Active Directory 管理员，先前创建的现有 Azure Active Directory 用户将无法再使用其 Azure Active Directory 凭据连接到数据库。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 标识进行连接

Azure Active Directory 身份验证支持使用 Azure AD 标识连接到数据库的以下方法：

- Azure Active Directory 密码
- 集成式 Azure Active Directory
- 采用了 MFA 的通用 Azure Active Directory
- 使用 Active Directory 应用程序证书或客户端机密

对 Active Directory 进行身份验证后，即可检索令牌。 此令牌是用于登录的密码。

> [!NOTE]
> 有关如何使用 Active Directory 令牌进行连接的更多详细信息，请参阅[使用 Azure AD 的 Azure Database for MySQL 配置和登录](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="additional-considerations"></a>其他注意事项

- 在任何时候，都只能为 Azure Database for MySQL 服务器配置一个 Azure AD 管理员。
- 只有 MySQL Azure AD 管理员才能使用 Azure Active Directory 帐户最初连接到 Azure Database for MySQL。 Active Directory 管理员可以配置后续的 Azure AD 数据库用户。
- 如果用户已从 Azure AD 中删除，则该用户将无法再使用 Azure AD 进行身份验证，因此将无法再为该用户获取访问令牌。 在这种情况下，尽管匹配的用户仍将保留在数据库中，但不能与该用户连接到服务器。
> [!NOTE]
> 在令牌过期之前（从令牌发出到60分钟），仍可以完成已删除 Azure AD 用户的登录。  如果还从 Azure Database for MySQL 删除用户，则会立即撤消此访问权限。
- 如果从服务器中删除 Azure AD 管理员，则服务器将不再与 Azure AD 租户关联，因此将为服务器禁用所有 Azure AD 登录名。 从同一租户添加新的 Azure AD 管理员将重新启用 Azure AD 登录名。
- Azure Database for MySQL 使用用户的唯一 Azure AD 用户 ID （而不是使用用户名）将访问令牌与 Azure Database for MySQL 用户匹配。 这意味着，如果在 Azure AD 中删除 Azure AD 用户，并且创建了一个同名的新用户，Azure Database for MySQL 会认为该用户是不同的用户。 因此，如果从 Azure AD 删除了某个用户，然后添加了具有相同名称的新用户，则新用户将无法连接到现有用户。

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建和填充 Azure AD，然后用 Azure Database for MySQL 配置 Azure AD，请参阅[Azure Database for MySQL 的 Azure AD 配置和登录](howto-configure-sign-in-azure-ad-authentication.md)。
- 有关登录名和数据库用户 Azure Database for MySQL 的概述，请参阅[在 Azure Database for MySQL 中创建用户](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
