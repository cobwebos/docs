---
title: 云中责任分担 - Microsoft Azure
description: 了解共担责任模型、由云服务提供商处理的安全任务，以及由你处理的任务。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "72518364"
---
# <a name="shared-responsibility-in-the-cloud"></a>云中责任分担

当你考虑和评估公有云服务时，必须了解共担责任模型、由云服务提供商处理的安全任务以及由你处理的任务。 工作负荷责任因各种因素而异，具体取决于工作负荷是托管在软件即服务 (SaaS) 上、平台即服务 (PaaS) 上、基础结构即服务 (IaaS) 上还是托管在本地数据中心

## <a name="division-of-responsibility"></a>责任划分
在本地数据中心，你拥有整个堆栈。 当你迁移到云时，某些责任将转移到 Microsoft。 下图说明了你和 Microsoft 之间的责任区域，具体取决于你的堆栈的部署类型。

![责任区域](./media/shared-responsibility/shared-responsibility.png)

对于所有云部署类型，拥有数据和标识。 需要负责保护由你控制的数据和标识、本地资源及云组件的安全（保护的项目因服务类型而异）。

无论部署类型如何，你始终要承担以下责任：

- 数据
- 终结点
- 帐户
- 访问管理

## <a name="cloud-security-advantages"></a>云的安全优势
云在解决长期存在的信息安全难题方面具有显著优势。 在本地环境中，组织的可用资源可能有限，无法尽责在安全措施上投资，使得攻击者能够利用所有层中的漏洞。

下图显示了一种传统方法，其中的许多安全责任由于资源有限而无法履行。 在启用云的方法中，你可以将日常安全责任转移到云服务提供商，并重新分配资源。

![云时代的安全优势](./media/shared-responsibility/cloud-enabled-security.png)

在启用云的方法中，你还可以利用基于云的安全功能来提高效率，并使用云智能来缩短威胁检测和响应时间。 通过将责任转移到云提供商，组织可以扩大安全覆盖范围，为其他优先业务重新调配安全资源与预算。

## <a name="next-steps"></a>后续步骤
若要详细了解你和 Microsoft 在 SaaS、PaaS 和 IaaS 部署中的责任划分，请参阅[云计算的共担责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。
