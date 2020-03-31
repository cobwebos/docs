---
title: 与多个租户一起为 MSSP 服务提供商使用 Azure 哨兵*微软文档
description: 如何使用多个租户到 MSSP 服务提供商的 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476009"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>在 Azure 哨兵中处理多个租户 

如果您是托管安全服务提供商 （MSSP），并且使用[Azure 灯塔](../lighthouse/overview.md)来管理客户的安全操作中心 （SOC），则可以管理客户的 Azure Sentinel 资源，而无需直接从您自己的 Azure 租户连接到客户的租户。 

## <a name="prerequisites"></a>先决条件
- [板载 Azure 灯塔](../lighthouse/how-to/onboard-customer.md)
- 要正常工作，租户必须在至少一个订阅上注册到 Azure Sentinel 资源提供程序。 如果租户中已注册了 Azure Sentinel，则已准备好开始。 如果没有，请从 Azure 门户中选择 **"订阅"，** 后跟**资源提供程序**。  然后，从**SOC -资源提供程序**屏幕中，搜索并选择`Microsoft.OperationalInsights``Microsoft.SecurityInsights`和 ，然后选择"**注册**"。
   ![检查资源提供程序](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>如何从其他租户访问 Azure 哨兵
1. 在 **"目录 + 订阅**"下，选择委派的目录以及客户的 Azure Sentinel 工作区所在的订阅。

   ![生成安全事件](media/multiple-tenants-service-providers/directory-subscription.png)

1. 打开 Azure 哨兵。 您将看到所选订阅中的所有工作区，并且您将能够像您自己的租户中的任何工作区一样无缝地使用它们。

> [!NOTE]
> 您将无法从托管工作区中部署 Azure Sentinel 中的连接器。 要部署连接器，必须直接登录到要部署连接器的租户，并在那里使用所需的权限进行身份验证。





## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何无缝管理多个 Azure Sentinel 租户。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

