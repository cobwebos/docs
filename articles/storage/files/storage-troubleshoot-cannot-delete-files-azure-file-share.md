---
title: Azure 文件共享–无法从 Azure 文件共享中删除文件
description: 识别删除 Azure 文件共享中的文件并对其进行故障排除。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
ms.openlocfilehash: b535578328e7ca77f1071187b6ac761bc7076ac1
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065972"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure 文件共享–无法从 Azure 文件共享中删除文件

从 Azure 文件共享删除文件的操作失败可能有多种症状：

**症状1：**

由于以下两个问题之一导致无法删除 azure 文件共享中的文件：

* 标记为删除的文件
* SMB 客户端可能正在使用指定的资源

**症状2：**

可用配额不足，无法处理此命令

## <a name="cause"></a>原因

如果在要安装文件共享的计算机上达到文件允许的并发打开句柄上限，则会出现错误1816。 有关详细信息，请参阅[Azure 存储性能和可伸缩性清单](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)。

## <a name="resolution"></a>分辨率

通过关闭某些句柄，减少并发打开句柄的数量。

## <a name="prerequisite"></a>先决条件

### <a name="install-the-latest-azure-powershell-module"></a>安装最新 Azure PowerShell 模块

* [安装 Azure Powershell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>连接到 Azure：

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>选择目标存储帐户的订阅：

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>为目标存储帐户创建上下文：

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>获取文件共享的当前打开句柄：

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>示例结果：

|HandleId|路径|clientIp|clientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12：16：50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12：36：20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12：36：53Z|0|0|9507758546259807489|
|259101229136|新建文件夹/测试 .zip|10.222.10.123|62758|2019-10-05|12：36：29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|测试 .zip|37.222.22.143|62758|2019-10-05|12：36：24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>关闭打开的句柄：

若要关闭打开的句柄，请使用以下命令：

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>后续步骤

* [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
* [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
* [对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)