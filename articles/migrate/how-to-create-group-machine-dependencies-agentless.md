---
title: 使用无代理依赖项可视化将计算机分组 Azure Migrate
description: 描述如何使用计算机依赖项以无代理方式创建组。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: b13ad375adf20a606b48755a6d57851abf89e549
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454659"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>设置无代理依赖项可视化以进行评估

本文介绍如何在 Azure Migrate：服务器评估中设置无代理依赖项映射。 此功能目前以预览版提供，可使用 Azure Migrate 设备发现的 VMware 计算机。 

> [!IMPORTANT]
> 对于使用 Azure Migrate 设备发现的 Azure VMware Vm，无代理依赖项可视化目前处于预览阶段。
> 某些功能可能不受支持或者受限。 此预览版涵盖客户支持，可用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="about-dependency-mapping"></a>关于依赖项映射

依赖关系映射有助于可视化要评估和迁移的计算机之间的依赖关系。 当您想要评估具有更高置信度的计算机时，通常使用依赖关系映射。

- 在 Azure Migrate：服务器评估中，将计算机组合到一起进行评估。 组通常由要一起迁移的计算机组成，依赖关系映射有助于交叉检查计算机依赖项，以便可以精确地对计算机进行分组。
- 使用映射，可以发现需要一起迁移的相互依赖的系统。 你还可以确定正在运行的系统是否仍在为用户提供服务，或者是否要解除授权而非迁移。
- 可视化依赖项有助于确保不会遗留任何内容，并避免在迁移过程中出现意外中断。

## <a name="about-agentless-visualization"></a>关于无代理可视化

无代理依赖项可视化不需要在计算机上安装任何代理。 它的工作原理是从启用了它的计算机捕获 TCP 连接数据。

- 启动依赖项发现后，设备会按5分钟的轮询间隔从计算机收集数据。
- 收集以下数据：
    - TCP 连接
    - 具有活动连接的进程的名称
    - 运行上述进程的已安装应用程序的名称
    - 不。 每个轮询间隔检测到的连接

## <a name="current-limitations"></a>当前限制

- 无代理依赖项可视化目前仅适用于 VMware Vm。
- 现在，你无法在 "依赖关系分析" 视图中添加或删除组中的服务器。
- 一组服务器的依赖关系图当前不可用。
- 目前，无法以表格格式下载依赖关系数据。

## <a name="before-you-start"></a>开始之前

- 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
- 无代理依赖项分析目前仅适用于 VMware 计算机。
- 如果已创建项目，请确保已[添加](how-to-assess.md)Azure Migrate： Server 评估工具。
- 请确保已在 Azure Migrate 中发现 VMware 计算机;为此，可以设置适用于[VMware](how-to-set-up-appliance-vmware.md)的 Azure Migrate 设备。 设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。 [了解详细信息](migrate-appliance.md)。
- 请确保 VMware Vm 支持无代理依赖项可视化，如下表中所示。


### <a name="supported-operating-systems"></a>支持的操作系统

无代理依赖项可视化支持的操作系统如下所示。

类型 | **受支持的操作系统**
--- | ---
**Windows** | Microsoft Windows Server 2016 <br/> Microsoft Windows Server 2012 R2 <br/> Microsoft Windows Server 2012 <br/> Microsoft Windows Server 2008 R2 （64）
**Linux** | Red Hat Enterprise Linux 7、6、5 <br/> Ubuntu Linux 14.04、16.04 <br/> Debian 7、8 <br/> Oracle Linux 6、7 <br/> CentOS 5、6、7  


## <a name="create-a-user-account-for-discovery"></a>创建用于发现的用户帐户

设置具有所需权限的用户帐户，以便服务器评估可以访问 VM 以便发现。 您可以指定一个用户帐户。

- **对 Windows vm 的必需权限**：用户帐户需要 "来宾" 访问权限。
- **Linux vm 上的所需权限**：帐户需要 root 权限。 此外，用户帐户需要/bin/netstat 和/bin/ls 文件上的以下两个功能： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。

## <a name="add-the-user-account-to-the-appliance"></a>将用户帐户添加到设备

需要将用户帐户添加到设备。

按如下所示添加帐户：

1. 打开 "设备管理" 应用。 导航到 "**提供 vCenter 详细信息**" 面板。
2. 在 "**发现应用程序和 vm 依赖关系**" 部分中，单击 "**添加凭据**"
3. 选择**操作系统**。
4. 提供帐户的友好名称。
5. 提供**用户名**和**密码**
6. 单击“ **保存**”。
7. 单击 "**保存并启动发现**"。

    ![添加 VM 用户帐户](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>启动依赖项发现

选择要启用依赖关系发现的计算机。

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 单击 "**依赖关系分析**" 图标。
3. 单击 "**启动依赖项发现**"。
3. 在 "**启动依赖项发现**" 页中，选择发现相关计算机的设备。
4. 从 "计算机" 列表中选择计算机。
5. 单击 "**启动依赖项发现**"。

    ![启动依赖项发现](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

启动依赖项发现后，你将能够可视化依赖项6小时。

## <a name="visualize-dependencies"></a>可视化依赖项

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 搜索要查看其依赖关系映射的计算机。
3. 单击 "**依赖关系**" 列中的 "**查看依赖关系**"。
4. 使用 "**持续时间**" 下拉列表更改要查看其地图的时间段。
5. 展开**客户端**组以列出依赖于所选计算机的计算机。
6. 展开**端口**组以列出与所选计算机具有依赖关系的计算机。
7. 若要导航到任何依赖计算机的映射视图，请单击计算机名称，然后单击 "**加载服务器映射**"。

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
3. 单击 "**停止依赖项发现**"。
3. 在 "**停止依赖关系发现**" 页中，选择要在其上发现要停止依赖关系发现的 vm 的**设备**。
4. 从 "计算机" 列表中选择计算机。
5. 单击 "**停止依赖项发现**"


## <a name="next-steps"></a>后续步骤

[将计算机分组](how-to-create-a-group.md)
