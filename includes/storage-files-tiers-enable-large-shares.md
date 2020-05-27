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
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536468"
---
默认情况下，虽然可将共享限制增加到 100 TiB，但标准文件共享只能跨越最多 5 TiB。 为此，必须在存储帐户级别启用大文件共享  功能。 高级存储帐户（*FileStorage*存储帐户）没有较大的文件共享功能标志，因为所有高级文件共享已启用，可预配到全部 100 TiB 容量。

只能在本地冗余或区域冗余标准存储帐户上启用大型文件共享。 启用大文件共享功能标志后，无法将冗余级别更改为异地冗余或地域冗余存储。

若要在现有存储帐户上启用大文件共享，请导航到存储帐户的目录中的“配置”视图，将大文件共享摇杆开关切换到“启用”： 

![在 Azure 门户中启用大文件共享摇杆开关的屏幕截图](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

还可以通过 [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 和 [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI 命令启用 100 TiB 文件共享。 有关启用大型文件共享的详细说明，请参阅[启用和创建大型文件共享](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

若要了解有关如何在新存储帐户上创建文件共享的详细信息，请参阅[创建 Azure 文件共享](../articles/storage/files/storage-how-to-create-file-share.md)。
