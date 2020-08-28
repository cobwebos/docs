---
title: 使用托管标识进行连接 - Azure Database for PostgreSQL - 单一服务器
description: 了解如何使用托管标识进行连接和身份验证，以便进行 Azure Database for PostgreSQL 身份验证
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b9603e43541ec1a364e4653caeeafc751f7e4f0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012086"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>使用托管标识连接到 Azure Database for PostgreSQL

本文介绍如何使用 Azure 虚拟机 (VM) 的用户分配标识来访问 Azure Database for PostgreSQL 服务器。 托管服务标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。 

学习如何：
- 授予 VM 对 Azure Database for PostgreSQL 服务器的访问权限
- 在数据库中创建一个代表 VM 用户分配标识的用户
- 使用 VM 标识获取访问令牌，并使用它查询 Azure Database for PostgreSQL 服务器
- 在 C# 示例应用程序中实现令牌检索

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源功能的托管标识，请参阅此[概述](../../articles/active-directory/managed-identities-azure-resources/overview.md)。 如果没有 Azure 帐户，请在继续操作前[注册免费帐户](https://azure.microsoft.com/free/)。
- 要执行所需的资源创建和角色管理，帐户在相应的范围（订阅或资源组）需要“所有者”权限。 如果需要有关角色分配的帮助，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](../../articles/role-based-access-control/role-assignments-portal.md)。
- 需要一个你希望使用托管标识来访问数据库的 Azure VM（例如运行 Ubuntu Linux）
- 需要已配置了 [Azure AD 身份验证](howto-configure-sign-in-aad-authentication.md)的 Azure Database for PostgreSQL 数据库服务器
- 要遵循 C# 示例，首先完成[如何使用 C# 进行连接](connect-csharp.md)的指南

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>为 VM 创建用户分配托管标识

使用 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 命令在订阅中创建标识。 可以使用虚拟机所在的相同资源组，也可以使用其他资源组。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

要在以下步骤中配置标识，请使用 [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) 命令将标识的资源 ID 和客户端 ID 存储在变量中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

现在，我们使用 [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令将用户分配标识分配给 VM：

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

要完成设置，请显示客户端 ID 的值，在接下来的几个步骤中你将需要该值：

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>为托管标识创建 PostgreSQL 用户

现在，作为 Azure AD 管理员用户连接到 PostgreSQL 数据库，并运行以下 SQL 语句：

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

使用用户名 `myuser`（替换为你选择的名称）进行身份验证时，托管标识现在具有访问权限。

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>从 Azure 实例元数据服务检索访问令牌

应用程序现在可以从 Azure 实例元数据服务检索访问令牌，并将其用于向数据库进行身份验证。

此令牌检索是通过向 `http://169.254.169.254/metadata/identity/oauth2/token` 发出 HTTP 请求并传递以下参数来完成的：

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID`（之前检索到的）

将返回包含 `access_token` 字段的 JSON 结果 - 此长文本值是托管标识访问令牌，在连接到数据库时应将其用作密码。

出于测试目的，可以在 shell 中运行以下命令。 请注意，需要安装 `curl`、`jq` 和 `psql` 客户端。

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

你现在已连接到之前配置的数据库。

## <a name="connecting-using-managed-identity-in-c"></a>在 C# 中使用托管标识进行连接

本部分介绍如何使用 VM 的用户分配托管标识获取访问令牌，并使用它调用 Azure Database for PostgreSQL。 Azure Database for PostgreSQL 以原生方式支持 Azure AD 身份验证，因此可以直接接受使用 Azure 资源托管标识获取的访问令牌。 创建到 PostgreSQL 的连接时，在密码字段中传递访问令牌。

以下 .NET 代码示例使用访问令牌来与 PostgreSQL 建立连接。 此代码必须在 VM 上运行，以访问 VM 用户分配托管标识的终结点。 使用访问令牌方法需要 .NET Framework 4.6 或更高版本或者 .NET Core 2.2 或更高版本。 替换 HOST、USER、DATABASE 和 CLIENT_ID 的值。

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

运行时，此命令将返回如下所示的输出：

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>后续步骤

* 查看[通过 Azure Database for PostgreSQL 进行 Azure Active Directory 身份验证](concepts-aad-authentication.md)的总体概念
