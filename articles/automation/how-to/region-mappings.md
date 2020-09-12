---
title: 链接的 Log Analytics 工作区支持的区域
description: 本文介绍自动化帐户与 Log Analytics 工作区之间受支持的区域映射，因为它与 Azure 自动化的某些功能相关。
ms.date: 09/03/2020
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: dd831789d5f09ca6a20cce13659d6c479845f74e
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440654"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>链接的 Log Analytics 工作区支持的区域

在 Azure 自动化中，你可以为你的服务器和虚拟机启用更新管理、更改跟踪和清点以及在空闲时间启动/停止 VM 功能。 这些功能与 Log Analytics 工作区有依赖关系，因此需要将工作区链接到自动化帐户。 但是，仅支持某些区域将它们链接在一起。 通常，如果你计划将自动化帐户链接到未启用这些功能的工作区，则映射 *不* 适用。

本文提供了支持的映射，以便在自动化帐户中成功启用和使用这些功能。

有关详细信息，请参阅 [Log Analytics 工作区和自动化帐户](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)。

## <a name="supported-mappings"></a>支持的映射

> [!NOTE]
> 如下表中所示，Log Analytics 和 Azure Automation 之间只能存在一个映射。

下表显示了受支持的映射：

|**Log Analytics 工作区域**|**Azure 自动化区域**|
|---|---|
|**美国**||
|EastUS<sup>1</sup>|EastUS2|
|美国西部 2|美国西部 2|
|WestCentralUS|WestCentralUS|
|**加拿大**||
|CanadaCentral|CanadaCentral|
|**亚太区**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>2</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**欧洲**||
|UKSouth|UKSouth|
|西欧|西欧|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>2</sup>|USGovArizona|

<sup>1</sup> Log Analytics 工作区到自动化帐户的 EastUS 映射不是精确的区域到区域的映射，但它是正确的映射。

<sup>2</sup> 在此区域中，仅支持更新管理，而其他功能（如更改跟踪和库存）目前不可用。

## <a name="unlink-a-workspace"></a>取消链接工作区

如果决定不再想要将自动化帐户与 Log Analytics 工作区集成，可以直接从 Azure 门户取消链接帐户。 在继续之前，如果正在使用更新管理、更改跟踪和库存，以及在空闲时间启动/停止 VM，首先需要将其[删除](move-account.md#remove-features)。 如果不删除，则无法完成取消链接操作。

删除这些功能后，可以按照以下步骤取消链接自动化帐户。

> [!NOTE]
> 某些功能（包括早期版本的 Azure SQL 监视解决方案）可能已创建需要在取消链接工作区之前删除的自动化资产。

1. 在 Azure 门户中，打开自动化帐户。 在“自动化帐户”页上，选择“相关资源”下的“链接的工作区” 。

2. 在“取消链接工作区”页上，选择“取消链接工作区”。 系统会提示用户确认是否要继续。

3. 当 Azure 自动化从 Log Analytics 工作区中取消链接帐户时，可以在菜单中的“通知”下跟踪进度。

4. 如果使用了更新管理，你可能想要删除以下不再需要的项：

    * 更新计划：每个计划都具有与所创建的更新部署匹配的名称。
    * 为功能创建的混合辅助角色组：每个名称都具有类似于 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` 的名称。

5. 如果使用了“在空闲时间启动/停止 VM”，可能想要删除以下不再需要的项：

    * 启动和停止 VM Runbook 计划
    * 启动和停止 VM Runbook
    * 变量

或者，也可在工作区内取消工作区与自动化帐户的链接。

1. 在工作区中，选择“相关资源”下的“自动化帐户” 。
2. 在“自动化帐户”页上，选择“取消链接帐户”。

## <a name="next-steps"></a>后续步骤

* 在[更新管理概述](../update-management/update-mgmt-overview.md)中详细了解更新管理。
* 在[更改跟踪和库存概述](../change-tracking.md)中了解更改跟踪和库存。
* 在[在空闲时间启动/停止 VM 概述](../automation-solution-vm-management.md)中了解在空闲时间启动/停止 VM。
