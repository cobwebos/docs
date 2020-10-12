---
title: 删除 Azure 自动化更新管理功能
description: 本文介绍如何停止使用更新管理并取消自动化帐户与 Log Analytics 工作区的关联。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4b4946da9f63299c7ba2b383d6c153673595a1ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87449905"
---
# <a name="remove-update-management-from-automation-account"></a>从自动化帐户删除更新管理

使用 Azure 自动化更新管理在虚拟机上启用对更新的管理后，你可能会决定停止使用它，并从帐户和关联的 Log Analytics 工作区中删除该配置。  本文介绍如何从托管 VM、自动化帐户和 Log Analytics 工作区中完全删除更新管理。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="remove-management-of-vms"></a>删除 VM 的管理

删除更新管理之前，首先需要停止管理 VM。 若要从功能中取消注册 VM，请参阅[从更新管理中删除 VM](update-mgmt-remove-vms.md)。

## <a name="remove-updatemanagement-solution"></a>删除 UpdateManagement 解决方案

你需要执行以下步骤来完全删除更新管理，然后才能取消自动化帐户与工作区的关联。 你个将从工作区中删除 Updates 解决方案。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 当你开始键入时，列表中会根据输入筛选建议。 选择“Log Analytics”。

3. 在 Log Analytics 工作区列表中，选择启用更新管理时所选的工作区。

4. 在左侧选择“解决方案”。  

5. 在解决方案列表中，选择“Updates (工作区名称)”。 在解决方案的“概述”页上选择“删除”。  出现确认提示时，请选择“是”。

## <a name="unlink-workspace-from-automation-account"></a>取消工作区与自动化帐户的链接

1. 在 Azure 门户中选择“自动化帐户”。

2. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

3. 在“取消关联工作区”页面上，单击“取消关联工作区”，然后根据提示进行响应 。

   ![“取消链接工作区”页](media/update-mgmt-remove-feature/automation-unlink-workspace-blade.png)

当它尝试取消关联 Log Analytics 工作区时，你可在菜单中的“通知”下跟踪进度。

或者，你也可在 Log Analytics 工作区内取消该工作区与自动化帐户的关联：

1. 在 Azure 门户中，选择“Log Analytics”。

2. 在工作区中，选择“相关资源”下的“自动化帐户” 。

3. 在“自动化帐户”页上，选择“取消链接帐户”。

当它尝试取消关联自动化时，你可在菜单中的“通知”下跟踪进度。

## <a name="cleanup-automation-account"></a>清理自动化帐户

如果更新管理的配置支持 Azure SQL 监视功能的早期版本，则设置该功能时可能已经创建了自动化资产，你应将这些资产删除。 对于更新管理，建议删除以下不再需要的项：

   * 更新计划 - 每个计划都具有与所创建的更新部署匹配的名称。
   * 为更新管理创建的混合辅助角色组 - 每个混合辅助角色组的命名都类似于 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8。

## <a name="next-steps"></a>后续步骤

若要重新启用此功能，请参阅 [从自动化帐户启用更新管理](update-mgmt-enable-automation-account.md)， [通过浏览 Azure 门户启用更新管理](update-mgmt-enable-portal.md)， [启用从 Runbook 更新管理](update-mgmt-enable-runbook.md)，或 [从 Azure VM 启用更新管理](update-mgmt-enable-vm.md)。
