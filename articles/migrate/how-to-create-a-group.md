---
title: 分组计算机以便使用 Azure Migrate 进行评估 | Microsoft 文档
description: 介绍如何在使用 Azure Migrate 服务运行评估之前分组计算机。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68301708"
---
# <a name="create-a-group-for-assessment"></a>为评估创建组

本文介绍如何创建计算机组, 以便 Azure Migrate 进行评估:服务器评估。

[Azure Migrate](migrate-services-overview.md)可帮助你迁移到 Azure。 Azure Migrate 提供了一个集中式中心来跟踪将本地基础结构、应用程序和数据迁移到 Azure 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure 工具, 以及第三方独立软件供应商 (ISV) 产品。 

## <a name="grouping-machines"></a>分组计算机

你可以将计算机收集到组中, 以评估它们是否适合迁移到 Azure, 以及如何获取 Azure 规模和成本估算。 有几种方法可以创建组:

- 如果知道需要一起迁移的计算机, 则可以在 Azure Migrate 中手动创建组。
- 如果你不确定需要组合在一起的计算机, 则可以使用 Azure Migrate 中的依赖项可视化功能来创建组。 

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

## <a name="create-a-group-manually"></a>手动创建组

你可以在[创建评估](how-to-create-assessment.md)的同时创建组。

如果要在创建评估之外手动创建组, 请执行以下操作:

1. 在 Azure Migrate 项目 >**概述**中, 单击 "**评估和迁移服务器**"。 在**Azure Migrate 中:服务器评估**, 单击**组**
    - 如果尚未添加 Azure Migrate:服务器评估工具, 单击以添加它。 [了解详细信息](how-to-assess.md)。
    - 如果尚未创建 Azure Migrate 项目, 请[了解详细信息](how-to-add-tool-first-time.md)。

    ![选择组](./media/how-to-create-a-group/select-groups.png)

2. 单击 "**组**" 图标。
3. 在 "**创建组**" 中, 指定组名称, 并在 "**设备名称**" 中选择要用于计算机发现的 Azure Migrate 设备。
1. 从 "计算机" 列表中, 选择要添加到组 >**创建**的计算机。

    ![创建组](./media/how-to-create-a-group/create-group.png)

你现在可以在[创建评估](how-to-create-assessment.md)时使用此组。

## <a name="refine-a-group-with-dependency-mapping"></a>使用依赖关系映射优化组

依赖关系映射有助于实现跨计算机的依赖关系。 当你想要评估具有更高置信度的计算机组时, 通常使用依赖关系映射。
- 在运行评估之前, 它可帮助您交叉检查计算机的依赖关系。 
- 它还有助于有效地计划迁移到 Azure, 方法是确保不会遗留任何内容, 从而避免迁移过程中出现意外中断。
- 你可以发现需要一起迁移的相互依赖的系统, 并确定正在运行的系统是否仍在为用户提供服务, 或者是否是已解除授权而非迁移的候选系统。

如果已[设置依赖关系映射](how-to-create-group-machine-dependencies.md), 并且想要细化现有组, 请执行以下操作:

1. 在 "**服务器**" 选项卡**的 Azure Migrate:服务器评估**磁贴, 单击 "**组**"。
2. 单击要优化的组。
    - 如果尚未设置依赖关系映射, "**依赖项**" 列将显示 "**需要安装**状态"。 对于要为其可视化依赖项的每个 VM, 单击 "**需要安装**"。 请在每个 VM 上安装几个代理, 然后才能映射计算机依赖项。 [了解详细信息](how-to-create-group-machine-dependencies.md)。

        ![添加依赖项映射](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 如果已设置依赖关系映射, 请在 "组" 页上单击 "**查看依赖项**", 打开组依赖项映射。

3. 单击 "**查看依赖关系**" 后, 组依赖关系映射会显示以下内容:

    - 入站 (客户端) 和出站 (服务器) 与组中安装了依赖关系代理的所有计算机之间的 TCP 连接。
    - 未安装依赖关系代理的依赖计算机按端口号进行分组。
    - 安装了依赖关系代理的依赖计算机显示为单独的框。
    - 在计算机内运行的进程。 展开每个计算机框可查看进程。
    - 计算机属性 (包括 FQDN、操作系统、MAC 地址)。 单击每个计算机框可查看详细信息。

4. 若要查看所选时间间隔内的依赖项, 请通过指定开始日期和结束日期或持续时间来修改时间范围 (默认情况下为一个小时)。

    > [!NOTE]
    > 时间范围最长可达一小时。 如果需要更长的范围, 请使用[Azure Monitor 来查询依赖数据](how-to-create-group-machine-dependencies.md)的时间较长。

5. 确定要添加到组或从组中删除的依赖项后, 可以修改组。 使用 Ctrl + 单击来添加或删除组中的计算机。

    - 你只能添加已发现的计算机。
    - 添加和删除计算机会使组的过去评估失效。
    - 修改组时，你可以视情况创建新的评估。


## <a name="next-steps"></a>后续步骤

了解如何设置和使用[依赖关系映射](how-to-create-group-machine-dependencies.md)来创建高可信度组。

