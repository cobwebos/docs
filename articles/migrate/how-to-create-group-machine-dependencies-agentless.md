---
title: 在 Azure 迁移服务器评估中设置无代理依赖关系分析
description: 在 Azure 迁移服务器评估中设置无代理依赖项分析。
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455700"
---
# <a name="set-up-agentless-dependency-visualization"></a>设置无代理依赖项可视化 

本文介绍如何在 Azure 迁移：服务器评估中设置无代理依赖项分析。 [依赖关系分析](concepts-dependency-visualization.md)可帮助您识别和了解要评估和迁移到 Azure 的计算机之间的依赖项。


> [!IMPORTANT]
> 使用 Azure 迁移：服务器评估工具发现的无代理依赖项可视化当前仅针对 VMware VM 处于预览状态。
> 功能可能有限或不完整。
> 此预览版由客户支持提供，可用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



## <a name="before-you-start"></a>开始之前

- [了解](concepts-dependency-visualization.md#agentless-analysis)无代理依赖项分析。
- [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)为 VMware VM 设置无代理依赖项可视化的先决条件和支持要求
- 请确保[已创建](how-to-add-tool-first-time.md)Azure 迁移项目。
- 如果已创建项目，请确保[已添加](how-to-assess.md)Azure 迁移：服务器评估工具。
- 请确保已设置[Azure 迁移设备](migrate-appliance.md)以发现本地计算机。 了解如何为[VMware](how-to-set-up-appliance-vmware.md) VM 设置设备。 设备发现本地计算机，并将元数据和性能数据发送到 Azure 迁移：服务器评估。


## <a name="current-limitations"></a>当前限制

- 现在，您无法在依赖项分析视图中从组中添加或删除服务器。
- 一组服务器的依赖项映射当前不可用。
- 目前，依赖项数据无法以表格格式下载。

## <a name="create-a-user-account-for-discovery"></a>创建用于发现的用户帐户

设置用户帐户，以便服务器评估可以访问 VM 进行发现。 [了解](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)客户要求。


## <a name="add-the-user-account-to-the-appliance"></a>将用户帐户添加到设备

将用户帐户添加到设备。

1. 打开设备管理应用。 
2. 导航到 **"提供 vCenter 详细信息**"面板。
3. 在 **"发现 VM 上的应用程序和依赖项**"中，单击"**添加凭据**"
3. 选择**操作系统**，为帐户提供友好名称和**用户名**/**密码**
6. 单击“保存”。****
7. 单击 **"保存"并开始发现**。

    ![添加 VM 用户帐户](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>启动依赖项发现

选择要在其上启用依赖项发现的计算机。

1. 在**Azure 迁移：服务器评估中**，单击 **"发现服务器**"。
2. 单击**依赖项分析**图标。
3. 单击"**添加服务器**"。
3. 在 **"添加服务器"** 页中，选择发现相关计算机的设备。
4. 从机器列表中，选择机器。
5. 单击"**添加服务器**"。

    ![启动依赖项发现](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

您可以在开始依赖项发现后大约六小时可视化依赖项。

## <a name="visualize-dependencies"></a>可视化依赖项

1. 在**Azure 迁移：服务器评估中**，单击 **"发现服务器**"。
2. 搜索要查看的计算机。
3. 在 **"依赖项"** 列中，单击"**查看依赖项"**
4. 使用 **"时间持续时间**"下拉下下拉列表更改要查看地图的时间段。
5. 展开**客户端**组以列出依赖于所选计算机的计算机。
6. 展开 **"端口"** 组以列出具有所选计算机依赖项的计算机。
7. 要导航到任何从属计算机的地图视图，请单击计算机名称>**加载服务器映射**

    ![展开服务器端口组和加载服务器映射](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![展开客户端组 ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 展开所选计算机以查看每个依赖项的进程级详细信息。

    ![展开服务器以显示进程](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 依赖项的过程信息并不总是可用的。 如果不可用，则依赖项将用标记为"未知进程"的进程进行描述。

## <a name="stop-dependency-discovery"></a>停止依赖项发现

选择要停止依赖项发现的计算机。

1. 在**Azure 迁移：服务器评估中**，单击 **"发现服务器**"。
2. 单击**依赖项分析**图标。
3. 单击 **"删除服务器**"。
3. 在 **"删除服务器"** 页中，选择正在发现要停止依赖项发现的 VM**的设备**。
4. 从机器列表中，选择机器。
5. 单击 **"删除服务器**"。


## <a name="next-steps"></a>后续步骤

[对机器进行分组](how-to-create-a-group.md)以进行评估。
