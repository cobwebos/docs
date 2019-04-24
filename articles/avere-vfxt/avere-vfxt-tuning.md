---
title: Avere vFXT 群集优化 - Azure
description: 用于优化 Avere vFXT for Azure 性能的自定义设置概述
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409153"
---
# <a name="cluster-tuning"></a>群集优化


大多数 vFXT 群集都可以受益于自定义的性能设置。 这些设置有助于群集最充分地利用特定的工作流、数据集和工具。 

这项自定义操作应在支持代表的配合下完成，因为它通常涉及到配置 Avere 控制面板中未提供的功能。

本部分将介绍一些可以完成的自定义优化。

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>常规优化

可以根据数据集质量或工作流样式建议这些更改。

* 如果工作负荷是写入密集型的，请增大写缓存大小的默认值 20%。 
* 如果数据集涉及到大量小文件，请提高群集缓存的文件计数限制。 
* 如果工作涉及到在两个存储库之间复制或移动数据，请调整用于移动数据的线程数： 
  * 若要提高速度，可以增加所用的并行线程数。
  * 如果后端存储卷过载，可能需要减少所用的并行线程数。
* 如果群集缓存使用 NFSv4 ACL 的核心文件管理器的数据，请启用访问模式缓存，以简化特定客户端的文件授权。

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>云 NAS 或云网关优化

若要在云 NAS 或网关方案（其中的 vFXT 群集会提供云容器的 NAS 式访问）中的 vFXT 群集与云存储之间利用更高的数据速度，支持代表可以建议更改如下所述的设置，以便更主动地将数据从缓存推送到存储卷：

* 增加群集与存储容器之间的 TCP 连接数
* 减小群集与存储之间的通信 REST 超时值，以便在写入未立即成功时更快地重试写入  
* 增加段大小，使每个后端写入段传输 8 MB 而不是 1 MB 的数据块

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>云爆发或混合 WAN 优化

在云爆发方案或混合存储 WAN 优化方案（其中的 vFXT 群集会在云与本地硬件存储之间提供集成），以下更改可能有所帮助：

* 增加群集与核心文件管理器之间允许的 TCP 连接数
* 为远程核心文件管理器启用 WAN 优化设置（此设置可用于远程本地文件管理器或不同 Azure 区域中的核心云文件管理器。）
* 增大 TCP 套接字缓冲区大小（取决于工作负荷和性能需求）
* 启用“始终转发”设置，以减少冗余缓存的文件（取决于工作负荷和性能需求）

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>帮助优化 Avere vFXT for Azure

使用[获取系统帮助](avere-vfxt-open-ticket.md)中所述的过程来联系支持人员，以获取这些优化工作的帮助。 