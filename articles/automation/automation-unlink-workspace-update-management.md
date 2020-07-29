---
title: 为更新管理操作取消工作区与自动化帐户的关联
description: 本文介绍如何为更新管理操作取消 Log Analytics 工作区与自动化帐户的关联
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835778"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>为更新管理操作取消工作区与自动化帐户的关联

在[更新管理](automation-update-management.md)操作期间，可决定不将自动化帐户与 Log Analytics 工作区进行集成。 本文介绍如何取消工作区与帐户的关联。

1. 通过 https://portal.azure.com 登录到 Azure。

2. 删除虚拟机 (VM) 的更新管理。 请参阅[从更新管理中删除 VM](automation-remove-vms-from-update-management.md)。

3. 如果更新管理包含 Azure SQL 监视功能的早期版本，则设置该功能时可能已经创建了自动化资产，你应将这些资产删除。 对于更新管理，建议删除以下不再需要的项：

   * 更新计划 - 每个计划都具有与所创建的更新部署匹配的名称。
   * 为更新管理创建的混合辅助角色组 - 每个混合辅助角色组的命名都类似于 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8。

4. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

5. 在“取消关联工作区”页面上，单击“取消关联工作区”。

   ![“取消链接工作区”页](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png)。

6. 当 Azure 自动化尝试取消关联 Log Analytics 工作区时，可在菜单中的“通知”下跟踪进度。

或者，你也可在 Log Analytics 工作区内取消该工作区与自动化帐户的关联：

1. 在工作区中，选择“相关资源”下的“自动化帐户” 。 
2. 在“自动化帐户”页面上，选择“取消关联帐户”。

## <a name="next-steps"></a>后续步骤

* 要使用此功能，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要对功能错误进行故障排除，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理错误进行故障排除，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 若要对 Linux 更新代理错误进行故障排除，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。
