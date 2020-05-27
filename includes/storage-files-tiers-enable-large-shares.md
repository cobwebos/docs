---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597764"
---
默认情况下，标准文件共享只能跨越 5 TiB，尽管共享限制可以增加到 100 TiB。 为此，必须在存储帐户级别启用*大型文件共享*功能。 高级存储帐户（*文件存储*存储帐户）没有大型文件共享功能标志，因为已启用所有高级文件共享以预配高达 100 个 TiB 的全部容量。

您只能在本地冗余或区域冗余标准存储帐户上启用大型文件共享。 启用大型文件共享功能标志后，无法将冗余级别更改为异地冗余或地理区域冗余存储。

要在现有存储帐户上启用大型文件共享，请导航到存储帐户目录中的 **"配置"** 视图，并将大型文件共享摇臂开关切换到已启用：

![Azure 门户中启用大型文件共享摇臂开关的屏幕截图](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

还可以通过[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount)PowerShell cmdlet 和[`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)Azure CLI 命令启用 100 个 TiB 文件共享。

要了解有关如何在新存储帐户上启用大型文件共享的更多详细信息，请参阅[创建 Azure 文件共享](../articles/storage/files/storage-how-to-create-file-share.md)。