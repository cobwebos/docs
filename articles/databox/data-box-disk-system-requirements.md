---
title: Microsoft Azure Data Box 磁盘系统要求 | Microsoft Docs
description: 了解 Azure Data Box 磁盘的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 0effc6af1ddc3273f0c6e2bf9cbfd0f2ecadf0a2
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747316"
---
# <a name="azure-data-box-disk-system-requirements"></a>Azure Data Box Disk 系统要求

本文介绍了 Microsoft Azure Data Box 磁盘解决方案以及连接到 Data Box 磁盘的客户端的重要系统要求。 建议在部署 Data Box 磁盘之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

系统要求包括连接到磁盘的客户端支持的平台、支持的存储帐户和存储类型。


## <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

下面是通过连接到 Data Box 磁盘的客户端进行磁盘解锁和数据复制操作所支持的操作系统列表。

| **操作系统** | **测试的版本** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04、16.04、18.04 <br> 8.11、9 <br> 7.0 <br> 6.5、6.9、7.0、7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Windows 客户端所需的其他软件

对于 Windows 客户端，还应安装以下软件。

| **软件**| **版本** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Linux 客户端所需的其他软件

对于 Linux 客户端，Data Box Disk 工具集安装以下必需软件：

- dislocker
- OpenSSL

## <a name="supported-connection"></a>支持的连接

包含数据的客户端计算机必须拥有 USB 3.0 或更高版本的端口。 磁盘使用提供的数据线连接到此客户端。 

## <a name="supported-storage-accounts"></a>支持的存储帐户

下面是 Data Box 磁盘支持的存储类型列表。

| **存储帐户** | **说明** |
| --- | --- |
| 经典 | 标准 |
| 常规用途  |标准；同时支持 V1 和 V2。 同时支持热层和冷层。 |

>[!NOTE]
> 不支持 Azure Data Lake Storage Gen 2 帐户。


## <a name="supported-storage-types"></a>支持的存储类型

下面是 Data Box 磁盘支持的存储类型列表。

| **文件格式** | **说明** |
| --- | --- |
| Azure 块 blob | |
| Azure 页 blob  | |


## <a name="next-step"></a>后续步骤

* [部署 Azure Data Box 磁盘](data-box-disk-deploy-ordered.md)

