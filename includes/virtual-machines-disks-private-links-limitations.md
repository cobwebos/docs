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
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535473"
---
- 只有一个虚拟网络可以链接到磁盘访问对象。
- 虚拟网络必须与磁盘访问对象处于同一订阅中才能链接它们。
- 使用同一磁盘访问对象最多可以同时导入或导出 10 个磁盘或快照。
- 无法请求手动批准将虚拟网络链接到磁盘访问对象。
- 与磁盘访问对象关联的增量快照不支持差异功能。