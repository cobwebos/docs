---
title: Azure 自动化和 Log Analytics 工作区映射
description: 本文介绍自动化帐户与 Log Analytics 工作区之间允许用于支持解决方案的映射
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849507"
---
# <a name="workspace-mappings"></a>工作区映射

如果启用更新管理、更改跟踪和清单等解决方案，或在空闲时间启动/停止 VM 解决方案，则仅支持某些区域来链接 Log Analytics 工作区和自动化帐户。 此映射仅适用于自动化帐户和 Log Analytics 工作区。 向自动化帐户或 Log Analytics 的工作区报告的资源可以驻留在其他区域。

## <a name="supported-mappings"></a>支持的映射

下表显示了受支持的映射：

|**Log Analytics 工作区区域**|**Azure 自动化区域**|
|---|---|
|**美国**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**加拿大**||
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

<sup>1</sup> EastUS 将工作区 Log Analytics 映射到自动化帐户的工作区映射不是精确区域到区域的映射，但它是正确的映射。

<sup>2</sup>由于容量限制，在创建新资源时区域不可用。 这包括自动化帐户和 Log Analytics 工作区。 但是，区域中预先存在的链接资源应该继续工作。

## <a name="unlink-workspace"></a>取消链接工作区

如果你决定不再想要将自动化帐户与 Log Analytics 工作区集成，则可以直接从 Azure 门户取消链接你的帐户。 在继续操作之前，首先需要删除更新管理、更改跟踪和清单，或在空闲时间启动/停止 VM 解决方案（如果你使用它们）。 如果不删除这些文件，此过程将无法继续。 查看已导入的特定解决方案的主题，了解删除该解决方案所需的步骤。

删除这些解决方案后，可以执行以下步骤取消链接自动化帐户。

> [!NOTE]
> 某些解决方案（包括早期版本的 Azure SQL 监视解决方案）可能已创建自动化资产并可能还需要在取消链接工作区之前删除。

1. 从 Azure 门户中打开自动化帐户，并在“自动化帐户”页左侧的“相关资源”部分下，选择“链接工作区”。

2. 在“取消链接工作区”页上，单击“取消链接工作区”。 您将收到一条提示，确认是否要继续。

3. 当 Azure 自动化尝试从 Log Analytics 工作区中取消链接该帐户时，可以在菜单中的“通知”下跟踪进度。

如果使用了“更新管理”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 更新计划 - 每个计划都将具有与所创建的更新部署匹配的名称

* 为解决方案创建的混合辅助角色组-每个组的名称与 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`）类似。

如果使用了“在非工作时间启动/停止 VM”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 启动和停止 VM Runbook 计划
* 启动和停止 VM Runbook
* 变量

或者，还可以从 "Log Analytics" 工作区中取消工作区与自动化帐户的链接。 在工作区中，选择 "**相关资源**" 下的 "**自动化帐户**"。 在 "自动化帐户" 页上，选择 "**取消链接帐户**"。

## <a name="next-steps"></a>后续步骤

了解如何载入以下解决方案：

更新管理和更改跟踪和清单：

* 从[虚拟机](../automation-onboard-solutions-from-vm.md)
* 从[自动化帐户](../automation-onboard-solutions-from-automation-account.md)
* [浏览多台计算机](../automation-onboard-solutions-from-browse.md)时
* 从[runbook](../automation-onboard-solutions.md)

在非工作时间启动/停止 VM

* [部署在空闲时间启动/停止 VM](../automation-solution-vm-management.md)
