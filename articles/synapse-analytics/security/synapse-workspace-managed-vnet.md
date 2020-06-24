---
title: Azure Synapse Analytics 中的托管虚拟网络
description: 本文介绍 Azure Synapse Analytics 中的托管虚拟网络
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b46ce6f6164479853bc762cb1ca45d67f7f80930
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194359"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics 托管虚拟网络（预览版）

本文将介绍 Azure Synapse Analytics 中的托管虚拟网络。

## <a name="managed-workspace-vnet"></a>托管工作区 VNet

创建 Azure Synapse 工作区时，可以选择将其关联到一个 VNet。 与工作区关联的 VNet 由 Azure Synapse 管理。 此 VNet 称为托管工作区 VNet。

托管工作区 VNet 以四种方式提供值：

- 使用托管工作区 VNet，可以将管理 VNet 的负担转移给 Azure Synapse。
- 无需在自己的 VNet 上配置入站 NSG 规则，即可让 Azure Synapse 管理流量进入 VNet。 这些 NSG 规则的配置错误会导致客户的服务中断。
- 不需要基于峰值负载为 Spark 群集创建子网。
- 托管工作区 VNet 与托管专用终结点一起防止数据渗透。 只能在具有关联的托管工作区 VNet 的工作区中创建托管专用终结点。

创建具有关联的托管工作区 VNet 的工作区可确保你的工作区是与其他工作区隔离的网络。 Azure Synapse 在工作区中提供了各种分析功能：数据集成、Apache Spark、SQL 池和 SQL 按需版本。

如果工作区具有托管工作区 VNet，则会在其中部署数据集成和 Spark 资源。 托管工作区 VNet 还为 Spark 活动提供用户级隔离，因为每个 Spark 群集都在其自己的子网中。

SQL 池和 SQL 按需版本是多租户功能，因此位于托管工作区 VNet 外部。 与 SQL 池和 SQL 按需版本进行的工作区内通信使用 Azure 专用链接。 当你创建与托管工作区 VNet 关联的工作区时，系统会自动为你创建这些专用链接。

>[!IMPORTANT]
>创建工作区后，无法更改此工作区配置。 例如，你无法重新配置没有托管工作区 VNet 与之关联的工作区并将某个 VNet 关联到它。 同样，你也无法重新配置具有关联的托管工作区 VNet 的工作区并将 VNet 与之解除关联。

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>创建具有托管工作区 VNet 的 Azure Synapse 工作区

如果尚未注册网络资源提供程序，请注册它。 通过注册资源提供程序来配置订阅，以供资源提供程序使用。 [注册](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)时，从资源提供程序列表中选择“Microsoft.Network”。

若要创建具有关联的托管工作区 VNet 的 Azure Synapse 工作区，请在 Azure 门户中选择“安全性 + 网络”选项卡，然后选中“启用托管虚拟网络”复选框。

如果将此复选框保留为未选中状态，则工作区不会有与之关联的 VNet。

>[!IMPORTANT]
>只能在具有托管工作区 VNet 的工作区中使用专用链接。

![启用托管工作区 VNet](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>未来，将阻止来自托管工作区 VNet 的所有出站流量（通过托管专用终结点的出站流量除外）。 建议创建托管专用终结点来连接到工作区外部的所有 Azure 数据源。 

可以通过在 Azure 门户中选择“概览”来检查 Azure Synapse 工作区是否关联到某个托管工作区 VNet。

![Azure 门户中的工作区概览](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>后续步骤

创建 [Azure Synapse 工作区](../quickstart-create-workspace.md)

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
