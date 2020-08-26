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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420273"
---
- 只有一个虚拟网络可以链接到磁盘访问对象。
- 虚拟网络必须与磁盘访问对象处于同一订阅中才能链接它们。
- 使用同一磁盘访问对象最多可以同时导入或导出 10 个磁盘或快照。
- 无法请求手动批准将虚拟网络链接到磁盘访问对象。
- 与磁盘访问对象关联的增量快照不支持差异功能。
- 无法使用 AzCopy 下载通过专用链接来连接到存储帐户的磁盘/快照的 VHD。 但是，可使用 AzCopy 将 VHD 下载到你的 VM 中。
