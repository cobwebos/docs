---
title: Azure 自动化和 Log Analytics 工作区映射
description: 本指南介绍了支持解决方案允许自动化帐户和 Log Analytics 工作区之间的映射
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9faa261f4b6293d778c5da685f630a21fd88f600
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478576"
---
# <a name="workspace-mappings"></a>工作区映射

在启用更新管理、 更改跟踪和清单或启动/停止 Vm 等解决方案时在非工作时间期间，只有特定区域支持链接的 Log Analytics 工作区和自动化帐户。 此映射仅适用于自动化帐户和 Log Analytics 工作区。 报告到自动化帐户或 Log Analytics 工作区的资源可以位于其他区域。

## <a name="supported-mappings"></a>受支持的映射

下表显示了受支持的映射：

|**Log Analytics 工作区区域**|**Azure 自动化区域**|
|---|---|
|**美国**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**亚太区**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**欧洲**||
|UKSouth|UKSouth|
|西欧|西欧|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS 映射到自动化帐户的 Log Analytics 工作区不精确区域到另一个区域映射，而是正确的映射。

<sup>2</sup>由于容量限制范围区域不可用时创建新的资源。 这包括自动化帐户和 Log Analytics 工作区。 但是，在区域中预先存在链接的资源应继续工作。

## <a name="unlink-workspace"></a>取消链接工作区

如果你决定不再想要将你的自动化帐户与 Log Analytics 工作区相集成，您可以取消链接你直接从 Azure 门户的帐户。 在继续之前，首先需要在非工作时间解决方案期间删除更新管理、 更改跟踪和清单或启动/停止 Vm，如果正在使用它们。 如果不删除它们，此过程将无法继续。 查看已导入的特定解决方案的主题，了解删除该解决方案所需的步骤。

删除这些解决方案后，可以执行以下步骤取消链接自动化帐户。

> [!NOTE]
> 某些解决方案（包括早期版本的 Azure SQL 监视解决方案）可能已创建自动化资产并可能还需要在取消链接工作区之前删除。

1. 从 Azure 门户中打开自动化帐户，并在“自动化帐户”页左侧的“相关资源”部分下，选择“链接工作区”   。

2. 在“取消链接工作区”页上，单击“取消链接工作区”  。 你将收到提示用户确认要继续。

3. 当 Azure 自动化尝试从 Log Analytics 工作区中取消链接该帐户时，可以在菜单中的“通知”  下跟踪进度。

如果使用了“更新管理”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 更新计划 - 每个计划都将具有与所创建的更新部署匹配的名称

* 为解决方案创建的混合辅助角色组 - 每个混合辅助角色组的命名都将类似于 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8。

如果使用了“在非工作时间启动/停止 VM”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 启动和停止 VM Runbook 计划
* 启动和停止 VM Runbook
* 变量

或者，您可以还取消链接工作区在自动化帐户中从 Log Analytics 工作区。 在工作区中，选择**自动化帐户**下**相关资源**。 在自动化帐户页上选择**取消链接帐户**。

## <a name="next-steps"></a>后续步骤

了解如何载入以下解决方案：

更新管理和更改跟踪和清单：

* 从[虚拟机](../automation-onboard-solutions-from-vm.md)
* 从你[自动化帐户](../automation-onboard-solutions-from-automation-account.md)
* 当[浏览多个计算机](../automation-onboard-solutions-from-browse.md)
* 从[runbook](../automation-onboard-solutions.md)

在非工作时间启动/停止 VM

* [部署在非工作时间启动/停止 Vm](../automation-solution-vm-management.md)