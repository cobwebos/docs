---
title: Azure 自动化和 Log Analytics 工作区映射
description: 本文介绍自动化帐户与 Log Analytics 工作区之间允许用于支持解决方案的映射
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1bc47fe6e1f0730bcff49e138df92f85ba3ef1a8
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114473"
---
# <a name="workspace-mappings"></a>工作区映射

启用更新管理、更改跟踪和清单或在空闲时间启动/停止 VM 时，仅支持某些区域在订阅中链接 Log Analytics 工作区和自动化帐户。 此映射仅适用于自动化帐户和 Log Analytics 工作区。 Log Analytics 工作区和自动化帐户必须位于同一订阅中，但可以在部署到同一区域的不同资源组中。

有关详细信息，请参阅[Log Analytics 工作区和自动化帐户](../azure-monitor/insights/solutions.md#log-analytics- workspace-and-automation-account)。

## <a name="supported-mappings"></a>支持的映射

下表显示了受支持的映射：

|**Log Analytics 工作区区域**|**Azure 自动化区域**|
|---|---|
|**反馈**||
|EastUS<sup>1</sup>|EastUS2|
|美国西部 2|美国西部 2|
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

<sup>1</sup> EastUS 将工作区 Log Analytics 映射到自动化帐户的工作区映射不是精确区域到区域的映射，但它是正确的映射。

<sup>2</sup>由于容量限制，在创建新资源时区域不可用。 这包括自动化帐户和 Log Analytics 工作区。 但是，区域中预先存在的链接的资源应该继续工作。

## <a name="unlink-workspace"></a>取消链接工作区

如果你决定不再想要将自动化帐户与 Log Analytics 工作区集成，则可以直接从 Azure 门户取消链接你的帐户。 在继续操作之前，首先需要删除更新管理、更改跟踪和清单，并在使用时在空闲时间启动/停止 VM。 如果不删除它们，则无法完成链接操作。 查看每个要启用的项目，以了解删除它所需的步骤。

删除它们后，你可以执行以下步骤来取消关联你的自动化帐户。

> [!NOTE]
> 某些解决方案（包括早期版本的 Azure SQL monitoring 解决方案）可能已创建自动化资产，并可能需要在取消链接工作区之前删除。

1. 在 Azure 门户中，打开自动化帐户。 在 "自动化帐户" 页上，选择 "**相关资源**" 下的 "**链接工作区**"。

2. 在“取消链接工作区”页上，单击“取消链接工作区”****。 你会收到一条提示，确认是否要继续。

3. 当 Azure 自动化尝试从 Log Analytics 工作区中取消链接该帐户时，可以在菜单中的“通知”**** 下跟踪进度。

4. 如果使用更新管理，则可能需要删除在删除后不再需要的以下项。

    * 更新计划-每个计划都具有与您创建的更新部署匹配的名称。
    * 为解决方案创建的混合辅助角色组-每个组都有`machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`一个类似于的名称。

5. 如果使用在空闲时间启动/停止 VM，则可以选择删除在删除后不需要的以下项。

    * 启动和停止 VM Runbook 计划
    * 启动和停止 VM Runbook
    * 变量

或者，你可以从工作区的自动化帐户中取消链接工作区。

1. 在工作区中，选择 "**相关资源**" 下的 "**自动化帐户**"。 
2. 在 "自动化帐户" 页上，选择 "**取消链接帐户**"。

## <a name="next-steps"></a>后续步骤

* 了解如何载入更新管理和更改跟踪和清单：

    * 从[虚拟机](../automation-onboard-solutions-from-vm.md)
    * 从[自动化帐户](../automation-onboard-solutions-from-automation-account.md)
    * [浏览多台计算机](../automation-onboard-solutions-from-browse.md)时
    * 从[runbook](../automation-onboard-solutions.md)

* 了解如何载入在空闲时间启动/停止 VM：

    * [在空闲时间启动/停止 VM 概述](../automation-solution-vm-management.md)
