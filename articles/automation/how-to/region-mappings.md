---
title: Azure 自动化和日志分析工作区映射
description: 本文介绍了自动化帐户和日志分析工作区之间允许的映射，以支持解决方案
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681833"
---
# <a name="workspace-mappings"></a>工作区映射

启用更新管理、更改跟踪和库存等解决方案或非工作时间启动/停止 VM 等解决方案时，仅支持某些区域链接日志分析工作区和自动化帐户。 此映射仅适用于自动化帐户和日志分析工作区。 报告到自动化帐户或日志分析工作区的资源可以驻留在其他区域。

## <a name="supported-mappings"></a>支持的映射

下表显示了受支持的映射：

|**Log Analytics 工作区区域**|**Azure 自动化区域**|
|---|---|
|**我们**||
|东US<sup>1</sup>|EastUS2|
|美国西部 2|美国西部 2|
|西中院<sup>2</sup>|西中院<sup>2</sup>|
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

<sup>1</sup>将日志分析工作区映射到自动化帐户的 EastUS 映射不是区域映射的确切区域，而是正确的映射。

<sup>2</sup>由于容量限制，在创建新资源时，该区域不可用。 这包括自动化帐户和日志分析工作区。 然而，该区域已有的相互联系资源应继续发挥作用。

## <a name="unlink-workspace"></a>取消链接工作区

如果您决定不再希望将自动化帐户与日志分析工作区集成，则可以直接从 Azure 门户取消链接帐户。 在继续操作之前，首先需要删除更新管理、更改跟踪和库存，以及使用非工作时间解决方案时启动/停止 VM。 如果不删除它们，则无法完成取消链接操作。 查看已导入的特定解决方案的主题，了解删除该解决方案所需的步骤。

删除这些解决方案后，可以执行以下步骤取消链接自动化帐户。

> [!NOTE]
> 某些解决方案（包括早期版本的 Azure SQL 监视解决方案）可能已创建了自动化资产，可能需要在取消链接工作区之前删除。

1. 从 Azure 门户打开自动化帐户。 在"自动化帐户"页上，选择 **"关联资源**"下的 **"链接工作区**"。

2. 在“取消链接工作区”页上，单击“取消链接工作区”****。 您是否希望继续，您将收到一个提示验证。

3. 当 Azure 自动化尝试从 Log Analytics 工作区中取消链接该帐户时，可以在菜单中的“通知”**** 下跟踪进度。

4. 如果使用更新管理解决方案，则可选地可能需要删除删除解决方案后不再需要的以下项目。

    * 更新计划 - 每个都有一个与您创建的更新部署匹配的名称。
    * 为解决方案创建的混合工作组 - 每个组的名称都类似于`machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`。

5. 如果在非工作时间使用"开始/停止 VM"解决方案，则可以选择删除删除解决方案后不需要的以下项目。

    * 启动和停止 VM Runbook 计划
    * 启动和停止 VM Runbook
    * 变量

或者，您也可以从工作区中的自动化帐户取消链接工作区。 

1. 在工作区中，在 **"相关资源**"下选择 **"自动化帐户**"。 
2. 在"自动化帐户"页上，选择 **"取消链接帐户**"。

## <a name="next-steps"></a>后续步骤

* 了解如何在更新管理和更改跟踪和库存解决方案中加入：

    * 从[虚拟机](../automation-onboard-solutions-from-vm.md)
    * 从[您的自动化帐户](../automation-onboard-solutions-from-automation-account.md)
    * [浏览多台计算机](../automation-onboard-solutions-from-browse.md)时
    * 从[运行簿](../automation-onboard-solutions.md)

* 了解如何在非工作时间使用启动/停止 VM 解决方案：

    * [在非工作时间部署启动/停止 VM](../automation-solution-vm-management.md)
