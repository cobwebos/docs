---
title: "配置 Azure Active Directory 身份验证 - SQL | Microsoft Docs"
description: "了解如何通过使用 Azure Active Directory 身份验证连接到 SQL 数据库和 SQL 数据仓库。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/10/2017
ms.author: rickbyh
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 61a52813769891aa63373437e9300d4f8f47fab2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/08/2017

---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证

本文介绍如何创建和填充 Azure AD，以及之后如何通过 Azure SQL 数据库和 SQL 数据仓库使用 Azure AD。 有关概述，请参阅 [Azure Active Directory 身份验证](sql-database-aad-authentication.md)。

>  [!NOTE]  
>  不支持使用 Azure Active Directory 帐户连接到 Azure VM 上运行的 SQL Server。 请改用域 Active Directory 帐户。

## <a name="create-and-populate-an-azure-ad"></a>创建并填充 Azure AD
创建 Azure AD 并在其中填充用户和组。 创建初始域 Azure AD 托管的域。 Azure AD 也可以是本地 Active Directory 域服务，该服务可以与 Azure AD 联合。

有关详细信息，请参阅[将本地标识与 Azure Active Directory 集成](../active-directory/active-directory-aadconnect.md)、[将自己的域名添加到 Azure AD](../active-directory/active-directory-add-domain.md)、[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目录](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[使用 Windows PowerShell 管理 Azure AD](/powershell/azure/overview?view=azureadps-2.0) 和[混合标识所需端口和协议](../active-directory/active-directory-aadconnect-ports.md)。

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>可选：关联或更改当前与 Azure 订阅关联的 Active Directory
要将数据库与组织的 Azure AD 目录相关联，请允许该目录成为托管数据库的 Azure 订阅的一个受信任目录。 有关详细信息，请参阅 [Azure 订阅与 Azure AD 的关联方式](https://msdn.microsoft.com/library/azure/dn629581.aspx)。

**其他信息：**每个 Azure 订阅都与某个 Azure AD 实例存在信任关系。 这意味着，此订阅信任该目录对用户、服务和设备执行身份验证。 多个订阅可以信任同一个目录，但一个订阅只能信任一个目录。 可以访问 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)，在“设置”选项卡下查看订阅信任的目录。 订阅与目录之间的这种信任关系不同于订阅与 Azure 中所有其他资源（网站、数据库等）之间的信任关系，在后一种关系中，这些资源更像是订阅的子资源。 如果某个订阅过期，则对该订阅关联的其他那些资源的访问权限也将终止。 但是，目录将保留在 Azure 中，并且可以将另一个订阅与该目录相关联，然后继续管理目录用户。 有关资源的详细信息，请参阅[了解 Azure 中的资源访问](https://msdn.microsoft.com/library/azure/dn584083.aspx)。

以下过程介绍如何更改给定订阅的关联目录。
1. 以 Azure 订阅管理员身份连接到 [Azure 经典门户](https://manage.windowsazure.com/)。
2. 在左侧标题中，选择“设置”。
3. 订阅显示在“设置”屏幕中。 如果未显示所需订阅，请单击顶部的“订阅”，下拉“按目录筛选”框，选择包含你的订阅的目录，并单击“应用”。
   
    ![选择订阅][4]
4. 在“设置”区域中，单击订阅，并单击页面底部的“编辑目录”。
   
    ![AD-设置-门户][5]
5. 在“编辑目录”框中，选择与 SQL Server 或 SQL 数据仓库相关联的 Azure Active Directory，并单击箭头转到下一步。
   
    ![编辑-目录-选择][6]
6. 在“确认”目录“映射”对话框中，确认“全部协同管理员都将被删除”。
   
    ![编辑-目录-确认][7]
7. 单击复选标记以重新加载门户。

   > [!NOTE]
   > 更改目录时，将删除所有协同管理员、Azure AD 用户和组以及目录支持的资源用户的访问权限，他们不再有权访问此订阅或其资源。 只有作为服务管理员时，用户才能基于新的目录配置主体的访问权限。 此更改可能需要大量时间来传播到所有资源。 更改目录时还会更改 SQL 数据库和 SQL 数据仓库的 Azure AD 管理员，并且不允许任何现有 Azure AD 用户访问数据库。 必须重置 Azure AD 管理员（如下所述），并且必须创建新的 Azure AD 用户。
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>为 Azure SQL Server 创建 Azure AD 管理员
每个托管 SQL 数据库或 SQL 数据仓库的 Azure SQL Server 开始时只使用单个服务器管理员帐户，即整个 Azure SQL Server 的管理员。 必须创建第二个 SQL Server 管理员，这是一个 Azure AD 帐户。 此主体在 master 数据库中作为包含的数据库用户创建。 作为管理员，服务器管理员帐户是每个用户数据库中 **db_owner** 角色的成员，并且以 **dbo** 用户身份输入每个用户数据库。 有关服务器管理员帐户的详细信息，请参阅[在 Azure SQL 数据库中管理数据库和登录名](sql-database-manage-logins.md)。

将 Azure Active Directory 与异地复制结合使用时，必须为主服务器和辅助服务器配置 Azure Active Directory 管理员。 如果服务器没有 Azure Active Directory 管理员，则 Azure Active Directory 登录名和用户会收到“无法连接到服务器”错误。

> [!NOTE]
> 如果用户使用的不是基于 Azure AD 的帐户（包括 Azure SQL Server 管理员帐户），则无法创建基于 Azure AD 的用户，这是因为他们无权使用 Azure AD 验证建议的数据库用户。
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>为 Azure SQL Server 预配 Azure Active Directory 管理员

以下两个过程演示如何使用 PowerShell 在 Azure 门户中为 Azure SQL Server 预配 Azure Active Directory 管理员。

### <a name="azure-portal"></a>Azure 门户
1. 在 [Azure 门户](https://portal.azure.com/)右上角，单击相关连接以下拉包含可能 Active Directory 的列表。 选择正确的 Active Directory 作为默认的 Azure AD。 此步骤将与 Active Directory 关联的订阅链接到 Azure SQL Server，确保为 Azure AD 和 SQL Server 使用相同的订阅。 （Azure SQL Server 托管的可能是 Azure SQL 数据库或 Azure SQL 数据仓库。）   
    ![选择-AD][8]   
    
2. 在左侧标题中，选择“SQL Server”、选择你的“SQL Server”，然后在“SQL Server”边栏选项卡中，单击“Active Directory 管理员”。   
3. 在“Active Directory 管理员”边栏选项卡中，单击“设置管理员”。   
    ![选择 Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. 在“添加管理员”边栏选项卡中，搜索某个用户、将选择该用户或组作为管理员，并单击“选择”。 （“Active Directory 管理员”边栏选项卡会显示 Active Directory 的所有成员和组。 若用户或组为灰显，则无法选择，因为不支持它们作为 Azure AD 管理员。 （请参阅[将 Azure Active Directory 身份验证与使用 SQL 数据库或 SQL 数据仓库进行身份验证结合使用](sql-database-aad-authentication.md)的“Azure AD 功能和限制”部分中支持的管理员列表。）基于角色的访问控制 (RBAC) 仅适用于该门户，不会传播到 SQL Server。   
    ![选择管理员](./media/sql-database-aad-authentication/select-admin.png)  
    
5. 在“Active Directory 管理员”边栏选项卡顶部，单击“保存”。   
    ![保存管理员](./media/sql-database-aad-authentication/save-admin.png)   

更改管理员的过程可能需要几分钟时间。 然后，新管理员将出现在“Active Directory 管理员”框中。

   > [!NOTE]
   > 设置 Azure AD 管理员时，此新的管理员名称（用户或组）不能已作为 SQL Server 身份验证用户存在于虚拟 master 数据库中。 如果存在，Azure AD 管理员设置会失败；将回滚其创建，并指示此管理员（名称）已存在。 由于这种 SQL Server 身份验证用户不是 Azure AD 的一部分，因此使用 Azure AD 身份验证连接到服务器的任何尝试都会失败。
   > 


之后要删除管理员，请在“Active Directory 管理员”边栏选项卡顶部，单击“删除管理员”，并单击“保存”。

### <a name="powershell"></a>PowerShell
若要运行 PowerShell cmdlet，需要已安装并运行 Azure PowerShell。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

若要预配 Azure AD 管理员，请执行以下 Azure PowerShell 命令：

* Add-AzureRmAccount
* Select-AzureRmSubscription

用于预配和管理 Azure AD 管理员的 Cmdlet：

| Cmdlet 名称 | 说明 |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |为 Azure SQL Server 或 Azure SQL 数据仓库预配 Azure Active Directory 管理员。 （必须来自当前订阅。） |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |为 Azure SQL Server 或 Azure SQL 数据仓库删除 Azure Active Directory 管理员。 |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |返回有关为 Azure SQL Server 或 Azure SQL 数据仓库配置的当前 Azure Active Directory 管理员的信息。 |

使用 PowerShell 命令 get-help 可查看有关其中每个命令的更多详细信息，例如 ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``。

以下脚本为名为 **Group-23** 的资源组中的 **demo_server** 服务器预配名为 **DBA_Group** 的 Azure AD 管理员组（对象 ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`）：

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 输入参数接受 Azure AD 显示名称或用户主体名称。 例如，``DisplayName="John Smith"`` 和 ``DisplayName="johns@contoso.com"``。 对于 Azure AD 组，只支持 Azure AD 显示名称。

> [!NOTE]
> Azure PowerShell 命令 ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` 不会阻止你为不受支持的用户预配 Azure AD 管理员。 可以预配不受支持的用户，但其无法连接到数据库。 
> 

以下示例使用可选的 **ObjectID**：

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> 当 **DisplayName** 不唯一时，需要使用 Azure AD **ObjectID**。 若要检索 **ObjectID** 和 **DisplayName** 值，请使用 Azure 经典门户的 Active Directory 部分，并查看用户或组的属性。
> 

以下示例返回有关 Azure SQL Server 的当前 Azure AD 管理员的信息：

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

下面的示例删除一个 Azure AD 管理员：

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

也可以使用 REST API 预配 Azure Active Directory 管理员。 有关详细信息，请参阅 [Azure SQL 数据库的 Azure SQL 数据库操作的 Service Management REST API 参考和操作](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>CLI  
也可以通过调用以下 CLI 命令来预配 Azure AD 管理员：
| 命令 | 说明 |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#create) |为 Azure SQL Server 或 Azure SQL 数据仓库预配 Azure Active Directory 管理员。 （必须来自当前订阅。） |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#delete) |为 Azure SQL Server 或 Azure SQL 数据仓库删除 Azure Active Directory 管理员。 |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#list) |返回有关为 Azure SQL Server 或 Azure SQL 数据仓库配置的当前 Azure Active Directory 管理员的信息。 |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#update) |为 Azure SQL Server 或 Azure SQL 数据仓库更新 Azure Active Directory 管理员。 |

有关 CLI 命令的详细信息，请参阅 [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server)。  


## <a name="configure-your-client-computers"></a>配置客户端计算机
在所有客户端计算机上，如果应用程序或用户从中使用 Azure AD 标识连接到 Azure SQL 数据库或 Azure SQL 数据仓库，则必须安装以下软件：

* .NET Framework 4.6 或更高版本，可从 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx) 下载。
* 用于 SQL Server 的 Azure Active Directory 身份验证库 (**ADALSQL.DLL**)，提供多个语言版本（x86 和 amd64），可从下载中心中的[用于 Microsoft SQL Server 的 Microsoft Active Directory 身份验证库](http://www.microsoft.com/download/details.aspx?id=48742)下载。

可通过执行以下操作满足这些要求：

* 安装 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 或 [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) 符合 .NET Framework 4.6 要求。
* SSMS 安装 **ADALSQL.DLL** 的 x86 版本。
* SSDT 安装 **ADALSQL.DLL** 的 amd64 版本。
* [Visual Studio 下载](https://www.visualstudio.com/downloads/download-visual-studio-vs)提供的最新 Visual Studio 符合 .NET Framework 4.6 要求，但并未安装必需的 amd64 版本的 **ADALSQL.DLL**。

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>在映射到 Azure AD 标识的数据库中创建包含的数据库用户

Azure Active Directory 身份验证要求以包含的数据库用户的身份创建数据库用户。 基于 Azure AD 标识的包含的数据库用户是在 master 数据库中不具有登录名的数据库用户，它映射到与数据库相关联的 Azure AD 目录中的标识。 Azure AD 标识可以是单独的用户帐户，也可以是组。 有关包含的数据库用户的详细信息，请参阅[包含的数据库用户 - 使数据库可移植](https://msdn.microsoft.com/library/ff929188.aspx)。

> [!NOTE]
> 不能使用门户创建数据库用户（管理员除外）。 RBAC 角色不会传播到 SQL Server、SQL 数据库或 SQL 数据仓库。 Azure RBAC 角色用于管理 Azure 资源，不会应用于数据库权限。 例如，**SQL Server 参与者**角色不会授予连接到 SQL 数据库或 SQL 数据仓库的访问权限。 必须使用 Transact-SQL 语句直接在数据库中授予访问权限。
>

若要创建基于 Azure AD 的包含的数据库用户（而不是拥有数据库的服务器管理员），请以至少具有 **ALTER ANY USER** 权限的用户身份使用 Azure AD 标识连接到数据库。 然后，使用以下 Transact-SQL 语法：

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* 可以是 Azure AD 用户的用户主体名称，也可以是 Azure AD 组的显示名称。

**示例：**若要创建代表 Azure AD 联合或托管域用户的包含的数据库用户：
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

若要创建代表 Azure AD 或联合域组的包含的数据库用户，请提供安全组的显示名称：
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

若要创建代表可使用 Azure AD 令牌连接的应用程序的包含的数据库用户：

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  无法从与 Azure 订阅关联的 Azure Active Directory 之外的 Azure Active Directory 直接创建用户。 但是，可以将关联 Active Directory 中从其他 Active Directory 导入的用户成员（称为外部用户）添加到租户 Active Directory 中的 Active Directory 组。 通过为该 AD 组创建包含的数据库用户，外部 Active Directory 中的用户可以获取对 SQL 数据库的访问权限。   

有关基于 Azure Active Directory 标识创建包含的数据库用户的详细信息，请参阅 [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)。

> [!NOTE]
> 删除 Azure SQL Server 的 Azure Active Directory 管理员会阻止所有 Azure AD 身份验证用户连接到服务器。 必要时，SQL 数据库管理员可以手动删除无法使用的 Azure AD 用户。   

>  [!NOTE]
>  如果收到 **Connection Timeout Expired** 消息，则可能需要将连接字符串的 `TransparentNetworkIPResolution` 参数设置为 false。 有关详细信息，请参阅 [.NET Framework 4.6.1 的连接超时问题 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)。   

   
创建数据库用户时，该用户会收到 **CONNECT** 权限，并能够以 **PUBLIC** 角色的成员身份连接到该数据库。 最初，仅供用户使用的权限是授予 **PUBLIC** 角色的任何权限，或者授予其所属任何 Windows 组的任何权限。 预配基于 Azure AD 的包含的数据库用户后，可以授予用户其他权限，方法与向任何其他类型的用户授予权限相同。 通常，将权限授予数据库角色，并将用户添加到角色。 有关详细信息，请参阅[数据库引擎权限基础知识](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)。 有关特殊 SQL 数据库角色的详细信息，请参阅[在 Azure SQL 数据库中管理数据库和登录名](sql-database-manage-logins.md)。
如果将联合域用户导入到管理域，则此用户必须使用托管域标识。

> [!NOTE]
> Azure AD 用户在数据库元数据中均标记为类型 E (EXTERNAL_USER)，而组则标记为类型 X (EXTERNAL_GROUPS)。 有关详细信息，请参阅 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)。 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>使用 SSMS 或 SSDT 连接到用户数据库或数据仓库  
若要确认 Azure AD 管理员已正确设置，请使用 Azure AD 管理员帐户连接到 **master** 数据库。
若要预配基于 Azure AD 的包含的数据库用户（而不是拥有数据库的服务器管理员），请使用具有数据库访问权限的 Azure AD 标识连接到数据库。

> [!IMPORTANT]
> [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 Visual Studio 2015 中的 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) 支持 Azure Active Directory 身份验证。 2016 年 8 月版 SSMS 也包括对 Active Directory 通用身份验证的支持，这样管理员就能要求用户使用手机、短信、带 PIN 码的智能卡或移动应用通知进行多重身份验证。
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>借助 Azure AD 标识使用 SSMS 或 SSDT 进行连接  

以下过程演示如何使用 SQL Server Management Studio 或 SQL Server 数据库工具连接到具有 Azure AD 标识的 SQL 数据库。

### <a name="active-directory-integrated-authentication"></a>Active Directory 集成身份验证

如果从联合域使用 Azure Active Directory 凭据登录到 Windows，则使用此方法。

1. 启动 Management Studio 或 Data Tools 后，在“连接到服务器”（或“连接到数据库引擎”）对话框的“身份验证”框中，选择“Active Directory - 集成”。 由于会为连接提供现有凭据，因此无需密码，也无法输入密码。   

    ![选择 AD 集成身份验证][11]
2. 单击“选项”按钮，在“连接属性”页上的“连接到数据库”框中，键入你所要连接的用户数据库的名称。 （仅对“通用且具有 MFA 连接”选项支持“AD 域名或租户 ID”，否则它处于灰显状态。）  

    ![选择数据库名称][13]

## <a name="active-directory-password-authentication"></a>Active Directory 密码身份验证

在使用 Azure AD 托管域与 Azure AD 主体名称连接时使用此方法。 还可以将其用于无需访问该域的联合帐户，例如远程工作时。

如果从未与 Azure 联合的域中使用凭证登录到 Windows，或者基于初始域或客户端域通过 Azure AD 使用 Azure AD 身份验证，请使用此方法。

1. 启动 Management Studio 或 Data Tools 后，在“连接到服务器”（或“连接到数据库引擎”）对话框的“身份验证”框中，选择“Active Directory - 密码”。
2. 在“用户名”框中，以 **username@domain.com** 格式键入 Azure Active Directory 用户名。 这必须是来自 Azure Active Directory 的帐户或来自与 Azure Active Directory 联合的域的帐户。
3. 在“密码”框中，为 Azure Active Directory 帐户或联合域帐户键入用户密码。

    ![选择 AD 密码身份验证][12]
4. 单击“选项”按钮，在“连接属性”页上的“连接到数据库”框中，键入你所要连接的用户数据库的名称。 （请参阅前一选项的图。）

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>使用 Azure AD 标识从客户端应用程序进行连接

以下过程演示如何使用 Azure AD 标识，从客户端应用程序连接到 SQL 数据库。

###  <a name="active-directory-integrated-authentication"></a>Active Directory 集成身份验证

若要使用集成的 Windows 身份验证，域的 Active Directory 必须与 Azure Active Directory 联合。 连接到数据库的客户端应用程序（或服务）必须运行在已使用用户的域凭据加入域的计算机上。

若要使用集成的身份验证和 Azure AD 标识连接到数据库，数据库连接字符串中的身份验证关键字必须设置为 Active Directory Integrated。 下面的 C# 代码示例使用 ADO.NET。

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

不支持使用连接字符串关键字 ``Integrated Security=True`` 连接到 Azure SQL 数据库。 在进行 ODBC 连接时，需删除空格，并将“Authentication”设置为“ActiveDirectoryIntegrated”。

### <a name="active-directory-password-authentication"></a>Active Directory 密码身份验证

要使用集成的身份验证和 Azure AD 标识连接到数据库，必须将“Authentication”关键字设置为“Active Directory Password”。 连接字符串必须包含“User ID/UID”和“Password/PWD”关键字和值。 下面的 C# 代码示例使用 ADO.NET。

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

通过 [Azure AD 身份验证 GitHub 演示](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)中提供的演示代码示例，了解有关 Azure AD 身份验证方法的详细信息。

## <a name="azure-ad-token"></a>Azure AD 令牌
此身份验证方法允许从 Azure Active Directory (AAD) 获取令牌，使中间层服务能够连接到 Azure SQL 数据库或 Azure SQL 数据仓库。 这样，便可以实现包含基于证书的身份验证的复杂方案。 必须完成四个基本步骤才能使用 Azure AD 令牌身份验证：

1. 在 Azure Active Directory 中注册应用程序，并获取代码的客户端 ID。 
2. 创建代表应用程序的数据库用户。 （前面在步骤 6 中已完成。）
3. 在运行应用程序的客户端计算机上创建证书。
4. 为应用程序添加用作密钥的证书。

示例连接字符串：

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

有关详细信息，请参阅 [SQL Server 安全性博客](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)。

### <a name="sqlcmd"></a>sqlcmd

以下语句使用版本 13.1 的 sqlcmd 进行连接，该版本可从[下载中心](http://go.microsoft.com/fwlink/?LinkID=825643)下载。

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>后续步骤
- 有关 SQL 数据库中的访问和控制的概述，请参阅 [SQL 数据库访问和控制](sql-database-control-access.md)。
- 有关 SQL 数据库中的登录名、用户和数据库角色的概述，请参阅[登录名、用户和数据库角色](sql-database-manage-logins.md)。
- 有关数据库主体的详细信息，请参阅[主体](https://msdn.microsoft.com/library/ms181127.aspx)。
- 有关数据库角色的详细信息，请参阅[数据库角色](https://msdn.microsoft.com/library/ms189121.aspx)。
- 有关 SQL 数据库中的防火墙规则的详细信息，请参阅 [SQL 数据库防火墙规则](sql-database-firewall-configure.md)。

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png


