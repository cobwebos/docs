---
title: 配置 Azure HPC 缓存设置
description: 说明如何为缓存配置其他设置，如 MTU 和无 squash，以及如何从 Azure Blob 存储目标访问快速快照。
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: a3bab06166110a3627bb3a99d51ceb09b0c7ed80
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871416"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>配置其他 Azure HPC 缓存设置

Azure 门户中的 "**配置**" 页具有自定义多个设置的选项。 大多数用户不需要更改这些设置的默认值。

本文还介绍了如何将快照功能用于 Azure Blob 存储目标。 快照功能没有可配置的设置。

若要查看设置，请在 Azure 门户中打开缓存的 "**配置**" 页。

![Azure 门户中的 "配置" 页的屏幕截图](media/configuration.png)

## <a name="adjust-mtu-value"></a>调整 MTU 值
<!-- linked from troubleshoot-nas article -->

可以通过使用标记为 " **MTU 大小**" 的下拉菜单来选择缓存的最大传输单元大小。

默认值为1500个字节，但你可以将其更改为1400。

> [!NOTE]
> 如果降低缓存的 MTU 大小，请确保与缓存通信的客户端和存储系统具有相同的 MTU 设置或较低的值。

降低缓存 MTU 值有助于在缓存的其他网络中解决数据包大小限制。 例如，某些 Vpn 无法成功传输完全大小的1500字节的数据包。 减小通过 VPN 发送的数据包大小可能会消除这一问题。 但请注意，较低的缓存 MTU 设置意味着与缓存通信的任何其他组件（包括客户端和存储系统）都必须具有低 MTU 设置，以避免通信问题。

如果不想更改其他系统组件上的 MTU 设置，则不应降低缓存的 MTU 设置。 还可以使用其他解决方案来解决 VPN 数据包大小限制。 有关诊断和解决此问题的详细信息，请参阅 NAS 故障排除一文中的[调整 VPN 数据包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)。

若要详细了解 Azure 虚拟网络中的 MTU 设置，请阅读[Azure vm 的 tcp/ip 性能优化](../virtual-network/virtual-network-tcpip-performance-tuning.md)。

## <a name="configure-root-squash"></a>配置根 squash
<!-- linked from troubleshoot -->

**Enable root squash**设置控制 Azure HPC 缓存如何处理来自客户端计算机上的根用户的请求。

启用根 squash 后，当客户端通过 Azure HPC 缓存发送请求时，会自动将客户端的根用户映射到用户 "无人"。 它还会阻止客户端请求使用设置 UID 权限位。

如果已禁用 root squash，则客户端根用户（UID 0）发出的请求会作为根传递到后端 NFS 存储系统。 此配置可能会允许不适当的文件访问。

在缓存上设置根 squash 有助于补偿用作存储目标的``no_root_squash`` NAS 系统上所需的设置。 （了解有关[NFS 存储目标先决条件](hpc-cache-prereqs.md#nfs-storage-requirements)的详细信息。）与 Azure Blob 存储目标一起使用时，它还可以提高安全性。

默认设置为 **"是"**。 （2020年4月之前创建的缓存可能会默认设置为 "**否**"。）

## <a name="view-snapshots-for-blob-storage-targets"></a>查看 blob 存储目标的快照

Azure HPC 缓存会自动为 Azure Blob 存储目标保存存储快照。 快照提供后端存储容器内容的快速参考点。

快照不是数据备份的替代，它们不包含有关缓存数据状态的任何信息。

> [!NOTE]
> 此快照功能与 NetApp 或 Isilon 存储软件中包含的快照功能不同。 在拍摄快照之前，这些快照实现会刷新从缓存到后端存储系统的更改。
>
> 为提高效率，Azure HPC 缓存快照不会先刷新更改，只记录已写入 Blob 容器的数据。 此快照不表示缓存数据的状态，因此它可能不包括最近的更改。

此功能仅适用于 Azure Blob 存储目标，并且无法更改其配置。

每隔8小时在 UTC 0:00、08:00 和16:00 执行快照。

Azure HPC 缓存会存储每日、每周和每月快照，直到它们被新的替换。 这些限制包括：

* 每日最多20个快照
* 最多8个每周快照
* 最多3个月快照

从 blob 存储目标的`.snapshot`命名空间的目录中访问快照。
