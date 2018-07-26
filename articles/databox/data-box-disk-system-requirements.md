---
title: Microsoft Azure Data Box 磁盘系统要求 | Microsoft Docs
description: 了解 Azure Data Box 磁盘的软件和网络要求
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
ms.openlocfilehash: 7138fa70c8b5615ad84196703f3bd76009ba5811
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011485"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Azure Data Box 磁盘系统要求（预览版）

本文介绍了 Microsoft Azure Data Box 磁盘解决方案以及连接到 Data Box 磁盘的客户端的重要系统要求。 建议在部署 Data Box 磁盘之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

> [!IMPORTANT]
> Data Box 磁盘以预览版提供。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

系统要求包括连接到磁盘的客户端支持的平台、支持的存储帐户和存储类型。


## <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

下面是通过连接到 Data Box 磁盘的客户端进行磁盘解锁和数据复制操作所支持的操作系统列表。

| **操作系统/平台** | **版本** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4.0 |
| .NET framework |4.5 |

> [!NOTE] 
> 需要在运行磁盘解锁工具并用于复制数据的客户端上启用 BitLocker。


## <a name="supported-storage-accounts"></a>支持的存储帐户

下面是 Data Box 磁盘支持的存储类型列表。

| **存储帐户** | **说明** |
| --- | --- |
| 经典 | 标准 |
| 常规用途  |标准；同时支持 V1 和 V2。 同时支持热层和冷层。 |


## <a name="supported-storage-types"></a>支持的存储类型

下面是 Data Box 磁盘支持的存储类型列表。

| **文件格式** | **说明** |
| --- | --- |
| Azure 块 blob | |
| Azure 页 blob  | |


## <a name="next-step"></a>后续步骤

* [部署 Azure Data Box 磁盘](data-box-disk-deploy-ordered.md)

