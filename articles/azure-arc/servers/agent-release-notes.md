---
title: 已启用 Azure Arc 的服务器 (预览版) 代理的新增功能
description: 本文提供了适用于 Azure Arc 的服务器 (预览版) 代理的发行说明。 对于许多汇总问题，还提供了其他详细信息的链接。
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236609"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>已启用 Azure Arc 的服务器 (预览版) 代理的新增功能

已启用 Azure Arc 的服务器 (预览版) 连接的计算机代理不断地获得改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复

## <a name="august-2020"></a>2020 年 8 月

版本：0.11

- 支持 Ubuntu 20.04。

- 扩展部署的可靠性改进。

### <a name="known-issues"></a>已知问题

如果使用的是较旧版本的 Linux 代理并将其配置为使用代理服务器，则需要在升级后重新配置代理服务器设置。 要执行此操作，请运行 `sudo azcmagent_proxy add http://proxyserver.local:83`。

## <a name="next-steps"></a>后续步骤

在评估或启用启用 Arc 的服务器之前 (预览) 跨多台混合计算机，查看 [已连接计算机代理概述](agent-overview.md) ，了解要求、有关代理的技术详细信息以及部署方法。