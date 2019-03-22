---
title: ActiveDirectoryInteractive 连接到 SQL | Microsoft Docs
description: 使用 SqlAuthenticationMethod.ActiveDirectoryInteractive 模式连接到 Azure SQL 数据库的 C# 代码示例（附带说明）。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: 5f089148bafbab21721c83c0d4b6977a7db86320
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834578"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>使用 Active Directory MFA 连接到 Azure SQL 数据库

本文提供用于连接到 Microsoft Azure SQL 数据库的 C# 程序。 该程序使用支持 [Azure Active Directory (Azure AD) 多重身份验证 (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 的交互模式身份验证。

有关 SQL 工具 MFA 支持的详细信息，请参阅 [SQL Server Data Tools (SSDT) 中的 Azure Active Directory 支持](https://docs.microsoft.com/sql/ssdt/azure-active-directory)。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>针对 Azure SQL 数据库的多重身份验证

自 .NET Framework 版本 4.7.2 开始，枚举 [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 具有一个新值 - `ActiveDirectoryInteractive`。 在客户端 C# 程序中，该枚举值指示系统使用支持 MFA 的 Azure AD 交互模式连接到 Azure SQL 数据库。 然后，运行该程序的用户将看到以下对话框：

* 一个显示 Azure AD 用户名并要求输入用户密码的对话框。

   如果用户域与 Azure AD 联合，则不会显示此对话框，因为不需要密码。

   如果 Azure AD 策略对用户实施 MFA，则会显示接下来的两个对话框。

* 当用户首次执行 MFA 时，系统会显示一个对话框，要求提供短信所要发送到的手机号码。 每条短信提供了用户必须在下一对话框中输入的验证码。

* 一个要求提供系统已发送到手机的 MFA 验证码的对话框。

有关如何将 Azure AD 配置为要求 MFA 的信息，请参阅[云中的 Azure 多重身份验证入门](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)。

有关这些对话框的屏幕截图，请参阅[为 SQL Server Management Studio 和 Azure AD 配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

> [!TIP]
> 可以使用我们的 **.NET API 浏览器**工具页搜索 .NET Framework API：
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> 也可以使用可选的 **?term=&lt;搜索值&gt;** 参数直接进行搜索：
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>在 Azure 门户中配置 C# 应用程序

在开始之前，[Azure SQL 数据库服务器](sql-database-get-started-portal.md)应已创建且可用。

### <a name="register-your-app-and-set-permissions"></a>注册应用并设置权限


若要使用 Azure AD 身份验证，必须将 C# 程序注册为 Azure AD 应用程序。 只有 Azure AD 管理员或者分配有 Azure AD“应用程序开发人员”角色的用户才能注册应用。 有关分配角色的详细信息，请参阅[使用 Azure Active Directory 将管理员和非管理员角色分配到用户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

 完成应用注册后，会生成并显示一个**应用程序 ID**。 程序中必须包含此 ID 才能建立连接。


注册应用程序并为其设置所需的权限：

1. 在 Azure 门户中，转到“Azure Active Directory” > “应用注册” > “新建应用程序注册”。

    ![应用注册](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    创建应用注册后，将生成并显示“应用程序 ID”值。

    ![显示的应用 ID](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. “已注册的应用” > “设置” > “所需权限” > “添加”

    ![已注册应用的权限设置](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. “所需权限” > “添加” > “选择 API” > “Azure SQL 数据库”

    ![为 Azure SQL 数据库添加对 API 的访问](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. “API 访问” > “选择权限” > “委托的权限”

    ![为 Azure SQL 数据库委托对 API 的权限](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>为 SQL 数据库服务器设置 Azure AD 管理员


要使 C# 程序正常运行，Azure SQL 服务器管理员需要为 Azure SQL 服务器分配 Azure AD 管理员。 

 * “SQL Server” > “Active Directory 管理员” > “设置管理员”

有关 Azure SQL 数据库 Azure AD 管理员和用户的详细信息，请参阅[通过 SQL 数据库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)的“为 Azure SQL 数据库服务器预配 Azure Active Directory 管理员”部分中的屏幕截图。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>将非管理员用户添加到特定的数据库（可选）

SQL 数据库服务器的 Azure AD 管理员可以运行 C# 示例程序。 数据库中的 Azure AD 用户可以运行该程序。 已在数据库中并对该数据库拥有 `ALTER ANY USER` 权限的 Azure AD SQL 管理员或 Azure AD 用户可以添加用户。

可以使用 SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 命令将用户添加到数据库。 例如，`CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

有关详细信息，请参阅[将 Azure Active Directory 身份验证与 SQL 数据库、托管实例或 SQL 数据仓库结合使用](sql-database-aad-authentication.md)。

## <a name="new-authentication-enum-value"></a>新的身份验证枚举值

该 C# 示例依赖于 [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 命名空间。 枚举 `SqlAuthenticationMethod` 对于多重身份验证而言特别重要，其值如下：

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   结合 Azure AD 用户名使用此值可实施 MFA。 此值是本文的重点。 它通过显示用户密码的对话框，接着显示 MFA 验证的对话框（如果 MFA 应用于此用户）来生成交互式体验。 此值自 .NET Framework 版本 4.7.2 起提供。

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  对联合帐户使用此值。 对于联合帐户，Windows 域已知用户名。 此身份验证方法不支持 MFA。

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  使用此值进行需要 Azure AD 用户名和密码的身份验证。 Azure SQL 数据库执行身份验证。 此方法不支持 MFA。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>在 Azure 门户中设置 C# 参数值

要使 C# 程序成功运行，需将适当的值分配到静态字段。 此处显示了包含示例值的字段。 此外，显示了 Azure 门户中可以获取所需值的位置：

| 静态字段名称 | 示例值 | Azure 门户中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | “SQL 服务器” > “按名称筛选” |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | “Azure Active Directory” > “用户” > “新建来宾用户” |
| Initial_DatabaseName | "myDatabase" | “SQL 服务器” > “SQL 数据库” |
| ClientApplicationID | “a94f9c62-97fe-4d19-b06d-111111111111” | “Azure Active Directory” > “应用注册” > “按名称搜索” > “应用程序 ID” |
| RedirectUri | new Uri("https://mywebserver.com/") | “Azure Active Directory” > “应用注册” > “按名称搜索” > “[你的应用注册]” > “设置” > “RedirectURI”<br /><br />在本文中，任何有效值均适用于 RedirectUri，因为此处并不使用 RedirectUri。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 进行验证

在运行 C# 程序之前，最好在 SSMS 中检查设置和配置是否正确。 然后，可将任何 C# 程序错误范围缩小到源代码级别。

### <a name="verify-sql-database-firewall-ip-addresses"></a>验证 SQL 数据库防火墙 IP 地址

从同一建筑物中你要运行 C# 程序的同一台计算机运行 SSMS。 可以使用任何**身份验证**模式来运行此测试。 如果有任何迹象表明数据库服务器防火墙不接受 IP 地址，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-firewall-configure.md)获得帮助。

### <a name="verify-azure-active-directory-mfa"></a>验证 Azure Active Directory MFA

再次运行 SSMS，这一次将“身份验证”设为“具有 MFA 支持的 Active Directory - 通用”。 此选项需要 SSMS 17.5 或更高版本。

有关详细信息，请参阅[为 SSMS 和 Azure AD 配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

> [!NOTE]
> 如果你是数据库中的来宾用户，则还需要提供数据库的 Azure AD 域名 -“选项” > “AD 域名或租户 ID”。 若要在 Azure 门户中查找域名，请选择“Azure Active Directory” > “自定义域名”。 在 C# 示例程序中，不必要提供域名。

## <a name="c-code-example"></a>C# 代码示例

示例 C# 程序依赖于 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 程序集。

若要安装此包，请在 Visual Studio 中选择“项目” > “管理 NuGet 包”。 搜索并安装 **Microsoft.IdentityModel.Clients.ActiveDirectory**。

### <a name="c-source-code"></a>C# 源代码

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>C# 的实际测试输出

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库，但未来的所有开发都不适用于 Az.Sql 模块。 有关这些 cmdlet，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 命令在 Az 模块和 AzureRm 模块中的参数是大体上相同的。

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)