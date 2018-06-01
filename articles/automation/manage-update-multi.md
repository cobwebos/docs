---
title: 管理多个 Azure 虚拟机的更新
description: 本主题介绍如何管理 Azure 虚拟机的更新。
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1f34255bdbcc8761f1c68adbb2f1828521f789e4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194013"
---
# <a name="manage-updates-for-multiple-machines"></a>管理多个计算机的更新

可以使用更新管理来管理 Windows 和 Linux 虚拟机的更新和修补程序。 可以从 [Azure 自动化](automation-offering-get-started.md)帐户执行以下操作：

- 载入虚拟机。
- 评估可用更新的状态。
- 计划安装所需的更新。
- 查看部署结果，验证是否已成功将更新应用到所有启用了更新管理的虚拟机。

## <a name="prerequisites"></a>先决条件

若要使用更新管理，需要具备以下条件：

- Azure 自动化运行方式帐户。 有关如何创建帐户的说明，请参阅 [Azure 自动化入门](automation-offering-get-started.md)。

- 一个安装了某个受支持的操作系统的虚拟机或计算机。

## <a name="supported-operating-systems"></a>支持的操作系统

以下操作系统支持更新管理。

### <a name="windows"></a>Windows

- Windows Server 2008 及更高版本，以及针对 Windows Server 2008 R2 SP1 及更高版本的更新部署。 不支持 Nano Server。

  若要提供相关支持，以便将更新部署到 Windows Server 2008 R2 SP1，需要 .NET Framework 4.5 和 Windows Management Framework 5.0 或更高版本。

- 不支持 Windows 客户端操作系统。

Windows 代理也必须配置为与 Windows Server Update Services (WSUS) 服务器通信或有权访问 Microsoft 更新。

> [!NOTE]
> System Center Configuration Manager 无法同时管理 Windows 代理。
>

### <a name="linux"></a>Linux

- CentOS 6 (x86/x64) 和 7 (x64)

- Red Hat Enterprise 6 (x86/x64) 和 7 (x64)

- SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)

- Ubuntu 12.04 LTS 和更高版本 (x86/x64)

> [!NOTE]
> 若要防止在 Ubuntu 上的维护时段外应用更新，请重新配置无人参与升级包，禁用自动更新。 有关详细信息，请参阅[“Ubuntu 服务器指南”中的“自动更新”主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

Linux 代理必须具有访问更新存储库的权限。

此解决方案不支持配置为向多个 Log Analytics 工作区进行报告的适用于 Linux 的 OMS 代理。

## <a name="enable-update-management-for-azure-virtual-machines"></a>启用 Azure 虚拟机的更新管理

在 Azure 门户中，打开自动化帐户，然后选择“更新管理”。

在窗口顶部，选择“添加 Azure VM”。

![“添加 Azure VM”选项卡](./media/manage-update-multi/update-onboard-vm.png)

选择要上架的虚拟机。 此时会显示“启用更新管理”对话框。 选择“启用”以载入虚拟机。 载入完成后，为虚拟机启用了更新管理。

![“启用更新管理”对话框](./media/manage-update-multi/update-enable.png)

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>启用非 Azure 虚拟机和计算机的更新管理

如需如何为非 Azure Windows 虚拟机和计算机启用更新管理的说明，请参阅[将 Windows 计算机连接到 Azure 中的 Log Analytics 服务](../log-analytics/log-analytics-windows-agent.md)。

如需如何为非 Azure Linux 虚拟机和计算机启用更新管理的说明，请参阅[将 Linux 计算机连接到 Log Analytics](../log-analytics/log-analytics-agent-linux.md)。

## <a name="view-computers-attached-to-your-automation-account"></a>查看附加到自动化帐户的计算机

启用计算机的更新管理以后，单击“计算机”即可查看其信息。 提供“名称”、“符合性”、“环境”、“OS 类型”、“关键更新和安全更新”、“其他更新”和“更新代理准备”等计算机信息。

  ![查看“计算机”选项卡](./media/manage-update-multi/update-computers-tab.png)

最近启用了更新管理的计算机可能尚未进行评估。 这些计算机的符合性状态将会是“未评估”。  下面是一个列表，其中包含符合性状态的值：

- 符合 - 计算机不缺少关键更新或安全更新。

- 不符合 - 计算机缺少至少一项关键更新或安全更新。

- 未评估 - 未在预期的时间范围内收到计算机的更新评估数据。  对于 Linux 计算机，这是指过去 3 小时；对于 Windows 计算机，这是指过去 12 小时。

要查看代理的状态，请单击“更新代理准备”列中的链接。 这会打开“混合辅助角色”页面，其中显示了混合辅助角色的状态。 下图显示了长时间未连接到更新管理的代理的示例。

![查看“计算机”选项卡](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>查看更新评估

启用更新管理后，“更新管理”对话框随即显示。 可在“缺少更新”选项卡上查看缺少更新的列表。

## <a name="collect-data"></a>收集数据

安装在虚拟机和计算机上的代理收集有关更新的数据，并将其发送到 Azure 更新管理。

### <a name="supported-agents"></a>支持的代理

下表介绍了此解决方案支持的连接的源：

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |更新管理从 Windows 代理收集有关系统更新的信息，并启动所需更新的安装。 |
| Linux 代理 |是 |更新管理从 Linux 代理收集有关系统更新的信息，并在支持的发行版上启动所需更新的安装。 |
| Operations Manager 管理组 |是 |更新管理从已连接的管理组中的代理收集有关系统更新的信息。 |
| Azure 存储帐户 |否 |Azure 存储不包含有关系统更新的信息。 |

### <a name="collection-frequency"></a>收集频率

对于每台托管的 Windows 计算机，每天运行两次扫描。 每隔 15 分钟就会调用一次 Windows API，以便查询上次更新时间，确定状态是否已更改。 在状态已更改的情况下启动符合性扫描。 对于每台托管的 Linux 计算机，每 3 小时运行一次扫描。

可能需要 30 分钟到 6 小时的时间，仪表板才会显示受托管计算机提供的已更新数据。

## <a name="schedule-an-update-deployment"></a>计划更新部署

若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。
可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

选择“更新管理”对话框顶部的“计划更新部署”，计划用于一个或多个虚拟机的新的更新部署。
在“新建更新部署”窗格中，指定以下信息：

- **名称**：提供用于标识更新部署的唯一名称。
- **OS 类型**：选择“Windows”或“Linux”。
- **要更新的计算机**：选择要更新的虚拟机。 计算机的就绪情况显示在**更新代理准备**列中。 这样可以在计划更新部署之前查看计算机的运行状况状态。

  ![“新建更新部署”窗格](./media/manage-update-multi/update-select-computers.png)

- **更新分类**：选择更新部署包含的软件的类型。 有关分类类型的说明，请参阅[更新分类](automation-update-management.md#update-classifications)。 分类类型：
  - 关键更新
  - 安全更新
  - 更新汇总
  - 功能包
  - 服务包
  - 定义更新
  - 工具
  - 更新
- **计划设置**：可以接受默认的日期和时间，即当前时间后 30 分钟。 也可以指定其他时间。
   还可以指定是一次性部署还是按计划定期部署。 若要设置定期计划，请选择“重复周期”下的“重复执行”选项。

   ![“计划设置”对话框](./media/manage-update-multi/update-set-schedule.png)

- **维护时段(分钟)**：指定要进行更新部署的时间段。 此设置有助于确保在定义的服务时段内执行更改。

配置完计划后，请选择“创建”按钮，返回到状态仪表板。 “已计划”表显示刚创建的部署计划。

> [!WARNING]
> 对于需要重启的更新，虚拟机会自动重启。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划的部署开始后，可以在“更新管理”对话框的“更新部署”选项卡上查看该部署的状态。
如果部署当前正在运行，则其状态为“正在进行”。 部署成功完成以后，其状态更改为“成功”。
如果部署中有一个或多个更新失败，则状态为“部分失败”。

![更新部署的状态](./media/manage-update-multi/update-view-results.png)

若要查看更新部署的仪表板，请选择已完成的部署。

“更新结果”窗格显示虚拟机上更新和部署结果的总数。
右侧的表格详细列出了每个更新的细目以及安装结果。 安装结果可能是以下值之一：

- 未尝试：由于定义的维护时段时长不足，因而未安装更新。
- 成功：更新成功。
- 失败：更新失败。

若要查看部署创建的所有日志条目，请选择“所有日志”。

若要查看管理目标虚拟机更新部署的 runbook 的作业流，请选择“输出”磁贴。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="next-steps"></a>后续步骤

- 若要详细了解更新管理（包括日志、输出和错误），请参阅 [Azure 中的更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)。
