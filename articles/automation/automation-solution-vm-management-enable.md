---
title: 启用 Azure 自动化“在空闲时间启动/停止 VM”
description: 本文介绍如何为 Azure VM 启用“在空闲时间启动/停止 VM”功能。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: dde2c3e4cf496bb15ca91c72d9a41936af7051c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83743753"
---
# <a name="enable-startstop-vms-during-off-hours"></a>启用“在空闲时间启动/停止 VM”

按顺序执行本主题中的步骤，即可使用新的或现有的自动化帐户以及链接的 Log Analytics 工作区为 VM 启用“在空闲时间启动/停止 VM”功能。 完成设置过程后，请配置变量来自定义该功能。

>[!NOTE]
>若要将此功能用于经典 VM，则需要一个经典运行方式帐户，但默认情况下不会创建该帐户。 请参阅[创建经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。
>

## <a name="create-resources-for-the-feature"></a>为该功能创建资源

1. 登录 Azure [门户](https://portal.azure.com)。
2. 搜索并选择“自动化帐户”。
3. 在“自动化帐户”页，选择列表中的自动化帐户。
4. 在自动化帐户中，选择“相关资源”下的“启动/停止 VM” 。 在此处，可以单击“详细了解和启用解决方案”。 如果已部署了该功能，则可以单击“管理解决方案”在列表中找到它。

   ![从自动化帐户启用](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > 此外，你也可以单击“创建资源”，在 Azure 门户中的任意位置创建资源。 在“市场”页面中，键入类似于“启动”或“启动/停止”的关键字 。 开始键入时，会根据输入筛选该列表。 或者，可以键入功能完整名称中的一个或多个关键字，然后按 Enter。 在搜索结果中选择“在空闲时间启动/停止 VM”。

5. 在所选部署的“在空闲时间启动/停止 VM”页中查看摘要信息，然后单击“创建”。

   ![Azure 门户](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>配置功能

创建资源后，即会显示“添加解决方案”页面。 系统会提示先要配置功能，然后才可以将它导入自动化订阅。 请参阅[配置“在空闲时间启动/停止 VM”](automation-solution-vm-management-config.md)。

   ![VM 管理中的“添加解决方案”页面](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>选择 Log Analytics 工作区

1. 在“添加解决方案”页面上，选择“工作区”。 选择链接到自动化帐户所用的 Azure 订阅的 Log Analytics 工作区。 

2. 如果没有工作区，请选择“新建工作区”。 在“Log Analytics 工作区”页上，请执行以下步骤：

   - 为新的 Log Analytics 工作区指定名称，例如“ContosoLAWorkspace”。
   - 如果选择的默认值不合适，请从下拉列表中选择要链接到的“订阅”。
   - 对于“资源组”，可以创建新资源组，或选择现有的资源组。
   - 选择“位置” 。
   - 选择“定价层”。 选择“每 GB (独立)”选项。 Azure Monitor 日志已更新[定价](https://azure.microsoft.com/pricing/details/log-analytics/)，“每 GB”层是唯一的选项。

   > [!NOTE]
   > 在启用功能时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

3. 在“Log Analytics 工作区”页上提供所需信息后，单击“创建”。 你可以在菜单中的“通知”下面跟踪操作进度，完成后将返回到“添加解决方案”页面。

## <a name="add-automation-account"></a>添加自动化帐户

再次访问“添加解决方案”页面，选择“自动化帐户”。 可以选择尚未链接到 Log Analytics 工作区的现有自动化帐户。 如果要创建新的 Log Analytics 工作区，可以创建与它关联的新自动化帐户。 选择现有的自动化帐户，或者单击“创建自动化帐户”，并在“添加自动化帐户”页的“名称”字段中提供自动化帐户的名称： 

系统会根据所选的 Log Analytics 工作区自动填充所有其他选项。 无法修改这些选项。 Azure 运行方式帐户是此功能为 Runbook 包含的默认身份验证方法。 

单击“确定”后，系统会验证配置选项并创建自动化帐户。 可以在菜单中的“通知”下面跟踪操作进度。

## <a name="define-feature-parameters"></a>定义功能参数

1. 在“添加解决方案”页面上，选择“配置”。 此时会显示“参数”页面。

    ![解决方案的“参数”页面](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. 为“目标资源组名称”字段指定一个值。 词字段将定义包含该功能要管理的 VM 的组名称。 你可以输入多个名称，使用逗号分隔（这些值不区分大小写）。 如果想要针对订阅中的所有资源组内的 VM，可以使用通配符。 这些值存储在 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 变量中。

    > [!IMPORTANT]
    > “目标资源组名称”的默认值是 &ast; 。 该设置面向订阅中的所有 VM。 如果不希望该功能面向订阅中的所有 VM，请务必在选择计划前提供一个资源组名称列表。
  
3. 为“VM 排除列表(字符串)”字段指定一个值。 该值是目标资源组中的一个或多个虚拟机的名称。 你可以输入多个名称，使用逗号分隔（这些值不区分大小写）。 支持使用通配符。 该值存储在变量 `External_ExcludeVMNames` 中。
  
4. 使用“计划”字段来选择按功能进行 VM 管理的计划。 为你的计划选择一个开始日期和时间，以创建从所选时间开始的每日重复计划。 无法选择其他区域。 若要在配置功能后将计划配置为特定时区，请参阅[修改启动和关闭计划](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)。

5. 要从[操作组](../azure-monitor/platform/action-groups.md)接收电子邮件通知，请在“电子邮件通知”字段中接受默认值“是”，并提供有效的电子邮件地址。 。 如果你选择了“否”，但后来想要接收电子邮件通知，则可以使用有效的电子邮件地址（以逗号分隔）更新创建的操作组。 

6. 启用以下警报规则：

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>创建警报

“在空闲时间启动/停止 VM”不包括预定义的一组警报。 查看[通过 Azure Monitor 创建日志警报](../azure-monitor/platform/alerts-log.md)，了解如何创建作业失败警报，以支持 DevOps 或操作过程和程序。

## <a name="deploy-the-feature"></a>部署功能

1. 配置功能所需的初始设置后，单击“确定”以关闭“参数”页面。

2. 单击“创建”。 系统会验证所有设置，然后在订阅中部署该功能。 此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。

    > [!NOTE]
    > 如果你具有 Azure 云解决方案提供商 (Azure CSP) 订阅，则部署完成之后，在自动化帐户中转到“共享资源”下的“变量”，然后将 [External_EnableClassicVMs](automation-solution-vm-management.md#variables) 变量设置为“False”。 这会使解决方案停止查找经典 VM 资源。

## <a name="next-steps"></a>后续步骤

* 要设置该功能，请参阅[配置“在空闲时间启动/停止 VM”](automation-solution-vm-management-config.md)。
* 若要解决功能问题，请参阅[“在空闲时间启动/停止 VM”问题的故障排除](troubleshoot/start-stop-vm.md)。