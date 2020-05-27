---
title: Microsoft Azure Data Box 磁盘系统要求 | Microsoft Docs
description: 了解 Azure Data Box 磁盘的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 6ac1fb1a69433be240e4250ea37835037f0b2ffa
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873939"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Azure Data Box Disk 系统要求

本文介绍了 Microsoft Azure Data Box 磁盘解决方案以及连接到 Data Box 磁盘的客户端的重要系统要求。 建议在部署 Data Box 磁盘之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

系统要求包括连接到磁盘的客户端支持的平台、支持的存储帐户和存储类型。

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>查看先决条件

1. 你一定已使用[教程：订购 Azure Data Box Disk](data-box-disk-deploy-ordered.md) 订购了 Data Box Disk。 你已收到磁盘，每个磁盘都连接一根电缆。
2. 你有一台可用的客户端计算机，可以从中复制数据。 客户端计算机必须：

    - 运行受支持的操作系统。
    - 安装其他必需的软件。

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

下面是通过连接到 Data Box 磁盘的客户端进行磁盘解锁和数据复制操作所支持的操作系统列表。

| **操作系统** | **测试的版本** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows（64 位） |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04、16.04、18.04 <br> 8.11、9 <br> 7.0 <br> 6.5、6.9、7.0、7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Windows 客户端所需的其他软件

对于 Windows 客户端，还应安装以下软件。

| **软件**| **版本** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.1|
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
| 经典 | Standard |
| 常规用途  |标准；同时支持 V1 和 V2。 同时支持热层和冷层。 |
| Blob 存储帐户 | |

## <a name="supported-storage-types-for-upload"></a>上传操作支持的存储类型

下面是使用 Data Box Disk 上传到 Azure 时支持的存储类型的列表。

| **文件格式** | **说明** |
| --- | --- |
| Azure 块 blob | |
| Azure 页 blob  | |
| Azure 文件  | |
| 托管磁盘 | |

::: zone target="docs"

## <a name="next-step"></a>后续步骤

* [部署 Azure Data Box 磁盘](data-box-disk-deploy-ordered.md)

::: zone-end

