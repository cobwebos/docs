---
title: 以递归方式为 Azure Data Lake Storage Gen2 设置 ACL | Microsoft Docs
description: 你可以以递归方式为父目录的现有子项添加、更新和删除访问控制列表。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/07/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: cedb6d162829d63aaac1a36b35abee1faeae3f1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843390"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>以递归方式为 Azure Data Lake Storage Gen2 设置访问控制列表 (ACL)

ACL 继承已可用于在父目录下创建的新子项。 你现在还可以以递归方式为父目录的现有子项添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。

> [!NOTE]
> 以递归方式设置访问列表的功能为公共预览版，在所有区域提供。  

[库](#libraries)  | [示例](#code-samples)  | [最佳做法](#best-practice-guidelines)  | [提供反馈](#provide-feedback)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。

- 执行递归 ACL 过程所需的正确权限。 正确的权限包括下列任一项： 

  - 一个预配的 Azure Active Directory (AD) [安全主体](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal)，它在目标容器父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。   

  - 你计划向其应用递归 ACL 过程的目标容器或目录的所有者用户。 这包括目标容器或目录中的所有子项。 

- 了解 ACL 如何应用于目录和文件。 请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

请参阅本文的“设置项目”部分，查看 PowerShell、.NET SDK 和 Python SDK 的安装指南。

## <a name="set-up-your-project"></a>设置项目

安装所需的库。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 确保已安装 .NET Framework。 请参阅[下载 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
 
2. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
3. 安装最新版本的 PowershellGet 模块。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. 关闭 PowerShell 控制台，然后重新将其打开。

5. 安装 **Az.Storage** 预览版模块。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="net"></a>[.NET](#tab/dotnet)

1. 打开一个命令窗口（例如：Windows PowerShell）。

2. 从你的项目目录中，使用 `dotnet add package` 命令安装 Azure.Storage.Files.DataLake 预览版包。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 将这些 using 语句添加到代码文件的顶部。

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. 下载[适用于 Python 的 Azure Data Lake Storage 客户端库](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)。

2. 安装使用 [pip](https://pypi.org/project/pip/) 下载的库。

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

将这些 import 语句添加到代码文件的顶部。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>连接到帐户

你可以使用 Azure Active Directory (AD) 或帐户密钥进行连接。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

打开 Windows PowerShell 命令窗口，使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的指示进行操作。

```powershell
Connect-AzAccount
```

如果你的标识已关联到多个订阅，请将活动订阅设置为要在其中创建和管理目录的存储帐户的订阅。 在此示例中，请将 `<subscription-id>` 占位符值替换为你的订阅 ID。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

接下来，选择希望命令如何获取存储帐户的授权。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>选项 1：使用 Azure Active Directory (AD) 获取授权

使用此方法，系统可确保你的用户帐户具有相应的 Azure 基于角色的访问控制 (Azure RBAC) 分配和 ACL 权限。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

下表显示了每个受支持的角色及其 ACL 设置功能。

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>选项 2：使用存储帐户密钥获取授权

利用此方法，系统不会检查 Azure RBAC 或 ACL 权限。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

若要使用本文中的代码片段，需创建一个表示存储帐户的 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 实例。

#### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

可以使用[适用于 .NET 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)，通过 Azure AD 对应用程序进行身份验证。

安装包后，将此 using 语句添加到代码文件的顶部。

```csharp
using Azure.Identity;
```

获取客户端 ID、客户端机密和租户 ID。 若要执行此操作，请参阅[从 Azure AD 获取用于对客户端应用程序的请求进行授权的令牌](../common/storage-auth-aad-app.md)。 作为该过程的一部分，你必须为安全主体分配以下 [azure 基于角色的访问控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 角色。 

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

此方法是连接到帐户的最简单方法。 

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> 有关更多示例，请参阅[适用于 .NET 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)文档。

### <a name="python"></a>[Python](#tab/python)

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

可以使用[适用于 Python 的 Azure 标识客户端库](https://pypi.org/project/azure-identity/)，通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。  若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。 作为该过程的一部分，你必须为安全主体分配以下 [azure 基于角色的访问控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 角色。 

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 有关更多示例，请参阅[适用于 Python 的 Azure 标识客户端库](https://pypi.org/project/azure-identity/)文档。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。 

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- 将 `storage_account_name` 占位符值替换为存储帐户的名称。

- 将 `storage_account_key` 占位符值替换为存储帐户访问密钥。

---

## <a name="set-an-acl-recursively"></a>以递归方式设置 ACL

*设置*acl 时，将**替换**整个 acl，包括它的所有条目。 如果要更改安全主体的权限级别，或将新的安全主体添加到 ACL 而不影响其他现有项，则应改为 *更新* acl。 若要更新 ACL 而不是替换它，请参阅本文的 " [以递归方式更新 acl](#update-an-acl-recursively) " 部分。   

本部分包含有关如何设置 ACL 的示例 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Set-AzDataLakeGen2AclRecursive` cmdlet 以递归方式设置 ACL。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 这些条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 如果要设置**默认**ACL 条目，请在运行**AzDataLakeGen2ItemAclObject**命令时使用 **-属性 namedobject.defaultscope**参数。 例如： `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`。

### <a name="net"></a>[.NET](#tab/dotnet)

通过调用 **DataLakeDirectoryClient.SetAccessControlRecursiveAsync** 方法以递归方式设置 ACL。 将[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[列表](/dotnet/api/system.collections.generic.list-1)传递给此方法。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。 

如果要设置**默认**的 ACL 项，则可将[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope)属性设置为**true**。 

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为的布尔参数 `isDefaultScope` ，该参数指定是否设置默认 ACL。 在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的构造函数中使用该参数。 ACL 的条目为拥有用户授予 "读取"、"写入" 和 "执行" 权限，为 "拥有" 组提供 "仅读" 和 "执行" 权限，并使所有人都无法访问。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

通过调用 **DataLakeDirectoryClient.set_access_control_recursive** 方法以递归方式设置 ACL。

如果要设置 **默认** acl 条目，请将字符串添加 `default:` 到每个 ACL 条目字符串的开头。 

此示例设置名为 `my-parent-directory` 的目录的 ACL。 

此方法接受一个名为的布尔参数 `is_default_scope` ，该参数指定是否设置默认 ACL。 如果该参数为 `True` ，则 ACL 项列表前面带有字符串 `default:` 。 

ACL 的条目为拥有用户授予 "读取"、"写入" 和 "执行" 权限，为 "拥有" 组提供 "仅读" 和 "执行" 权限，并使所有人都无法访问。 此示例中的最后一个 ACL 条目为特定用户提供了对象 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "的读取和执行权限。这些条目为拥有用户提供 "读取"、"写入" 和 "执行" 权限，为 "拥有" 组提供 "仅读" 和 "执行" 权限，并使所有人都无法访问。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>以递归方式更新 ACL

*更新*acl 时，将修改 acl，而不是替换 acl。 例如，你可以将新的安全主体添加到 ACL，而不会影响 ACL 中列出的其他安全主体。  若要替换 ACL 而不是对其进行更新，请参阅本文的 [设置 acl 递归](#set-an-acl-recursively) 部分。 

若要更新 ACL，请使用要更新的 ACL 项创建一个新的 ACL 对象，然后在 "更新 ACL" 操作中使用该对象。 不要获取现有 ACL，只需提供要更新的 ACL 项。

本部分包含有关如何更新 ACL 的示例。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 **Update-AzDataLakeGen2AclRecursive** cmdlet 以递归方式更新 ACL。 

此示例以写入权限更新某个 ACL 条目。 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 如果要更新**默认**ACL 条目，请在运行**AzDataLakeGen2ItemAclObject**命令时使用 **-属性 namedobject.defaultscope**参数。 例如： `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`。

### <a name="net"></a>[.NET](#tab/dotnet)

通过调用 **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync** 方法以递归方式更新 ACL。  将[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[列表](/dotnet/api/system.collections.generic.list-1)传递给此方法。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。 

如果要更新**默认**ACL 条目，则可以将[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope)属性设置为**true**。 

此示例以写入权限更新某个 ACL 条目。 此方法接受一个名为的布尔参数 `isDefaultScope` ，该参数指定是否更新默认 ACL。 在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的构造函数中使用该参数。

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

通过调用 **DataLakeDirectoryClient.update_access_control_recursive** 方法以递归方式更新 ACL。 如果要更新 **默认** acl 条目，请将字符串添加 `default:` 到每个 ACL 条目字符串的开头。 

此示例以写入权限更新某个 ACL 条目。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为的布尔参数 `is_default_scope` ，该参数指定是否更新默认 ACL。 如果该参数为 `True` ，则更新后的 ACL 条目将以字符串开头 `default:` 。  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>以递归方式删除 ACL 条目

你可以采用递归方式删除一个或多个 ACL 条目。 若要删除 ACL 条目，请为要删除的 ACL 条目创建新的 ACL 对象，然后在 "删除 ACL" 操作中使用该对象。 不要获取现有 ACL，只需提供要删除的 ACL 项。 

本部分包含有关如何删除 ACL 的示例。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 **Remove-AzDataLakeGen2AclRecursive** cmdlet 删除 ACL 条目。 

此示例从容器的根目录中删除 ACL 条目。  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> 如果要删除**默认**ACL 条目，请在运行**AzDataLakeGen2ItemAclObject**命令时使用 **-属性 namedobject.defaultscope**参数。 例如： `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`。

### <a name="net"></a>[.NET](#tab/dotnet)

通过调用 **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync** 方法删除 ACL 条目。 将[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[列表](/dotnet/api/system.collections.generic.list-1)传递给此方法。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。 

如果要删除**默认**ACL 条目，则可将[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[属性 namedobject.defaultscope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope)属性设置为**true**。 

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为的布尔参数 `isDefaultScope` ，该参数指定是否从默认 ACL 中删除该项。 在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的构造函数中使用该参数。

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

通过调用 **DataLakeDirectoryClient.remove_access_control_recursive** 方法删除 ACL 条目。 如果要删除 **默认** acl 条目，请将该字符串添加 `default:` 到 ACL 条目字符串的开头。 

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为的布尔参数 `is_default_scope` ，该参数指定是否从默认 ACL 中删除该项。 如果该参数为 `True` ，则更新后的 ACL 条目将以字符串开头 `default:` 。 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        if is_default_scope:
           acl = 'default:user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>从故障中恢复

你可能会遇到运行时错误或权限错误。 对于运行时错误，请从头开始重启此过程。 如果安全主体没有足够的权限修改要修改的目录层次结构中的目录或文件的 ACL，则会出现权限错误。 请解决权限问题，然后选择通过使用继续标记从故障点继续执行此过程，或者从头重启此过程。 如果希望从头开始重启，则无需使用继续标记。 你可以重新应用 ACL 条目，而不会产生任何负面影响。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

将结果返回到变量。 通过管道将失败的条目传输到格式化的表。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

根据表的输出，你可以修复所有权限错误，然后使用继续标记继续执行。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 ``null`` 值。 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 ``None`` 值。 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>资源

本部分包含指向库和代码示例的链接。

#### <a name="libraries"></a>库

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>代码示例

- PowerShell：[自述文件](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D) | [示例](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET：[自述文件](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0) | [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python:[自述文件](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0) | [示例](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>最佳做法准则

本部分提供了有关以递归方式设置 ACL 的一些最佳做法指南。 

#### <a name="handling-runtime-errors"></a>处理运行时错误

发生运行时错误可能有许多原因（例如：中断或客户端连接问题）。 如果遇到运行时错误，请重启递归 ACL 过程。 可以将 ACL 重新应用于项，而不会造成负面影响。 

#### <a name="handling-permission-errors-403"></a>处理权限错误 (403)

如果在运行递归 ACL 过程时遇到访问控制异常，则表明 AD [安全主体](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal)可能没有足够的权限将 ACL 应用于目录层次结构中的一个或多个子项。 发生权限错误时，此过程会停止，系统会提供一个继续标记。 请修复权限问题，然后使用继续标记来处理剩余的数据集。 已成功处理的目录和文件不需要再次处理。 你还可以选择重启递归 ACL 过程。 可以将 ACL 重新应用于项，而不会造成负面影响。 

#### <a name="credentials"></a>凭据 

建议你预配一个在目标存储帐户或容器范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色的 Azure AD 安全主体。 

#### <a name="performance"></a>性能 

为了减少延迟，建议你在与存储帐户位于同一区域中的 Azure 虚拟机 (VM) 中运行递归 ACL 过程。 

#### <a name="acl-limits"></a>ACL 限制

可应用于目录或文件的 ACL 的最大数目为 32 个访问 ACL 和 32 个默认 ACL。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>提供反馈或报告问题

你可以在上提供反馈或报告问题  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) 。

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [已知问题](data-lake-storage-known-issues.md)


