---
title: 使用托管服务标识确保从应用服务进行的 Azure SQL 数据库连接的安全 | Microsoft Docs
description: 了解如何使用托管服务标识让数据库连接更安全，以及如何将此应用到其他 Azure 服务。
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 173588c0200666c52f3ac0a5d2e70d667cfe3294
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445555"
---
# <a name="tutorial-secure-sql-database-connection-with-managed-service-identity"></a>教程：使用托管服务标识确保 SQL 数据库连接的安全

[应用服务](app-service-web-overview.md)在 Azure 中提供高度可缩放、自修补的 Web 托管服务。 它还为应用提供[托管服务标识](app-service-managed-service-identity.md)，这是一项统包解决方案，可以确保安全地访问 [Azure SQL 数据库](/azure/sql-database/)和其他 Azure 服务。 应用服务中的托管服务标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在本教程中，请将托管服务标识添加到在[教程：使用 SQL 数据库在 Azure 中构建 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)中构建的示例 ASP.NET Web 应用。 完成后，示例应用就可以安全地连接到 SQL 数据库，不需用户名和密码。

学习如何：

> [!div class="checklist"]
> * 启用托管服务标识
> * 授予 SQL 数据库访问服务标识的权限
> * 配置应用程序代码，以便使用 Azure Active Directory 身份验证通过 SQL 数据库进行身份验证
> * 在 SQL 数据库中向服务标识授予最低特权

> [!NOTE]
> 在本地 Active Directory (AD DS) 中，Azure Active Directory 身份验证不同于[集成式 Windows 身份验证](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))。 AD DS 和 Azure Active Directory 使用的身份验证协议完全不相同。 有关详细信息，请参阅 [Windows Server AD DS 与 Azure AD 之间的差别](../active-directory/fundamentals/understand-azure-identity-solutions.md#the-difference-between-windows-server-ad-ds-and-azure-ad)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

本文是[教程：使用 SQL 数据库在 Azure 中构建 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)的后续内容。 如果尚未学习该教程，请先学习该教程。 也可调整这些步骤，使用 SQL 数据库来构建自己的 ASP.NET 应用。

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-service-identity"></a>启用托管服务标识

若要为 Azure 应用启用服务标识，请在 Cloud Shell 中使用 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 命令。 在以下命令中，替换 *\<app name>*。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

以下示例演示了在 Azure Active Directory 中创建标识后的输出：

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

下一步将用到 `principalId` 的值。 若要在 Azure Active Directory 中查看新标识的详细信息，请使用 `principalId` 的值运行以下可选命令：

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>授予数据库访问标识的权限

接下来，请在 Cloud Shell 中使用 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin_create) 命令授予数据库访问应用的服务标识的权限。 在以下命令中，替换 *\<server_name>* 和 <principalid_from_last_step>。 键入 *\<admin_user>* 的管理员名称。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

托管服务标识现在可以访问 Azure SQL 数据库服务器了。

## <a name="modify-connection-string"></a>修改连接字符串

在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令修改以前为应用设置的连接。 在以下命令中，将 *\<app name>* 替换为应用的名称，将 *\<server_name>* 和 *\<db_name>* 替换为 SQL 数据库的相应名称。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>修改 ASP.NET 代码

在 Visual Studio 的 **DotNetAppSqlDb** 项目中打开 _packages.config_，在包列表中添加以下行。

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

打开 _Models\MyDatabaseContext.cs_，将以下 `using` 语句添加到文件顶部：

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

在 `MyDatabaseContext` 类中，添加以下构造函数：

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

此构造函数将自定义 SqlConnection 对象配置为使用应用服务提供的 Azure SQL 数据库的访问令牌。 有了访问令牌，应用服务应用就可以使用其托管服务标识通过 Azure SQL 数据库进行身份验证。 有关详细信息，请参阅[获取 Azure 资源的令牌](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources)。 可以使用 `if` 语句，通过 LocalDB 继续在本地测试应用。

> [!NOTE]
> `SqlConnection.AccessToken` 目前仅在 .NET Framework 4.6 及更高版本中受支持，在 [.NET Core](https://www.microsoft.com/net/learn/get-started/windows) 中不受支持。
>

若要使用这个新的构造函数，请打开 `Controllers\TodosController.cs` 并找到 `private MyDatabaseContext db = new MyDatabaseContext();` 行。 现有的代码使用默认的 `MyDatabaseContext` 控制器通过标准的连接字符串来创建数据库，该字符串在未经[更改](#modify-connection-string)的情况下以明文形式保存用户名和密码。

请将整行替换为以下代码：

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>发布更改

现在，剩下的操作是将更改发布到 Azure。

在“解决方案资源管理器”中，右键单击 “DotNetAppSqlDb”项目，并选择“发布”。

![从解决方案资源管理器发布](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

在发布页中单击“发布”。 当新网页显示待办事项列表时，表明应用使用了托管服务标识连接到数据库。

![Code First 迁移后的 Azure Web 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

现在应该可以像以前一样编辑待办事项列表了。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>向标识授予最低特权

在此前的步骤中，你可能已注意到托管服务标识是以 Azure AD 管理员身份连接到 SQL Server。 为了向托管服务标识授予最低特权，需以 Azure AD 管理员身份登录到 Azure SQL 数据库服务器，然后添加包含服务标识的 Azure Active Directory 组。 

### <a name="add-managed-service-identity-to-an-azure-active-directory-group"></a>向 Azure Active Directory 组添加托管服务标识

在 Cloud Shell 中，将应用的托管服务标识添加到名为 _myAzureSQLDBAccessGroup_ 的新 Azure Active Directory 组，如以下脚本所示：

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

若要查看每个命令的完整 JSON 输出，请删除参数 `--query objectId --output tsv`。

### <a name="reconfigure-azure-ad-administrator"></a>重新配置 Azure AD 管理员

你此前已经以 Azure AD 管理员身份为 SQL 数据库分配托管服务标识。 不能使用此标识进行交互式登录（以添加数据库用户），因此需使用实际的 Azure AD 用户。 若要添加 Azure AD 用户，请执行[为 Azure SQL 数据库服务器预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)中的步骤。 

### <a name="grant-permissions-to-azure-active-directory-group"></a>向 Azure Active Directory 组授予权限

在 Cloud Shell 中，使用 SQLCMD 命令登录到 SQL 数据库。 将 _\<servername>_ 替换为 SQL 数据库服务器名称，将 _\<AADusername>_ 和 _\<AADpassword>_ 替换为 Azure AD 用户的凭据。

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

在 SQL 提示符窗口中运行以下命令，将此前创建的 Azure Active Directory 组添加为用户。

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

键入 `EXIT`，返回到 Cloud Shell 提示符窗口。 接下来，请再次运行 SQLCMD，但需在 _\<dbname>_ 中指定数据库名称。

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

在所需数据库的 SQL 提示符窗口中运行以下命令，向 Azure Active Directory 组授予读取和写入权限。

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 启用托管服务标识
> * 授予 SQL 数据库访问服务标识的权限
> * 配置应用程序代码，以便使用 Azure Active Directory 身份验证通过 SQL 数据库进行身份验证
> * 在 SQL 数据库中向服务标识授予最低特权

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)
