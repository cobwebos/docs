---
title: 创建托管专用终结点以连接到数据源结果。
description: 本文将介绍如何从 Azure Synapse 工作区创建托管专用终结点到数据源。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428896"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>创建数据源的托管专用终结点（预览）

本文将介绍如何创建 Azure 中的数据源的托管专用终结点。 请参阅[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)以了解更多信息。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>第 1 步：在 Azure 门户中打开 Azure 突触工作区

可以从 Azure Synapse Studio 创建到数据源的托管专用终结点。 选择 Azure 门户中的 **"概述"** 选项卡，然后选择**启动同步工作室**。
![启动 Azure 同步工作室](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>第 2 步：导航到 Synapse 工作室中的托管虚拟网络选项卡

在 Azure 同步工作室中，从左侧导航中选择 **"管理**"选项卡。 选择**托管虚拟网络**，然后选择 **" 新建**"。
![创建新的托管专用终结点](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>第 3 步：选择数据源类型

选择数据源类型。 在这种情况下，目标数据源是 ADLS gen2 帐户。 选择“继续”。 
![选择目标数据源类型](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>第 4 步：输入有关数据源的信息

在下一个窗口中，输入有关数据源的信息。 在此示例中，我们将创建 ADLS gen2 帐户的托管专用终结点。 输入托管专用终结点**的名称**。 提供**Azure 订阅**和**存储帐户名称**。 选择“创建”  。
![输入目标数据源详细信息](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>第 5 步：验证托管专用终结点是否成功创建

提交请求后，您将看到其状态。 要验证成功创建托管专用终结点，请检查其*预配状态*。 您可能需要等待 1 分钟，然后选择 **"刷新"** 以更新预配状态。 您可以看到 ADLS 第 2 代帐户的托管专用终结点已成功创建。

您还可以看到*批准状态*处于*挂起状态*。 目标资源的所有者可以批准或拒绝专用终结点连接请求。 如果所有者批准专用终结点连接请求，则建立专用链接。 如果被拒绝，则未建立专用链接。
![托管专用终结点创建请求状态](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>后续步骤

了解有关[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)的更多