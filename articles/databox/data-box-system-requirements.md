---
title: Microsoft Azure Data Box 系统要求 | Microsoft Docs
description: 了解 Azure Data Box 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746924"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 系统要求

本文介绍 Microsoft Azure Data Box 以及连接到 Data Box 的客户端的重要系统要求。 建议在部署 Data Box 之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

系统需求包括：

* **连接到 Data Box 的主机的软件要求** - 介绍支持的平台、本地 Web UI 的浏览器、SMB 客户端以及可连接到 Data Box 的主机的任何其他要求。
* **Data Box 的网络要求** - 提供有关 Data Box 最佳操作的网络要求的信息。


## <a name="software-requirements"></a>软件要求

软件要求包括有关支持的操作系统、本地 Web UI 支持的浏览器和 SMB 客户端的信息。

### <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

下面是通过连接到 Data Box 设备的客户端进行数据复制操作所支持的操作系统列表。

| **操作系统** | **版本** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Linux 客户端支持的文件系统

| **协议** | **版本** | 
| --- | --- | 
| SMB |2.X 和更高版本 |
| NFS | 所有版本（直至并包括 4.1）|

### <a name="supported-storage-accounts"></a>支持的存储帐户

下面是 Data Box 设备支持的存储帐户和存储类型的列表。 有关所有不同类型的存储帐户及其完整功能的完整列表，请参阅[存储帐户类型](/azure/storage/common/storage-account-overview#types-of-storage-accounts)。

| **存储帐户/支持的存储类型** | **块 blob** |**页 blob*** |**Azure 文件** |**说明**|
| --- | --- | -- | -- | -- |
| 经典标准 | Y | Y | Y |
| 常规用途 v1 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v1 高级  |  | Y| | |
| 常规用途 v2 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v2 高级  |  |Y | | |
| Blob 存储标准 |Y | | |支持热和冷。 |

\* *- 上传到页 blob 的数据必须是 512 字节对齐，例如 vhds。*

>[!NOTE]
> 不支持 Azure Data Lake Storage Gen 2 帐户。


### <a name="supported-storage-types"></a>支持的存储类型

下面是 Data Box 设备支持的存储类型列表。

| **文件格式** | **说明** |
| --- | --- |
| Azure 块 blob | |
| Azure 页 blob  | 数据应为 512 字节对齐。|
| Azure 文件 | |


### <a name="supported-web-browsers"></a>受支持的 Web 浏览器

下面是本地 Web UI 支持的 Web 浏览器列表。

| **浏览器** | **版本** | **其他要求/说明** |
| --- | --- | --- |
| Google Chrome |最新版本 |已使用 Chrome 测试|
| Microsoft Edge |最新版本 | |
| FireFox | 最新版本 | 已使用 FireFox 测试|
| Internet Explorer |最新版本 |如果无法登录，请检查是否已启用 Cookie 和 Javascript。 若要启用 UI 访问，请将设备 IP 添加到“隐私操作”，以便设备可以访问 Cookie。 |


## <a name="networking-requirements"></a>网络要求

数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路复制数据，但复制速度会受影响。

## <a name="next-step"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)

