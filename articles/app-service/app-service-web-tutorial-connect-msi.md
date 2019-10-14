---
title: 使用托管标识确保 Azure SQL 数据库连接的安全 - Azure 应用服务 | Microsoft Docs
description: 了解如何使用托管标识让数据库连接更安全，以及如何将此方法应用到其他 Azure 服务。
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
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: b39c1596dd16f8ec6235878abdbf37492abd1ea8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177079"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>教程：使用托管标识确保从应用服务进行的 Azure SQL 数据库连接的安全

[应用服务](overview.md)在 Azure 中提供高度可缩放、自修补的 Web 托管服务。 它还为应用提供[托管标识](overview-managed-identity.md)，这是一项统包解决方案，可以确保安全地访问 [Azure SQL 数据库](/azure/sql-database/)和其他 Azure 服务。 应用服务中的托管标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在本教程中，你要将托管标识添加到在以下教程之一中生成的示例 Web 应用： 

- [教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)
- [教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](app-service-web-tutorial-dotnetcore-sqldb.md)

完成后，示例应用就可以安全地连接到 SQL 数据库，不需用户名和密码。

> [!NOTE]
> 本教程所述的步骤支持以下版本：
> 
> - .NET Framework 4.7.2 和更高版本。
> - .NET Core 2.2 和更高版本。
>

学习内容：

> [!div class="checklist"]
> * 启用托管标识
> * 授予 SQL 数据库访问托管标识的权限
> * 配置实体框架，将 Azure AD 身份验证用于 SQL 数据库
> * 使用 Azure AD 身份验证从 Visual Studio 连接到 SQL 数据库

> [!NOTE]
>在本地 Active Directory 域服务 (AD DS) 中，Azure AD 身份验证不同于[集成式 Windows 身份验证](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))  。 AD DS 和 Azure AD 使用的身份验证协议完全不相同。 有关详细信息，请参阅 [Azure AD 域服务文档](https://docs.microsoft.com/azure/active-directory-domain-services/)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

本文从你在[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)或[教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](app-service-web-tutorial-dotnetcore-sqldb.md)。 请先完成这两篇教程之一（如果尚未完成）。 也可调整这些步骤，使用 SQL 数据库来生成自己的 .NET 应用。

若要使用 SQL 数据库作为后端调试应用程序，请确保已经允许从计算机连接客户端。 否则，请遵循[使用 Azure 门户管理服务器级 IP 防火墙规则](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)中的步骤添加客户端 IP。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>向 Azure AD 用户授予数据库访问权限

首先，通过将 Azure AD 用户指定为 SQL 数据库服务器的 Active Directory 管理员，对 SQL 数据库启用 Azure AD 身份验证。 此用户与用于注册 Azure 订阅的 Microsoft 帐户不同。 它必须是你在 Azure AD 中创建、导入、同步或邀请到其中的用户。 有关允许的 Azure AD 用户的详细信息，请参阅 [SQL 数据库中的 Azure AD 功能和限制](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)。

如果 Azure AD 租户还没有用户，请按照[使用 Azure Active Directory 添加或删除用户](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤创建一个用户。

使用 [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) 查找 Azure AD 用户的对象 ID，并替换 \<user-principal-name>  。 结果会保存到变量中。

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> 若要查看 Azure AD 中所有用户主体名称的列表，请运行 `az ad user list --query [].userPrincipalName`。
>

使用 Cloud Shell 中的 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) 命令，将此 Azure AD 用户添加为 Active Directory 管理员。 在以下命令中，将“\<server-name>”  替换为 SQL 数据库服务器名称（不带 `.database.windows.net` 后缀）。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

有关添加 Active Directory 管理员的详细信息，请参阅[为 Azure SQL 数据库服务器预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>设置 Visual Studio

### <a name="windows"></a>Windows
Visual Studio for Windows 集成了 Azure AD 身份验证。 若要在 Visual Studio 中启用开发和调试，请在 Visual Studio 中添加 Azure AD 用户，方法是从菜单中依次选择“文件” > “帐户设置”，然后单击“添加帐户”    。

若要设置进行 Azure 服务身份验证的 Azure AD 用户，请从菜单中依次选择“工具” > “选项”，然后依次选择“Azure 服务身份验证” > “帐户选择”     。 选择已添加的 Azure AD 用户，然后单击“确定”  。

现已准备好将 SQL 数据库作为后端，使用 Azure AD 身份验证来开发和调试应用程序。

### <a name="macos"></a>MacOS

Visual Studio for Mac 未集成 Azure AD 身份验证。 不过，稍后将使用的 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 库可以使用 Azure CLI 中的令牌。 若要在 Visual Studio 中启用开发和调试，首先需要在本地计算机上[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

在本地计算机上安装 Azure CLI 后，请使用 Azure AD 用户通过以下命令登录 Azure CLI：

```bash
az login --allow-no-subscriptions
```
现已准备好将 SQL 数据库作为后端，使用 Azure AD 身份验证来开发和调试应用程序。

## <a name="modify-your-project"></a>修改项目

要对项目执行的步骤取决于它是 ASP.NET 项目还是 ASP.NET Core 项目。

- [修改 ASP.NET](#modify-aspnet)
- [修改 ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>修改 ASP.NET

在 Visual Studio 中，打开包管理器控制台，并添加 NuGet 包 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

在 Web.config 中，从文件顶部开始工作并进行以下更改  ：

- 在 `<configSections>` 中，添加以下部分声明：

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- 在 `</configSections>` 结束标记下方，为 `<SqlAuthenticationProviders>` 添加以下 XML 代码。

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- 找到名为 `MyDbConnection` 的连接字符串，并将其 `connectionString` 值替换为 `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`。 将 \<server-name> 和 \<db-name> 替换为你的服务器名称和数据库名称   。

这就是连接到 SQL 数据库所要完成的所有准备工作。 在 Visual Studio 中调试时，代码将使用[设置 Visual Studio](#set-up-visual-studio) 中配置的 Azure AD 用户。 稍后你将设置 SQL 数据库服务器，以允许应用服务应用的托管标识建立连接。

键入 `Ctrl+F5`，再次运行该应用。 浏览器中相同的 CRUD 应用程序现使用 Azure AD 身份验证直接连接到 Azure SQL 数据库。 此设置使你能够从 Visual Studio 运行数据库迁移。

### <a name="modify-aspnet-core"></a>修改 ASP.NET Core

在 Visual Studio 中，打开包管理器控制台，并添加 NuGet 包 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

[ASP.NET Core 和 SQL 数据库教程](app-service-web-tutorial-dotnetcore-sqldb.md)中完全未使用 `MyDbConnection` 连接字符串，因为本地开发环境使用 Sqlite 数据库文件，而 Azure 生产环境使用应用服务中的连接字符串。 使用 Active Directory 身份验证时，最好是让这两种环境使用相同的连接字符串。 在 *appsettings.json* 中，请将 `MyDbConnection` 连接字符串的值替换为：

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

在 *Startup.cs* 中，删除前面添加的代码节：

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

并将其替换为以下代码：

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

接下来，使用 SQL 数据库的访问令牌提供实体框架数据库上下文。 在 *Data\MyDatabaseContext.cs* 中，将以下代码添加到空的 `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` 构造函数的大括号中：

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> 为清楚起见，此演示代码是同步的。 有关详细信息，请参阅[构造函数的异步指南](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors)。

这就是连接到 SQL 数据库所要完成的所有准备工作。 在 Visual Studio 中调试时，代码将使用[设置 Visual Studio](#set-up-visual-studio) 中配置的 Azure AD 用户。 稍后你将设置 SQL 数据库服务器，以允许应用服务应用的托管标识建立连接。 `AzureServiceTokenProvider` 类将令牌缓存在内存中，在过期前才将其从 Azure AD 检索出来。 不需要任何自定义代码就可以刷新该令牌。

> [!TIP]
> 如果你配置的 Azure AD 用户可以访问多个租户，请使用所需的租户 ID 调用 `GetAccessTokenAsync("https://database.windows.net/", tenantid)` 以检索正确的访问令牌。

键入 `Ctrl+F5`，再次运行该应用。 浏览器中相同的 CRUD 应用程序现使用 Azure AD 身份验证直接连接到 Azure SQL 数据库。 此设置使你能够从 Visual Studio 运行数据库迁移。

## <a name="use-managed-identity-connectivity"></a>使用托管标识连接性

接下来，配置应用服务应用，使其使用系统分配的托管标识连接到 SQL 数据库。

### <a name="enable-managed-identity-on-app"></a>在应用上启用托管标识

若要为 Azure 应用启用托管标识，请在 Cloud Shell 中使用 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 命令。 在以下命令中，替换 \<app-name>  。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

这是一个输出示例：

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>将托管标识添加到 Azure AD 组

若要授予此标识对 SQL 数据库的访问权限，需要将其添加到 [Azure AD 组](../active-directory/fundamentals/active-directory-manage-groups.md)。 在 Cloud Shell 中，将其添加到名为 myAzureSQLDBAccessGroup 的新组中，如以下脚本所示  ：

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

若要查看每个命令的完整 JSON 输出，请删除参数 `--query objectId --output tsv`。

### <a name="grant-permissions-to-azure-ad-group"></a>向 Azure AD 组授予权限

在 Cloud Shell 中，使用 SQLCMD 命令登录到 SQL 数据库。 将 \<server-name> 替换为 SQL 数据库服务器名称，将 \<db-name> 替换为应用使用的数据库名称，将 \<aad-user-name> 和 \<aad-password> 替换为 Azure AD 用户的凭据     。

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

在所需数据库的 SQL 提示符窗口中运行以下命令，从而添加 Azure AD 组并授予应用所需的权限。 例如， 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

键入 `EXIT`，返回到 Cloud Shell 提示符窗口。

### <a name="modify-connection-string"></a>修改连接字符串

请记住，在 *Web.config* 或 *appsettings.json* 中所做的相同更改适用于托管标识，因此只需删除应用服务中的现有连接字符串，该字符串由 Visual Studio 在首次部署应用时创建。 使用以下命令，但将 \<app-name> 替换为应用名称  。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>发布更改

现在，剩下的操作是将更改发布到 Azure。

**如果你是在学完[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)** 后转到本教程，请在 Visual Studio 中发布更改。 在“解决方案资源管理器”  中，右键单击 “DotNetAppSqlDb”  项目，并选择“发布”  。

![从解决方案资源管理器发布](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

在发布页中单击“发布”。  

**如果你是在学完[教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](app-service-web-tutorial-dotnetcore-sqldb.md)** 后转到本教程，请运行以下命令使用 Git 发布更改：

```bash
git commit -am "configure managed identity"
git push azure master
```

当新网页显示待办事项列表时，表明应用使用了托管标识连接到数据库。

![Code First 迁移后的 Azure 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

现在应该可以像以前一样编辑待办事项列表了。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 启用托管标识
> * 授予 SQL 数据库访问托管标识的权限
> * 配置实体框架，将 Azure AD 身份验证用于 SQL 数据库
> * 使用 Azure AD 身份验证从 Visual Studio 连接到 SQL 数据库

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)
