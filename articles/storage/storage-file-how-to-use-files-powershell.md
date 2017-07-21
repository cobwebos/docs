---
title: "如何使用 PowerShell 管理 Azure 文件存储 | Microsoft Docs"
description: "了解如何使用 PowerShell 管理 Azure 文件存储。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 5d825c1139d49fb8d37c719d8e0ab8ba2da2ed8d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---
# <a name="how-to-use-powershell-to-manage-azure-file-storage"></a>如何使用 PowerShell 管理 Azure 文件存储
可以使用 Azure PowerShell 来创建和管理文件共享。

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>为 Azure 存储安装 PowerShell cmdlet
若要准备使用 PowerShell，请下载并安装 Azure PowerShell cmdlet。 有关安装点和安装说明，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

> [!NOTE]
> 建议你下载并安装最新的 Azure PowerShell 模块或升级到最新模块。
> 
> 

通过单击“开始”并键入 **Windows PowerShell** 打开 Azure PowerShell 窗口。 PowerShell 窗口将为你加载 Azure PowerShell 模块。

## <a name="create-a-context-for-your-storage-account-and-key"></a>为存储帐户和密钥创建上下文
创建存储帐户上下文。 该上下文封装了存储帐户名称和帐户密钥。 有关从 [Azure 门户](https://portal.azure.com)复制帐户密钥的说明，请参阅[查看和复制存储访问密钥](storage-create-storage-account.md#view-and-copy-storage-access-keys)。

请将下面示例中的 `storage-account-name` 和 `storage-account-key` 替换为你的存储帐户名称和密钥。

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>创建新的文件共享
创建名为 `logs` 的新共享。

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

现在，你在文件存储中已有一个文件共享。 接下来，我们将添加目录和文件。

> [!IMPORTANT]
> 文件共享的名称必须是全部小写。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>在文件共享中创建目录
在共享中创建目录。 在下面的示例中，目录名为 `CustomLogs`。

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>将本地文件上传到目录
现在，将本地文件上传到该目录。 以下示例从 `C:\temp\Log1.txt` 上传文件。 请编辑文件路径，使其指向你本地计算机上的有效文件。

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>列出目录中的文件
若要查看目录中的文件，你可以列出目录的所有文件。 此命令将返回 CustomLogs 目录中的文件和子目录（如果有的话）。

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile 将返回任何传入的目录对象的文件和目录列表。 “Get-AzureStorageFile -Share $s”将返回根目录中的文件和目录列表。 若要获取子目录中的文件列表，必须将子目录传递给 Get AzureStorageFile。 这就是此功能的作用 -- 到达管道的命令的第一部分将返回子目录 CustomLogs 的目录实例。 然后，该实例将传递到 Get-AzureStorageFile，从而返回 CustomLogs 中的文件和目录。

## <a name="copy-files"></a>复制文件
从 Azure PowerShell 的 0.9.7 版开始，可以将一个文件复制到另一个文件，将一个文件复制到一个 Blob，或将一个 Blob 复制到一个文件。 下面，我们演示如何使用 PowerShell cmdlet 执行这些复制操作。

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>后续步骤
请参阅以下链接以获取有关 Azure 文件存储的更多信息。

* [常见问题](storage-files-faq.md)
* [故障排除](storage-troubleshoot-file-connection-problems.md)
