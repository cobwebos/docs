---
title: 使用专用链接连接到 Synapse 工作区
description: 本文介绍如何使用专用链接连接到 Azure Synapse 工作区
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 17636bf993df5105093ca690e36db22493a2472e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005967"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>使用专用链接连接到 Azure Synapse 工作区（预览版）

本文介绍如何为 Azure Synapse 工作区创建专用终结点。 有关详细信息，请参阅[专用链接和专用终结点](https://docs.microsoft.com/azure/private-link/)。

## <a name="step-1-register-network-resource-provider"></a>步骤 1：注册网络资源提供程序

如果尚未注册网络资源提供程序，请注册它。 通过注册资源提供程序来配置订阅，以供资源提供程序使用。 [注册](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)时，从资源提供程序列表中选择“Microsoft.Network”。 如果已注册网络资源提供程序，则继续执行步骤 2。


## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>步骤 2：在 Azure 门户中打开 Azure Synapse 工作区

在“安全性”下选择“专用终结点连接”，然后选择“+ 专用终结点”  。
![在 Azure 门户中打开 Azure Synapse 工作区](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>步骤 3：选择订阅和区域详细信息

在“创建专用终结点”窗口中的“基本信息”选项卡下，选择“订阅”和“资源组”   。 为要创建的专用终结点指定“名称”。 选择要在其中创建专用终结点的“区域”。

在子网中创建专用终结点。 所选的订阅、资源组和区域将筛选专用终结点子网。 在完成时选择“下一步:资源”。
![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>步骤 4：选择 Azure Synapse 工作区详细信息

在“资源”选项卡中选择“连接到目录中的 Azure 资源” 。选择包含 Azure Synapse 工作区的“订阅”。 用于创建 Azure Synapse 工作空间的专用终结点的“资源类型”为 Microsoft.Synapse/workspaces。

选择 Azure Synapse 工作区作为“资源”。 每个 Azure Synapse 工作区都具有三个可以在其中创建专用终结点的“目标子资源”：Sql、SqlOnDemand 和 Dev。

在完成时选择“下一步:配置”以进入设置的下一部分。
![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

在“配置”选项卡中，选择要在其中创建专用终结点的“虚拟网络”和“子网”  。 还需要创建映射到专用终结点的 DNS 记录。

为“与专用 DNS 区域集成”选择“是”，以将专用终结点与专用 DNS 区域集成 。 如果没有与 Microsoft Azure 虚拟网络关联的专用 DNS 区域，则会创建一个新的专用 DNS 区域。 完成时选择“查看 + 创建”。

![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

部署完成后，在 Azure 门户中打开 Azure Synapse 工作区，并选择“专用终结点连接”。 将显示新专用终结点和与专用终结点关联的专用终结点连接名称。

![选择订阅和区域详细信息](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>后续步骤

了解有关[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)的详细信息

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
