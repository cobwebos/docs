---
title: 在云中共享责任-Microsoft Azure
description: 了解共享的责任模型，以及由云提供商处理哪些安全任务以及由您处理哪些任务。
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
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518364"
---
# <a name="shared-responsibility-in-the-cloud"></a>云中责任分担

当你考虑和评估公有云服务时，了解共享责任模型以及云提供商处理哪些安全任务以及由你处理哪些任务至关重要。 工作负荷的责任取决于工作负荷是托管在软件即服务（SaaS）、平台即服务（PaaS）、基础结构即服务（IaaS）还是本地数据中心内。

## <a name="division-of-responsibility"></a>责任划分
在本地数据中心，你拥有整个堆栈。 当你迁移到云时，一些职责会转移到 Microsoft。 下图说明了你和 Microsoft 之间的责任区域，具体取决于你的堆栈的部署类型。

![责任区域](./media/shared-responsibility/shared-responsibility.png)

对于所有云部署类型，拥有数据和标识。 需要负责保护由你控制的数据和标识、本地资源及云组件的安全（保护的项目因服务类型而异）。

无论部署类型是什么，都始终会保留以下责任：

- 数据
- 终结点
- 帐户
- 访问管理

## <a name="cloud-security-advantages"></a>云的安全优势
云提供了很大的优势，可解决长期的信息安全难题。 在本地环境中，组织的可用资源可能有限，无法尽责在安全措施上投资，使得攻击者能够利用所有层中的漏洞。

下图显示了一种传统方法，其中许多安全责任由于资源有限而不符合。 在启用云的方法中，你可以将日常安全责任转移到你的云提供商，并重新分配资源。

![云时代的安全优势](./media/shared-responsibility/cloud-enabled-security.png)

在支持云的方法中，你还可以利用基于云的安全功能，以获得更高的效率，并使用云智能来改善威胁检测和响应时间。 通过将责任转移到云提供商，组织可以扩大安全覆盖范围，为其他优先业务重新调配安全资源与预算。

## <a name="next-steps"></a>后续步骤
有关 SaaS、PaaS 和 IaaS 部署中与 Microsoft 之间的责任划分的详细信息，请参阅[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。
