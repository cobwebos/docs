---
title: "如何使用 Azure 文件共享快照（预览版）| Microsoft 文档"
description: "使用 Azure 文件共享快照。 Azure 文件共享快照是某个时间点拍摄的 Azure 文件共享的只读版本。 在创建共享快照后，可以读取、复制或删除该快照，但无法对其进行修改。 利用共享快照，可以在某个时间点备份共享。"
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 33b64e1ad3fd5a2a6954a02da0fb303acca54c40
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>使用 Azure 文件共享快照（预览版）
Azure 文件共享快照（预览版）是某个时间点拍摄的 Azure 文件共享的只读版本。 在创建共享快照后，可以读取、复制或删除该快照，但无法对其进行修改。 利用共享快照，可以在某个时间点备份共享。 在本文中，我们将学习如何创建、管理和删除 Azure 文件共享快照。 若要了解有关共享快照的详细信息，请参阅[共享快照概述](storage-snapshots-files.md)或[快照常见问题](storage-files-faq.md)

## <a name="create-azure-files-share-snapshots"></a>创建 Azure 文件共享快照

可以使用门户、Powershell、CLI、REST 或任何存储 SDK 来创建共享快照。 以下几部分将介绍如何使用门户、CLI 和 Powershell 创建共享快照。 

可以在使用文件共享时拍摄其共享快照。 但是，共享快照仅捕获在发出共享快照命令时已写入 Azure 文件共享的数据。 这可能不会包括由任何应用程序或操作系统缓存的所有数据。

### <a name="create-share-snapshot-using-portal"></a>使用门户创建共享快照  
只需导航到门户的文件共享，并选择“`Create a Snapshot`”按钮，以创建时间点共享快照。

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>使用 CLI 2.0 创建共享快照
可以使用 `az storage share snapshot` 命令创建共享快照：

```azurecli-interactive
az storage share snapshot -n <share name>
```

示例输出
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

### <a name="create-share-snapshot-using-powershell"></a>使用 Powershell 创建共享快照
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

## <a name="common-share-snapshot-operations"></a>常用的共享快照操作

可以通过 REST、客户端库、PowerShell 和门户使用 Windows 中的“以前版本”选项卡来枚举与文件共享关联的共享快照。 装载 Azure 文件共享后，可以使用 Windows 中的“以前版本”选项卡查看所有以前版本的文件。 在以下部分中，将介绍如何使用 Azure 门户、Windows 和 Azure CLI 2.0 来列出、浏览和还原共享快照。

### <a name="share-snapshot-operations-in-portal"></a>在门户中共享快照操作

可以在门户中查看文件共享的所有共享快照，并浏览共享快照以查看其内容

#### <a name="view-share-snapshot"></a>查看共享快照
在“快照”下的文件共享上，选择“查看快照”

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>列出并浏览共享快照内容
查看共享快照的列表，然后通过从所需时间戳选择共享快照直接浏览其内容。

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

此外，还可以在列表快照视图上选择“连接”按钮，以获取 `net use` 命令，以及可以直接浏览到的指向特定共享快照的目录路径。


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>从共享快照下载或还原
在门户中，从快照下载或还原所需的文件。

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Windows 中的文件共享快照操作
当已拍摄文件共享的共享快照后，即可从 Windows 上装载的 Azure 文件共享来查看共享、目录或特定文件的以前版本。 例如，下面是如何使用以前版本功能来查看并还原 Windows 中特定目录的以前版本：

> [!Note]  
> 可以在共享级别以及文件级别上执行同样的操作。 本列表中仅显示包含有关该目录或文件更改的版本。 如果某个特定目录或文件在两个共享快照之间未发生更改，则该共享快照仅在共享级别的以前版本列表中显示，而不在目录或文件的以前版本列表中显示。

#### <a name="mount-file-share"></a>装载文件共享
使用 net use 命令首先装载文件共享。

#### <a name="open-mounted-share-in-explorer"></a>在资源管理器中打开已装载的共享
转到文件资源管理器，并找到已装载的共享。

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>列出以前的版本
 导航到需要还原的项或父项。 双击以导航到所需的目录。 右键单击，然后从菜单中选择“属性”

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

选择"以前的版本”，以查看此目录的共享快照列表。 列表可能需要几秒钟才能加载，具体要取决于网速和共享快照的数量。

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

可以选择“打开”浏览特定快照 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>从以前的版本还原
还原以在共享快照创建时以递归方式将整个目录的内容复制到原始位置。
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0 中的文件共享快照操作
Azure CI 2.0 可用于执行相同的操作，如列出共享快照、浏览共享快照内容、从共享快照还原或下载文件，或删除共享快照。

#### <a name="list-share-snapshots"></a>列出共享快照

可使用 [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 和 `--include-snapshots` 列出特定共享的共享快照

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>示例输出
该命令将为你提供共享快照及其所有关联属性的列表。

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

#### <a name="browse-share-snapshots"></a>浏览共享快照
还可以使用 [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 浏览到特定共享快照以查看其内容。 用户必须指定我们想要浏览到 `--snapshot '2017-10-04T19:45:18.0000000Z'` 的共享名 `--share-name` 和时间戳

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>示例输出

你将会看到，共享快照的内容与该共享快照创建时间点的共享内容相同。

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
#### <a name="restore-from-share-snapshots"></a>从共享快照还原

可以通过使用 `az storage file download` 命令从共享快照复制或下载文件来还原文件

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>示例输出

你会看到，已下载文件及其属性的内容与该共享快照创建时间点的内容和属性相同。

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

## <a name="delete-azure-files-share-snapshot"></a>删除 Azure 文件共享快照

可以使用 Azure 门户、PowerShell、CLI、REST API 或任何存储 SDK 来删除文件共享快照。 以下几部分将介绍如何使用 Azure 门户、CLI 和 Powershell 删除共享快照。

可以浏览共享快照并使用任何比较工具查看两个共享快照之间的差异，以确定想要删除的共享快照。 

无法删除具有共享快照的共享。 必须先删除其所有共享快照才能删除该共享。

### <a name="delete-share-snapshot-using-portal"></a>使用门户删除共享快照  
可以导航到文件共享边栏选项卡并选择门户中的“`delete`”按钮来删除一个或多个共享快照。

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>使用 Azure CLI 2.0 删除共享快照
可以通过提供 `--snapshot '2017-10-04T23:28:35.0000000Z' ` 参数和共享快照时间戳使用 `[az storage share delete]` 命令来删除共享快照：

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

示例输出
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>使用 Powershell 删除共享快照
可以使用 `Remove-AzureStorageShare -Share` 命令创建共享快照：

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
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