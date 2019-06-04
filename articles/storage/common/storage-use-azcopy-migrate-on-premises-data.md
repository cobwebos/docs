---
title: 教程：使用 AzCopy 将本地数据迁移到 Azure 存储 | Microsoft 文档
description: 在本教程中，请使用 AzCopy 将数据迁移或复制到 Blob、表和文件内容或从其中迁移或复制出数据。 轻松将本地存储中的数据迁移到 Azure 存储中。
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 64d79abd1e142a231c08e02e7d62e8bfbab7b90e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244742"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>教程：使用 AzCopy 将本地数据迁移到云存储

AzCopy 是一个命令行工具，借助该工具，可使用简单命令将数据复制到 Azure Blob 存储、Azure 文件和 Azure 表存储或从其中复制出数据。 这些命令旨在实现最佳性能。 使用 AzCopy，可在文件系统和存储帐户之间或在存储帐户之间复制数据。 AzCopy 可以用来将数据从本地复制到存储帐户。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建存储帐户。 
> * 使用 AzCopy 上传所有数据。
> * 修改用于测试目的的数据。
> * 创建一个计划任务或 cron 作业，以标识要上传的新文件。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请下载最新版 AzCopy。 请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)。

如果使用 Windows，则需 [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx)，因为本教程使用它来计划任务。 Linux 用户会改用 crontab 命令。

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>创建容器

第一步是创建容器，因为 Blob 始终必须上传到容器中。 容器用作组织 Blob 组的方法，就像将计算机上的文件组织到文件夹中一样。

按照这些步骤创建容器：

1. 选择主页上的“存储帐户”  按钮，然后选择创建的存储帐户。
2. 选择“服务”  下的“Blob”  ，然后选择“容器”  。

   ![创建容器](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
容器名必须以字母或数字开头。 名称中只能包含字母、数字和连字符 (-)。 有关命名 Blob 和容器的更多规则，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

## <a name="download-azcopy"></a>下载 AzCopy

下载 AzCopy V10 可执行文件。

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

将 AzCopy 文件置于计算机上的任何位置。 将文件位置添加到系统路径变量，这样即可从计算机上的任何文件夹引用该可执行文件。

## <a name="authenticate-with-azure-ad"></a>使用 Azure AD 进行身份验证

首先，为标识分配[存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)角色。 请参阅[在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)。

然后打开命令提示符，键入以下命令，按 ENTER 键。

```azcopy
azcopy login
```

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。 

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，可以关闭浏览器窗口，开始使用 AzCopy。

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>将文件夹的内容上传到 Blob 存储

可使用 AzCopy 将文件夹中的所有文件上传到 [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) 或 [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) 上的 Blob 存储中。 若要上传文件夹中的所有 Blob，请输入以下 AzCopy 命令：

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* 将 `<local-folder-path>` 占位符替换为包含文件（例如 `C:\myFolder` 或 `/mnt/myFolder`）的文件夹的路径。

* 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

* 请将 `<container-name>` 占位符替换为所创建容器的名称。

若要将指定目录的内容以递归方式上传到 Blob 存储，请指定 `--recursive` 选项。 使用此选项运行 AzCopy 时，会同时上传所有子文件夹及其文件。

## <a name="upload-modified-files-to-blob-storage"></a>将修改的文件上传到 Blob 存储

可基于文件的上次修改时间，使用 AzCopy 上传文件。 

若要尝试此操作，可在源目录中修改文件或创建新文件，用于测试目的。 然后，使用 AzCopy `sync` 命令。

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* 将 `<local-folder-path>` 占位符替换为包含文件（例如 `C:\myFolder` 或 `/mnt/myFolder`）的文件夹的路径。

* 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

* 请将 `<container-name>` 占位符替换为所创建容器的名称。

若要详细了解 `sync` 命令，请参阅[同步文件](storage-use-azcopy-blobs.md#synchronize-files)。

## <a name="create-a-scheduled-task"></a>创建计划的任务

可创建用于运行 AzCopy 命令脚本的计划任务或 cron 作业。 此脚本标识新的本地数据，并按特定时间间隔将其上传到云存储。

将 AzCopy 命令复制到文本编辑器。 将 AzCopy 命令的参数值更新为合适的值。 将文件另存为适用于 AzCopy 的 `script.sh` (Linux) 或 `script.bat` (Windows)。 

这些示例假设你的文件夹名为 `myFolder`，你的存储帐户名为 `mystorageaccount`，你的容器名为 `mycontainer`。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

在本教程中，[Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) 用于在 Windows 上创建计划任务。 [Crontab](http://crontab.org/) 命令用于在 Linux 上创建 cron 作业。

 使用 Schtasks  ，管理员能够在本地或远程计算机上创建、删除、查询、更改、运行和结束计划的任务。 使用 Cron  ，Linux 和 Unix 用户能够使用 [cron 表达式](https://en.wikipedia.org/wiki/Cron#CRON_expression)在指定日期和时间运行命令或脚本。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

若要在 Linux 上创建 cron 作业，请在终端上输入以下命令：

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

在命令中指定 cron 表达式 `*/5 * * * *` 可指示 shell 脚本 `script.sh` 应每隔五分钟运行一次。 可计划让脚本在每日、每月或每年的特定时间运行。 若要了解有关设置作业执行日期和时间的详细信息，请参阅 [cron 表达式](https://en.wikipedia.org/wiki/Cron#CRON_expression)。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

若要在 Windows 上创建计划任务，请在命令提示符下或在 PowerShell 中输入以下命令：

此示例假设你的脚本位于计算机的根驱动器中你想要的任何位置。

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

命令使用：
- `/SC` 参数指定分钟计划。
- `/MO` 参数指定五分钟的间隔。
- `/TN` 参数指定任务名称。
- `/TR` 参数指定 `script.bat` 文件的路径。

若要了解有关在 Windows 上创建计划的任务的详细信息，请参阅 [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes)。

---

若要验证计划的任务或 cron 作业运行正常，在 `myFolder` 目录中创建新文件。 等待五分钟以确认已将新文件上传到存储帐户。 转到日志目录，以查看计划任务或 cron 作业的输出日志。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在本地和 Azure 存储之间移动数据，请单击以下链接：

* [将数据移入和移出 Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。  

有关 AzCopy 的详细信息，请参阅以下任何文章：

* [AzCopy 入门](storage-use-azcopy-v10.md)

* [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)

* [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

* [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)
 
* [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)
