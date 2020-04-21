---
title: 控制 SQL 按需版本（预览版）对存储帐户的访问
description: 介绍 SQL 按需版本（预览版）如何访问 Azure 存储，以及如何在 Azure Synapse Analytics 中控制 SQL 按需版本对存储的访问。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420051"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中控制 SQL 按需版本（预览版）对存储帐户的访问

SQL 按需版本（预览版）查询直接从 Azure 存储中读取文件。 由于存储帐户是 SQL 按需版本资源外部的对象，因此需要相应的凭据。 用户需要获得适用的权限才能使用必要的凭据。 本文介绍可用的凭据类型，以及为 SQL 和 Azure AD 用户进行的凭据查找是如何执行的。

## <a name="supported-storage-authorization-types"></a>支持的存储授权类型

已登录到 SQL 按需版本资源的用户必须获得访问和查询 Azure 存储中的文件的授权。 支持三种授权类型：

- [共享访问签名](#shared-access-signature)
- [托管的标识](#managed-identity)
- [用户标识](#user-identity)

> [!NOTE]
> [Azure AD 直通](#force-azure-ad-pass-through)是创建工作区时的默认行为。 如果使用 Azure AD 直通，则不需要为使用 AD 登录名访问的每个存储帐户创建凭据。 可以[禁用此行为](#disable-forcing-azure-ad-pass-through)。

在下表中可以找到目前支持的或者即将支持的不同授权类型。

| 授权类型                    | *SQL 用户*    | *Azure AD 用户*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | 支持     | 支持      |
| [托管标识](#managed-identity) | 不支持 | 不支持  |
| [用户标识](#user-identity)       | 不支持 | 支持      |

### <a name="shared-access-signature"></a>共享访问签名

共享访问签名 (SAS) 提供对存储帐户中的资源的委托访问权限。  使用 SAS，客户可向客户端授予对存储帐户中的资源的访问权限，而无需共享帐户密钥。 通过 SAS 可以精细控制向具有 SAS 的客户端授予的访问权限类型，包括有效期间隔、授予的权限、可接受的 IP 地址范围，以及可接受的协议 (https/http)。

可以导航到“Azure 门户”->“存储帐户”->“共享访问签名”->“配置权限”->“生成 SAS 和连接字符串”来获取 SAS 令牌。 

> [!IMPORTANT]
> 生成某个 SAS 令牌时，其开头会包含问号（“?”）。 若要在 SQL 按需版本中使用该令牌，必须在创建凭据时删除问号（“?”）。 例如：
>
> SAS 令牌：?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>用户标识

用户标识（也称为“直通”）是一种授权类型。在使用这种授权类型的情况下，登录到 SQL 按需版本的 Azure AD 用户的标识会用来授予数据访问权限。  在访问数据之前，Azure 存储管理员必须向 Azure AD 用户授予权限。 如上表中所示，SQL 用户类型不支持此授权类型。

> [!NOTE]
> 如果使用 [Azure AD 直通](#force-azure-ad-pass-through)，则不需要对使用 AD 登录名访问的每个存储帐户创建凭据。

> [!IMPORTANT]
> 需要具有存储 Blob 数据所有者/参与者/读取者角色才能使用自己的标识来访问数据。
> 即使你是存储帐户的所有者，也仍需将自己添加到存储 Blob 数据角色之一。
>
> 若要详细了解 Azure Data Lake Store Gen2 中的访问控制，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../../storage/blobs/data-lake-storage-access-control.md)一文。
>

### <a name="managed-identity"></a>托管标识

托管标识也称为 MSI。  它是 Azure Active Directory (Azure AD) 的一项功能，为 SQL 按需版本提供 Azure 服务。 此外，它还会在 Azure AD 中部署一个自动托管的标识。 此标识可用于对有关访问 Azure 存储中的数据的请求授权。

在访问数据之前，Azure 存储管理员必须向管理标识授予访问数据的权限。 向托管标识授予权限的方式与向任何其他 Azure AD 用户授予权限的方式相同。

## <a name="create-credentials"></a>创建凭据

若要查询 Azure 存储中的文件，SQL 按需版本终结点需要一个包含身份验证信息的服务器级凭据。 可通过运行 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 来添加凭据。 需要提供 CREDENTIAL NAME 参数。 该参数必须匹配存储中数据的一部分路径或完整路径（参阅下文）。

> [!NOTE]
> 不支持 FOR CRYPTOGRAPHIC PROVIDER 参数。

对于所有受支持的授权类型，凭据可以指向帐户、容器、任何目录（非根目录）或单个文件。

CREDENTIAL NAME 必须匹配容器、文件夹或文件的完整路径，其格式为：`<prefix>://<storage_account_path>/<storage_path>`

| 外部数据源       | 前缀 | 存储帐户路径                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob 存储         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 “<storage_path>”是存储中的路径，它指向所要读取的文件夹或文件。

> [!NOTE]
> 有一个特殊的 CREDENTIAL NAME `UserIdentity`，它会[强制使用 Azure AD 直通](#force-azure-ad-pass-through)。 请了解该参数在执行查询时对[凭据查找](#credential-lookup)的影响。

（可选）若要控制用户创建或删除凭据，管理员可向用户授予/拒绝 (GRANT/DENY) ALTER ANY CREDENTIAL 权限：

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>支持的存储和授权类型

可以使用授权和 Azure 存储类型的以下组合：

|                     | Blob 存储   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | 支持      | 不支持   | 支持     |
| *托管标识* | 不支持  | 不支持    | 不支持 |
| *用户标识*    | 支持      | 支持        | 支持     |

### <a name="examples"></a>示例

可以根据[授权类型](#supported-storage-authorization-types)使用以下 T-SQL 语法创建凭据。

**共享访问签名和 Blob 存储**

请将 <mystorageaccountname> 替换为实际存储帐户名称，并将 <mystorageaccountcontainername> 替换为实际容器名称：  

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**用户标识和 Azure Data Lake Storage Gen1**

请将 <mystorageaccountname> 替换为实际存储帐户名称，并将 <mystorageaccountcontainername> 替换为实际容器名称：  

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**用户标识和 Azure Data Lake Storage Gen2**

请将 <mystorageaccountname> 替换为实际存储帐户名称，并将 <mystorageaccountcontainername> 替换为实际容器名称：  

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>强制使用 Azure AD 直通

强制使用 Azure AD 直通是特殊 CREDENTIAL NAME `UserIdentity`（在 Azure Synapse 工作区预配期间自动创建）实现的默认行为。 它强制对每个 Azure AD 登录名的每个查询使用 Azure AD 直通，不管是否存在其他凭据，都会出现此行为。

> [!NOTE]
> Azure AD 直通是默认行为。 无需为 AD 登录名访问的每个存储帐户创建凭据。

如果[已对每个查询禁用了强制使用 Azure AD 直通](#disable-forcing-azure-ad-pass-through)，但现在想要重新启用，请执行：

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

若要对特定的用户启用强制使用 Azure AD 直通，可向该特定用户授予对凭据 `UserIdentity` 的 REFERENCE 权限。 以下示例对 user_name 启用强制使用 Azure AD 直通：

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

有关 SQL 按需版本如何查找要使用的凭据的详细信息，请参阅[凭据查找](#credential-lookup)。

## <a name="disable-forcing-azure-ad-pass-through"></a>禁用强制使用 Azure AD 直通

可[对每个查询禁用强制使用 Azure AD 直通](#force-azure-ad-pass-through)。 若要禁用此功能，请使用以下语句删除 `Userdentity` 凭据：

```sql
DROP CREDENTIAL [UserIdentity];
```

如果要重新启用此功能，请参阅[强制使用 Azure AD 直通](#force-azure-ad-pass-through)部分。

若要对特定的用户禁用强制使用 Azure AD 直通，可以拒绝特定用户对凭据 `UserIdentity` 的 REFERENCE 权限。 以下示例对 user_name 禁用强制使用 Azure AD 直通：

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

有关 SQL 按需版本如何查找要使用的凭据的详细信息，请参阅[凭据查找](#credential-lookup)。

## <a name="grant-permissions-to-use-credential"></a>授予使用凭据的权限

若要使用凭据，用户必须拥有对特定凭据的 REFERENCES 权限。 若要向 specific_user 授予对 storage_credential 的 REFERENCES 权限，请执行：

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

为了确保顺畅的 Azure AD 直通体验，默认情况下，所有用户都拥有使用 `UserIdentity` 凭据的权限。 这是在预配 Azure Synapse 工作区时通过自动执行以下语句实现的：

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>凭据查找

为查询授权时，凭据查找将用于访问存储帐户，它基于以下规则：

- 用户是以 Azure AD 登录名登录的

  - 如果 UserIdentity 凭据存在，并且用户对其拥有 reference 权限，则将使用 Azure AD 直通，否则将[按路径查找凭据](#lookup-credential-by-path)

- 用户是以 SQL 登录名登录的

  - 使用[按路径查找凭据](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>按路径查找凭据

如果禁用了强制使用 Azure AD 直通，则凭据查找将基于存储路径（深度优先）以及对该特定凭据存在 REFERENCES 权限的事实。 如果可用来访问同一文件的凭据有多个，SQL 按需版本将使用最具体的凭据。  

下面是对以下文件路径进行查询的示例：account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext 

凭据查找将按以下顺序完成：

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

如果某个用户对凭据编号 5 没有 REFERENCES 权限，则 SQL 按需版本将检查该用户是否对更高一级的凭据拥有 REFERENCES 权限，直到找到该用户对其拥有 REFERENCES 权限的凭据。 如果未找到此类权限，则将返回错误消息。

### <a name="credential-and-path-level"></a>凭据和路径级别

根据所需的路径形状，运行查询时需要符合以下要求：

- 如果查询针对多个文件（文件夹，带有或不带通配符），则用户至少需要对根目录级别（容器级别）的凭据拥有访问权限。 之所以需要此访问级别，是因为列表文件的相对路径是从根目录开始的（Azure 存储限制）
- 如果查询针对单个文件，则用户需要对任意级别的凭据拥有访问权限，因为 SQL 按需版本会直接访问该文件，即无需列出文件夹。

|                  | *帐户* | *根目录* | *任何其他目录* | *File*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *单个文件*    | 支持 | 支持        | 支持             | 支持     |
| *多个文件* | 支持 | 支持        | 不支持         | 不支持 |

## <a name="next-steps"></a>后续步骤

下面列出的文章可帮助你了解如何查询不同的文件夹类型、文件类型，以及如何创建和使用视图：

- [查询单个 CSV 文件](query-single-csv-file.md)
- [查询文件夹和多个 CSV 文件](query-folders-multiple-csv-files.md)
- [查询特定文件](query-specific-files.md)
- [查询 Parquet 文件](query-parquet-files.md)
- [创建和使用视图](create-use-views.md)
- [查询 JSON 文件](query-json-files.md)
- [查询 Parquet 嵌套类型](query-parquet-nested-types.md)
