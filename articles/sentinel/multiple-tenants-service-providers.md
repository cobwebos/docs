---
title: 将多个租户用于 MSSP 服务提供程序的 Azure Sentinel |Microsoft Docs
description: 如何将多个租户用于 MSSP 服务提供程序的 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240493"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>在 Azure Sentinel 中使用多个租户 

如果你是托管安全服务提供商（MSSP），并且你正在使用[Azure Lighthouse](../lighthouse/overview.md)来管理客户的安全运营中心（SOC），则可以管理客户的 Azure Sentinel 资源，而无需直接连接到客户的租户，来自你自己的 Azure 租户。 

## <a name="prerequisites"></a>先决条件
- [载入 Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- 为了使其正常工作，必须至少在一个订阅上向 Azure Sentinel 资源提供程序注册你的租户。 如果租户中有已注册的 Azure Sentinel，就可以开始使用了。 如果不是，则在 Azure 门户中，选择 "**订阅**"，然后选择 " `Microsoft.Security.Insights` **资源提供程序**"，然后搜索并选择 "**注册**"。
   ![检查资源提供程序](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>如何从其他租户访问 Azure Sentinel
1. 在 "**目录和订阅**" 下，选择委托的目录，以及客户的 Azure Sentinel 工作区所在的订阅。

   ![生成安全事件](media/multiple-tenants-service-providers/directory-subscription.png)

1. 打开 Azure Sentinel。 你将看到所选订阅中的所有工作区，并且你能够无缝地处理这些工作区，就像你自己租户中的任何工作区一样。

> [!NOTE]
> 你将无法从托管的工作区中部署 Azure Sentinel 中的连接器。 若要部署连接器，必须直接登录要在其上部署连接器的租户，并使用所需的权限对其进行身份验证。





## <a name="next-steps"></a>后续步骤
本文档介绍了如何无缝地管理多个 Azure Sentinel 租户。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

