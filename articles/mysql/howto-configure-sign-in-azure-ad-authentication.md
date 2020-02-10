---
title: 使用 Azure Active Directory Azure Database for MySQL-单一服务器
description: 了解如何设置 Azure Active Directory （Azure AD），以便通过 Azure Database for MySQL 单一服务器进行身份验证
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: bb3a8c94b377fb9c9150945ec4cf5980e006dd34
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110608"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 通过 MySQL 进行身份验证

本文将指导你完成如何使用 Azure Database for MySQL 配置 Azure Active Directory 访问以及如何使用 Azure AD 令牌进行连接的步骤。

> [!IMPORTANT]
> Azure Database for MySQL 的 Azure AD 身份验证目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>设置 Azure AD 管理员用户

只有 Azure AD 管理员用户才能创建/启用基于 Azure AD 身份验证的用户。 若要创建和 Azure AD 管理员用户，请按照以下步骤操作

1. 在 Azure 门户中，选择要为 Azure AD 启用 Azure Database for MySQL 的实例。
2. 在 "设置" 下，选择 Active Directory 管理员：

![设置 azure ad 管理员][2]

3. 选择要 Azure AD 管理员的客户租户中有效的 Azure AD 用户。

> [!IMPORTANT]
> 设置管理员时，会将新用户添加到具有完全权限管理员权限的 Azure Database for MySQL 服务器。

每个 MySQL 服务器只能创建一个 Azure AD 管理员，选择另一个管理员将覆盖为服务器配置的现有 Azure AD 管理员。

在将来的版本中，我们将支持指定 Azure AD 组而不是单个用户来拥有多个管理员，但目前尚不支持此项。

配置管理员后，你现在可以登录：

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>使用 Azure AD 连接到 Azure Database for MySQL

下面的高级关系图汇总了对 Azure Database for MySQL 使用 Azure AD 身份验证的工作流：

![身份验证流][1]

我们已将 Azure AD 集成设计为使用 mysql CLI 等常见的 MySQL 工具，这些工具不 Azure AD 感知，仅支持在连接到 MySQL 时指定用户名和密码。 我们会将 Azure AD 令牌作为密码传递，如上图所示。

我们当前已经测试了以下客户端：

- MySQLWorkbench 
- Mysql CLI

我们还测试了最常见的应用程序驱动程序，你可以在此页的末尾查看详细信息。

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

调用 Azure CLI 工具获取步骤1中经过身份验证的 Azure AD 用户访问 Azure Database for MySQL 的访问令牌。

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

该令牌是一个基本的64字符串，它对有关经过身份验证的用户的所有信息进行编码，并将其作为 Azure Database for MySQL 服务的目标。

> [!NOTE]
> 访问令牌的有效期介于5分钟到60分钟之间。 建议你在开始登录到 Azure Database for MySQL 之前获取访问令牌。

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>步骤3：使用令牌作为密码登录 MySQL

连接时，需要将访问令牌用作 MySQL 用户密码。 使用 GUI 客户端（如 MySQLWorkbench）时，可以使用上面的方法来检索令牌。 

使用 CLI 时，可以使用此短期连接： 

**示例（Linux/macOS）：**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

请注意 "启用-明文-插件" 设置-你需要对其他客户端使用类似的配置，以确保在不进行哈希处理的情况下将令牌发送到服务器。

你现在可以使用 Azure AD 身份验证向你的 MySQL 服务器进行身份验证。

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中创建 Azure AD 用户

若要将 Azure AD 用户添加到 Azure Database for MySQL 数据库，请在连接后执行以下步骤（请参阅有关如何连接的后面部分）：

1. 首先确保 Azure AD 用户 `<user>@yourtenant.onmicrosoft.com` 是 Azure AD 租户中的有效用户。
2. 以 Azure AD 管理员用户身份登录到你的 Azure Database for MySQL 实例。
3. 在 Azure Database for MySQL 中创建用户 `<user>@yourtenant.onmicrosoft.com`。

**示例：**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

对于超过32个字符的用户名，建议改为使用别名，以便在连接时使用： 

示例：

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> 通过 Azure AD 对用户进行身份验证时，不会向用户授予访问 Azure Database for MySQL 数据库中的对象的任何权限。 您必须手动向用户授予所需的权限。

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中创建 Azure AD 组

若要为数据库 Azure AD 启用访问权限，请使用与用户相同的机制，但要指定组名称，请执行以下操作：

**示例：**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

登录时，组的成员将使用其个人访问令牌，但使用指定为用户名的组名称进行签名。

## <a name="token-validation"></a>令牌验证

Azure Database for MySQL 中的 Azure AD 身份验证确保了 MySQL server 中存在用户，并通过验证令牌的内容来检查令牌的有效性。 执行以下令牌验证步骤：

-   令牌由 Azure AD 签名，但未被篡改
-   为与服务器关联的租户 Azure AD 颁发令牌
-   令牌尚未过期
-   令牌适用于 Azure Database for MySQL 资源（而不是其他 Azure 资源）

## <a name="compatibility-with-application-drivers"></a>与应用程序驱动程序的兼容性

大多数驱动程序均受支持，但请确保使用以明文形式发送密码的设置，以便在不进行修改的情况下发送令牌。

* C/C++
  * libmysqlclient：支持
  * mysql-连接器-c + +：受支持
* Java
  * Connector/J （mysql-连接器-java）：支持，必须使用 `useSSL` 设置
* Python
  * Connector/Python：支持
* Ruby
  * mysql2：支持
* .NET
  * mysql-连接器-net：支持，需要为 mysql_clear_password 添加插件
  * mysql-net/MySqlConnector：支持
* Node.js
  * mysqljs：不支持（不以明文形式发送无修补程序的令牌）
  * mysql2：支持
* Perl
  * DBD：： mysql：支持
  * Net：： MySQL：不支持
* 开始
  * sql-驱动程序：支持，将 `?tls=true&allowCleartextPasswords=true` 添加到连接字符串

## <a name="next-steps"></a>后续步骤

* 查看[Azure Database for MySQL 单一服务器 Azure Active Directory 身份验证](concepts-azure-ad-authentication.md)的总体概念

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
