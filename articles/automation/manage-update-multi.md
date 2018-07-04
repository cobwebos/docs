---
title: 管理多个 Azure 虚拟机的更新
description: 本文介绍了如何管理 Azure 虚拟机的更新。
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59a00f5605f7664148b65f2ec9a88fbaa9057ccf
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946051"
---
# <a name="manage-updates-for-multiple-machines"></a>管理多个计算机的更新

可以使用“更新管理”解决方案来管理 Windows 和 Linux 虚拟机的更新和修补程序。 可以从 [Azure 自动化](automation-offering-get-started.md)帐户执行以下操作：

- 载入虚拟机
- 评估可用更新的状态
- 计划安装所需的更新
- 查看部署结果，验证是否已成功将更新应用到所有启用了“更新管理”的虚拟机

## <a name="prerequisites"></a>先决条件

若要使用“更新管理”，需要具备以下条件：

- Azure 自动化运行方式帐户。 若要了解如何创建帐户，请参阅 [Azure 自动化入门](automation-offering-get-started.md)。
- 一个安装了某个受支持的操作系统的虚拟机或计算机。

## <a name="supported-operating-systems"></a>支持的操作系统

以下操作系统支持“更新管理”：

|操作系统  |说明  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 仅支持更新评估。         |
|Windows Server 2008 R2 SP1 和更高版本     |需要 Windows PowerShell 4.0 或更高版本。 （[下载 WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)）</br> 为提高可靠性，建议使用 Windows PowerShell 5.1。 （[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）         |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。        |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 12.04 LTS、14.04 LTS 和 16.04 LTS (x86/x64)      |Linux 代理必须具有访问更新存储库的权限。         |

> [!NOTE]
> 若要防止在 Ubuntu 上的维护时段外应用更新，请重新配置无人参与升级包，禁用自动更新。 有关详细信息，请参阅[“Ubuntu 服务器指南”中的“自动更新”主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

Linux 代理必须具有访问更新存储库的权限。

此解决方案不支持适用于 Linux 且配置为向多个 Azure Log Analytics 工作区报告的 Operations Management Suite (OMS) 代理。

## <a name="enable-update-management-for-azure-virtual-machines"></a>为 Azure 虚拟机启用“更新管理”

在 Azure 门户中，打开自动化帐户，然后选择“更新管理”。

选择“添加 Azure VM”。

![“添加 Azure VM”选项卡](./media/manage-update-multi/update-onboard-vm.png)

选择要上架的虚拟机。 

在“启用更新管理”下，选择“启用”以载入虚拟机。

![“启用更新管理”对话框](./media/manage-update-multi/update-enable.png)

载入完成后，便为虚拟机启用了“更新管理”。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>为非 Azure 虚拟机和计算机启用更新管理

若要了解如何为非 Azure Windows 虚拟机和计算机启用“更新管理”，请参阅[将 Windows 计算机连接到 Azure 中的 Log Analytics 服务](../log-analytics/log-analytics-windows-agent.md)。

若要了解如何为非 Azure Linux 虚拟机和计算机启用“更新管理”，请参阅[将 Linux 计算机连接到 Log Analytics](../log-analytics/log-analytics-agent-linux.md)。

## <a name="view-computers-attached-to-your-automation-account"></a>查看附加到自动化帐户的计算机

为计算机启用更新管理后，可以通过选择“计算机”来查看计算机信息。 可以查看有关计算机的以下信息：*计算机名称*、*符合性状态*、*环境*、*OS 类型*、*已安装的关键和安全更新*、*已安装的其他更新*和*更新代理准备*。

  ![查看“计算机”选项卡](./media/manage-update-multi/update-computers-tab.png)

最近刚启用“更新管理”的计算机可能尚未进行评估。 这些计算机的符合性状态是“未评估”。 下面是符合性状态的可能值的列表：

- **符合**：计算机不缺少关键更新或安全更新。

- **不符合** - 计算机缺少至少一项关键更新或安全更新。

- **未评估** - 未在预期的时间范围内收到计算机的更新评估数据。 对于 Linux 计算机，预期的时间范围是过去 3 小时内。 对于 Windows 计算机，预期的时间范围是过去 12 小时内。

若要查看代理的状态，请选择“更新代理准备”列中的链接。 选择此选项将打开“混合辅助角色”窗格，并且将显示混合辅助角色的状态。 下图显示了长时间未连接到“更新管理”的代理的示例。

![查看“计算机”选项卡](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”窗格随即打开。 可在“缺少更新”选项卡上查看缺少更新的列表。

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

对于每台托管的 Windows 计算机，每天运行两次扫描。 每隔 15 分钟就会调用一次 Windows API 来查询上次更新时间，以确定状态是否已更改。 如果状态已更改，则会启动符合性扫描。 对于每台托管的 Linux 计算机，每 3 小时运行一次扫描。

可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

## <a name="schedule-an-update-deployment"></a>计划更新部署

若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。 可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

若要为一台或多台虚拟机计划新的更新部署，请在“更新管理”下选择“计划更新部署”。

在“新建更新部署”窗格中，指定以下信息：

- **名称**：输入用于标识更新部署的唯一名称。
- **操作系统**：选择 **Windows** 或 **Linux**。
- **要更新的计算机**：选择要更新的虚拟机。 计算机的就绪情况显示在**更新代理准备**列中。 可以在计划更新部署之前查看计算机的运行状况状态。

  ![“新建更新部署”窗格](./media/manage-update-multi/update-select-computers.png)

- **更新分类**：选择要在更新部署包括的软件的类型。 有关分类类型的说明，请参阅[更新分类](automation-update-management.md#update-classifications)。 分类类型：
  - 关键更新
  - 安全更新
  - 更新汇总
  - 功能包
  - 服务包
  - 定义更新
  - 工具
  - 更新

- **要排除的更新** - 选择此选项会打开“排除”页面。 输入要排除的知识库文章或包名称。

- **计划设置**：可以接受默认的日期和时间，即当前时间后 30 分钟。 也可以指定其他时间。

   还可以指定是一次性部署还是按计划定期部署。 若要设置定期计划，请在“重复周期”下选择“定期”。

   ![“计划设置”对话框](./media/manage-update-multi/update-set-schedule.png)
- **维护时段(分钟)**：指定要进行更新部署的时间段。 此设置有助于确保在定义的服务时段内执行更改。

配置完计划后，选择“创建”按钮以返回到状态仪表板。 “已计划”表会显示你创建的部署计划。

> [!WARNING]
> 对于需要重启的更新，虚拟机会自动重启。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划的部署开始后，可以在“更新管理”下的“更新部署”选项卡上查看该部署的状态。

如果部署当前正在运行，则其状态为“正在进行”。 部署成功完成以后，其状态更改为“成功”。

如果部署中有一个或多个更新失败，则状态为“部分失败”。

![更新部署的状态](./media/manage-update-multi/update-view-results.png)

若要查看更新部署的仪表板，请选择已完成的部署。

“更新结果”窗格会显示虚拟机的更新数和部署结果。 右侧的表格详细列出了每个更新的细目以及安装结果。 安装结果可能是以下值之一：

- **未尝试**：由于定义的维护时段时长不足，因而未安装更新。
- **成功**：更新成功。
- **失败**：更新失败。

若要查看部署创建的所有日志条目，请选择“所有日志”。

若要查看管理着目标虚拟机更新部署的 runbook 的作业流，请选择“输出”磁贴。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="next-steps"></a>后续步骤

- 若要详细了解“更新管理”（包括日志、输出和错误），请参阅 [Azure 中的更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)。
