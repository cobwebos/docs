---
title: 云中的共享责任 - 微软 Azure
description: 了解共享责任模型以及哪些安全任务由云提供商处理，以及哪些任务由您处理。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518364"
---
# <a name="shared-responsibility-in-the-cloud"></a>云中责任分担

在考虑和评估公共云服务时，了解共享责任模型以及哪些安全任务由云提供商处理以及哪些任务由您处理至关重要。 工作负载职责因工作负载是托管在软件即服务 （SaaS）、平台即服务 （PaaS）、基础架构即服务 （IaaS） 还是本地数据中心而异

## <a name="division-of-responsibility"></a>责任划分
在本地数据中心中，您拥有整个堆栈。 当您迁移到云时，一些职责会转移到 Microsoft。 下图根据堆栈的部署类型说明了您和 Microsoft 之间的责任领域。

![责任区域](./media/shared-responsibility/shared-responsibility.png)

对于所有云部署类型，拥有数据和标识。 需要负责保护由你控制的数据和标识、本地资源及云组件的安全（保护的项目因服务类型而异）。

无论部署类型如何，您始终保留以下职责：

- 数据
- 终结点
- Account
- 访问管理

## <a name="cloud-security-advantages"></a>云的安全优势
云为解决长期信息安全挑战提供了显著优势。 在本地环境中，组织的可用资源可能有限，无法尽责在安全措施上投资，使得攻击者能够利用所有层中的漏洞。

下图显示了一种传统方法，即由于资源有限，许多安全责任未履行。 在支持云的方法中，您可以每天将安全职责转移给云提供商并重新分配资源。

![云时代的安全优势](./media/shared-responsibility/cloud-enabled-security.png)

在支持云的方法中，您还可以利用基于云的安全功能提高有效性，并使用云智能来提高威胁检测和响应时间。 通过将责任转移到云提供商，组织可以扩大安全覆盖范围，为其他优先业务重新调配安全资源与预算。

## <a name="next-steps"></a>后续步骤
有关在 SaaS、PaaS 和 IaaS 部署中您和 Microsoft 之间的责任划分的详细信息，请参阅[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。
