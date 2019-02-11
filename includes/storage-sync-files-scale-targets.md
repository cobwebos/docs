---
title: include 文件
description: include 文件
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: fea31bf1c5869164dde1eb7014b2aacc8fcc5e9c
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757540"
---
| 资源 | 目标 | 硬限制 |
|----------|--------------|------------|
| 每个区域的存储同步服务数 | 15 个存储同步服务 | 是 |
| 每个存储同步服务的同步组数 | 100 个同步组 | 是 |
| 每个存储同步服务的已注册服务器 | 99 台服务器 | 是 |
| 每个同步组的云终结点数 | 1 个云终结点 | 是 |
| 每个同步组的服务器终结点数 | 50 个服务器终结点 | 否 |
| 每个服务器的服务器终结点数 | 30 个服务器终结点 | 是 |
| 终结点大小 | 4 TiB | 否 |
| 每个同步组的文件系统对象数（目录和文件） | 2500 万个对象 | 否 |
| 目录中的最大文件系统对象（目录和文件）数 | 200,000 个对象 | 是 |
| 最大对象（目录和文件）安全描述符大小 | 4 KiB | 是 |
| 文件大小 | 100 GiB | 否 |
| 要进行分层的文件的最小文件大小 | 64 KiB | 是 |
| 并发同步会话 | V4 代理：限制因可用系统资源而异。 <BR> V3 代理：每个处理器 2 个活动的同步会话，或每个服务器最多 8 个活动的同步会话 | 是
