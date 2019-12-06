---
title: 在 Azure Data Lake Storage Gen2 中使用 & Acl 的 PowerShell （预览版）
description: 使用 PowerShell cmdlet 管理已启用分层命名空间（HNS）的存储帐户中的目录和文件和目录访问控制列表（ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 5eac6b112b46d1b2c80321bdeeee7f4e1fc5f4ac
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873907"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>在 Azure Data Lake Storage Gen2 中使用 & Acl 的 PowerShell （预览版）

本文介绍如何使用 PowerShell 创建和管理已启用分层命名空间（HNS）的存储帐户中的目录、文件和权限。 

> [!IMPORTANT]
> 本文中所述的 PowerShell 模块目前为公共预览版。

[Gen1 到 Gen2 映射](#gen1-gen2-map) | [提供反馈](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>必备组件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间（HNS）的存储帐户。 按照[以下](data-lake-storage-quickstart-create-account.md)说明创建一个。
> * .NET Framework 安装4.7.2 或更高版本。 请参阅[下载 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
> * PowerShell 版本 `5.1` 或更高版本。

## <a name="install-powershell-modules"></a>安装 PowerShell 模块

1. 使用以下命令验证安装的 PowerShell 版本是否 `5.1` 或更高版本。 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    若要升级 PowerShell 版本，请参阅[升级现有 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. 安装最新的**PowershellGet**模块。 然后，关闭并重新打开 Powershell 控制台。

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  安装**Az** preview module。

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>连接到帐户

1. 打开 Windows PowerShell 命令窗口。

2. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

3. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为要在其中创建和管理目录的存储帐户的订阅。

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅的 ID。

4. 获取存储帐户。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * 将 `<resource-group-name>` 的占位符值替换为资源组的名称。

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

5. 获取存储帐户上下文。

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过使用 `New-AzDatalakeGen2FileSystem` cmdlet 来创建一个。 

此示例将创建一个名为 `my-file-system`的文件系统。

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

此示例将添加同一目录，同时还会设置权限、umask、属性值和元数据值。 

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
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用 `Move-AzDataLakeGen2Item` cmdlet 重命名或移动目录。

此示例将目录从名称 `my-directory` 重命名为 `my-new-directory`的名称。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

此示例将名为 `my-directory` 的目录移动到名为 `my-subdirectory``my-directory-2` 的子目录。 此示例还将 umask 应用到子目录。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>删除目录

使用 `Remove-AzDataLakeGen2Item` cmdlet 删除目录。

此示例删除名为 `my-directory`的目录。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

您可以使用 `-Force` 参数删除文件而不会出现提示。

## <a name="download-from-a-directory"></a>从目录下载

使用 `Get-AzDataLakeGen2ItemContent` cmdlet 从目录下载文件。

此示例从名为 `my-directory`的目录中下载名为 `upload.txt` 的文件。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>列出目录内容

使用 `Get-AzDataLakeGen2ChildItem` cmdlet 列出目录的内容。

此示例列出名为 `my-directory`的目录的内容。 

若要列出文件系统的内容，请从命令中省略 `-Path` 参数。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

此示例列出名为 `my-directory` 的目录的内容，并在列表中包含 Acl。 它还使用 `-Recurse` 参数列出所有子目录的内容。

若要列出文件系统的内容，请从命令中省略 `-Path` 参数。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用 `New-AzDataLakeGen2Item` cmdlet 将文件上传到目录。

此示例将名为 `upload.txt` 的文件上传到名为 `my-directory`的目录。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

此示例将上传同一文件，但随后会设置目标文件的权限、umask、属性值和元数据值。 此示例还将这些值输出到控制台。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
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
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>删除文件

使用 `Remove-AzDataLakeGen2Item` cmdlet 删除文件。

此示例删除名为 `upload.txt`的文件。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

您可以使用 `-Force` 参数删除文件而不会出现提示。

## <a name="manage-access-permissions"></a>管理访问权限

你可以获取、设置和更新目录和文件的访问权限。

### <a name="get-directory-and-file-permissions"></a>获取目录和文件权限

使用 `Get-AzDataLakeGen2Item`cmdlet 获取目录或文件的 ACL。

此示例获取**目录**的 acl，然后将 acl 打印到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

此示例获取**文件**的 acl，然后将 acl 打印到控制台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下图显示了获取目录的 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在此示例中，拥有用户具有读取、写入和执行权限。 拥有组仅具有 "读取" 和 "执行" 权限。 有关访问控制列表的详细信息，请参阅[Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-directory-and-file-permissions"></a>设置目录和文件权限

使用 `New-AzDataLakeGen2ItemAclObject` cmdlet 为拥有用户、拥有组或其他用户创建 ACL。 然后，使用 `Update-AzDataLakeGen2Item` cmdlet 来提交 ACL。

此示例在**目录**中为所属用户、拥有组或其他用户设置 acl，然后将 acl 打印到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
此示例在**文件**中为所属用户、拥有组或其他用户设置 acl，然后将 acl 打印到控制台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下图显示了设置文件的 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在此示例中，拥有用户和拥有组只有读取和写入权限。 所有其他用户都具有 "写入" 和 "执行" 权限。 有关访问控制列表的详细信息，请参阅[Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="update-directory-and-file-permissions"></a>更新目录和文件权限

使用 `Get-AzDataLakeGen2Item` cmdlet 获取目录或文件的 ACL。 然后，使用 `New-AzDataLakeGen2ItemAclObject` cmdlet 创建新的 ACL 条目。 使用 `Update-AzDataLakeGen2Item` cmdlet 应用新 ACL。

此示例为用户提供对目录的 "写入" 和 "执行" 权限。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
此示例为用户提供对文件的 "写入" 和 "执行" 权限。

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>对文件系统中的所有项设置权限

可以将 `Get-AzDataLakeGen2Item` 和 `-Recurse` 参数与 `Update-AzDataLakeGen2Item` cmdlet 一起使用，以便在文件系统中以递归方式设置所有目录和文件的 ACL。 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 到 Gen2 的映射

下表显示了用于 Data Lake Storage Gen1 映射到 Data Lake Storage Gen2 的 cmdlet 的 cmdlet。

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| 说明 |
|--------|---------|-----|
|AzDataLakeStoreChildItem|AzDataLakeGen2ChildItem|默认情况下，AzDataLakeGen2ChildItem cmdlet 仅列出第一级子项。 -递归参数以递归方式列出子项目。 |
|AzDataLakeStoreItem<br>AzDataLakeStoreItemAclEntry<br>AzDataLakeStoreItemOwner<br>AzDataLakeStoreItemPermission|AzDataLakeGen2Item|AzDataLakeGen2Item cmdlet 的输出项具有这些属性的属性： Acl、所有者、组和权限。|
|AzDataLakeStoreItemContent|AzDataLakeGen2FileContent|AzDataLakeGen2FileContent cmdlet 将文件内容下载到本地文件。|
|移动-AzDataLakeStoreItem|移动-AzDataLakeGen2Item||
|新-AzDataLakeStoreItem|新-AzDataLakeGen2Item|此 cmdlet 将从本地文件上传新文件内容。|
|AzDataLakeStoreItemOwner<br>AzDataLakeStoreItemPermission<br>AzDataLakeStoreItemAcl|更新-AzDataLakeGen2Item|AzDataLakeGen2Item cmdlet 仅更新单个项，而不是以递归方式更新。 如果要以递归方式进行更新，请使用 AzDataLakeStoreChildItem cmdlet 将项列出，然后使用 AzDataLakeGen2Item cmdlet 将管道列出。|
|AzDataLakeStoreItem|AzDataLakeGen2Item|如果项不存在，AzDataLakeGen2Item cmdlet 将报告错误。|



## <a name="see-also"></a>另请参阅

* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [对 Azure 存储使用 Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
* [存储 PowerShell cmdlet](/powershell/module/az.storage)。

