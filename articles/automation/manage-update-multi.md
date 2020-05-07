---
title: 管理多个 Azure 虚拟机的更新
description: 本文介绍如何管理 Azure 和非 Azure 虚拟机的更新。
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 6a878ecf4519a852a9798b320bda26cd490487a4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731962"
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>管理多个 Azure 虚拟机的更新

可以使用 Azure 自动化更新管理来管理 Windows 和 Linux 虚拟机的更新和修补程序。 可以从 [Azure 自动化](automation-offering-get-started.md)帐户执行以下操作：

- 载入虚拟机。
- 评估可用更新的状态。
- 计划安装所需的更新。
- 查看部署结果，验证更新是否已成功应用到已启用更新管理的所有虚拟机。

要了解更新管理的系统要求，请参阅[更新管理客户端要求](automation-update-management.md#client-requirements)。

## <a name="prerequisites"></a>先决条件

* 一个安装了某个受支持的操作系统的虚拟机或计算机。
* 访问适用于 Linux Vm 的更新存储库载入到更新管理。

## <a name="enable-update-management-for-azure-virtual-machines"></a>为 Azure 虚拟机启用“更新管理”

在 Azure 门户中，打开自动化帐户，然后选择“更新管理”****。

选择“添加 Azure VM”****。

![“添加 Azure VM”选项卡](./media/manage-update-multi/update-onboard-vm.png)

选择要上架的虚拟机。

在“启用更新管理”**** 下，选择“启用”**** 以载入虚拟机。

![“启用更新管理”对话框](./media/manage-update-multi/update-enable.png)

载入完成后，便为虚拟机启用了“更新管理”。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>为非 Azure 虚拟机和计算机启用更新管理

需要在运行于企业网络或其他云环境中的 Vm 上安装适用于 Windows 和 Linux 的 Log Analytics 代理，才能使用更新管理启用它们。 若要了解将代理部署到在 Azure 外部托管的计算机的系统要求和支持的方法，请参阅[Log Analytics 代理概述](../azure-monitor/platform/log-analytics-agent.md)。

## <a name="view-computers-attached-to-your-automation-account"></a>查看附加到自动化帐户的计算机

为计算机启用更新管理后，可以通过选择“计算机”来查看计算机信息。**** 可以查看有关计算机的以下信息：计算机名称、符合性状态、环境、OS 类型、已安装的关键和安全更新、已安装的其他更新和更新代理准备。

  ![查看“计算机”选项卡](./media/manage-update-multi/update-computers-tab.png)

最近刚启用“更新管理”的计算机可能尚未进行评估。 这些计算机的符合性状态是`Not assessed`。 下面是符合性状态的可能值的列表：

- `Compliant`：不缺少关键更新或安全更新的计算机。
- `Non-compliant`：至少缺少一项关键更新或安全更新的计算机。
- `Not assessed`：没有在预期时间范围内从计算机接收到更新评估数据。 对于 Linux 计算机，预期的时间范围为最后一个小时。 对于 Windows 计算机，预期的时间范围为最后12个小时。

若要查看代理的状态，请选择 "**更新代理准备情况**" 列中的链接。 选择此选项将打开“混合辅助角色”窗格，并且将显示混合辅助角色的状态。 下图显示了长时间未连接到“更新管理”的代理的示例。

![查看“计算机”选项卡](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>查看更新评估

启用更新管理后，更新管理窗格会打开。 可在“缺少更新”**** 选项卡上查看缺少更新的列表。

## <a name="collect-data"></a>收集数据

安装在虚拟机和计算机上的代理收集有关更新的数据。 代理将该数据发送到 Azure 更新管理。

### <a name="supported-agents"></a>支持的代理

下表介绍了此解决方案支持的连接的源：

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |“更新管理”从 Windows 代理收集有关系统更新的信息，并开始安装必需的更新。 |
| Linux 代理 |是 |“更新管理”从 Linux 代理收集有关系统更新的信息，然后开始在受支持的发行版上安装必需的更新。 |
| Operations Manager 管理组 |是 |“更新管理”从已连接的管理组中的代理收集有关系统更新的信息。 |
| Azure 存储帐户 |否 |Azure 存储不包含有关系统更新的信息。 |

### <a name="collection-frequency"></a>收集频率

计算机完成更新符合性扫描后，代理会将信息批量转发到 Azure Monitor 日志。 在 Windows 计算机上，符合性扫描默认情况下每 12 小时运行一次。

如果 MMA 重启，除了按扫描计划扫描，更新符合性扫描还会在更新安装前和更新安装后的 15 分钟内启动。

对于 Linux 计算机，默认情况下，每小时执行一次符合性扫描。 如果 MMA 代理重启，则会在 15 分钟内启动符合性扫描。

可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

## <a name="schedule-an-update-deployment"></a>计划更新部署

若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。 可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

>[!NOTE]
>计划某个更新部署时，它会创建链接到 **Patch-MicrosoftOMSComputers** runbook 的[计划](shared-resources/schedules.md)资源，以便在目标计算机上处理更新部署。 如果在创建部署后通过 Azure 门户或 PowerShell 删除计划资源，则会破坏计划的更新部署，在你尝试通过门户重新配置它时出现错误。 只能通过删除相应的部署计划来删除计划资源。
>

若要为一台或多台虚拟机计划新的更新部署，请在“更新管理”下选择“计划更新部署”。********

在“新建更新部署”**** 窗格中，指定以下信息：

- **名称**：输入用于标识更新部署的唯一名称。
- **操作系统**：选择 **Windows** 或 **Linux**。
- **要更新的组**：基于订阅、资源组、位置和标记的组合定义查询，以生成要包含在部署中的 Azure vm 动态组。 对于非 Azure Vm，保存的搜索用于创建要包含在你的部署中的动态组。 若要了解详细信息，请参阅[动态组](automation-update-management-groups.md)。
- **要更新的计算机**：选择“已保存的搜索”、“已导入的组”或“计算机”，进而选择要更新的计算机。

   >[!NOTE]
   >选择 "保存的搜索" 选项不会返回计算机标识，只返回其名称。 如果多个资源组中有多个具有相同名称的 Vm，则会在结果中返回它们。 建议使用 "**要更新的组**" 选项，以确保包括与条件相匹配的唯一 vm。

   如果选择**计算机**，则会在 "**更新代理准备情况**" 列中显示计算机的就绪状态。 可以在计划更新部署之前查看计算机的运行状况状态。 若要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅[Azure Monitor 日志中的计算机组](../azure-monitor/platform/computer-groups.md)

  ![“新建更新部署”窗格](./media/manage-update-multi/update-select-computers.png)

- **更新分类**：选择要在更新部署包括的软件的类型。 有关分类类型的说明，请参阅[更新分类](automation-view-update-assessments.md#update-classifications)。 分类类型：
  - 关键更新
  - 安全更新
  - 更新汇总
  - 功能包
  - 服务包
  - 定义更新
  - 工具
  - 更新

- **要包含/排除的更新** - 这会打开“包含/排除”页。 要包含或排除的更新位于单独的选项卡上。 有关如何处理包含的详细信息，请参阅[计划更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)。

> [!NOTE]
> 请务必注意，排除项会替代包含项。 例如，如果定义 `*` 的排除规则，全部排除后将不会安装任何修补程序或包。 已排除的修补程序仍显示为在计算机中缺少。 对于 Linux 计算机，如果包含包且已排除相关包，将不会安装此包。

> [!NOTE]
> 你不能指定已被取代的更新包含在更新部署中。
>

- **计划设置**：可以接受默认的日期和时间，即当前时间后 30 分钟。 也可以指定其他时间。

   还可以指定是一次性部署还是按计划定期部署。 若要设置定期计划，请在“重复周期”下选择“定期”。********

   ![“计划设置”对话框](./media/manage-update-multi/update-set-schedule.png)

- **前脚本 + 后脚本**：选择要在部署前和部署后运行的脚本。 若要了解详细信息，请参阅[管理前脚本和后脚本](pre-post-scripts.md)。
- **维护时段(分钟)**：指定要进行更新部署的时间段。 此设置有助于确保在定义的服务时段内执行更改。

- **重启控制** - 此设置确定如何为更新部署处理重启。

   |选项|说明|
   |---|---|
   |必要时请重启| **（默认）** 必要时且在维护时段允许的情况下开始重启。|
   |永远重启|无论是否需要重启，都会开始重启。 |
   |永不重启|无论是否需要重启，都会禁止重启。|
   |仅重启 - 不安装更新|此选项忽略安装更新，仅开始重启。|

配置完计划后，选择“创建”**** 按钮以返回到状态仪表板。 “已计划”**** 表会显示你创建的部署计划。

> [!NOTE]
> 更新管理支持部署第一方更新和预下载的修补程序。 这需要在修补的系统上进行更改，请参阅[第一方和预下载支持](automation-configure-windows-update.md#pre-download-updates)以了解如何在系统上配置这些设置。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划的部署开始后，可以在“更新管理”  下的“更新部署”  选项卡上查看该部署的状态。

如果部署当前正在运行，则其状态为“正在进行”。**** 部署成功完成以后，其状态更改为“成功”。****

如果部署中有一个或多个更新失败，则状态为“部分失败”****。

![更新部署的状态](./media/manage-update-multi/update-view-results.png)

若要查看更新部署的仪表板，请选择已完成的部署。

“更新结果”窗格会显示虚拟机的更新数和部署结果。 右侧的表格详细列出了每个更新的细目以及安装结果。 安装结果可能是以下值之一：

- `Not attempted`：未安装更新，因为在定义的维护时段中没有足够的时间可用。
- `Succeeded`：更新成功。
- `Failed`：更新失败。

**** 若要查看部署创建的所有日志条目，请选择“所有日志”。

若要查看管理着目标虚拟机更新部署的 runbook 的作业流，请选择“输出”磁贴。

**** 若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="next-steps"></a>后续步骤

若要详细了解更新管理日志、输出和错误，请参阅[更新管理的查询更新记录](automation-update-management-query-logs.md)。
