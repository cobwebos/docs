---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376616"
---
- 只有一个虚拟网络可以链接到磁盘访问对象。
- 虚拟网络必须与磁盘访问对象处于同一订阅中才能链接它们。
- 使用同一磁盘访问对象最多可以同时导入或导出 10 个磁盘或快照。
- 无法请求手动批准将虚拟网络链接到磁盘访问对象。
- 增量快照与磁盘访问对象关联时，将无法导出。
- 无法使用 AzCopy 下载通过专用链接来连接到存储帐户的磁盘/快照的 VHD。 但是，可使用 AzCopy 将 VHD 下载到你的 VM 中。
