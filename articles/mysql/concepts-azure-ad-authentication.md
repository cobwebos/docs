---
title: Active Directory 身份验证 - Azure Database for MySQL
description: 了解使用 Azure Active Directory 向 Azure Database for MySQL 进行身份验证的概念
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 942789d5541e796247af2dbdf9371cce4701105e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171899"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 向 MySQL 进行身份验证

Microsoft Azure Active Directory (Azure AD) 身份验证是一种使用 Azure AD 中定义的标识连接到 Azure Database for MySQL 的机制。
通过 Azure AD 身份验证，可以在一个中心位置集中管理数据库用户标识和其他 Microsoft 服务，从而简化权限管理。

使用 Azure AD 带来的优势包括：

- 以统一的方式跨 Azure 服务对用户进行身份验证
- 在统一的位置管理密码策略和密码轮换
- Azure Active Directory 支持多种形式的身份验证，无需存储密码
- 客户可以使用外部 (Azure AD) 组管理数据库权限。
- Azure AD 身份验证使用 MySQL 数据库用户在数据库级别对标识进行身份验证
- 支持对连接到 Azure Database for MySQL 的应用程序进行基于令牌的身份验证

若要配置和使用 Azure Active Directory 身份验证，请遵循以下过程：

1. 根据需要，创建 Azure Active Directory，并使用用户标识进行填充。
2. （可选）关联 Active Directory，或更改当前与 Azure 订阅关联的 Active Directory。
3. 创建 Azure Database for MySQL 服务器的 Azure AD 管理员。
4. 在数据库中创建可映射到 Azure AD 标识的数据库用户。
5. 通过检索 Azure AD 标识的令牌并登录，连接到数据库。

> [!NOTE]
> 若要了解如何创建和填充 Azure AD，并为 Azure Database for PostgreSQL 配置 Azure AD，请参阅[为 Azure Database for MySQL 配置 Azure AD 并使用它登录](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="architecture"></a>体系结构

以下概要图概述了对 Azure Database for MySQL 使用 Azure AD 身份验证进行身份验证的工作原理。 箭头表示通信路径。

![身份验证流][1]

## <a name="administrator-structure"></a>管理员结构

使用 Azure AD 身份验证时，有两个 MySQL 服务器管理员帐户：原始 MySQL 管理员和 Azure AD 管理员。 只有基于 Azure AD 帐户的管理员可以在用户数据库中创建第一个 Azure AD 包含的数据库用户。 Azure AD 管理员登录名可以是 Azure AD 用户，也可以是 Azure AD 组。 当管理员为组帐户时，可以由任何组成员使用，从而为 MySQL 服务器启用多个 Azure AD 管理员。 以管理员身份使用组帐户可便于集中添加和删除 Azure AD 中的组成员，而无需更改 MySQL 服务器中的用户或权限，从而提高了可管理性。 无论何时都仅可配置一个 Azure AD 管理员（一个用户或组）。

![管理结构][2]

## <a name="permissions"></a>权限

若要新建可使用 Azure AD 进行身份验证的用户，必须具有指定的 Azure AD 管理员角色。 此用户是通过为特定 Azure Database for MySQL 服务器配置 Azure AD 管理员帐户来分配的。

若要新建 Azure AD 数据库用户，必须以 Azure AD 管理员身份连接。 [为 Azure Database for MySQL 配置 Azure AD 并使用它登录](howto-configure-sign-in-azure-ad-authentication.md)中对此进行了演示。

只有在为 Azure Database for MySQL 创建 Azure AD 管理员后，才能进行任何 Azure AD 身份验证。 如果已从服务器删除 Azure Active Directory 管理员，先前创建的现有 Azure Active Directory 用户便无法再使用其 Azure Active Directory 凭据连接到数据库。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 标识进行连接

Azure Active Directory 身份验证支持使用 Azure AD 标识连接到数据库的以下方法：

- Azure Active Directory 密码
- 集成式 Azure Active Directory
- 采用了 MFA 的通用 Azure Active Directory
- 使用 Active Directory 应用程序证书或客户端密码
- [托管标识](howto-connect-with-managed-identity.md)

执行 Active Directory 身份验证后，就可以检索令牌了。 此令牌是用于登录的密码。

请注意，目前仅 Azure AD 用户角色支持管理操作，例如添加新用户。

> [!NOTE]
> 若要详细了解如何使用 Active Directory 令牌进行连接，请参阅[为 Azure Database for MySQL 配置 Azure AD 并使用它登录](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="additional-considerations"></a>其他注意事项

- Azure Active Directory 身份验证仅适用于 MySQL 5.7 和更高版本。
- 无论何时都只能为 Azure Database for MySQL 服务器配置一个 Azure AD 管理员。
- 只有 MySQL 的 Azure AD 管理员最初可以使用 Azure Active Directory 帐户连接到 Azure Database for MySQL。 Active Directory 管理员可以配置后续的 Azure AD 数据库用户。
- 如果已从 Azure AD 中删除某个用户，此用户就无法再使用 Azure AD 进行身份验证，所以也就无法再获取此用户的访问令牌。 在这种情况下，尽管匹配用户仍保留在数据库中，但无法使用此用户连接到服务器。
> [!NOTE]
> 在令牌到期前（自令牌颁发起最长 60 分钟后到期），仍可以使用已删除的 Azure AD 用户进行登录。  如果同时还从 Azure Database for MySQL 中删除了用户，此访问权限会立即撤销。
- 如果从服务器中删除 Azure AD 管理员，那么服务器将不再与 Azure AD 租户关联，因此也就会对服务器禁用所有 Azure AD 登录。 从同一租户中添加新的 Azure AD 管理员会重新启用 Azure AD 登录。
- Azure Database for MySQL 使用用户的唯一 Azure AD 用户 ID（而不是用户名），将访问令牌与 Azure Database for MySQL 用户进行匹配。 也就是说，如果你在 Azure AD 中删除 Azure AD 用户，然后新建了一个同名用户，那么 Azure Database for MySQL 会认为这是一个不同的用户。 因此，如果你从 Azure AD 中删除某个用户，然后添加了一个同名的新用户，那么新用户将无法使用现有用户进行连接。

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建和填充 Azure AD，并为 Azure Database for PostgreSQL 配置 Azure AD，请参阅[为 Azure Database for MySQL 配置 Azure AD 并使用它登录](howto-configure-sign-in-azure-ad-authentication.md)。
- 如需 Azure Database for MySQL 的登录和数据库用户概述，请参阅[在 Azure Database for MySQL 中创建用户](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
