---
title: Azure 基础结构安全性 | Microsoft Docs
description: 本文介绍 Microsoft 如何确保 Azure 数据中心的安全性。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903850"
---
# <a name="azure-infrastructure-security"></a>Azure 基础结构安全性
Microsoft Azure 在由 Microsoft 管理和运营的数据中心运行。 这些地理位置分散的数据中心符合有关安全性和可靠性的关键行业标准，例如 ISO/IEC 27001:2013 和 NIST SP 800-53。 它们由 Microsoft 运营人员进行管理和监控。 这些运营人员在提供全球最大的全天候持续运行的联机服务方面拥有多年经验。

本系列文章提供了有关 Microsoft 如何保护 Azure 基础结构的信息。 文章讨论了：

- [物理安全性](azure-physical-security.md)
- [可用性](azure-infrastructure-availability.md)
- [组件和边界](azure-infrastructure-components.md)
- [网络体系结构](azure-infrastructure-network.md)
- [生产网络](azure-production-network.md)
- [SQL 数据库](azure-infrastructure-sql.md)
- [操作](azure-infrastructure-operations.md)
- [监视](azure-infrastructure-monitoring.md)
- [完整性](azure-infrastructure-integrity.md)
- [数据保护](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>共担责任模型
请务必了解你与 Microsoft 之间的责任分工。 在本地，拥有整个堆栈，但迁移到云后，某些责任将转移到 Microsoft。 下面的责任矩阵展示了在软件即服务 (SaaS)、平台即服务 (PaaS) 和服务架构 (IaaS) 部署中，分别由你和 Microsoft 负责的堆栈区域。

![共担责任][1]

不管部署类型为何，都必须负责的项目包括：

- 数据
- 终结点
- 帐户
- 访问管理

确保了解你与 Microsoft 在 SaaS、PaaS 和 IaaS 部署中的责任分工。 有关更多详细信息，请参阅[云计算的责任分担](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产操作和管理](azure-infrastructure-operations.md)
- [监视 Azure 基础结构](azure-infrastructure-monitoring.md)
- [Azure 基础结构完整性](azure-infrastructure-integrity.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
