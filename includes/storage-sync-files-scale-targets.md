---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391558"
---
| 资源 | 确定目标 | 硬限制 |
|----------|--------------|------------|
| 每个区域的存储同步服务数 | 20个存储同步服务 | 是 |
| 每个存储同步服务的同步组数 | 100 个同步组 | 是 |
| 每个存储同步服务的已注册服务器 | 99 台服务器 | 是 |
| 每个同步组的云终结点 | 1 个云终结点 | 是 |
| 每个同步组的服务器终结点 | 50 个服务器终结点 | No |
| 每个服务器的服务器终结点数 | 30 个服务器终结点 | 是 |
| 每个同步组的文件系统对象数（目录和文件） | 50000000对象 | No |
| 目录中的最大文件系统对象（目录和文件）数 | 5000000对象 | 是 |
| 最大对象（目录和文件）安全描述符大小 | 64 KiB | 是 |
| 文件大小 | 100 GiB | No |
| 要进行分层的文件的最小文件大小 | 64 KiB | 是 |
| 并发同步会话 | V4 代理和更高版本：限制因可用系统资源而异。 <BR> V3 agent：每个处理器有两个活动的同步会话，每个服务器最多八个活动的同步会话。 | 是

> [!Note]  
> Azure 文件同步终结点可以扩展到 Azure 文件共享的大小。 如果达到 Azure 文件共享大小限制，同步将无法运行。
