---
title: 保留 Azure Data Box 的文件 Acl、属性和时间戳
description: 在通过 SMB 将数据复制到 Azure Data Box 过程中保留的 Acl、时间戳和属性。 将元数据复制到 Windows 和 Linux 数据复制工具。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950306"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>保留 Azure Data Box 的文件 Acl、属性和时间戳

Azure Data Box 允许你在将数据发送到 Azure 时保留访问控制列表 (Acl) 、时间戳和文件属性。 本文介绍通过服务器消息块将数据复制到 Data Box 时可以传输的元数据 (SMB) ，以将数据上传到 Azure 文件。 

提供了用于通过 Windows 和 Linux 数据复制工具复制元数据的特定步骤。 在将数据传输到 blob 存储时不保留元数据。

在本文中，已传输的 Acl、时间戳和文件属性统称为 *元数据*。

## <a name="transferred-metadata"></a>传输的元数据

将 Data Box 中的数据上传到 Azure 文件时，将传输以下元数据。

#### <a name="timestamps"></a>时间戳

传输以下时间戳：
- CreationTime
- LastWriteTime

未传输以下时间戳：
- LastAccessTime
  
#### <a name="file-attributes"></a>文件属性

除非另有说明，否则会传输文件和目录上的文件属性。

传输以下文件属性：
- 仅 FILE_ATTRIBUTE_READONLY (文件) 
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- 仅 FILE_ATTRIBUTE_DIRECTORY (目录) 
- FILE_ATTRIBUTE_ARCHIVE
- 仅 FILE_ATTRIBUTE_TEMPORARY (文件) 
- FILE_ATTRIBUTE_NO_SCRUB_DATA

不传输以下文件属性：
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
不传输目录上的只读属性。

#### <a name="acls"></a>ACL

复制并传输通过 SMB 复制到 Data Box 的目录和文件的所有 Acl。 传输包括 (Dacl) 和系统 Acl (Sacl) 的自由 Acl。 对于 Linux，只传输 Windows NT Acl。

在通过网络文件系统 (NTS) 和使用数据复制服务传输数据时，Acl 不会传输。 数据复制服务直接从共享读取数据，而不能读取 Acl。

即使数据复制工具不复制 Acl，目录和文件上的默认 Acl 也会传输到 Azure 文件。 默认 Acl 具有内置管理员帐户、系统帐户和用于在 Data Box 中装载和复制数据的 SMB 共享用户帐户的权限。

Acl 包含具有以下属性的安全描述符： Acl、所有者、组、SACL。

默认情况下启用 Acl 传输。 你可能想要在 Data Box 上的本地 web UI 中禁用此设置。 有关详细信息，请参阅 [使用本地 WEB UI 管理你的 Data Box 和 Data Box Heavy](./data-box-local-web-ui-admin.md)。

> [!NOTE]
> 不会复制包含包含条件访问控制项的 Acl (ACE) 字符串的文件。 这是已知问题。 若要解决此情况，请将这些文件手动复制到 Azure 文件共享，方法是装入该共享，然后使用支持复制 Acl 的复制工具。

## <a name="copying-data-and-metadata"></a>复制数据和元数据

若要传输数据的 Acl、时间戳和属性，请使用以下过程将数据复制到 Data Box 中。 

### <a name="windows-data-copy-tool"></a>Windows 数据复制工具

若要通过 SMB 将数据复制到 Data Box，请使用 SMB 兼容文件复制工具（如） `robocopy` 。 下面的示例命令将复制所有文件和目录，并将元数据与数据一起传输。

使用 `/copyall` 或选项时 `/dcopy:DAT` ，请确保未禁用所需的备份操作员特权。 有关详细信息，请参阅 [使用本地 WEB UI 管理你的 Data Box 和 Data Box Heavy](./data-box-local-web-ui-admin.md)。 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|选项 |说明 |
|------------------- | ----- |
|`/copyall` |复制所有属性。|
|`/e`      |复制子目录，包括空目录。         |
|`/dcopy:DAT`  |复制数据、属性和时间戳。 注意：/dcopy： DAT 选项必须用于 `CreationTime` 在目录上传输。 |
|`/r:3`    |指定对失败的副本进行3次重试。         |
|`/w:60`   |指定两次重试之间的等待时间为60秒。         |
|`/is`     |包括相同的文件。         |
|`/nfl`    |不记录文件名。         |
|`/ndl`    |不记录目录名称。        |
|`/np`     |不显示复制操作的进度。         |
|`/MT:32 or 64`  |使用具有32或64线程的多线程处理。           |
|`/fft`    |减少任何文件系统的时间戳粒度。        |
|`/log+:<LogFile>`  |将输出追加到现有的日志文件。|

有关这些参数的详细信息 `robocopy` ，请参阅 [教程：通过 SMB 将数据复制到 Azure Data Box](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Linux 数据复制工具

在 Linux 中传输元数据的过程分为两个步骤。 首先，使用工具（如）复制源数据 `rsync` ，该工具不会复制元数据。 复制数据后，可以使用或等工具复制元数据 `smbcacls` `cifsacl` 。 

下面的示例命令执行第一步，使用复制数据 `rsync` 。 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>后续步骤

- [通过 SMB 将数据复制到 Azure Data Box](./data-box-deploy-copy-data.md)