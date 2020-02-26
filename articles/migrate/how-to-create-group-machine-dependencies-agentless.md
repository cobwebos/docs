---
title: 在 Azure Migrate 中设置无代理依赖项可视化
description: 使用 Azure Migrate 服务器评估中的无代理依赖项可视化设置组。
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589124"
---
# <a name="set-up-agentless-dependency-visualization"></a>设置无代理依赖项可视化 

本文介绍如何在 Azure Migrate：服务器评估中设置依赖项可视化。 [依赖关系可视化](concepts-dependency-visualization.md#what-is-dependency-visualization)可帮助你识别并了解你想要评估和迁移到 Azure 的计算机上的依赖关系。

无代理依赖项可视化可以帮助你识别计算机依赖项，而无需在计算机上安装任何代理。 它的工作原理是从启用了它的计算机捕获 TCP 连接数据。

> [!IMPORTANT]
> 无代理依赖项可视化目前仅适用于 Azure VMware Vm，并通过 Azure Migrate：服务器评估工具发现。
> 功能可能会受到限制或不完整。
> 此预览版涵盖客户支持，可用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="current-limitations"></a>当前限制

- 现在，你无法在 "依赖关系分析" 视图中添加或删除组中的服务器。
- 一组服务器的依赖关系图当前不可用。
- 目前，无法以表格格式下载依赖关系数据。

## <a name="before-you-start"></a>开始之前

- [查看](concepts-dependency-visualization.md#agentless-visualization)与无代理依赖项可视化相关的要求和成本。
- 查看设置无代理依赖项可视化效果的[支持要求](migrate-support-matrix-vmware.md#agentless-dependency-visualization)。
- 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
- 如果已创建项目，请确保已[添加](how-to-assess.md)Azure Migrate： Server 评估工具。
- 请确保已将[Azure Migrate 设备](migrate-appliance.md)设置为发现本地计算机。 了解如何为[VMware](how-to-set-up-appliance-vmware.md) vm 设置设备。 设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。


## <a name="create-a-user-account-for-discovery"></a>创建用于发现的用户帐户

设置用户帐户，以便服务器评估可以访问 VM 以便发现。 您可以指定一个用户帐户。

- **Windows vm**：用户帐户必须是本地管理员或域管理员。
- **Linux vm**：帐户需要 root 权限。 此外，用户帐户需要/bin/netstat 和/bin/ls 文件上的以下两个功能： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。

## <a name="add-the-user-account-to-the-appliance"></a>将用户帐户添加到设备

将用户帐户添加到设备。

1. 打开 "设备管理" 应用。 
2. 导航到 "**提供 vCenter 详细信息**" 面板。
3. 在 "**发现 vm 上的应用程序和依赖项**" 中，单击 "**添加凭据**"
3. 选择**操作系统**，提供帐户的友好名称，**用户名**/**密码**
6. 单击“保存”。
7. 单击 "**保存并启动发现**"。

    ![添加 VM 用户帐户](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>启动依赖项发现

选择要启用依赖关系发现的计算机。

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 单击 "**依赖关系分析**" 图标。
3. 单击 "**添加服务器**"。
3. 在 "**添加服务器**" 页中，选择发现相关计算机的设备。
4. 从 "计算机" 列表中选择计算机。
5. 单击 "**添加服务器**"。

    ![启动依赖项发现](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

启动依赖项发现后，你可以在六个小时内可视化依赖项。

## <a name="visualize-dependencies"></a>可视化依赖项

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 搜索要查看的计算机。
3. 在 "**依赖项**" 列中，单击 "**查看依赖项**"
4. 使用 "**持续时间**" 下拉列表更改要查看其地图的时间段。
5. 展开**客户端**组以列出依赖于所选计算机的计算机。
6. 展开**端口**组以列出与所选计算机具有依赖关系的计算机。
7. 若要导航到任何依赖计算机的映射视图，请单击计算机名称 > "**加载服务器映射**"

    ![展开服务器端口组和加载服务器映射](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![展开客户端组 ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 展开所选计算机，查看每个依赖关系的进程级详细信息。

    ![展开 "服务器" 以显示进程](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 依赖关系的进程信息并非始终可用。 如果不可用，则会用标记为 "未知进程" 的进程来描述依赖关系。

## <a name="stop-dependency-discovery"></a>停止依赖项发现

选择要停止依赖关系发现的计算机。

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 单击 "**依赖关系分析**" 图标。
3. 单击 "**删除服务器**"。
3. 在 "**删除服务器**" 页中，选择要在其上发现要停止依赖关系发现的 vm 的**设备**。
4. 从 "计算机" 列表中选择计算机。
5. 单击 "**删除服务器**"。


## <a name="next-steps"></a>后续步骤

[将计算机分组](how-to-create-a-group.md)以进行评估。
