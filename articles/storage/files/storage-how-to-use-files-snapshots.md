---
title: "使用共享快照（预览版）| Microsoft Docs"
description: "作为备份共享的一种方式，共享快照是某个时间点拍摄的 Azure 文件共享的只读版本。"
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: eb5c7d0fcb9e1106dbc0aa577ba5ecfa1bf6bee7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="work-with-share-snapshots-preview"></a>使用共享快照（预览版）
共享快照（预览版）是某个时间点拍摄的 Azure 文件共享的只读版本。 在创建共享快照后，可以读取、复制或删除该快照，但无法对其进行修改。 利用共享快照，可以对共享内容在某个时间点的状态进行备份。 

本文介绍如何创建、管理和删除共享快照。 有关详细信息，请参阅[共享快照概述](storage-snapshots-files.md)或[快照常见问题解答](storage-files-faq.md)。

## <a name="create-a-share-snapshot"></a>创建共享快照

可以使用 Azure 门户、PowerShell、Azure CLI、REST API 或任何 Azure 存储 SDK 来创建共享快照。 以下部分介绍如何使用门户、CLI 和 PowerShell 创建共享快照。 

可以在使用文件共享时拍摄其共享快照。 但是，共享快照仅捕获在发出共享快照命令时已写入文件共享的数据。 这可能不会包括由任何应用程序或操作系统缓存的所有数据。

### <a name="create-a-share-snapshot-by-using-the-portal"></a>使用门户创建共享快照  
若要创建时间点共享快照，请转到门户中的文件共享，然后选择“创建快照”。

>   ![门户中的快照菜单](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 创建共享快照
可以使用 `az storage share snapshot` 命令创建共享快照：

```azurecli-interactive
az storage share snapshot -n <share name>
```

示例输出：
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>使用 PowerShell 创建共享快照
可以使用 `$share.Snapshot()` 命令创建共享快照：

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>执行常用的共享快照操作

可以使用 Windows 中的“以前版本”选项卡或通过 REST、客户端库、PowerShell 和门户来枚举与文件共享关联的共享快照。 装载文件共享后，可以使用 Windows 中的“以前版本”选项卡查看所有以前版本的文件。 

以下部分介绍如何使用 Azure 门户、Windows 和 Azure CLI 2.0 来列出、浏览到和还原共享快照。

### <a name="share-snapshot-operations-in-the-portal"></a>门户中的共享快照操作

可以在门户中查看文件共享的所有共享快照，并浏览到共享快照以查看其内容。

#### <a name="view-a-share-snapshot"></a>查看共享快照
在文件共享的“快照”下，选择“查看快照”。

![门户中的“查看快照”命令](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>列出并浏览到共享快照内容
查看共享快照的列表，然后通过选择所需的时间戳直接浏览到一个快照的内容。

![时间戳列表中的选定快照](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

还可以在列表快照视图上选择“连接”按钮，以获取 `net use` 命令，以及指向特定共享快照的目录路径。 可以直接浏览到该快照。


![带命令框的“连接”窗格](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>从共享快照下载或还原
从门户下载或还原快照中的文件，只需分别使用“下载”或“还原”按钮即可。

![“下载”和“还原”按钮](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Windows 中的共享快照操作
拍摄文件共享的共享快照后，即可从 Windows 上装载的文件共享来查看共享、目录或特定文件的以前版本。 例如，下面介绍如何使用“以前版本”功能来查看并还原 Windows 中目录的以前版本。

> [!Note]  
> 可以在共享级别和文件级别执行相同的操作。 列表中仅显示更改了该目录或文件的版本。 如果某个特定目录或文件在两个共享快照之间未发生更改，则该共享快照在共享级别的以前版本列表中显示，而不在目录或文件的以前版本列表中显示。

#### <a name="mount-a-file-share"></a>装载文件共享
首先，使用 `net use` 命令装载文件共享。

#### <a name="open-a-mounted-share-in-file-explorer"></a>在文件资源管理器中打开已装载的共享
转到文件资源管理器，并找到已装载的共享。

![文件资源管理器中的已装载共享](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>列出以前版本
浏览到需要还原的项或父项。 通过双击转到所需的目录。 右键单击，然后从菜单中选择“属性”。

![所选目录的右键单击菜单](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

选择"以前版本”，以查看此目录的共享快照列表。 列表可能需要几秒钟才能加载，具体要取决于网速和目录中共享快照的数量。

![“以前版本”选项卡](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

可以选择“打开”以打开特定快照。 

![打开的快照](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>从以前版本还原
选择“还原”，以递归方式将整个目录在共享快照创建时包含的内容复制到原始位置。
 ![警告消息中的“还原”按钮](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0 中的共享快照操作
Azure CI 2.0 可用于执行各种操作，如列出共享快照、浏览到共享快照内容、从共享快照还原或下载文件，或删除共享快照。

#### <a name="list-share-snapshots"></a>列出共享快照

可以将 [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 和 `--include-snapshots` 配合使用，列出特定共享的快照：

```azurecli-interactive 
az storage share list --include-snapshots
```

该命令提供共享快照及其所有关联属性的列表。 以下输出为示例：

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>浏览到共享快照
可以使用 [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 浏览到特定共享快照并查看其内容。 指定要浏览到的共享名称和时间戳，如以下示例所示：

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

在输出中可以看到，共享快照的内容与该共享快照创建时间点的共享内容相同：

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>从共享快照还原

可以通过从共享快照复制或下载文件来还原文件。 使用 `az storage file download` 命令，如以下示例所示：

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

在输出中可以看到，已下载文件及其属性的内容与该共享快照创建时间点的内容和属性相同：

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

### <a name="file-share-snapshot-operations-in-azure-powershell"></a>Azure PowerShell 中的文件共享快照操作
Azure PowerShell 可用于执行相同的操作，如列出共享快照、浏览共享快照内容、从共享快照还原或下载文件，或删除共享快照。

#### <a name="list-share-snapshots"></a>列出共享快照

可使用 `Get-AzureStorageShare` 列出特定共享的共享快照

```powershell
Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
```

#### <a name="browse-share-snapshots"></a>浏览共享快照
也可以浏览特定的共享快照，使用 `Get-AzureStorageFile` （包含指向特定快照的 `-Share` 值）来查看其内容

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
Get-AzureStorageFile -Share $snapshot
```

#### <a name="restore-from-share-snapshots"></a>从共享快照还原

可以通过使用 `Get-AzureStorageFileContent` 命令从共享快照复制或下载文件来还原文件

```powershell
$download='C:\Temp\Download'
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $download
```

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
$directory = Get-AzureStorageFile -ShareName "ContosoShare06" -Path "ContosoWorkingFolder" | Get-AzureStorageFile
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $directory
```

## <a name="delete-a-share-snapshot"></a>删除共享快照

可以使用 Azure 门户、PowerShell、CLI、REST API 或任何存储 SDK 来删除共享快照。 以下部分介绍如何使用 Azure 门户、CLI 和 PowerShell 删除共享快照。

可以浏览到共享快照，然后使用任何比较工具查看两个快照的差异。 然后即可确定要删除的共享快照。 

无法删除具有共享快照的共享。 若要删除共享，必须先删除其所有共享快照。

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>使用门户删除共享快照  
在门户中，可以转到文件共享边栏选项卡，然后使用“删除”按钮来删除一个或多个共享快照。

>   ![“删除”按钮](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 删除共享快照
可以使用 `[az storage share delete]` 命令删除共享快照。 在以下示例中，将共享快照时间戳用于 `--snapshot '2017-10-04T23:28:35.0000000Z' ` 参数：

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

示例输出：
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>使用 PowerShell 删除共享快照
可以使用 `Remove-AzureStorageShare -Share` 命令删除共享快照：

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>后续步骤
* [快照概述](storage-snapshots-files.md)
* [快照 FAQ](storage-files-faq.md)
