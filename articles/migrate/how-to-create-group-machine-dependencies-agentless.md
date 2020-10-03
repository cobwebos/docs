---
title: 在 Azure Migrate 服务器评估中设置无代理依赖项分析
description: 在 Azure Migrate 服务器评估中设置无代理依赖项分析。
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 57e5add810cf4fac232bce08fc7ca96df0a7c3a0
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667463"
---
# <a name="analyze-machine-dependencies-agentless"></a>分析计算机依赖项（无代理）

本文介绍如何在 Azure Migrate：服务器评估中设置无代理依赖项分析。 [依赖关系分析](concepts-dependency-visualization.md) 有助于识别和了解计算机之间的依赖关系，以便进行评估和迁移到 Azure。


> [!IMPORTANT]
> 无代理依赖项可视化目前处于预览阶段，可通过 Azure Migrate：服务器评估工具发现的 VMware Vm。
> 功能可能会受到限制或不完整。
> 此预览版涵盖客户支持，可用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="current-limitations"></a>当前限制

- 在 "依赖关系分析" 视图中，当前无法在组中添加或删除服务器。
- 一组服务器的依赖关系图当前不可用。
- 可以同时为1000服务器设置依赖关系数据收集。 您可以通过对多个1000的批次进行排序来分析更多的服务器。

## <a name="before-you-start"></a>开始之前

- [查看](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 支持的操作系统和所需的权限。
- 确保：
    - 具有 Azure Migrate 项目。 如果没有，请立即 [创建](how-to-add-tool-first-time.md) 一个。
    - 检查是否已将 Azure Migrate： Server 评估工具 [添加](how-to-assess.md) 到项目。
    - 设置 [Azure Migrate 设备](migrate-appliance.md) ，以发现本地计算机。 为 VMware Vm[设置设备](how-to-set-up-appliance-vmware.md)。 设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。
- 检查是否在要分析的每个 VM 上安装了 VMware Tools (10.2) 。


## <a name="create-a-user-account-for-discovery"></a>创建用于发现的用户帐户

设置用户帐户，以便服务器评估可以访问 VM 来发现依赖关系。 [了解](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Windows 和 Linux vm 的帐户要求。


## <a name="add-the-user-account-to-the-appliance"></a>将用户帐户添加到设备

将用户帐户添加到设备。

1. 打开 "设备管理" 应用。 
2. 导航到 " **提供 vCenter 详细信息** " 面板。
3. 在 "**发现 vm 上的应用程序和依赖项**" 中，单击 "**添加凭据**"
3. 选择**操作系统**，提供帐户的友好名称和**用户名** / **密码**
6. 单击“ **保存**”。
7. 单击 " **保存并启动发现**"。

    ![添加 VM 用户帐户](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>启动依赖项发现

选择要启用依赖关系发现的计算机。 

1. 在 **Azure Migrate：服务器评估**中，单击 " **发现的服务器**"。
2. 单击 " **依赖关系分析** " 图标。
3. 单击 " **添加服务器**"。
4. 在 " **添加服务器** " 页中，选择发现相关计算机的设备。
5. 从 "计算机" 列表中选择计算机。
6. 单击 " **添加服务器**"。

    ![启动依赖项发现](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

启动依赖项发现后，你可以在六个小时内可视化依赖项。 如果要启用多台计算机，则可以使用 [PowerShell](#start-or-stop-dependency-discovery-using-powershell) 执行此操作。

## <a name="visualize-dependencies"></a>可视化依赖项

1. 在 **Azure Migrate：服务器评估**中，单击 " **发现的服务器**"。
2. 搜索要查看的计算机。
3. 在 "**依赖项**" 列中，单击 "**查看依赖项**"
4. 使用 " **持续时间** " 下拉列表更改要查看其地图的时间段。
5. 展开 **客户端** 组以列出依赖于所选计算机的计算机。
6. 展开 **端口** 组以列出与所选计算机具有依赖关系的计算机。
7. 若要导航到任何依赖计算机的映射视图，请单击计算机名称 > "**加载服务器映射**"

    ![展开服务器端口组和加载服务器映射](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![展开客户端组 ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 展开所选计算机，查看每个依赖关系的进程级详细信息。

    ![展开 "服务器" 以显示进程](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 依赖关系的进程信息并非始终可用。 如果不可用，则会用标记为 "未知进程" 的进程来描述依赖关系。

## <a name="export-dependency-data"></a>导出依赖关系数据

1. 在 **Azure Migrate：服务器评估**中，单击 " **发现的服务器**"。
2. 单击 " **依赖关系分析** " 图标。
3. 单击 " **导出应用程序依赖项**"。
4. 在 " **导出应用程序依赖关系** " 页上，选择发现相关计算机的设备。
5. 选择开始时间和结束时间。 请注意，只能下载过去30天内的数据。
6. 单击 " **导出依赖项**"。

依赖关系数据以 CSV 格式导出和下载。 下载的文件包含所有启用了依赖关系分析的计算机之间的依赖关系数据。 

![导出依赖项](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>依赖关系信息

导出的 CSV 中的每一行对应于在指定时间段内观察到的依赖项。 

下表汇总了导出的 CSV 中的字段。 请注意，仅为启用了无代理依赖关系分析的服务器填充服务器名称、应用程序和进程字段。

**字段名称** | **详细信息**
--- | --- 
Timeslot | 观察依赖项的 timeslot。 <br/> 在当前6小时的槽上捕获依赖关系数据。
源服务器名称 | 源计算机的名称 
源应用程序 | 源计算机上的应用程序名称 
源进程 | 源计算机上进程的名称 
目标服务器名称 | 目标计算机的名称
目标 IP | 目标计算机的 IP 地址
目标应用程序 | 目标计算机上的应用程序名称
目标进程 | 目标计算机上进程的名称 
Destination Port | 目标计算机上的端口号


## <a name="stop-dependency-discovery"></a>停止依赖项发现

选择要停止依赖关系发现的计算机。 

1. 在 **Azure Migrate：服务器评估**中，单击 " **发现的服务器**"。
2. 单击 " **依赖关系分析** " 图标。
3. 单击 " **删除服务器**"。
3. 在 " **删除服务器** " 页中，选择要在其上发现要停止依赖关系发现的 vm 的 **设备** 。
4. 从 "计算机" 列表中选择计算机。
5. 单击 " **删除服务器**"。

如果要停止依赖于多台计算机，则可以使用 [PowerShell](#start-or-stop-dependency-discovery-using-powershell) 执行此操作。


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>使用 PowerShell 启动或停止依赖项发现

从 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 存储库下载 PowerShell 模块。


### <a name="log-in-to-azure"></a>登录 Azure

1. 使用 AzAccount cmdlet 登录到 Azure 订阅。

    ```PowerShell
    Connect-AzAccount
    ```
    如果使用 Azure 政府版，请使用以下命令。
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. 选择已在其中创建 Azure Migrate 项目的订阅 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 导入下载的 AzMig_Dependencies PowerShell 模块

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>启用或禁用依赖项数据收集

1. 使用以下命令获取 Azure Migrate 项目中发现的 VMware Vm 列表。 在下面的示例中，项目名称为 FabrikamDemoProject，其所属的资源组为 FabrikamDemoRG。 计算机列表将保存在 FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    在该文件中，可以看到 VM 显示名称、依赖关系集合的当前状态和所有已发现 Vm 的 ARM ID。 

2. 若要启用或禁用依赖关系，请创建一个输入 CSV 文件。 文件需要具有标头为 "ARM ID" 的列。 CSV 文件中的任何其他标头都将被忽略。 你可以使用上一步中生成的文件创建 CSV。 创建文件的副本，以保留要在其上启用或禁用依赖项的 Vm。 

    在下面的示例中，将在输入文件 FabrikamDemo_VMs_Enable.csv 的 Vm 列表中启用依赖项分析。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    在下面的示例中，将在输入文件 FabrikamDemo_VMs_Disable.csv 的 Vm 列表上禁用依赖项分析。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>可视化 Power BI 中的网络连接

Azure Migrate 提供了一个 Power BI 模板，你可以使用它来一次可视化多个服务器的网络连接，并按进程和服务器进行筛选。 若要可视化，请按照以下说明加载包含依赖数据的 Power BI。

1. 从 GitHub 上 [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 存储库下载 PowerShell 模块和 Power BI 模板。

2. 使用以下说明登录到 Azure： 
- 使用 AzAccount cmdlet 登录到 Azure 订阅。

    ```PowerShell
    Connect-AzAccount
    ```

- 如果使用 Azure 政府版，请使用以下命令。

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- 选择已在其中创建 Azure Migrate 项目的订阅 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 导入下载的 AzMig_Dependencies PowerShell 模块

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 运行以下命令。 此命令将下载 CSV 中的依赖关系数据，并处理它以生成可用于 Power BI 中的可视化的独特依赖项的列表。 在下面的示例中，项目名称为 FabrikamDemoProject，其所属的资源组为 FabrikamDemoRG。 系统会为 FabrikamAppliance 发现的计算机下载依赖项。 唯一的依赖项将保存在 FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. 打开下载的 Power BI 模板

6. 将下载的依赖项数据加载到 Power BI 中。
    - 在 Power BI 中打开模板。
    - 单击工具栏上的 " **获取数据** "。 
    - 从公共数据源选择 **文本/CSV** 。
    - 选择已下载的依赖项文件。
    - 单击“加载”。
    - 将看到一个表，其中包含 CSV 文件的名称。 可以在右侧的 "字段" 栏中查看表。 将其重命名为 AzMig_Dependencies
    - 在工具栏中单击 "刷新"。

    "网络连接" 图表和 "源服务器名称"、"目标服务器名称"、"源进程名称"、"目标进程名称" 切片器应与导入的数据一起亮起。

7. 直观显示服务器和进程筛选的网络连接的地图。 保存文件。


## <a name="next-steps"></a>后续步骤

[分组计算机](how-to-create-a-group.md) 以进行评估。
