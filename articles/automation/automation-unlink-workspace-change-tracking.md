---
title: 取消工作区与自动化帐户的关联来启用更改跟踪和库存操作
description: 本文介绍如何取消 Log Analytics 工作区与自动化帐户的关联来启用更改跟踪和库存操作
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828247"
---
# <a name="unlink-workspace-from-automation-account"></a>取消工作区与自动化帐户的关联

在启用[更改跟踪和库存](change-tracking.md)操作时，可决定不将自动化帐户与 Log Analytics 工作区进行集成。 本文介绍如何取消工作区与帐户的关联。

1. 通过 https://portal.azure.com 登录到 Azure。

2. 删除虚拟机 (VM) 的更新管理。 请参阅[从“更改跟踪和库存”中删除 VM](automation-remove-vms-from-change-tracking.md)。

3. 如果更改跟踪和库存操作包含 Azure SQL 监视功能的早期版本，则设置该功能时可能已经创建了自动化资产，你应将这些资产删除。 找到这些资产，并将其删除。

4. 请打开自动化帐户，然后选择左侧“相关资源”下的“关联的工作区” 。

5. 在“取消关联工作区”页面上，单击“取消关联工作区”。

   ![“取消关联工作区”页面](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png)。

6. 当 Azure 自动化尝试取消关联 Log Analytics 工作区时，可在菜单中的“通知”下跟踪进度。

或者，你也可在 Log Analytics 工作区内取消该工作区与自动化帐户的关联：

1. 在工作区中，选择“相关资源”下的“自动化帐户” 。 
2. 在“自动化帐户”页面上，选择“取消关联帐户”。

## <a name="next-steps"></a>后续步骤

* 要使用更改跟踪和库存，请参阅[管理更改跟踪和库存](change-tracking-file-contents.md)。
* 要排查常见功能问题，请参阅[排查更改跟踪和库存问题](troubleshoot/change-tracking.md)。
