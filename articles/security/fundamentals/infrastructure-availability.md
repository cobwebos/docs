---
title: Azure 基础结构可用性-Azure 安全性
description: 本文提供了有关 Microsoft 保护 Azure 基础结构的安全, 并提供客户数据的最高可用性的信息。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727215"
---
# <a name="azure-infrastructure-availability"></a>Azure 基础结构可用性
本文提供了有关 Microsoft 保护 Azure 基础结构的安全, 并提供客户数据的最高可用性的信息。 Azure 基于通过虚拟化技术实现的全面冗余提供可靠的可用性。

## <a name="temporary-outages-and-natural-disaster"></a>临时停电和自然灾害
Microsoft 云基础结构和运营团队设计、生成、运营云基础结构并提高其安全性。 该团队确保 Azure 基础结构提供高可用性和可靠性、高效率、智能可伸缩性。 该团队提供更安全、更专用且更受信任的云。

不间断电源和大量电池可确保在发生短期电力中断时仍然持续供电。 应急发电机为长时间的停电和计划内维护提供备用电源。 如果发生自然灾难，数据中心可以使用现场燃料储备。

高速且可靠的光纤网络将数据中心与其他主要中心和 Internet 用户连接起来。 计算节点将工作负荷托管在离用户较近的位置，以减少延迟、提供异地冗余并提高整体服务复原能力。 一组工程师全天候工作，以确保服务持续可用。

Microsoft 通过高级监视和事件响应、服务支持以及备份故障转移功能确保高可用性。 地理位置分散的 Microsoft 运营中心每周 7 天、每天 24 小时运营。 Azure 网络是世界上最大的网络之一。 光纤和内容分发网络连接数据中心和边缘节点，以确保高性能和可靠性。

## <a name="disaster-recovery"></a>灾难恢复
Azure 使数据在两个位置保持持久性。 可以选择备份站点的位置。 在这两个位置，Azure 始终维护数据的三个正常副本。

## <a name="database-availability"></a>数据库可用性
Azure 确保可通过具有持续数据库可用性的 Internet 网关从 Internet 访问数据库。 监视服务以 5 分钟的时间间隔评估活动数据库的运行状况和状态。

## <a name="storage-availability"></a>存储可用性
Azure 通过高度可缩放且持久的存储服务提供存储，该服务提供连接终结点。 这意味着应用程序可以直接访问存储服务。 存储服务在保持事务完整性的同时有效地处理传入存储请求。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何帮助保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](physical-security.md)
- [Azure 信息系统的组件和边界](infrastructure-components.md)
- [Azure 网络体系结构](infrastructure-network.md)
- [Azure 生产网络](production-network.md)
- [Azure SQL 数据库安全功能](infrastructure-sql.md)
- [Azure 生产运营和管理](infrastructure-operations.md)
- [Azure 基础结构监视](infrastructure-monitoring.md)
- [Azure 基础结构完整性](infrastructure-integrity.md)
- [Azure 客户数据保护](protection-customer-data.md)
