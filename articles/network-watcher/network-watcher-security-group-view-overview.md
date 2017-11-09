---
title: "Azure 网络观察程序中的安全组视图简介 | Microsoft 文档"
description: "此页概述了网络观察程序安全视图功能"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.openlocfilehash: f4175875b68c52e68588b8d0debd003ab73427ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Azure 网络观察程序中的网络安全组视图简介 | Microsoft 文档

网络安全组可以在子网级别或 NIC 级别关联。 在子网级别关联时，网络安全组将应用于子网中的所有 VM 实例。 网络安全组视图针对虚拟机返回在 NIC 和子网级别关联的所有已配置 NSG 和规则，以供深入了解配置。 此外，VM 中的每个 NIC 将返回有效的安全规则。 使用网络安全组视图，可以评估网络漏洞的 VM，如打开端口。 还可以基于[已配置规则和有效安全规则之间的比较](network-watcher-nsg-auditing-powershell.md)，验证网络安全组是否按预期方式工作。

更多扩展用例在安全合规性和审核方面。 在组织中可以定义一组规范性安全规则作为安全监管模型。 通过将规范性规则与网络中每个 VM 的有效规则相比较，可以编程方式实现定期合规性审核。

在门户中，规则按生效时间、子网、网络接口和默认值划分。 这会提供一个应用于虚拟机的规则的简单视图。 无论哪个标签页，提供的下载按钮均可将所有安全规则轻松地转换为 CSV 文件。

![安全组视图][1]

可以选择规则并打开一个新的边栏选项卡以显示网络安全组以及源和目标前缀。 从此边栏选项卡可以直接导航到网络安全组资源。

![明细][2]

### <a name="next-steps"></a>后续步骤

通过访问[使用 PowerShell 审核网络安全组设置](network-watcher-nsg-auditing-powershell.md)，了解如何审核网络安全组设置

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









