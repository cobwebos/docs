---
title: 用于文件和 ACL 的 Azure Data Lake Storage Gen2 PowerShell（预览版）
description: 使用 PowerShell cmdlet 在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 58548c5cb1aa6aba6dda09d5d420b36bb8154726
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656397"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 PowerShell 管理 Azure Data Lake Storage Gen2 中的目录、文件和 ACL（预览版）

本文介绍如何使用 PowerShell 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

> [!IMPORTANT]
> 本文中所述的 PowerShell 模块目前以公共预览版提供。

[第 1 代到第 2 代映射](#gen1-gen2-map) | [提供反馈](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。
> * 已安装 .NET Framework 4.7.2 或更高版本。 请参阅[下载 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
> * PowerShell `5.1` 或更高版本。

## <a name="install-powershell-modules"></a>安装 PowerShell 模块

1. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. 安装最新的 **PowershellGet** 模块。 然后，关闭并重新打开 PowerShell 控制台。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. 安装 **Az.Storage** 预览版模块。

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>连接到帐户

打开 Windows PowerShell 命令窗口，使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的指示进行操作。

```powershell
Connect-AzAccount
```

如果你的标识已关联到多个订阅，请将活动订阅设置为要在其中创建和管理目录的存储帐户的订阅。 在此示例中，请将 `<subscription-id>` 占位符值替换为你的订阅 ID。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

接下来，选择希望命令如何获取存储帐户的授权。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>选项 1：使用 Azure 活动目录 （AD） 获取授权

如果使用此方法，系统可确保用户帐户具有适当的基于角色的访问控制 (RBAC) 分配和 ACL 权限。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>选项 2：使用存储帐户密钥获取授权

使用此方法，系统不检查 RBAC 或 ACL 权限。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以使用 `New-AzDatalakeGen2FileSystem` cmdlet 创建一个文件系统。 

此示例创建名为 `my-file-system` 的文件系统。

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>创建目录

使用 `New-AzDataLakeGen2Item` cmdlet 创建目录引用。 

此示例将名为 `my-directory` 的目录添加到文件系统。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

此示例添加相同的目录，但同时还会设置权限、umask、属性值和元数据值。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>显示目录属性

此示例使用 `Get-AzDataLakeGen2Item` cmdlet 获取目录，然后将属性值输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用 `Move-AzDataLakeGen2Item` cmdlet 重命名或移动目录。

此示例将目录的名称 `my-directory` 重命名为 `my-new-directory`。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> 如果要在没有`-Force`提示的情况下覆盖，请使用 参数。

此示例将名为 `my-directory` 的目录移到名为 `my-subdirectory` 的 `my-directory-2` 子目录。 此示例还将 umask 应用到该子目录。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>删除目录

使用 `Remove-AzDataLakeGen2Item` cmdlet 删除目录。

此示例删除名为 `my-directory` 的目录。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

可以使用 `-Force` 参数删除文件，而不会看到提示。

## <a name="download-from-a-directory"></a>从目录下载

使用 `Get-AzDataLakeGen2ItemContent` cmdlet 从目录中下载文件。

此示例从名为 `my-directory` 的目录中下载名为 `upload.txt` 的文件。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>列出目录内容

使用 `Get-AzDataLakeGen2ChildItem` cmdlet 列出目录的内容。 可以使用可选参数`-OutputUserPrincipalName`获取用户的名称（而不是对象 ID）。

此示例列出名为 `my-directory` 的目录的内容。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

下面的`ACL`示例列出了`Permissions``Group``Owner`目录中每个项目的属性。 需要`-FetchProperty`该参数来获取`ACL`属性的值。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

若要列出文件系统的内容，请在命令中省略 `-Path` 参数。

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用 `New-AzDataLakeGen2Item` cmdlet 将文件上传到目录。

此示例将名为 `upload.txt` 的文件上传到名为 `my-directory` 的目录。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

此示例上传同一文件，但随后会设置目标文件的权限、umask、属性值和元数据值。 此示例还会将这些值输出到控制台。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>显示文件属性

此示例使用 `Get-AzDataLakeGen2Item` cmdlet 获取文件，然后将属性值输出到控制台。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>删除文件

使用 `Remove-AzDataLakeGen2Item` cmdlet 删除文件。

此示例删除名为 `upload.txt` 的文件。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

可以使用 `-Force` 参数删除文件，而不会看到提示。

## <a name="manage-access-permissions"></a>管理访问权限

您可以获取、设置和更新文件系统、目录和文件的访问权限。

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 为命令授权，请确保已为安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-permissions"></a>获取权限

使用 `Get-AzDataLakeGen2Item` cmdlet 获取目录或文件的 ACL。


此示例获取**文件系统**的 ACL，然后将 ACL 打印到控制台。

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例获取某个**目录**的 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

此示例获取某个**文件**的 ACL，然后将 ACL 输出到控制台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下图显示了获取目录 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在此示例中，所有者用户拥有读取、写入和执行权限。 所有者组仅拥有读取和执行权限。 有关这些访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-or-update-permissions"></a>设置或更新权限

使用 `set-AzDataLakeGen2ItemAclObject` cmdlet 为所有者用户、所有者组或其他用户创建 ACL。 然后使用 `Update-AzDataLakeGen2Item` cmdlet 提交 ACL。

本示例为拥有的用户、拥有组或其他用户在**文件系统**上设置 ACL，然后将 ACL 打印到控制台。

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例针对所有者用户、所有者组或其他用户的**目录**设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
此示例针对所有者用户、所有者组或其他用户的**文件**设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下图显示了设置文件 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在此示例中，所有者用户和所有者组只拥有读取和写入权限。 所有其他用户拥有写入和执行权限。 有关这些访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。


### <a name="set-permissions-on-all-items-in-a-file-system"></a>对文件系统中的所有项设置权限

可以在 `Update-AzDataLakeGen2Item` cmdlet 中结合使用 `Get-AzDataLakeGen2Item` 和 `-Recurse` 参数，以递归方式设置文件系统中所有目录和文件的 ACL。 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>第 1 代到第 2 代映射

下表显示了用于数据存储库的 cmdlet 第 1 代如何映射到数据湖存储 Gen2 的 cmdlet。

|数据存储湖存储第 1 代 cmdlet| 数据存储湖存储第2代 cmdlet| 说明 |
|--------|---------|-----|
|获取阿兹达特湖存储子项目|获取-阿兹达莱克根2子项目|默认情况下，获取-AzDataLakeGen2ChildItem cmdlet 仅列出第一级子项。 -Recurse 参数递归地列出子项。 |
|获取-阿兹达数据湖存储项目<br>获取-阿兹达湖存储项目Aclentry<br>获取阿兹达数据湖存储项目所有者<br>获取阿兹达数据湖存储项目权限|获取-阿兹达莱克根2项目|Get-AzDataLakeGen2Item cmdlet 的输出项具有以下属性：Acl、所有者、组、权限。|
|获取阿兹达数据湖存储项目内容|获取-阿兹达莱克根2文件内容|获取-AzDataLakeGen2文件内容 cmdlet 将文件内容下载到本地文件。|
|移动-阿兹达湖存储项目|移动-阿兹达莱克根2项目||
|新-阿兹达湖存储项目|新-阿兹达莱克根2项目|此 cmdlet 从本地文件上载新文件内容。|
|删除-阿兹达数据湖存储项目|删除-阿兹达莱克根2项目||
|设置-阿兹达湖存储项目所有者<br>设置-阿兹达湖存储项目权限<br>设置-阿兹达湖存储项目Acl|更新-阿兹达莱克根2项目|更新-AzDataLakeGen2Item cmdlet 仅更新单个项目，而不是递归更新。 如果要以递归方式更新，请使用 Get-AzDataLakeStoreChildItem cmdlet 列出项目，然后管道到更新-AzDataLakeGen2Item cmdlet。|
|测试-阿兹达湖存储项目|获取-阿兹达莱克根2项目|如果项目不存在，Get-AzDataLakeGen2Item cmdlet 将报告错误。|



## <a name="see-also"></a>请参阅

* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [将 Azure PowerShell 与 Azure 存储一起使用](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
* [存储电源外壳 cmdlet](/powershell/module/az.storage)。

