---
title: 使用专用链接连接到 Azure Synapse 工作区
description: 本文介绍如何使用专用链接连接到 Azure Synapse 工作区
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432185"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>使用专用链接（预览版）连接到 Azure Synapse 工作区

本文介绍如何在 Azure Synapse 工作区中创建专用终结点。 有关详细信息，请参阅[私有链接和专用终结点](https://docs.microsoft.com/azure/private-link/)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>步骤1：在 Azure 门户中打开 Azure Synapse 工作区

选择 "**安全**" 下的**专用终结点连接**，然后选择 " **+ 专用终结点**"。
![在 Azure 门户中打开 Azure Synapse 工作区](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>步骤2：选择订阅和区域详细信息

在 "**创建专用终结点**" 窗口中的 "**基本**信息" 选项卡下，选择你的**订阅**和**资源组**。 为要创建的专用终结点指定一个**名称**。 选择要在其中创建专用终结点的**区域**。

专用终结点在子网中创建。 所选的订阅、资源组和区域筛选专用终结点子网。 选择 "**下一步"：完成时资源 >** 。
![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>步骤3：选择 Azure Synapse 工作区详细信息

在 "**资源**" 选项卡的 "我的目录" 中选择 "**连接到 azure 资源**"。选择包含 azure Synapse 工作区的**订阅**。 用于在 Azure Synapse 工作区中创建专用终结点的**资源类型**是*Synapse/工作区*。

选择 Azure Synapse 工作区作为**资源**。 每个 Azure Synapse 工作区都有三个**目标子资源**，你可以在其中创建专用终结点： Sql、SqlOnDemand 和 Dev。

选择**下一步：配置>** 转到安装程序的下一部分。
![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

在 "**配置**" 选项卡中，选择应在其中创建专用终结点的**虚拟网络**和**子网**。 还需要创建映射到专用终结点的 DNS 记录。

选择 **"是"** ，将**与专用 dns 区域集成**，将专用终结点与专用 dns 区域集成。 如果没有与 VNet 关联的专用 DNS 区域，则会创建一个新的专用 DNS 区域。 选择 "完成后**查看 + 创建**"。

![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

部署完成后，在 Azure 门户中打开 Azure Synapse 工作区，并选择 "**专用终结点连接**"。 将显示新的专用终结点和关联到专用终结点的专用终结点连接名称。

![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>后续步骤

详细了解[托管的工作区 VNet](./synapse-workspace-managed-vnet.md)

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
