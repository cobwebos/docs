---
title: 删除 Azure 自动化更新管理功能
description: 本文介绍如何停止使用更新管理，以及如何从 Log Analytics 工作区取消链接自动化帐户。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4b4946da9f63299c7ba2b383d6c153673595a1ab
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449905"
---
# <a name="remove-update-management-from-automation-account"></a>从自动化帐户中删除更新管理

使用 Azure 自动化更新管理在虚拟机上启用对更新的管理后，你可能会决定停止使用它，并将配置从帐户和链接 Log Analytics 工作区中删除。  本文介绍如何从托管 Vm、自动化帐户和 Log Analytics 工作区中完全删除更新管理。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="remove-management-of-vms"></a>删除 Vm 管理

删除更新管理之前，首先需要停止管理 Vm。 若要从功能中取消注册 Vm，请参阅[从更新管理中删除 vm](update-mgmt-remove-vms.md) 。

## <a name="remove-updatemanagement-solution"></a>删除 UpdateManagement 解决方案

你需要执行以下步骤来完全删除更新管理，然后才能将自动化帐户从工作区中取消链接。 将从工作区中删除**更新**解决方案。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 当你开始键入时，列表中会根据输入筛选建议。 选择“Log Analytics”****。

3. 在 Log Analytics 工作区列表中，选择启用更新管理时选择的工作区。

4. 在左侧选择“解决方案”****。  

5. 在解决方案列表中，选择 "**更新（工作区名称）**"。 在解决方案的“概述”页上选择“删除”。******** 出现确认提示时，请选择“是”。****

## <a name="unlink-workspace-from-automation-account"></a>取消工作区与自动化帐户的链接

1. 在 Azure 门户中选择“自动化帐户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在 "**取消链接工作区**" 页上，选择 "**取消链接工作区**并响应提示"。

   ![“取消关联工作区”页面](media/update-mgmt-remove-feature/automation-unlink-workspace-blade.png)

当它尝试取消链接 Log Analytics 工作区时，可以从菜单中的 "**通知**" 下跟踪进度。

或者，你也可在 Log Analytics 工作区内取消该工作区与自动化帐户的关联：

1. 在 Azure 门户中，选择“Log Analytics”。****

2. 从工作区的 "**相关资源**" 下选择 "**自动化帐户**"。

3. 在“自动化帐户”页上，选择“取消链接帐户”。

当它尝试取消链接自动化帐户时，可以从菜单中的 "**通知**" 下跟踪进度。

## <a name="cleanup-automation-account"></a>清理自动化帐户

如果更新管理配置为支持早期版本的 Azure SQL 监视，则功能的安装可能已创建应删除的自动化资产。 对于更新管理，建议删除以下不再需要的项：

   * 更新计划 - 每个计划都具有与所创建的更新部署匹配的名称。
   * 为更新管理创建的混合辅助角色组的名称类似于*com_9ceb8108 machine1 26c9-4051-b6b3-227600d715c8）*。

## <a name="next-steps"></a>后续步骤

若要重新启用此功能，请参阅[从自动化帐户启用更新管理](update-mgmt-enable-automation-account.md)，[通过浏览 Azure 门户启用更新管理](update-mgmt-enable-portal.md)，[启用从 Runbook 更新管理](update-mgmt-enable-runbook.md)，或[从 Azure VM 启用更新管理](update-mgmt-enable-vm.md)。
