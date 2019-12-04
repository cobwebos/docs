---
title: 使用 Azure Active Directory Azure Database for PostgreSQL-单一服务器
description: 了解如何设置 Azure Database for PostgreSQL 单一服务器的身份验证 Azure Active Directory （AAD）
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c929ac1c171547a4ff485fc43f0f329440f9c3b5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763634"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure Active Directory 通过 PostgreSQL 进行身份验证

本文将指导你完成如何使用 Azure Database for PostgreSQL 配置 Azure Active Directory 访问以及如何使用 Azure AD 令牌进行连接的步骤。

> [!IMPORTANT]
> Azure Database for PostgreSQL 的 Azure AD 身份验证目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>设置 Azure AD 管理员用户

只有 Azure AD 管理员用户才能创建/启用基于 Azure AD 身份验证的用户。 若要创建和 Azure AD 管理员用户，请按照以下步骤操作

1. 在 Azure 门户中，选择要为 Azure AD 启用 Azure Database for PostgreSQL 的实例。
2. 在 "设置" 下，选择 Active Directory 管理员：

![设置 azure ad 管理员][2]

3. 选择要 Azure AD 管理员的客户租户中有效的 Azure AD 用户。

> [!IMPORTANT]
> 设置管理员时，会将新用户添加到具有完全权限管理员权限的 Azure Database for PostgreSQL 服务器。 Azure Database for PostgreSQL 中的 Azure AD 管理员用户将拥有角色 `azure_ad_admin`。

每个 PostgreSQL 服务器只能创建一个 Azure AD 管理员，选择另一个管理员将覆盖为服务器配置的现有 Azure AD 管理员。 可以指定 Azure AD 组，而不是单个用户来拥有多个管理员。 请注意，你随后将用组名称登录以进行管理。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在 Azure Database for PostgreSQL 中创建 Azure AD 用户

若要将 Azure AD 用户添加到 Azure Database for PostgreSQL 数据库，请在连接后执行以下步骤（请参阅有关如何连接的后面部分）：

1. 首先确保 Azure AD 用户 `<user>@yourtenant.onmicrosoft.com` 是 Azure AD 租户中的有效用户。
2. 以 Azure AD 管理员用户身份登录到你的 Azure Database for PostgreSQL 实例。
3. 在 Azure Database for PostgreSQL 中创建角色 `<user>@yourtenant.onmicrosoft.com`。
4. 使 `<user>@yourtenant.onmicrosoft.com` 成为角色 azure_ad_user 的成员。 必须仅向 Azure AD 用户提供此。

**示例：**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 通过 Azure AD 对用户进行身份验证时，不会向用户授予访问 Azure Database for PostgreSQL 数据库中的对象的任何权限。 您必须手动向用户授予所需的权限。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>在 Azure Database for PostgreSQL 中创建 Azure AD 组

若要为数据库 Azure AD 启用访问权限，请使用与用户相同的机制，但要指定组名称，请执行以下操作：

**示例：**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

登录时，组的成员将使用其个人访问令牌，但使用指定为用户名的组名称进行签名。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 连接到 Azure Database for PostgreSQL

下面的高级关系图汇总了对 Azure Database for PostgreSQL 使用 Azure AD 身份验证的工作流：

![身份验证流][1]

我们已经设计了 Azure AD 集成，使用 psql 等常见 PostgreSQL 工具，这些工具不 Azure AD 感知，仅支持在连接到 PostgreSQL 时指定用户名和密码。 我们会将 Azure AD 令牌作为密码传递，如上图所示。

我们当前已经测试了以下客户端：

- psql 命令行（使用 PGPASSWORD 变量传递令牌，请参阅下文）
- Azure Data Studio （使用 PostgreSQL 扩展）
- 其他基于 libpq 的客户端（例如，常用应用程序框架和 Orm）

> [!NOTE]
> 请注意，目前不支持将 Azure AD 令牌与 pgAdmin 一起使用，因为它具有256字符的硬编码限制（标记超过）。

以下是用户/应用程序执行身份验证所需的步骤，Azure AD 如下所述：

### <a name="step-1-authenticate-with-azure-ad"></a>步骤1：用 Azure AD 进行身份验证

请确保已[安装 Azure CLI](/cli/azure/install-azure-cli)。

调用 Azure CLI 工具来向 Azure AD 进行身份验证。 它要求你为 Azure AD 的用户 ID 和密码。

```
az login
```

此命令将启动 "Azure AD 身份验证" 页的浏览器窗口。

> [!NOTE]
> 你还可以使用 Azure Cloud Shell 来执行这些步骤。
> 请注意，在 Azure Cloud Shell 中检索 Azure AD 访问令牌时，需要显式调用 `az login` 并再次登录（在单独的窗口中包含代码）。 在该登录后，`get-access-token` 命令将按预期方式工作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>步骤2：检索 Azure AD 访问令牌

调用 Azure CLI 工具获取步骤1中经过身份验证的 Azure AD 用户访问 Azure Database for PostgreSQL 的访问令牌。

示例（适用于公有云）：

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述资源值必须完全按所示方式指定。 对于其他云，可以使用查看资源值：

```shell
az cloud show
```

对于 Azure CLI 版本2.0.71 和更高版本，可在以下更方便的所有云版本中指定命令：

```shell
az account get-access-token --resource-type oss-rdbms
```

身份验证成功后，Azure AD 将返回访问令牌：

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

该令牌是一个基本的64字符串，它对有关经过身份验证的用户的所有信息进行编码，并将其作为 Azure Database for PostgreSQL 服务的目标。

> [!NOTE]
> 访问令牌的有效期介于5分钟到60分钟之间。 建议你在开始登录到 Azure Database for PostgreSQL 之前获取访问令牌。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>步骤3：使用令牌作为密码以使用 PostgreSQL 登录

在连接时，需要使用访问令牌作为 PostgreSQL 用户密码。

使用 `psql` 命令行客户端时，需要通过 `PGPASSWORD` 环境变量传递访问令牌，因为访问令牌超出了 `psql` 可以直接接受的密码长度：

Windows 示例：

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 示例：

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

现在，你可以使用 Azure Database for PostgreSQL 启动连接，如下所示：

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

你现在可以使用 Azure AD 身份验证向 PostgreSQL 服务器进行身份验证。

## <a name="token-validation"></a>令牌验证

Azure Database for PostgreSQL 中的 Azure AD 身份验证确保用户存在于 PostgreSQL 服务器中，并通过验证令牌的内容来检查令牌的有效性。 执行以下令牌验证步骤：

-   令牌由 Azure AD 签名，但未被篡改
-   为与服务器关联的租户 Azure AD 颁发令牌
-   令牌尚未过期
-   令牌适用于 Azure Database for PostgreSQL 资源（而不是其他 Azure 资源）

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>将现有 PostgreSQL 用户迁移到基于 Azure AD 的身份验证

你可以为现有用户启用 Azure AD 身份验证。 需要考虑以下两种情况：

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>案例1： PostgreSQL 用户名与 Azure AD 用户主体名称匹配

如果你的现有用户已与 Azure AD 用户名相匹配，则你可以向其授予 `azure_ad_user` 角色，以便为其启用 Azure AD 身份验证：

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

它们现在可以使用 Azure AD 凭据登录，而不是使用以前配置的 PostgreSQL 用户密码。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>案例2： PostgreSQL 用户名与 Azure AD 用户主体名称不同

如果 PostgreSQL 用户在 Azure AD 中不存在或者具有不同的用户名，你可以使用 Azure AD 组作为此 PostgreSQL 用户进行身份验证。 可以通过创建一个名称与 PostgreSQL 用户匹配的 Azure AD 组，然后将角色 azure_ad_user 授予现有的 PostgreSQL 用户，来将现有 Azure Database for PostgreSQL 用户迁移到 Azure AD 中：

```sql
GRANT azure_ad_user TO "DBReadUser";
```

这假定你已在 Azure AD 中创建了组 "DBReadUser"。 属于此组的用户现在可以作为此用户登录到数据库。

## <a name="next-steps"></a>后续步骤

* 查看[Azure Database for PostgreSQL 单一服务器 Azure Active Directory 身份验证](concepts-aad-authentication.md)的总体概念

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
