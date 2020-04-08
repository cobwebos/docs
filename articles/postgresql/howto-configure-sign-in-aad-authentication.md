---
title: 使用 Azure 活动目录 - Azure 数据库用于后格雷SQL - 单个服务器
description: 了解如何设置 Azure 活动目录 （AAD） 以便使用 Azure 数据库进行 PostgreSQL - 单服务器身份验证
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804701"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure 活动目录使用 PostgreSQL 进行身份验证

本文将介绍如何使用 Azure 数据库为 PostgreSQL 配置 Azure 活动目录访问以及如何使用 Azure AD 令牌进行连接的步骤。

> [!IMPORTANT]
> 用于 PostgreSQL 的 Azure 数据库的 Azure AD 身份验证当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>设置 Azure AD 管理员用户

只有 Azure AD 管理员用户可以创建/启用用户进行基于 Azure AD 的身份验证。 我们建议不要将 Azure AD 管理员用于常规数据库操作，因为它具有提升的用户权限（例如，创建 B）。

要设置 Azure AD 管理员（可以使用用户或组），请按照以下步骤操作

1. 在 Azure 门户中，选择要为 Azure AD 启用的 PostgreSQL 的 Azure 数据库实例。
2. 在"设置"下，选择活动目录管理员：

![设置 Azure 广告管理员][2]

3. 在客户租户中选择有效的 Azure AD 用户，作为 Azure AD 管理员。

> [!IMPORTANT]
> 设置管理员时，新用户将添加到具有完全管理员权限的 PostgreSQL 服务器的 Azure 数据库中。 Azure 数据库中的 Azure Ad 管理员用户将具有 该角色`azure_ad_admin`。

每个 PostgreSQL 服务器只能创建一个 Azure AD 管理员，而另一个服务器的选择将覆盖为服务器配置的现有 Azure AD 管理员。 您可以指定 Azure AD 组，而不是单个用户，以具有多个管理员。 请注意，您随后将使用组名称登录以进行管理。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 连接到 Azure 数据库以进行 PostgreSQL

以下高级关系图总结了使用 Azure 数据库进行 Azure AD 身份验证的工作流，

![身份验证流][1]

我们设计了 Azure AD 集成，以便使用常见的 PostgreSQL 工具（如 psql），这些工具不了解 Azure AD，并且仅在连接到 PostgreSQL 时支持指定用户名和密码。 我们将 Azure AD 令牌作为密码传递，如上图所示。

我们目前已经测试了以下客户：

- psql 命令行（利用 PGPASSWORD 变量传递令牌，见下文）
- Azure 数据工作室（使用 PostgreSQL 扩展）
- 其他基于 libpq 的客户端（例如通用应用程序框架和 ORM）

> [!NOTE]
> 请注意，当前不支持将 Azure AD 令牌与 pgAdmin 一起使用，因为它对密码的硬编码限制为 256 个字符（令牌超过）。

以下是用户/应用程序需要执行以下描述的 Azure AD 身份验证的步骤：

### <a name="step-1-authenticate-with-azure-ad"></a>第 1 步：使用 Azure AD 进行身份验证

确保安装了 Azure [CLI。](/cli/azure/install-azure-cli)

调用 Azure CLI 工具以使用 Azure AD 进行身份验证。 它要求您提供 Azure AD 用户 ID 和密码。

```azurecli-interactive
az login
```

此命令将启动到 Azure AD 身份验证页的浏览器窗口。

> [!NOTE]
> 您还可以使用 Azure 云外壳执行这些步骤。
> 请注意，在 Azure 云外壳中检索 Azure AD 访问令牌时，需要显式调用`az login`并重新登录（在带有代码的单独窗口中）。 该登录后，`get-access-token`命令将按预期工作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>第 2 步：检索 Azure AD 访问令牌

调用 Azure CLI 工具以从步骤 1 获取 Azure AD 身份验证用户的访问令牌，以访问 PostgreSQL 的 Azure 数据库。

示例（公共云）：

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述资源值必须完全按照所示指定。 对于其他云，可以使用以下情况来查看资源值：

```azurecli-interactive
az cloud show
```

对于 Azure CLI 版本 2.0.71 及更高版本，可以在以下更方便版本中为所有云指定该命令：

```azurecli-interactive
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

令牌是一个 Base 64 字符串，用于编码有关经过身份验证的用户的所有信息，该字符串针对 PostgreSQL 服务的 Azure 数据库。

> [!NOTE]
> 访问令牌的有效期在 5 分钟到 60 分钟之间。 我们建议您在启动 PostgreSQL 的 Azure 数据库登录之前获取访问令牌。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>第 3 步：使用令牌作为密码使用 PostgreSQL 登录

连接时，您需要使用访问令牌作为 PostgreSQL 用户密码。

使用`psql`命令行客户端时，需要通过`PGPASSWORD`环境变量传递访问令牌，因为访问令牌超过可以直接接受的`psql`密码长度：

窗口示例：

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 示例：

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

现在，您可以像通常一样启动与 Azure 数据库的连接， 以便进行 PostgreSQL：

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

现在，使用 Azure AD 身份验证对 PostgreSQL 服务器进行身份验证。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在 Azure 数据库中为 PostgreSQL 创建 Azure AD 用户

要将 Azure AD 用户添加到用于 PostgreSQL 数据库的 Azure 数据库，请在连接后执行以下步骤（请参阅后面有关如何连接的部分）：

1. 首先，确保 Azure AD`<user>@yourtenant.onmicrosoft.com`用户是 Azure AD 租户中的有效用户。
2. 以 Azure AD 管理员用户身份登录到 Azure 数据库以获取 PostgreSQL 实例。
3. 在`<user>@yourtenant.onmicrosoft.com`Azure 数据库中为 PostgreSQL 创建角色。
4. 成为`<user>@yourtenant.onmicrosoft.com`角色的成员azure_ad_user。 这只能提供给 Azure AD 用户。

**例子：**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 通过 Azure AD 对用户进行身份验证不会授予用户访问 PostgreSQL 数据库 Azure 数据库中的对象的任何权限。 您必须手动授予用户所需的权限。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>在 Azure 数据库中为 PostgreSQL 创建 Azure AD 组

要启用 Azure AD 组以访问数据库，请使用与用户相同的机制，而是指定组名称：

**例子：**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

登录时，组成员将使用其个人访问令牌，但使用指定为用户名的组名称进行签名。

## <a name="token-validation"></a>令牌验证

Azure 数据库中的 Azure AD 身份验证用于 PostgreSQL 可确保用户存在于 PostgreSQL 服务器中，并且它通过验证令牌的内容来检查令牌的有效性。 执行以下令牌验证步骤：

- 令牌由 Azure AD 签名，未被篡改
- 令牌由 Azure AD 颁发给与服务器关联的租户
- 令牌尚未过期
- 令牌用于 PostgreSQL 资源的 Azure 数据库（而不是另一个 Azure 资源）

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>将现有 PostgreSQL 用户迁移到基于 Azure AD 的身份验证

您可以为现有用户启用 Azure AD 身份验证。 有两种情况需要考虑：

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>案例 1：PostgreSQL 用户名与 Azure AD 用户主体名称匹配

如果现有用户已匹配 Azure AD 用户名，则可以将`azure_ad_user`角色授予他们，以便启用它们进行 Azure AD 身份验证：

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

他们现在将能够使用 Azure AD 凭据登录，而不是使用以前配置的 PostgreSQL 用户密码。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>案例 2：PostgreSQL 用户名不同于 Azure AD 用户主体名称

如果 PostgreSQL 用户在 Azure AD 中不存在或具有其他用户名，则可以使用 Azure AD 组作为此 PostgreSQL 用户进行身份验证。 通过创建名称与 PostgreSQL 用户匹配的 Azure AD 组，然后将角色azure_ad_user授予现有 PostgreSQL 用户，可以将现有用于 PostgreSQL 用户的现有 Azure 数据库迁移到 Azure AD：

```sql
GRANT azure_ad_user TO "DBReadUser";
```

这假定您在 Azure AD 中创建了一个组"DBReadUser"。 属于该组的用户现在可以作为此用户登录到数据库。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据库查看 Azure 活动目录身份验证的总体概念，用于 PostgreSQL - 单个服务器](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
