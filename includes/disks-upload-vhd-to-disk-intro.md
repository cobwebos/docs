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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420953"
---
本文介绍如何使用 AzCopy 将 VHD 从本地计算机上传到 Azure 托管磁盘，或将托管磁盘复制到其他区域。 此过程（直接上传）还允许你将最大大小为 32 TiB 的 VHD 直接上传到托管磁盘。 目前，标准 HDD、标准 SSD 和高级 SSD 托管磁盘支持直接上传。 超级磁盘尚不支持此功能。

若要为 Azure 中的 IaaS VM 提供备份解决方案，建议使用直接上传方法将客户备份还原到托管磁盘。 从 Azure 外部的源上传 VHD 时，速度取决于本地带宽。 从 Azure VM 上传或复制时，带宽将与标准 HDD 相同。