---
title: Microsoft Azure Data Box 磁盘系统要求 | Microsoft Docs
description: 了解 Azure Data Box 磁盘的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: d10ca5d704892ae0a1494d729b46abf0fc06aa64
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092246"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Azure Data Box 磁盘系统要求（预览版）

本文介绍了 Microsoft Azure Data Box 磁盘解决方案以及连接到 Data Box 磁盘的客户端的重要系统要求。 建议在部署 Data Box 磁盘之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

> [!IMPORTANT]
> Data Box 磁盘以预览版提供。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

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

