---
title: 将多个租户用于 MSSP 服务提供程序的 Azure Sentinel |Microsoft Docs
description: 如何将多个租户用于 MSSP 服务提供程序的 Azure Sentinel。
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
ms.openlocfilehash: caa79b572d0024b93abd2d32ca99d92cc2a8b4bb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582069"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>在 Azure Sentinel 中使用多个租户 

如果你是托管安全服务提供商（MSSP），并且你正在使用[Azure Lighthouse](../lighthouse/overview.md)来管理客户的安全运营中心（SOC），则可以管理客户的 azure Sentinel 资源，而无需直接连接到客户的租户，只需从你自己的 azure 租户。 

## <a name="prerequisites"></a>必备条件
- [载入 Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- 为了使其正常工作，必须至少在一个订阅上向 Azure Sentinel 资源提供程序注册你的租户。 如果租户中有已注册的 Azure Sentinel，就可以开始使用了。 如果没有，请在 Azure 门户中选择 "**订阅**"，然后选择 "**资源提供程序**"，然后搜索 `Microsoft.Security.Insights` 并选择 "**注册**"。
   ![检查资源提供程序](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>如何从其他租户访问 Azure Sentinel
1. 在 "**目录和订阅**" 下，选择委托的目录，以及客户的 Azure Sentinel 工作区所在的订阅。

   ![生成安全事件](media/multiple-tenants-service-providers/directory-subscription.png)

1. 打开 Azure Sentinel。 你将看到所选订阅中的所有工作区，并且你能够无缝地处理这些工作区，就像你自己租户中的任何工作区一样。

> [!NOTE]
> 你将无法从托管的工作区中部署 Azure Sentinel 中的连接器。 若要部署连接器，必须直接登录要在其上部署连接器的租户，并使用所需的权限对其进行身份验证。





## <a name="next-steps"></a>后续步骤
本文档介绍了如何无缝地管理多个 Azure Sentinel 租户。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

