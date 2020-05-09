---
title: 启用 Azure 自动化在空闲时间启动/停止 VM 解决方案
description: 本文介绍如何在 Azure 虚拟机的非工作时间解决方案中启用 Azure 自动化启动/停止 VM。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: ed9937576334fcba6707f2737e92c3cddce2d7f7
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864209"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>启用 Azure Automation 启动/停止 Vm 解决方案

执行以下步骤，将**在空闲时间启动/停止 VM**解决方案添加到新的或现有的自动化帐户，并链接 Log Analytics 工作区。 完成载入过程后，请配置变量以自定义解决方案。

>[!NOTE]
>若要将此解决方案用于经典 Vm，需要使用经典运行方式帐户，默认情况下不会创建该帐户。 有关创建经典运行方式帐户的说明，请参阅[创建经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。
>

## <a name="enable-solution"></a>启用解决方案

1. 登录到 Azure[门户](https://portal.azure.com)。

2. 搜索并选择 "**自动化帐户**"。

3. 在 "自动化帐户" 页上，从列表中选择你的自动化帐户。

4. 在自动化帐户中，选择 "**相关资源**" 下的 "**启动/停止 VM** "。 在此处，可以单击“详细了解和启用解决方案”。**** 如果已部署“启动/停止 VM”解决方案，可单击“管理解决方案”，在列表中找到并选择它****。

   ![从自动化帐户启用](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > 也可以单击“创建资源”，在 Azure 门户中的任意位置创建该解决方案。**** 在“市场”页面中，键入类似于“启动”或“启动/停止”的关键字********。 开始键入时，会根据输入筛选该列表。 或者，可以键入解决方案完整名称中的一个或多个关键，然后按 Enter。 在搜索结果中选择“在空闲时间启动/停止 VM”****。

5. 在所选解决方案的“在空闲时间启动/停止 VM”页中查看摘要信息，并单击“创建”********。

   ![Azure 门户](media/automation-solution-vm-management/azure-portal-01.png)

6. 此时会显示“添加解决方案”页面。 系统会提示你配置解决方案，然后才能将其导入自动化订阅。

   ![VM 管理中的“添加解决方案”页面](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. 在“添加解决方案”页面上，选择“工作区”****。 选择链接到自动化帐户所在的同一个 Azure 订阅的 Log Analytics 工作区。 如果没有工作区，请选择“新建工作区”****。 在 "Log Analytics 工作区" 页上，执行以下步骤：

   - 为新的 Log Analytics 工作区指定名称，例如**ContosoLAWorkspace**。
   - 从下拉列表中选择要链接到的**订阅**（如果选择的默认值不合适）。
   - 对于“资源组”****，可以创建新资源组，或选择现有的资源组。
   - 选择一个**位置**。
   - 选择**定价层**。 选择“每 GB (独立)”选项****。 Azure Monitor 日志具有更新的[定价](https://azure.microsoft.com/pricing/details/log-analytics/)，并且每 GB 的级别是唯一的选择。

   > [!NOTE]
   > 启用解决方案时，仅支持某些区域以链接 Log Analytics 工作区和自动化帐户。
   >
   > 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

8. 在“Log Analytics 工作区”页上提供所需信息后，单击“创建”****。 可以在菜单中的“通知”下面跟踪操作进度，完成后将返回到“添加解决方案”页面。****

9. 在“添加解决方案”页面中，选择“自动化帐户”****。 如果要创建新的 Log Analytics 工作区，则可以创建一个与之关联的新自动化帐户，也可以选择尚未链接到 Log Analytics 工作区的现有自动化帐户。 选择现有的自动化帐户，或单击 "**创建自动化帐户**"，并在 "添加自动化帐户" 页上提供以下信息：
 
   - 在“名称”字段中输入自动化帐户的名称****。

     系统会根据所选的 Log Analytics 工作区自动填充所有其他选项。 无法修改这些选项。 “Azure 运行方式帐户”是此解决方案为 Runbook 包含的默认身份验证方法。 单击“确定”后，系统会验证配置选项并创建自动化帐户。**** 可以在菜单中的“通知”下面跟踪操作进度****。

10. 最后，在“添加解决方案”页面上，选择“配置”。**** 此时会显示“参数”页面。

    ![解决方案的“参数”页面](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   在此处，系统会提示：
  
   - 指定“目标资源组名称”。**** 这些值是包含此解决方案要管理的 VM 的资源组名称。 可以输入多个名称，使用逗号分隔（这些值不区分大小写）。 如果想要针对订阅中的所有资源组内的 VM，可以使用通配符。 此值存储在 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 变量中。
  
   - 指定“VM 排除列表(字符串)”。**** 该值是目标资源组中的一个或多个虚拟机的名称。 可以输入多个名称，使用逗号分隔（这些值不区分大小写）。 支持使用通配符。 此值存储在 **External_ExcludeVMNames** 变量中。
  
   - 选择“计划”。**** 选择计划的日期和时间。 将从所选时间开始，创建重复每日计划。 无法选择其他区域。 若要在配置解决方案后将计划配置为特定时区，请参阅[修改启动和关闭计划](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)。
  
   - 要从操作组接收“电子邮件通知”，请接受默认值“是”，并提供有效的电子邮件地址********。 如果选择了“否”，但后来想要接收电子邮件通知，则可以使用有效的电子邮件地址（以逗号分隔）更新创建的[操作组](../azure-monitor/platform/action-groups.md)****。 还需要启用以下警报规则：

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > “目标资源组名称”的默认值是 &ast;********。 这面向订阅中的所有 VM。 如果不希望解决方案面向订阅中的所有 VM，则需要在启用计划前，将此值更新到资源组名称列表。

11. 配置解决方案所需的初始设置后，单击“确定”以关闭“参数”页面并选择“创建”。******** 

系统会验证所有设置，然后在订阅中部署该解决方案。 此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度****。

> [!NOTE]
> 如果你有 Azure 云解决方案提供商（Azure CSP）订阅，则在部署完成后，在你的自动化帐户中，请参阅 "**共享资源**" 下的 "**变量**"，并将 " [External_EnableClassicVMs](automation-solution-vm-management.md#variables) " 变量设置为**False**。 这会使解决方案停止查找经典 VM 资源。

## <a name="next-steps"></a>后续步骤

启用解决方案后，可以[将其配置](automation-solution-vm-management-config.md)为支持 VM 管理要求。