---
title: 使用 Windows VM 访问 Azure SQL
description: 本教程逐步讲解如何使用 Windows VM 托管服务标识访问 Azure SQL。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: ace7f11eeea081077855a409824272b4b55f3c33
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247221"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-sql"></a>教程：使用 Windows VM 托管服务标识访问 Azure SQL

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Windows 虚拟机 (VM) 的托管服务标识来访问 Azure SQL 服务器。 托管服务标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。 学习如何：

> [!div class="checklist"]
> * 在 Windows VM 上启用托管服务标识 
> * 授予 VM 对 Azure SQL 服务器的访问权限
> * 使用 VM 标识获取访问令牌，并使用它查询 Azure SQL 服务器

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Windows 虚拟机

本教程将新建 Windows VM。  还可以在现有 Azure VM 上启用托管服务标识。

1.  单击 Azure 门户左上角的“创建资源”按钮。
2.  选择“计算”，然后选择“Windows Server 2016 Datacenter”。 
3.  输入虚拟机信息。 此处创建的用户名和密码是用于登录虚拟机的凭据。
4.  在下拉列表中为虚拟机选择正确的订阅。
5.  要选择在其中创建虚拟机的新资源组，请选择“新建”。 完成后，单击“确定”。
6.  选择 VM 大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在“设置”页中保留默认值，然后单击“确定”。

    ![Alt 图像文本](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>在 VM 上启用托管服务标识 

可以通过虚拟机托管标识从 Azure AD 中获取访问令牌，无需在代码中插入凭据。 启用托管服务标识会告诉 Azure 为 VM 创建托管标识。 在表面下，在 VM 上启用托管服务标识会执行两项操作：向 Azure Active Directory 注册 VM 以创建其托管标识，以及在 VM 上配置标识。

1.  对于“虚拟机”，请选择要在其上启用托管标识的虚拟机。  
2.  单击左侧导航栏中的“配置”。 
3.  此时，将会看到托管服务标识。 若要注册并启用托管服务标识，请选择“是”，若要禁用，请选择“否”。 
4.  务必单击“保存”，以保存配置。  
    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>授予 VM 对 Azure SQL 服务器中的数据库的访问权限

现在，可以授予 VM 对 Azure SQL 服务器中的数据库的访问权限。  对于此步骤，可以使用现有的或创建新的 SQL 服务器。  若要使用 Azure 门户创建新的服务器和数据库，请遵循此 [Azure SQL 快速入门](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)。 [Azure SQL 文档](https://docs.microsoft.com/azure/sql-database/)中还提供了有关使用 Azure CLI 和 Azure PowerShell 执行这些操作的快速入门。

授予 VM 对数据库的访问权限需要执行三个步骤：
1.  在 Azure AD 中创建一个组，并使 VM 托管服务标识成为该组的成员。
2.  为 SQL 服务器启用 Azure AD 身份验证。
3.  在数据库中创建一个代表 Azure AD 组的**包含的用户**。

> [!NOTE]
> 通常，会创建一个直接映射到 VM 的托管服务标识的包含的用户。  目前，Azure SQL 不允许将代表 VM 托管服务标识的 Azure AD 服务主体映射到包含的用户。  支持的解决方法是，使 VM 托管服务标识成为 Azure AD 组的成员，然后在数据库中创建一个代表该组的包含的用户。


### <a name="create-a-group-in-azure-ad-and-make-the-vm-managed-service-identity-a-member-of-the-group"></a>在 Azure AD 中创建一个组，并使 VM 托管服务标识成为该组的成员

可以使用现有的 Azure AD 组，使用 Azure AD PowerShell 创建新组。  

首先，安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模块。 然后使用 `Connect-AzureAD` 登录，运行以下命令创建该组，并将其保存在变量中：

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

输出如下所示。另请检查变量的值：

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM Managed Service Identity access to SQL
```

接下来，将 VM 的托管服务标识添加到该组。  你需要托管服务标识的 **ObjectId**，这可以使用 Azure PowerShell 来获取。  首先，下载 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 然后使用 `Connect-AzureRmAccount` 登录，并运行以下命令：
- 如果有多个 Azure 订阅，请确保将会话上下文设置为所需的订阅。
- 列出 Azure 订阅中的可用资源，并检查资源组和 VM 名称是否正确。
- 使用 `<RESOURCE-GROUP>` 和 `<VM-NAME>` 的相应值获取托管服务标识 VM 的属性。

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

输出如下所示。另请检查 VM 的托管服务标识的服务主体对象 ID：
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

现在，将 VM 托管服务标识添加到该组。  只能使用 Azure AD PowerShell 将服务主体添加到组。  运行以下命令：
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

如果此后还检查了组成员身份，则输出如下所示：

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>为 SQL 服务器启用 Azure AD 身份验证

创建组并将 VM 托管服务标识添加到成员身份后，可使用以下步骤[配置 SQL 服务器的 Azure AD 身份验证](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server)：

1.  在 Azure 门户的左侧导航栏中选择“SQL 服务器”。
2.  单击要启用 Azure AD 身份验证的 SQL 服务器。
3.  在边栏选项卡的“设置”部分中，单击“Active Directory 管理员”。
4.  在命令栏中单击“设置管理员”。
5.  选择要设为服务器管理员的 Azure AD 用户帐户，单击“选择”。
6.  在命令栏中，单击“保存”。

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>在数据库中创建一个代表 Azure AD 组的包含的用户

在下一步骤中，需要使用 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 在开始之前，查看以下文章了解有关 Azure AD 集成的背景知识可能也有帮助：

- [使用 SQL 数据库和 SQL 数据仓库进行通用身份验证（MFA 的 SSMS 支持）](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  启动 SQL Server Management Studio。
2.  在“连接到服务器”对话框的“服务器名称”字段中，输入 SQL 服务器名称。
3.  在“身份验证”字段中，选择“Active Directory - 通用且具有 MFA 支持”。
4.  在“用户名”字段中，输入已设为服务器管理员的 Azure AD 帐户的名称，例如 helen@woodgroveonline.com
5.  单击“选项” 。
6.  在“连接到数据库”字段中，输入要配置的非系统数据库的名称。
7.  单击“连接”。  完成登录过程。
8.  在“对象资源管理器”中，展开“数据库”文件夹。
9.  右键单击某个用户数据库，并单击“新建查询”。
10.  在查询窗口中输入以下行，在工具栏中单击“执行”：
    
     ```
     CREATE USER [VM Managed Service Identity access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     命令应会成功完成，并创建组的包含的用户。
11.  清除查询窗口中的内容，输入以下行，在工具栏中单击“执行”：
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM Managed Service Identity access to SQL]
     ```

     命令应会成功完成，并授予包含的用户读取整个数据库的权限。

VM 中运行的代码现在可从托管服务标识获取令牌，并使用该令牌在 SQL 服务器中进行身份验证。

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>使用 VM 标识获取访问令牌，并使用该令牌调用 Azure SQL 

Azure SQL 原本就支持 Azure AD 身份验证，因此可以直接接受使用托管服务标识获取的访问令牌。  使用**访问令牌**方法来与 SQL 建立连接。  在某种程度上，这是将 Azure SQL 与 Azure AD 集成，不同于在连接字符串中提供凭据。

以下 .Net 代码示例使用访问令牌来与 SQL 建立连接。  此代码必须在 VM 上运行才能访问 VM 托管服务标识终结点。  使用访问令牌方法需要安装 **.Net Framework 4.6** 或更高版本。  相应地替换 AZURE-SQL-SERVERNAME 和 DATABASE 的值。  请注意，Azure SQL 的资源 ID 为“https://database.windows.net/”。

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
    // Call Managed Service Identity endpoint.
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

1.  在门户中，导航到“虚拟机”并转到 Windows 虚拟机，然后在“概述”中，单击“连接”。 
2.  输入创建 Windows VM 时添加的用户名和密码。 
3.  现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。 
4.  使用 PowerShell 的 `Invoke-WebRequest` 向本地托管服务标识终结点发出请求，以获取 Azure SQL 的访问令牌。

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

5.  与 SQL 服务器建立连接。 请记得替换 AZURE-SQL-SERVERNAME 和 DATABASE 的值。
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    接下来，创建一个查询并将其发送到服务器。  请记得替换 TABLE 的值。

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

检查 `$DataSet.Tables[0]` 的值，以查看查询结果。  恭喜，你已在无需提供凭据的情况下使用 VM 托管服务标识查询了数据库！

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建托管服务标识来访问 Azure SQL Server。  若要了解有关 Azure SQL Server 的详细信息，请参阅：

> [!div class="nextstepaction"]
>[Azure SQL 数据库服务](/azure/sql-database/sql-database-technical-overview)
