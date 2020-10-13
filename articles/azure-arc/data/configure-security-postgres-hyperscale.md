---
title: 为已启用 Azure Arc 的 PostgreSQL 超大规模服务器组配置安全性
description: 为已启用 Azure Arc 的 PostgreSQL 超大规模服务器组配置安全性
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4f89ace7130e95ba109edcf6becca1e15c8d32c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273194"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>为已启用 Azure Arc 的 PostgreSQL 超大规模服务器组配置安全性

本文档介绍与服务器组安全相关的各个方面：
- 静态加密
- 用户管理
   - 常规透视
   - 更改 _postgres_ 管理用户的密码

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>静态加密
可以通过加密存储数据库的磁盘和/或使用数据库函数对插入或更新的数据进行加密，来实现静态加密。

### <a name="hardware-linux-host-volume-encryption"></a>硬件： Linux 主机卷加密
实施系统数据加密可以保护驻留在支持 Azure Arc 的数据服务安装程序所使用的磁盘上的任何数据。 你可以阅读有关本主题的详细信息：
- 通常，Linux 上的[静态数据加密](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) 
- 具有 LUKS 的磁盘加密 `cryptsetup` 命令 (Linux) # B2 https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) 具体来说，由于启用了 Azure Arc 的数据服务在你提供的物理基础结构上运行，因此你需要负责保护基础结构。

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>软件：使用 `pgcrypto` 服务器组中的 PostgreSQL 扩展
除了加密用于托管 Azure Arc 设置的磁盘之外，还可以配置启用了 Azure Arc 的 PostgreSQL 超大规模服务器组，以公开应用程序可用于加密数据库中的数据的机制)  (。 此 `pgcrypto` 扩展是 `contrib` Postgres 扩展的一部分，可在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中使用。 可在此处找到有关 `pgcrypto` 扩展[here](https://www.postgresql.org/docs/current/pgcrypto.html)的详细信息。
总而言之，使用以下命令可以启用扩展，并使用它：


#### <a name="create-the-pgcrypto-extension"></a>创建 `pgcrypto` 扩展
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
CREATE EXTENSION pgcrypto;
```

> 可在 [此处](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) 找到有关如何连接的详细信息。

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>验证是否列出了可以在服务器组中使用的扩展
你可以 `pgcrypto` 通过列出你的服务器组中提供的扩展来验证扩展是否可供使用。
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
select * from pg_extension;
```
你应会看到 `pgcrypto` 你是否已启用并在上面指示的命令中创建它。

#### <a name="use-the-pgcrypto-extension"></a>使用 `pgcrypto` 扩展
现在，你可以调整应用程序的代码，使其使用提供的任何函数 `pgcrypto` ：
- 常规哈希函数
- 密码哈希函数
- PGP 加密函数
- 原始加密函数
- 随机数据函数

例如，生成哈希值。 运行以下命令：

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

返回以下哈希：

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

或，例如：

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

返回以下哈希：

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

例如，若要存储加密数据（例如密码），请执行以下操作：

假设应用程序将机密存储在下表中：

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

创建用户时，会对其密码进行加密：

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

现在，我看到密码已加密：

```console
select * from mysecrets;
```

输出：

- USERid：1
- 用户名： Me
- USERpassword： $ 1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

当我连接到我的应用程序并传递密码时，它将在表中查找， `mysecrets` 如果提供给应用程序的密码与表中存储的密码匹配，将返回用户的名称。 例如：

- 我传递了错误的密码：
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   输出 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- 我传递了正确的密码：

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   输出：

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

此小示例演示了如何使用 Postgres 扩展在启用了 PostgreSQL 超大规模的 Azure Arc 中加密静态数据 (存储加密的数据) `pgcrypto` ，你的应用程序可以使用提供的函数 `pgcrypto` 来操作此加密数据。

## <a name="user-management"></a>用户管理
### <a name="general-perspectives"></a>常规透视
可以使用标准 Postgres 方式创建用户或角色。 但是，如果这样做，这些项目将仅在协调器角色上可用。 在预览期间，这些用户/角色尚不能够访问在服务器组的协调器节点外部和工作器节点上分布的数据。 原因在于，在预览版中，用户定义不会复制到工作器节点。

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>更改 _postgres_ 管理用户的密码
启用 Azure Arc 后，PostgreSQL 超大规模附带了标准 Postgres 管理用户 _Postgres_ ，你可以在创建服务器组时设置密码。
用于更改其密码的命令的常规格式为：
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

其中--admin-password 是与 AZDATA_PASSWORD **会话**环境变量中的某个值相关的布尔值。
如果 AZDATA_PASSWORD **会话**的环境变量存在并且具有值，则运行上述命令会将 postgres 用户的密码设置为此环境变量的值。

如果 AZDATA_PASSWORD **会话**的环境变量存在，但没有值或 AZDATA_PASSWORD **会话**的环境变量不存在，则运行上述命令将提示用户以交互方式输入密码

#### <a name="changing-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>以交互方式更改 postgres 管理用户的密码：
1. 删除 AZDATA_PASSWORD **会话**的环境变量或删除其值
2. 运行以下命令：
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   例如：
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   系统将提示你输入密码并进行确认：
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   更新密码时，该命令的输出将显示：
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="changing-the-password-of-the-postgres-administrative-user-using-the-azdata_password-sessions-environment-variable"></a>使用 AZDATA_PASSWORD **会话**的环境变量更改 postgres 管理用户的密码：
1. 将 AZDATA_PASSWORD **会话**的环境变量的值设置为你想要的密码。
2. 运行命令：
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   例如：
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   更新密码时，该命令的输出将显示：
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> 若要验证 AZDATA_PASSWORD 会话的环境变量是否存在以及它所具有的值，请运行：
> - 在 Linux 客户端上：
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - 在带有 PowerShell 的 Windows 客户端上：
> ```console
> echo $env:AZDATA_PASSWORD
> ```



## <a name="next-steps"></a>后续步骤
- 有关扩展的详细信息，请参阅 `pgcrypto` [此处](https://www.postgresql.org/docs/current/pgcrypto.html)。
- [在此处](using-extensions-in-postgresql-hyperscale-server-group.md)阅读有关如何使用 Postgres 扩展的详细信息。

