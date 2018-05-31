---
title: ActiveDirectoryInteractive 连接到 SQL | Microsoft Docs
description: 使用 SqlAuthenticationMethod.ActiveDirectoryInteractive 模式连接到 Azure SQL 数据库的 C# 代码示例（附带说明）。
services: sql-database
author: GithubMirek
manager: jhubbard
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 163b55e0f1727cc2ba9e3ee84eb58fe29f7a005d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779632"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>使用 ActiveDirectoryInteractive 模式连接到 Azure SQL 数据库

本文提供可连接到 Microsoft Azure SQL 数据库的可运行 C# 代码示例。 C# 程序使用支持 Azure AD 多重身份验证 (MFA) 的身份验证交互模式。 例如，连接尝试可以包括正发送至手机的验证代码。

有关 SQL 工具 MFA 支持的详细信息，请参阅 [SQL Server Data Tools (SSDT) 中的 Azure Active Directory 支持](https://docs.microsoft.com/sql/ssdt/azure-active-directory)。




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod .ActiveDirectoryInteractive 枚举值

自 .NET Framework 版本 4.7.2 起，枚举 [SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 有一个新值“.ActiveDirectoryInteractive”。 客户端 C# 程序使用此枚举值时，此值指示系统使用支持 MFA 的 Azure AD 交互模式对 Azure SQL 数据库进行身份验证。 然后，运行该程序的用户将看到以下对话框：

1. 一个显示 Azure AD 用户名并要求 Azure AD 用户密码的对话框。
    - 如果不需要密码，将不会显示此对话框。 如果用户域与 Azure AD 联合，则不需要密码。

    如果 Azure AD 中设置的策略将 MFA 应用于该用户，则接着将显示以下对话框。

2. 仅在用户首次体验 MFA 方案时，系统会显示额外对话框。 对话框要求提供要对其发送文本消息的移动电话号码。 每条消息提供用户必须在下一对话框中输入的“验证码”。

3. 另一个要求提供系统已发送到移动电话的 MFA 验证码的对话框。

有关如何将 Azure AD 配置为要求 MFA 的信息，请参阅[云中的 Azure 多重身份验证入门](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)。

有关这些对话框的屏幕截图，请参阅[为 SQL Server Management Studio 和 Azure AD 配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。

> [!TIP]
> 以下指向我们便捷的“.NET API 浏览器”工具的链接提供适用于所有 .NET Framework API 类型的常规搜索页：
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> 通过将类型名称添加到可选追加的“?term=”参数，搜索页可将结果准备就绪，等待我们去了解：
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>通过使用 Azure 门户准备 C#

我们假定已[创建 Azure SQL 数据库服务器](sql-database-get-started-portal.md)且该服务器可用。


### <a name="a-create-an-app-registration"></a>A. 创建应用注册

若要使用 Azure AD 身份验证，当程序尝试连接时，C# 客户端程序必须提供 GUID 作为 clientId。 完成应用注册可在 Azure 门户中生成并显示 GUID，标记为“应用程序 ID”。 导航步骤如下所示：

1. “Azure 门户”&gt;“Azure Active Directory”&gt;“应用注册”

    ![应用注册](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. 生成并显示“应用程序 ID”值。

    ![显示的应用 ID](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. “已注册应用”&gt;“设置”&gt;“所需权限”&gt;“添加”

    ![已注册应用的权限设置](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. “所需权限”&gt;“添加 API 访问”&gt;“选择一个 API”&gt;“Azure SQL 数据库”

    ![为 Azure SQL 数据库添加对 API 的访问](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. “API 访问”&gt;“选择权限”&gt;“委托的权限”

    ![为 Azure SQL 数据库委托对 API 的权限](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. 在 SQL 数据库服务器上设置 Azure AD 管理员

每个 Azure SQL 数据库服务器都有自己的 Azure AD SQL 逻辑服务器。 对于本 C# 方案，必须为 Azure SQL 服务器设置 Azure AD 管理员。

1. “SQL Server”&gt;“Active Directory 管理员”&gt;“设置管理员”

    - 有关 Azure SQL 数据库 Azure AD 管理员和用户的详细信息，请参阅[通过 SQL 数据库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)的“为 Azure SQL 数据库服务器预配 Azure Active Directory 管理员”部分中的屏幕截图。


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. 准备将 Azure AD 用户连接到特定的数据库

在特定于 Azure SQL 数据库服务器的 Azure AD 中，可以添加应对特定数据库有访问权限的用户。

有关详细信息，请参阅[将 Azure Active Directory 身份验证与 SQL 数据库、托管实例或 SQL 数据仓库结合使用](sql-database-aad-authentication.md)。


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. 将非管理员用户添加到 Azure AD

SQL 数据库服务器的 Azure AD 管理员可用来连接到 SQL 数据库服务器。 但是，更常规的方法是添加一个非管理员用户到 Azure AD。 将非管理员用户用于连接时，如果 MFA 由 Azure AD 应用于此用户，将调用 MFA 序列。




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

C# 程序依赖于命名空间“Microsoft.IdentityModel.Clients.ActiveDirectory”。 此命名空间的类在程序集中的名称相同。

- 使用 NuGet 下载并安装 ADAL 程序集。
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- 将引用添加到程序集，支持 C# 程序编译。




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod 枚举

C# 示例依赖的一个命名空间是“System.Data.SqlClient”。 需要特别注意的是枚举“SqlAuthenticationMethod”。 此枚举有以下值：

- **SqlAuthenticationMethod.ActiveDirectory*：&nbsp;将此值与 Azure AD 用户名一起使用，实现多重身份验证 MFA。
    - 此值是本文的重点。 它通过显示用户密码的对话框，接着显示 MFA 验证的对话框（如果 MFA 应用于此用户）来生成交互式体验。
    - 此值自 .NET Framework 版本 4.7.2 起提供。

- **SqlAuthenticationMethod.ActiveDirectory*Integrated***：&nbsp;对联合帐户使用此值。 对于联合帐户，Windows 域已知用户名。 此方法不支持 MFA。

- **SqlAuthenticationMethod.ActiveDirectory*Password***：&nbsp;使用此值进行需要 Azure AD 用户和用户密码的身份验证。 Azure SQL 数据库执行身份验证。 此方法不支持 MFA。




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>在 Azure 门户准备 C# 参数值

为了 C# 程序的成功运行，必须将相应值分配到以下静态字段。 这些静态字段的作用就像程序的参数。 此处显示使用虚构值的字段。 还显示 Azure 门户中可以获取相应值的位置：


| 静态字段名称 | 虚构值 | Azure 门户中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | “my-favorite-sqldb-svr.database.windows.net” | “SQL server”&gt;“按名称筛选” |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | “Azure Active Directory” &gt;“用户”&gt;“新来宾用户” |
| Initial_DatabaseName | “master” | “SQL server”&gt;“SQL 数据库” |
| ClientApplicationID | “a94f9c62-97fe-4d19-b06d-111111111111” | “Azure Active Directory”&gt;“应用注册”<br /> &nbsp; &nbsp; &gt;“按名称搜索”&gt;“应用程序 ID” |
| RedirectUri | 新 Uri（“https://bing.com/”） | “Azure Active Directory”&gt;“应用注册”<br /> &nbsp; &nbsp; &gt;“按名称搜索”&gt; [Your-App-regis]&gt;<br /> &nbsp; &nbsp;“设置”&gt;“RedirectURI”<br /><br />本文中，任何有效值均适用于 RedirectUri。 示例中并未真正使用值。 |
| &nbsp; | &nbsp; | &nbsp; |


具体取决于特定的方案，可能不需要前表中的所有参数值。




## <a name="run-ssms-to-verify"></a>运行 SSMS 进行验证

在运行 C# 程序前，它有助于运行 SQL Server Management Studio (SSMS)。 SSMS 运行可验证各种配置是否正确。 然后，C# 程序的任何失败都可以缩小到其源代码。


#### <a name="verify-sql-database-firewall-ip-addresses"></a>验证 SQL 数据库防火墙 IP 地址

从同一生成过程（稍后将运行 C# 程序）中的同一台计算机运行 SSMS。 可以使用你认为最简单的任意“身份验证”模式。 如果有任何迹象表明数据库服务器防火墙不接受 IP 地址，则可以依照 [Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-firewall-configure.md)所述修复此问题。


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>验证用于 Azure AD 的多重身份验证 (MFA)

再次运行 SSMS，这一次将“身份验证”设为“具有 MFA 支持的 Active Directory - 通用”。 必须具有 SSMS 17.5 或更高版本才可使用此选项。

有关详细信息，请参阅[为 SSMS 和 Azure AD 配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。




## <a name="c-code-example"></a>C# 代码示例

若要编译此 C# 示例，必须将引用添加到名为“Microsoft.IdentityModel.Clients.ActiveDirectory”的 DLL 程序集。


#### <a name="reference-documentation"></a>参考文档

- “System.Data.SqlClient”命名空间：
    - 搜索：&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - 指向：&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- “Microsoft.IdentityModel.Clients.ActiveDirectory”命名空间：
    - 搜索：&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - 指向：&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>C# 源代码，分两部分

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>C# 程序的第二个部分

为了更好地视觉展示，C# 程序分为两个代码块。 若要运行程序，请将这两个代码块粘贴在一起。

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

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

&nbsp;


## <a name="next-steps"></a>后续步骤

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

