---
title: 配置 Azure HPC 缓存设置
description: 说明如何为缓存配置其他设置（如 MTU 和非根壁切，以及如何从 Azure Blob 存储目标访问快速快照）。
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538812"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>配置其他 Azure HPC 缓存设置

Azure 门户中的 **"配置"** 页具有用于自定义多个设置的选项。 大多数用户不需要从默认值更改这些值。

本文还介绍如何使用快照功能实现 Azure Blob 存储目标。 快照功能没有可配置设置。

要查看这些设置，请参阅 Azure 门户中的缓存 **"配置"** 页。

![Azure 门户中配置页的屏幕截图](media/configuration.png)

## <a name="adjust-mtu-value"></a>调整 MTU 值
<!-- linked from troubleshoot-nas article -->

您可以使用标记为**MTU 大小的**下拉菜单为缓存选择最大传输单元大小。

默认值为 1500 字节，但您可以将其更改为 1400 字节。

> [!NOTE]
> 如果降低缓存的 MTU 大小，请确保与缓存通信的客户端和存储系统具有相同的 MTU 设置或较低的值。

降低缓存 MTU 值可以帮助您解决缓存网络其余部分的数据包大小限制。 例如，某些 VPN 无法成功传输全尺寸 1500 字节数据包。 减小通过 VPN 发送的数据包的大小可能会消除此问题。 但是，请注意，较低的缓存 MTU 设置意味着与缓存通信的任何其他组件（包括客户端和存储系统）也必须具有较低的设置，以避免与缓存的通信问题。

如果不想更改其他系统组件上的 MTU 设置，则不应降低缓存的 MTU 设置。 还有其他解决方案需要解决 VPN 数据包大小限制。 阅读 NAS 故障排除文章中[的"调整 VPN 数据包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)"，了解有关诊断和解决此问题的更多内容。

通过读取[Azure VM 的 TCP/IP 性能调优](../virtual-network/virtual-network-tcpip-performance-tuning.md)，了解有关 Azure 虚拟网络中 MTU 设置的更多内容。

## <a name="configure-root-squash"></a>配置根壁
<!-- linked from troubleshoot -->

**启用根壁挤压**设置控制 Azure HPC 缓存允许根访问的方式。 根壁球有助于防止根级访问来自未经授权的客户端。

此设置允许用户在缓存级别控制根访问，这有助于补偿用作存储目标的 NAS 系统``no_root_squash``所需的设置。 （阅读有关[NFS 存储目标先决条件的更多](hpc-cache-prereqs.md#nfs-storage-requirements)内容。当与 Azure Blob 存储目标一起使用时，它还可以提高安全性。

默认设置为 **"是**"。 （2020 年 4 月之前创建的缓存可能具有默认设置 **"否**"）启用此功能后，此功能还防止在客户端请求缓存中使用 set-UID 权限位。

## <a name="view-snapshots-for-blob-storage-targets"></a>查看 Blob 存储目标的快照

Azure HPC 缓存会自动为 Azure Blob 存储目标保存存储快照。 快照为后端存储容器的内容提供了一个快速参考点。 快照不能替代数据备份，并且不包含有关缓存数据状态的任何信息。

> [!NOTE]
> 此快照功能不同于 NetApp、Isilon 或 ZFS 存储软件中包含的快照功能。 这些快照实现在拍摄快照之前将更改从缓存刷新到后端存储系统。
>
> 为提高效率，Azure HPC Cache 快照不会先刷新更改，而只记录已写入 Blob 容器的数据。 此快照不表示缓存数据的状态，因此可能会排除最近的更改。

此功能仅适用于 Azure Blob 存储目标，并且无法更改其配置。

快照每八小时拍摄一次，在 UTC 0：00、08：00 和 16：00。

Azure HPC 缓存存储每日、每周和每月快照，直到它们被新快照替换。 这些限制包括：

* 每日最多 20 张快照
* 每周最多 8 次快照
* 最多 3 个每月快照

从 Blob 存储目标`.snapshot`命名空间中的目录中访问快照。
