---
title: "管理多个 Azure 虚拟机的更新 | Microsoft Docs"
description: "上架 Azure 虚拟机以管理更新。"
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>管理多个 Azure 虚拟机的更新

使用更新管理可以管理 Azure 虚拟机的更新和修补程序。
在 [Azure 自动化](automation-offering-get-started.md)帐户中，可以快速上架虚拟机、评估可用更新的状态、计划所需更新的安装以及查看部署结果，以验证更新是否已成功地应用到启用了更新管理的所有虚拟机。

## <a name="prerequisites"></a>先决条件

要完成本指南中的步骤，需要：

* 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
* Azure 资源管理器虚拟机（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>启用 Azure 虚拟机的更新管理

1. 在 Azure 门户中，打开自动化帐户。
2. 在屏幕的左侧，选择“更新管理”。
3. 在屏幕顶部，单击“添加 Azure 虚拟机”。
    ![上架虚拟机](./media/manage-update-multi/update-onboard-vm.png)
4. 选择要上架的虚拟机。 显示“启用更新管理”屏幕。
5. 单击“启用”。

   ![启用更新管理](./media/manage-update-multi/update-enable.png)

为虚拟机启用了更新管理。

## <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”屏幕随即显示。 可在“缺少更新”选项卡上查看缺少更新的列表。

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

## <a name="next-steps"></a>后续步骤

* 若要了解有关更新管理的详细信息，请参阅[更新管理](../operations-management-suite/oms-solution-update-management.md)。
