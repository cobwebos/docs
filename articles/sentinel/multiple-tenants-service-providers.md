---
title: 管理 Azure 中的多个租户作为托管安全服务提供程序 |Microsoft Docs
description: 如何使用 Azure Lighthouse 在 Azure Sentinel 中加入和管理多个租户，作为托管安全服务提供商 (MSSP) 。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578134"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>以 MSSP 的形式管理 Azure 中的多个租户

如果你是托管安全服务提供商 (MSSP) 并且使用 [Azure Lighthouse](../lighthouse/overview.md) 向客户提供安全操作中心 (SOC) 服务，则可以直接从自己的 azure 租户管理客户的 azure Sentinel 资源，而无需连接到客户的租户。 

## <a name="prerequisites"></a>必备条件

- [载入 Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- 为了使其正常工作，MSSP 租户) 的租户 (必须至少在一个订阅上注册 Azure Sentinel 资源提供程序。 此外，每个客户的租户都必须注册了资源提供程序。 如果已在租户中注册 Azure Sentinel，并已在其用户中注册了，则已准备好开始使用。 若要验证注册，请执行以下步骤：

    1. 从 "Azure 门户中选择" **订阅** "，然后从菜单中选择相关订阅。

    1. 从订阅屏幕上的导航菜单中的 " **设置**" 下，选择 " **资源提供程序**"。

    1. 从 ***订阅名称* |资源提供程序** 屏幕上，搜索并选择 " *microsoft.operationalinsights* " 和 " *SecurityInsights*"，然后检查 " **状态** " 列。 如果提供程序的状态为 *NotRegistered*，请选择 " **注册**"。
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="检查资源提供程序":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>如何访问托管租户中的 Azure Sentinel

1. 在 " **目录和订阅**" 下，选择委托的目录 (Directory = 租户) ，以及客户的 Azure Sentinel 工作区所在的订阅。

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="检查资源提供程序":::

1. 打开 Azure Sentinel。 你将看到所选订阅中的所有工作区，并且你能够无缝地处理这些工作区，就像你自己租户中的任何工作区一样。

> [!NOTE]
> 你将无法从托管的工作区中部署 Azure Sentinel 中的连接器。 若要部署连接器，必须直接登录要在其上部署连接器的租户，并使用所需的权限对其进行身份验证。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何无缝地管理多个 Azure Sentinel 租户。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

