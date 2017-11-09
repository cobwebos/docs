---
title: "管理多个 Azure 虚拟机的更新 | Microsoft Docs"
description: "本主题介绍如何管理 Azure 虚拟机的更新。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: 80a6caff51631637825d560d270198be0336e806
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>管理多个 Azure 虚拟机的更新

使用更新管理可以管理 Azure 虚拟机的更新和修补程序。
在 [Azure 自动化](automation-offering-get-started.md)帐户中，可以快速上架虚拟机、评估可用更新的状态、计划所需更新的安装以及查看部署结果，以验证更新是否已成功地应用到启用了更新管理的所有虚拟机。

## <a name="prerequisites"></a>先决条件

若要使用更新管理，需要具备以下条件：

* 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 自动化入门](automation-offering-get-started.md)。

* 一个安装了某个受支持的操作系统的虚拟机或计算机。

## <a name="supported-operating-systems"></a>支持的操作系统

以下操作系统支持更新管理。

### <a name="windows"></a>Windows

* Windows Server 2008 及更高版本，以及针对 Windows Server 2008 R2 SP1 及更高版本的更新部署。  不支持服务器核心和 Nano Server 安装选项。

    > [!NOTE]
    > 若要提供相关支持，以便将更新部署到 Windows Server 2008 R2 SP1，需要 .NET Framework 4.5 和 WMF 5.0 或更高版本。
    > 
* 不支持 Windows 客户端操作系统。

Windows 代理也必须配置为与 Windows Server Update Services (WSUS) 服务器通信或有权访问 Microsoft 更新。

> [!NOTE]
> System Center Configuration Manager 无法同时管理 Windows 代理。
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) 和 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) 和 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)  
* Ubuntu 12.04 LTS 和更高版本 x86/x64   

> [!NOTE]  
> 若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置无人参与升级包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

Linux 代理必须具有访问更新存储库的权限。

> [!NOTE]
> 此解决方案不支持适用于 Linux 且配置为向多个 OMS 工作区报告的 OMS 代理。  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>启用 Azure 虚拟机的更新管理

1. 在 Azure 门户中，打开自动化帐户。
2. 在屏幕的左侧，选择“更新管理”。
3. 在屏幕顶部，单击“添加 Azure 虚拟机”。
    ![上架虚拟机](./media/manage-update-multi/update-onboard-vm.png)
4. 选择要上架的虚拟机。 显示“启用更新管理”屏幕。
5. 单击“启用”。

   ![启用更新管理](./media/manage-update-multi/update-enable.png)

为虚拟机启用了更新管理。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>启用非 Azure 虚拟机和计算机的更新管理

如需如何为非 Azure Windows 虚拟机和计算机启用更新管理的说明，请参阅[将 Windows 计算机连接到 Azure 中的 Log Analytics 服务](../log-analytics/log-analytics-windows-agents.md)。

如需如何为非 Azure Linux 虚拟机和计算机启用更新管理的说明，请参阅[将 Linux 计算机连接到 Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md)。

## <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”屏幕随即显示。 可在“缺少更新”选项卡上查看缺少更新的列表。

## <a name="data-collection"></a>数据收集

安装在虚拟机和计算机上的代理收集有关更新的数据，并将其发送到 Azure 更新管理。

### <a name="supported-agents"></a>支持的代理

下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 |是 |更新管理从 Windows 代理收集有关系统更新的信息，并启动所需更新的安装。 |
| Linux 代理 |是 |更新管理从 Linux 代理收集有关系统更新的信息，并在支持的发行版上启动所需更新的安装。 |
| Operations Manager 管理组 |是 |更新管理从已连接的管理组中的代理收集有关系统更新的信息。 |
| Azure 存储帐户 |否 |Azure 存储不包含有关系统更新的信息。 |

### <a name="collection-frequency"></a>收集频率

对于每台托管的 Windows 计算机，每天执行两次扫描。 每隔 15 分钟就会调用一次 Windows API，以便查询上次更新时间，从而确定状态是否已更改，并在状态已更改的情况下启动符合性扫描。  对于每台托管的 Linux 计算机，每 3 小时执行一次扫描。

可能需要 30 分钟到 6 小时的时间，仪表板才会显示受托管计算机提供的已更新数据。

## <a name="schedule-an-update-deployment"></a>计划更新部署

若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。
可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

单击“更新管理”屏幕顶部的“计划更新部署”，计划用于虚拟机的新的更新部署。 在“新建更新部署”屏幕中，指定以下信息：

* 名称- 提供用于标识更新部署的唯一名称。
* OS 类型 - 选择 Windows 或 Linux。
* 要更新的计算机 - 选择想要更新的虚拟机。

  ![选择要更新的虚拟机](./media/manage-update-multi/update-select-computers.png)

* 更新分类- 选择部署中包含的更新部署的软件类型。 分类类型：
  * 关键更新
  * 安全更新
  * 更新汇总
  * 功能包
  * 服务包
  * 定义更新
  * 工具
  * 更新
* 计划设置- 可以接受默认的日期和时间，即当前时间后 30 分钟，或指定不同的时间。
   还可以指定部署是发生一次还是设置定期计划。 单击“重复周期”下的“重复执行”选项可设置定期计划。

   ![更新计划设置屏幕](./media/manage-update-multi/update-set-schedule.png)

* 维护时段(分钟) - 指定要在其中进行更新部署的时间段。  这有助于确保在定义的服务时段内执行更改。

完成配置计划后，单击“创建”按钮，然后返回到状态仪表板。
请注意，“已计划”表显示刚创建的部署计划。

> [!WARNING]
> 对于需要重新启动的更新，虚拟机将自动重启。

## <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划的部署开始后，可以在“更新管理”屏幕的“更新部署”选项卡上查看该部署的状态。
如果部署当前正在运行，则状态显示为“正在运行”。 如果部署已成功完成，则状态会更改为“成功”。
如果部署中有一个或多个更新失败，则状态为“部分失败”。

![更新部署状态 ](./media/manage-update-multi/update-view-results.png)

单击已完成的更新部署，查看该更新部署的仪表板。

在“更新结果”中，磁贴总结了虚拟机上更新和部署结果的总数。
右侧的表格详细列出了每个更新的细目以及安装结果，结果可能是以下值之一：

* 未尝试 - 由于定义的维护时段时长不足，因而未安装更新。
* 成功 - 更新成功
* 失败 - 更新失败

单击“所有日志”，查看部署创建的所有日志条目。

单击“输出”磁贴，查看负责管理目标虚拟机更新部署的 runbook 的作业流。

单击“错误”，查看有关部署中的任何错误的详细信息。

若要详细了解日志、输出和错误信息，请参阅[更新管理](../operations-management-suite/oms-solution-update-management.md)。

## <a name="next-steps"></a>后续步骤

* 若要了解有关更新管理的详细信息，请参阅[更新管理](../operations-management-suite/oms-solution-update-management.md)。