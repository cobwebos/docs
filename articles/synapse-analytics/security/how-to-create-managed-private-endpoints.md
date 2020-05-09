---
title: 创建托管专用终结点以连接到数据源结果。
description: 本文介绍如何从 Azure Synapse 工作区创建到数据源的托管专用终结点。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: d6a0a84050cb8f1b3ad0c04aa66c5b3797282f2e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983221"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>创建到数据源的托管专用终结点（预览）

本文介绍如何在 Azure 中创建数据源的托管专用终结点。 若要了解详细信息，请参阅[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>步骤1：在 Azure 门户中打开 Azure Synapse 工作区

你可以从 Azure Synapse Studio 创建一个托管专用终结点到你的数据源。 在 Azure 门户中选择 "**概述**" 选项卡，然后选择 "**启动 Synapse Studio**"。
![启动 Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>步骤2：导航到 Synapse Studio 中的 "托管虚拟网络" 选项卡

在 Azure Synapse Studio 中，从左侧导航栏中选择 "**管理**" 选项卡。 选择 "**托管虚拟网络**"，然后选择 " **+ 新建**"。
![创建新的托管专用终结点](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>步骤3：选择数据源类型

选择数据源类型。 在这种情况下，目标数据源是 ADLS Gen2 帐户。 选择“继续”  。
![选择目标数据源类型](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>步骤4：输入有关数据源的信息

在下一个窗口中，输入有关数据源的信息。 在此示例中，我们要创建一个 ADLS Gen2 帐户的托管专用终结点。 输入托管专用终结点的**名称**。 提供**Azure 订阅**和**存储帐户名称**。 选择“创建”。 
![输入目标数据源详细信息](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>步骤5：验证托管专用终结点是否已成功创建

提交请求后，你将看到其状态。 若要验证是否创建了已成功创建托管专用终结点，请检查其*设置状态*。 可能需要等待1分钟，然后选择 "**刷新**" 以更新预配状态。 你可以看到已成功创建 ADLS Gen2 帐户的托管专用终结点。

你还可以看到*审批状态*为 "*挂起*"。 目标资源的所有者可以批准或拒绝专用终结点连接请求。 如果所有者批准专用终结点连接请求，则建立一个专用链接。 如果拒绝，则不建立专用链接。
![托管专用终结点创建请求状态](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>后续步骤

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)