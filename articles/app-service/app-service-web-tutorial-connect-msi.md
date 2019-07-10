---
title: 使用托管标识确保 Azure SQL 数据库连接的安全 - Azure 应用服务 | Microsoft Docs
description: 了解如何使用托管标识让数据库连接更安全，以及如何将此应用到其他 Azure 服务。
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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481020"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>教程：使用托管标识确保从应用服务进行的 Azure SQL 数据库连接的安全

[应用服务](overview.md)在 Azure 中提供高度可缩放、自修补的 Web 托管服务。 它还为应用提供[托管标识](overview-managed-identity.md)，这是一项统包解决方案，可以确保安全地访问 [Azure SQL 数据库](/azure/sql-database/)和其他 Azure 服务。 应用服务中的托管标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在本教程中，请将托管标识添加到在[教程：使用 SQL 数据库在 Azure 中构建 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)。 完成后，示例应用就可以安全地连接到 SQL 数据库，不需用户名和密码。

> [!NOTE]
> 此方案目前受 .NET Framework 4.7.2 及更高版本的支持。 [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) 支持此方案，但它尚未包括在应用服务的默认映像中。 
>

学习如何：

> [!div class="checklist"]
> * 启用托管标识
> * 授予 SQL 数据库访问托管标识的权限
> * 配置实体框架，将 Azure AD 身份验证用于 SQL 数据库
> * 使用 Azure AD 身份验证从 Visual Studio 连接到 SQL 数据库

> [!NOTE]
>在本地 Active Directory 域服务 (AD DS) 中，Azure AD 身份验证不同于[集成式 Windows 身份验证](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))  。 AD DS 和 Azure AD 使用的身份验证协议完全不相同。 有关详细信息，请参阅 [Azure AD 域服务文档](https://docs.microsoft.com/azure/active-directory-domain-services/)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

本文从你在[教程：使用 SQL 数据库在 Azure 中构建 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)。 如果尚未学习该教程，请先学习该教程。 也可调整这些步骤，使用 SQL 数据库来构建自己的 ASP.NET 应用。

若要使用 SQL 数据库作为后端调试应用程序，请确保已经[允许从计算机连接客户端](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>授予 Azure AD 用户对数据库的访问权限

首先，通过将 Azure AD 用户指定为 SQL 数据库服务器的 Active Directory 管理员，对 SQL 数据库启用 Azure AD 身份验证。 此用户与用于注册 Azure 订阅的 Microsoft 帐户不同。 它必须是你在 Azure AD 中创建、导入、同步或邀请到其中的用户。 有关允许的 Azure AD 用户的详细信息，请参阅 [SQL 数据库中的 Azure AD 功能和限制](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)。 

使用 [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) 查找 Azure AD 用户的对象 ID，并替换 \<user-principal-name>  。 结果会保存到变量中。

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> 若要查看 Azure AD 中所有用户主体名称的列表，请运行 `az ad user list --query [].userPrincipalName`。
>

使用 Cloud Shell 中的 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) 命令，将此 Azure AD 用户添加为 Active Directory 管理员。 在以下命令中，替换 \<server-name>  。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

有关添加 Active Directory 管理员的详细信息，请参阅[为 Azure SQL 数据库服务器预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>设置 Visual Studio

若要在 Visual Studio 中启用开发和调试，请在 Visual Studio 中添加 Azure AD 用户，方法是从菜单中依次选择“文件” > “帐户设置”，然后单击“添加帐户”    。

若要设置进行 Azure 服务身份验证的 Azure AD 用户，请从菜单中依次选择“工具” > “选项”，然后依次选择“Azure 服务身份验证” > “帐户选择”     。 选择已添加的 Azure AD 用户，然后单击“确定”  。

现已准备好将 SQL 数据库作为后端，使用 Azure AD 身份验证来开发和调试应用程序。

## <a name="modify-aspnet-project"></a>修改 ASP.NET 项目

在 Visual Studio 中，打开包管理器控制台，并添加 NuGet 包 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
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

键入 `Ctrl+F5`，再次运行该应用。 浏览器中相同的 CRUD 应用程序现使用 Azure AD 身份验证直接连接到 Azure SQL 数据库。 此设置使你能够运行数据库迁移。 稍后，将更改部署到应用服务时，相同的设置将用于应用的托管标识。

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

请记住，在 `Web.config` 中所做的同一更改适用于托管标识，因此只需删除应用中的现有连接字符串，该字符串由 Visual Studio 在首次部署应用时创建。 使用以下命令，但将 \<app-name> 替换为应用名称  。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>发布更改

现在，剩下的操作是将更改发布到 Azure。

在“解决方案资源管理器”  中，右键单击 “DotNetAppSqlDb”  项目，并选择“发布”  。

![从解决方案资源管理器发布](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

在发布页中单击“发布”。  当新网页显示待办事项列表时，表明应用使用了托管标识连接到数据库。

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
