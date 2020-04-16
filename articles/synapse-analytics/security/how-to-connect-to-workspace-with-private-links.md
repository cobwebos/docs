---
title: 使用专用链接连接到 Azure 同步工作区
description: 本文将教您如何使用专用链接连接到 Azure Synapse 工作区
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432185"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>使用专用链接连接到 Azure 同步工作区（预览）

本文将介绍如何创建专用终结点到 Azure Synapse 工作区。 有关详细信息，请参阅[私人链接和专用终结点](https://docs.microsoft.com/azure/private-link/)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>第 1 步：在 Azure 门户中打开 Azure 突触工作区

选择 **"安全"** 下的**专用终结点连接**，然后选择 **" 专用终结点**"。
![在 Azure 门户中打开 Azure 同步工作区](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>第 2 步：选择订阅和区域详细信息

在 **"创建专用终结点**"窗口中的 **"基本"** 选项卡下，选择 **"订阅****和资源组**"。 向要创建的专用终结点指定**名称**。 选择要创建专用终结点**的区域**。

专用终结点在子网中创建。 所选订阅、资源组和地区筛选专用终结点子网。 选择 **"下一步"：资源>** 完成后。
![选择订阅和地区详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>第 3 步：选择 Azure 同步工作区详细信息

在 **"资源"** 选项卡**中选择"连接到目录中的 Azure 资源**。选择包含 Azure 同步工作区的**订阅**"。 用于将专用终结点创建到 Azure Synapse 工作区**的资源类型**是*Microsoft。*

选择 Azure 同步工作区作为**资源**。 每个 Azure Synapse 工作区都有三个目标**子资源**，您可以创建专用终结点：Sql、SqlOnDemand 和 Dev。

选择 **"下一步"：配置>** 前进到设置的下一部分。
![选择订阅和地区详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

在 **"配置"** 选项卡中，选择应在其中创建专用终结点的**虚拟网络和****子网**。 您还需要创建映射到专用终结点的 DNS 记录。

选择 **"是**"以**与专用 DNS 区域集成**，以便将专用终结点与专用 DNS 区域集成。 如果没有与 VNet 关联的专用 DNS 区域，则会创建新的专用 DNS 区域。 选择 **"审阅+ 创建**"，完成后。

![选择订阅和地区详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

部署完成后，在 Azure 门户中打开 Azure 突触工作区，然后选择**专用终结点连接**。 将显示与专用终结点关联的新专用终结点和专用终结点连接名称。

![选择订阅和地区详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>后续步骤

了解有关[托管工作区 VNet 的更多](./synapse-workspace-managed-vnet.md)

了解有关[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)的更多

[创建到数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
