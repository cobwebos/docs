---
title: 使用 Azure 活动目录 - MySQL 的 Azure 数据库
description: 了解如何为 MySQL 的 Azure 数据库设置 Azure 活动目录 （Azure AD） 进行身份验证
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298999"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure 活动目录使用 MySQL 进行身份验证

本文将介绍如何使用 MySQL 的 Azure 数据库配置 Azure 活动目录访问以及如何使用 Azure AD 令牌进行连接的步骤。

> [!IMPORTANT]
> MySQL Azure 数据库的 Azure AD 身份验证当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>设置 Azure AD 管理员用户

只有 Azure AD 管理员用户可以创建/启用用户才能进行基于 Azure AD 的身份验证。 要创建和 Azure AD 管理员用户，请按照以下步骤操作

1. 在 Azure 门户中，选择要为 Azure AD 启用的 MySQL 的 Azure 数据库实例。
2. 在"设置"下，选择活动目录管理员：

![设置 Azure 广告管理员][2]

3. 在客户租户中选择有效的 Azure AD 用户，作为 Azure AD 管理员。

> [!IMPORTANT]
> 设置管理员时，新用户将添加到具有完全管理员权限的 MySQL 服务器的 Azure 数据库。

每个 MySQL 服务器只能创建一个 Azure AD 管理员，而另一个服务器的选择将覆盖为服务器配置的现有 Azure AD 管理员。

在将来的版本中，我们将支持指定 Azure AD 组，而不是单个用户，以具有多个管理员，但目前尚不支持这样做。

配置管理员后，您现在可以登录：

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>使用 Azure AD 连接到 MySQL 的 Azure 数据库

以下高级关系图总结了使用 Azure 数据库进行 MySQL 的 Azure AD 身份验证的工作流：

![身份验证流][1]

我们设计了 Azure AD 集成，以便使用常见的 MySQL 工具（如 mysql CLI），这些工具不了解 Azure AD，并且仅在连接到 MySQL 时支持指定用户名和密码。 我们将 Azure AD 令牌作为密码传递，如上图所示。

我们目前已经测试了以下客户：

- MySQLWork工作台 
- Mysql CLI

我们还测试了最常见的应用程序驱动程序，您可以在此页面的末尾看到详细信息。

以下是用户/应用程序需要执行以下描述的 Azure AD 身份验证的步骤：

### <a name="step-1-authenticate-with-azure-ad"></a>第 1 步：使用 Azure AD 进行身份验证

确保安装了 Azure [CLI。](/cli/azure/install-azure-cli)

调用 Azure CLI 工具以使用 Azure AD 进行身份验证。 它要求您提供 Azure AD 用户 ID 和密码。

```
az login
```

此命令将启动到 Azure AD 身份验证页的浏览器窗口。

> [!NOTE]
> 您还可以使用 Azure 云外壳执行这些步骤。
> 请注意，在 Azure 云外壳中检索 Azure AD 访问令牌时，需要显式调用`az login`并重新登录（在带有代码的单独窗口中）。 该登录后，`get-access-token`命令将按预期工作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>第 2 步：检索 Azure AD 访问令牌

调用 Azure CLI 工具以从步骤 1 获取 Azure AD 已身份验证用户的访问令牌，以访问 MySQL 的 Azure 数据库。

示例（公共云）：

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述资源值必须完全按照所示指定。 对于其他云，可以使用以下情况来查看资源值：

```shell
az cloud show
```

对于 Azure CLI 版本 2.0.71 及更高版本，可以在以下更方便版本中为所有云指定该命令：

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

令牌是一个 Base 64 字符串，用于编码有关经过身份验证的用户的所有信息，该字符串针对 MySQL 服务的 Azure 数据库。

> [!NOTE]
> 访问令牌的有效期在 5 分钟到 60 分钟之间。 我们建议您在开始登录 MySQL 的 Azure 数据库之前获取访问令牌。

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>第 3 步：使用令牌作为密码使用 MySQL 登录

连接时，您需要使用访问令牌作为 MySQL 用户密码。 使用 GUI 客户端（如 MySQLWorkbench）时，可以使用上述方法检索令牌。 

使用 CLI 时，您可以使用此速记连接： 

**示例（Linux/macOS）：**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

请注意"启用-明文插件"设置 - 您需要与其他客户端使用类似的配置，以确保令牌发送到服务器而不进行哈希处理。

现在，使用 Azure AD 身份验证对 MySQL 服务器进行身份验证。

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>在 Azure 数据库中为 MySQL 创建 Azure AD 用户

要将 Azure AD 用户添加到 MySQL 数据库的 Azure 数据库，请在连接后执行以下步骤（请参阅后面有关如何连接的部分）：

1. 首先，确保 Azure AD`<user>@yourtenant.onmicrosoft.com`用户是 Azure AD 租户中的有效用户。
2. 以 Azure AD 管理员用户身份登录到 MySQL 实例的 Azure 数据库。
3. 在`<user>@yourtenant.onmicrosoft.com`Azure 数据库中为 MySQL 创建用户。

**例子：**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

对于超过 32 个字符的用户名，建议您改用别名，在连接时使用： 

示例：

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> 通过 Azure AD 对用户进行身份验证不会授予用户访问 MySQL 数据库 Azure 数据库中的对象的任何权限。 您必须手动授予用户所需的权限。

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>在 Azure 数据库中为 MySQL 创建 Azure AD 组

要启用 Azure AD 组以访问数据库，请使用与用户相同的机制，而是指定组名称：

**例子：**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

登录时，组成员将使用其个人访问令牌，但使用指定为用户名的组名称进行签名。

## <a name="token-validation"></a>令牌验证

MySQL Azure 数据库中的 Azure AD 身份验证可确保用户存在于 MySQL 服务器中，并且它通过验证令牌的内容来检查令牌的有效性。 执行以下令牌验证步骤：

-   令牌由 Azure AD 签名，未被篡改
-   令牌由 Azure AD 颁发给与服务器关联的租户
-   令牌尚未过期
-   令牌用于 MySQL 资源的 Azure 数据库（而不是另一个 Azure 资源）

## <a name="compatibility-with-application-drivers"></a>与应用程序驱动程序兼容

大多数驱动程序都受支持，但请确保使用设置以明文形式发送密码，因此令牌无需修改即可发送。

* C/C++
  * 利米sql客户端：支持
  * mysql-连接器-c++：支持
* Java
  * 连接器/J（mysql-连接器-java）：支持，必须利用`useSSL`设置
* Python
  * 连接器/Python：支持
* Ruby
  * mysql2： 支持
* .NET
  * mysql 连接器网：支持，需要添加插件mysql_clear_password
  * mysql 网/MySqlConnector：支持
* Node.js
  * mysqljs： 不支持（不发送明文中的令牌，没有修补程序）
  * 节点-mysql2：支持
* Perl
  * DBD：：mysql：支持
  * 净：：MySQL：不支持
* Go
  * go-sql 驱动程序：支持，添加到`?tls=true&allowCleartextPasswords=true`连接字符串

## <a name="next-steps"></a>后续步骤

* 使用[MySQL 的 Azure 数据库查看 Azure 活动目录身份验证](concepts-azure-ad-authentication.md)的总体概念

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
