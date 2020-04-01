---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420953"
---
本文介绍如何使用 AzCopy 将 VHD 从本地计算机上载到 Azure 托管磁盘或将托管磁盘复制到其他区域。 此过程（直接上传）还使您能够将高达 32 TiB 大小的 VHD 直接上载到托管磁盘中。 目前，标准 HDD、标准 SSD 和高级 SSD 托管磁盘支持直接上传。 它不支持超磁盘。

如果要为 Azure 中的 IaaS VM 提供备份解决方案，我们建议使用直接上载将客户备份还原到托管磁盘。 将 VHD 从外部源上载到 Azure 时，速度将取决于本地带宽。 从 Azure VM 上载或复制时，您的带宽将与标准 HDD 相同。