---
title: 使用 Azure 网络观察程序“IP 流验证”功能验证流量 - Azure 门户 | Microsoft 文档
description: 本文介绍如何检查是允许还是拒绝进出虚拟机的流量
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b6d929f025c8b95709b7c0eb28ee78310e5f12a5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 网络观察程序的组件（即“IP 流验证”功能）检查是允许还是拒绝进出 VM 的流量

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


“IP 流验证”是网络观察程序的一项功能，用于验证是否允许流量进出虚拟机。 可以针对传入或传出流量运行验证。 此方案对于获取虚拟机是否可以与外部资源或其他资源通信的当前状态很有用。 可以使用“IP 流验证”功能验证网络安全组 (NSG) 规则是否正确配置，以及对 NSG 规则所阻止的流进行故障排除。 使用“IP 流验证”功能的另一个原因是确保要阻止的流量已由 NSG 正确阻止。

## <a name="before-you-begin"></a>开始之前

此方案假定用户已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序，或者已经有网络观察程序的实例。 此方案还假定要使用的包含有效虚拟机的资源组已存在。

## <a name="scenario"></a>场景

此方案使用“IP 流验证”功能来验证虚拟机是否可以通过端口 443 与其他计算机通信。 如果流量被拒绝，此方案将返回拒绝该流量的安全规则。 若要了解有关 IP 流验证的详细信息，请访问 [IP 流验证概述](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>运行“IP 流验证”

导航到网络观察程序并单击“IP 流验证”。 选择要从中验证流量的虚拟机和网络接口。 输入任何其他筛选信息，并单击“检查”。

单击“检查”后，将基于提供的标准检查流。 结果是**允许访问**或**拒绝访问**。 如果拒绝访问，则提供阻止流量的网络安全组 (NSG) 和安全规则。 如果拒绝流量是预期的行为，则该规则已成功。

> [!NOTE]
> “IP 流验证”功能要求 VM 资源已分配。

如下图所示，已允许出站 HTTPS 流量。

![“IP 流验证”概述][1]

如下图中所示，流量已更改为入站，入站端口已更改为 123。 现已拒绝流量，提供了“拒绝访问”消息以及拒绝流量的网络安全组和安全规则。

![IP 流量结果][2]

## <a name="next-steps"></a>后续步骤

如果流量不应被阻止但却被阻止，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)，查找已定义的网络安全组和安全规则。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













