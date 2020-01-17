---
title: Avere vFXT 群集优化 - Azure
description: 用于优化 Avere vFXT for Azure 性能的自定义设置概述
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152928"
---
# <a name="cluster-tuning"></a>群集优化

大多数 vFXT 群集都可以受益于自定义的性能设置。 这些设置有助于群集最充分地利用特定的工作流、数据集和工具。

此自定义应通过支持代表提供帮助，因为它可能涉及到配置 Avere 控制面板中没有的功能。

本部分介绍一些可完成的自定义优化。

## <a name="general-optimizations"></a>常规优化

可以根据数据集质量或工作流样式建议这些更改。

* 如果工作负荷是写入密集型的，请增大写缓存大小的默认值 20%。
* 如果数据集涉及到大量小文件，请提高群集缓存的文件计数限制。
* 如果工作涉及到在两个存储库之间复制或移动数据，请调整用于移动数据的线程数：
  * 若要提高速度，可以增加所用的并行线程数。
  * 如果后端存储卷过载，可能需要减少所用的并行线程数。
* 如果群集缓存使用 NFSv4 ACL 的核心文件管理器的数据，请启用访问模式缓存，以简化特定客户端的文件授权。

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>云 NAS 或云网关优化

在云 NAS 或网关方案中，vFXT 群集提供对云容器的 NAS 样式访问。 若要在 vFXT 群集和云存储之间利用更高的数据速度，你的代表可能会建议将设置更改为更主动地将数据从缓存推送到存储卷。 例如：

* 增加群集与存储容器之间的 TCP 连接数

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>云爆发或混合 WAN 优化

在云暴冲方案或混合存储广域网优化方案中，vFXT 群集提供云和本地硬件存储之间的集成。 这些更改可能会有所帮助：

* 增加群集与核心文件管理器之间允许的 TCP 连接数
* 为远程核心文件管理器启用 WAN 优化设置（此设置可用于远程本地文件管理器或不同 Azure 区域中的核心云文件管理器。）
* 增大 TCP 套接字缓冲区大小<sup>*</sup>
* 启用 "始终转发" 设置以减少冗余缓存文件<sup>*</sup>

<sup>*</sup>根据工作负荷和性能需求的不同，这些调整可能不适用于所有系统。

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>帮助优化 Avere vFXT for Azure

若要与支持人员联系以了解这些优化，请使用在[系统中获取帮助](avere-vfxt-open-ticket.md)中所述的过程。
