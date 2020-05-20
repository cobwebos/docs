---
title: 教程`:`使用托管标识访问 Azure SQL - Windows - Azure AD
description: 本教程将指导你完成使用 Windows VM 系统分配托管标识访问 Azure SQL 的过程。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fc5596c6914b77b09db10528af891d7e6bd0159
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977862"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>教程：使用 Windows VM 系统分配托管标识访问 Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Windows 虚拟机 (VM) 的系统分配标识访问 Azure SQL Server。 托管服务标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。 学习如何：

> [!div class="checklist"]
> * 授予 VM 对 Azure SQL 服务器的访问权限
> * 为 SQL 服务器启用 Azure AD 身份验证
> * 在数据库中创建一个代表 VM 的系统分配标识的包含用户
> * 使用 VM 标识获取访问令牌，并使用它查询 Azure SQL 服务器

## <a name="prerequisites"></a>必备条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>启用

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]


## <a name="grant-access"></a>授予访问权限

若要授予 VM 对 Azure SQL Server 中数据库的访问权限，可以使用现有 SQL Server，或创建一个新的 SQL Server。 若要使用 Azure 门户创建新的服务器和数据库，请遵循此 [Azure SQL 快速入门](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)。 [Azure SQL 文档](https://docs.microsoft.com/azure/sql-database/)中还提供了有关使用 Azure CLI 和 Azure PowerShell 执行这些操作的快速入门。

授予 VM 对数据库的访问权限需要执行两个步骤：

1. 为 SQL 服务器启用 Azure AD 身份验证。
2. 在数据库中创建一个代表 VM 的系统分配标识的**包含用户**。

### <a name="enable-azure-ad-authentication"></a>启用 Azure AD 身份验证

若要[为 SQL 服务器配置 Azure AD 身份验证](/azure/sql-database/sql-database-aad-authentication-configure)，请执行以下操作：

1.  在 Azure 门户的左侧导航栏中选择“SQL 服务器”。 
2.  单击要启用 Azure AD 身份验证的 SQL 服务器。
3.  在边栏选项卡的“设置”部分中，单击“Active Directory 管理员”。  
4.  在命令栏中单击“设置管理员”。 
5.  选择要设为服务器管理员的 Azure AD 用户帐户，单击“选择”。 
6.  在命令栏中，单击“保存”  。

### <a name="create-contained-user"></a>创建包含用户

本部分介绍如何在数据库中创建一个表示 VM 的系统分配标识的包含用户。 在此步骤中，需要使用 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 在开始之前，查看以下文章了解有关 Azure AD 集成的背景知识可能也有帮助：

- [使用 SQL 数据库和 SQL 数据仓库进行通用身份验证（MFA 的 SSMS 支持）](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB 需要唯一的 AAD 显示名称。 因此，AAD 帐户（如用户、组和服务主体（应用程序））以及启用了托管标识的 VM 名称必须在 AAD 中针对其显示名称进行唯一定义。 SQL DB 在使用 T-SQL 创建此类用户期间会检查 AAD 显示名称，如果它不唯一，则命令将无法请求为给定帐户提供唯一的 AAD 显示名称。

**若要创建包含用户，请执行以下操作：**

1. 启动 SQL Server Management Studio。
2. 在“连接到服务器”对话框的“服务器名称”字段中，输入 SQL 服务器名称。  
3. 在“身份验证”字段中，选择“Active Directory - 通用且具有 MFA 支持”。  
4. 在“用户名”字段中，输入已设为服务器管理员的 Azure AD 帐户的名称，例如 helen@woodgroveonline.com
5. 单击“选项”  。
6. 在“连接到数据库”字段中，输入要配置的非系统数据库的名称。 
7. 单击“连接”  。 完成登录过程。
8. 在“对象资源管理器”中，展开“数据库”文件夹。  
9. 右键单击某个用户数据库，并单击“新建查询”  。
10. 在查询窗口中输入以下行，在工具栏中单击“执行”： 

    > [!NOTE]
    > 以下命令中的 `VMName` 是在“先决条件”部分中对其启用系统分配标识的 VM 的名称。
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    该命令应该成功完成，为 VM 的系统分配标识创建包含的用户。
11. 清除查询窗口中的内容，输入以下行，在工具栏中单击“执行”： 

    > [!NOTE]
    > 以下命令中的 `VMName` 是在“先决条件”部分中对其启用系统分配标识的 VM 的名称。
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    命令应会成功完成，并授予包含的用户读取整个数据库的权限。

VM 中运行的代码现在可使用其系统分配托管标识获取令牌，并使用该令牌在 SQL 服务器中进行身份验证。

## <a name="access-data"></a>访问数据

本部分介绍如何使用 VM 的系统分配托管标识获取访问令牌，并使用它调用 Azure SQL。 Azure SQL 原本就支持 Azure AD 身份验证，因此可以直接接受使用 Azure 资源的托管标识获取的访问令牌。 使用**访问令牌**方法来与 SQL 建立连接。 在某种程度上，这是将 Azure SQL 与 Azure AD 集成，不同于在连接字符串中提供凭据。

以下 .NET 代码示例使用访问令牌来与 SQL 建立连接。 此代码必须在 VM 上运行才能访问 VM 的系统分配托管标识的终结点。 使用访问令牌方法需要 **.NET Framework 4.6** 或更高版本或 **.NET Core 2.2** 或更高版本。 相应地替换 AZURE-SQL-SERVERNAME 和 DATABASE 的值。 请注意，Azure SQL 的资源 ID 为“`https://database.windows.net/`”。

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
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
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

或者，可以使用 PowerShell 快速测试端到端设置，而无需在 VM 上编写和部署应用。

1.  在门户中，导航到“虚拟机”  并转到 Windows 虚拟机，然后在“概述”  中，单击“连接”  。
2.  输入创建 Windows VM 时添加的用户名  和密码  。
3.  现在，已经创建了与虚拟机的远程桌面连接  ，请在远程会话中打开 PowerShell  。
4.  使用 PowerShell 的 `Invoke-WebRequest` 向本地托管标识的终结点发出请求，以获取 Azure SQL 的访问令牌。

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    将响应从 JSON 对象转换为 PowerShell 对象。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    从响应中提取访问令牌。
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. 与 SQL 服务器建立连接。 请记得替换 AZURE-SQL-SERVERNAME 和 DATABASE 的值。
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    接下来，创建一个查询并将其发送到服务器。 请记得替换 TABLE 的值。

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

检查 `$DataSet.Tables[0]` 的值，以查看查询结果。


## <a name="disable"></a>禁用

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用系统分配托管标识来访问 Azure SQL Server。 若要了解有关 Azure SQL Server 的详细信息，请参阅：

> [!div class="nextstepaction"]
> [Azure SQL 数据库服务](/azure/sql-database/sql-database-technical-overview)
