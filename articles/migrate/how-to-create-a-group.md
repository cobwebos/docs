---
title: 分组计算机以便使用 Azure Migrate 进行评估 | Microsoft 文档
description: 介绍如何在使用 Azure Migrate 服务运行评估之前分组计算机。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301708"
---
# <a name="create-a-group-for-assessment"></a>创建用于评估的组

本文介绍如何使用 Azure 迁移：服务器评估创建用于评估的计算机组。

[Azure 迁移](migrate-services-overview.md)可帮助您迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。 

## <a name="grouping-machines"></a>对计算机进行分组

将计算机分组到组中，以评估它们是否适合迁移到 Azure，并获取 Azure 大小调整和成本估算。 创建组的方法有几种：

- 如果您知道需要一起迁移的计算机，则可以在 Azure 迁移中手动创建组。
- 如果不确定需要组合在一起的计算机，则可以使用 Azure 迁移中的依赖项可视化功能创建组。 

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

## <a name="create-a-group-manually"></a>手动创建组

您可以在[创建评估](how-to-create-assessment.md)的同时创建组。

如果要在创建评估之外手动创建组，请执行以下操作：

1. 在 Azure 迁移项目中>**概述**中，单击"**评估和迁移服务器**"。 在**Azure 迁移中：服务器评估**，单击 **"组**"
    - 如果尚未添加 Azure 迁移：服务器评估工具，请单击以添加该工具。 [了解详情](how-to-assess.md)。
    - 如果尚未创建 Azure 迁移项目，[则有关详细信息](how-to-add-tool-first-time.md)。

    ![选择组](./media/how-to-create-a-group/select-groups.png)

2. 单击 **"组"** 图标。
3. 在 **"创建组**"中，指定组名称，并在 **"设备名称**"中选择用于计算机发现的 Azure 迁移设备。
1. 从计算机列表中选择要添加到组>**创建**的计算机。

    ![创建组](./media/how-to-create-a-group/create-group.png)

现在，您可以在[创建评估](how-to-create-assessment.md)时使用此组。

## <a name="refine-a-group-with-dependency-mapping"></a>使用依赖项映射优化组

依赖关系映射可帮助您可视化跨计算机的依赖项。 当您要评估具有更高置信度的计算机组时，通常使用依赖项映射。
- 它可以帮助您在运行评估之前交叉检查计算机依赖项。 
- 它还有助于确保不留下任何内容，从而在迁移期间避免意外中断，从而有助于有效地规划迁移到 Azure。
- 您可以发现需要一起迁移的相互依赖的系统，并确定正在运行的系统是否仍在为用户提供服务，或者是停用而不是迁移的候选系统。

如果已[设置依赖项映射](how-to-create-group-machine-dependencies.md)，并且想要优化现有组，则执行以下操作：

1. 在 **"服务器"** 选项卡中，在**Azure 迁移：服务器评估**磁贴中，单击"**组**"。
2. 单击要优化的组。
    - 如果尚未设置依赖项映射，**则"依赖项"** 列将显示 **"需要安装**"状态。 对于要可视化依赖项的每个 VM，请单击 **"需要安装**"。 在每个 VM 上安装几个代理，然后才能映射计算机依赖项。 [了解详情](how-to-create-group-machine-dependencies.md)。

        ![添加依赖项映射](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 如果已设置依赖项映射，请在组页上单击 **"查看依赖项**"以打开组依赖项映射。

3. 单击 **"查看依赖项"** 后，组依赖项映射将显示以下内容：

    - 入站（客户端）和出站（服务器）TCP 连接和从已安装依赖项代理的组中的所有计算机的连接。
    - 未安装依赖项代理的从属计算机按端口号分组。
    - 安装了依赖项代理的从属计算机显示为单独的框。
    - 在机器内部运行的进程。 展开每个机器框以查看流程。
    - 机器属性（包括 FQDN、操作系统、MAC 地址）。 单击每个机器框以查看详细信息。

4. 要查看您选择的时间间隔中的依赖项，请通过指定开始和结束日期或持续时间来修改时间范围（默认情况下为一小时）。

    > [!NOTE]
    > 时间范围可达一小时。 如果需要较长的范围，请使用 Azure[监视器查询从属数据](how-to-create-group-machine-dependencies.md)的时间较长。

5. 标识要添加到组或从组中删除的依赖项后，可以修改组。 使用 Ctrl_Click 从组中添加或删除计算机。

    - 你只能添加已发现的计算机。
    - 添加和删除计算机会使组的过去评估无效。
    - 修改组时，你可以视情况创建新的评估。


## <a name="next-steps"></a>后续步骤

了解如何设置和使用[依赖项映射](how-to-create-group-machine-dependencies.md)来创建高置信度组。

