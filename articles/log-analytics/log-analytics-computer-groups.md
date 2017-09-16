---
title: "Log Analytics 日志搜索中的计算机组 | Microsoft Docs"
description: "Log Analytics 中的计算机组允许为一组特定的计算机设定日志搜索的范围。  本文介绍用于创建计算机组的不同方法以及如何在日志搜索中使用这些方法。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: a2ddc932343d54963a378ee27dc962a790326b2a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics 日志搜索中的计算机组

>[!NOTE]
> 本文介绍如何使用当前的 Log Analytics 查询语言使用计算机组。    如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则计算机组的工作方式会发生变化。  本文提供了对新查询语言中不同的语法和行为的注释。  


Log Analytics 中的计算机组允许为一组特定的计算机设定[日志搜索](log-analytics-log-searches.md)的范围。  每个组使用定义的查询或通过从不同源导入组填充计算机。  当日志搜索中包括组时，结果仅限于与组中的计算机匹配的记录。

## <a name="creating-a-computer-group"></a>创建计算机组
可以使用下表中的任一方法在 Log Analytics 中创建计算机组。  在以下各节中提供了每个方法的详细信息。 

| 方法 | 说明 |
|:--- |:--- |
| 日志搜索 |创建返回计算机列表的日志搜索，并将结果另存为计算机组。 |
| 日志搜索 API |使用日志搜索 API 基于日志搜索结果以编程方式创建计算机组。 |
| Active Directory |自动扫描属于 Active Directory 域成员的任何代理计算机的组成员身份，并在 Log Analytics 中为每个安全组创建一个组。 |
| WSUS |为目标组自动扫描 WSUS 服务器或客户端，并在 Log Analytics 中为每个组创建一个组。 |

### <a name="log-search"></a>日志搜索
从日志搜索创建的计算机组包含由定义的搜索查询返回的所有计算机。  每次使用计算机组时都会运行此查询，以反映创建组之后所做的任何更改。

使用以下过程从日志搜索创建计算机组。

1. [创建日志搜索](log-analytics-log-searches.md)，它将返回计算机列表。  搜索必须在查询中使用类似 **Distinct Computer** 或 **measure count() by Computer** 的内容来返回一组不同的计算机。  
2. 单击屏幕顶部的“保存”按钮。
3. 选择“是”将此查询另存为计算机组。
4. 为组键入**名称**和**类别**。  如果已存在具有相同名称和类别的搜索，则会提示进行覆盖。  不同类别中可以有采用相同名称的多个搜索。 

以下是可以另存为计算机组的示例搜索。

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则会对该流程做出以下更改以创建新计算机组。
>  
> - 创建计算机组的查询必须包括 `distinct Computer`。  下面是创建计算机组的查询示例。<br>`Heartbeat | where Computer contains "srv" | distinct Computer `
> - 创建新的计算机组时，必须指定一个除名称外的别名。  在如下所述的查询中使用计算机组时使用别名。  

### <a name="log-search-api"></a>日志搜索 API
使用日志搜索 API 创建的计算机组与使用日志搜索创建的搜索相同。

有关使用日志搜索 API 创建计算机组的详细信息，请参阅 [Log Analytics 日志搜索 REST API 中的计算机组](log-analytics-log-search-api.md#computer-groups)。

### <a name="active-directory"></a>Active Directory
在将 Log Analytics 配置为导入 Active Directory 组成员身份时，它将使用 OMS 代理分析任何加入域的计算机的组成员身份。  在 Log Analytics 中为 Active Directory 中的每个安全组创建一个计算机组，并且会将每台计算机添加到与其所属安全组对应的计算机组。  此成员身份每 4 小时持续更新一次。  

将 Log Analytics 配置为从 Log Analytics“**设置**”中的“**计算机组**”菜单导入 Active Directory 安全组。  选择“自动化”，并选择“从计算机导入 Active Directory 组成员身份”。  无需进一步的配置。

![Active Directory 中的计算机组](media/log-analytics-computer-groups/configure-activedirectory.png)

导入组后时，菜单将列出检测到组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

### <a name="windows-server-update-service"></a>Windows Server Update Service
在将 Log Analytics 配置为导入 WSUS 组成员身份时，它将使用 OMS 代理分析任何计算机的目标组成员身份。  如果使用客户端目标，连接到 OMS 并且属于任意 WSUS 目标组的所有计算机的组成员身份会导入到 Log Analytics。 如果使用服务器端目标，OMS 代理应安装在 WSUS 服务器上，以使组成员身份信息导入到 OMS。  此成员身份每 4 小时持续更新一次。 

将 Log Analytics 配置为从 Log Analytics“**设置**”中的“**计算机组**”菜单导入 Active Directory 安全组。  依次选择“Active Directory”和“从计算机导入 Active Directory 组成员身份”。  无需进一步的配置。

![Active Directory 中的计算机组](media/log-analytics-computer-groups/configure-wsus.png)

导入组后时，菜单将列出检测到组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

## <a name="managing-computer-groups"></a>管理计算机组
可以查看通过日志搜索或者通过 Log Analytics“设置”中“计算机组”菜单的日志搜索 API 中创建的计算机组。  单击“删除”列中的“x”可删除计算机组。  单击组的“查看成员”图标可运行返回其成员的组的日志搜索。 

![已保存的计算机组](media/log-analytics-computer-groups/configure-saved.png)

若要修改组，创建具有相同**类别**和**名称**的新组，以覆盖原来的组。

## <a name="using-a-computer-group-in-a-log-search"></a>在日志搜索中使用计算机组
使用以下语法来指代日志搜索中的计算机组。  指定“类别”为可选，并且仅当在不同类别中具有同名的计算机组时才需要。 

    $ComputerGroups[Category: Name]

运行搜索时，首先解析搜索中包含的任何计算机组的成员。  如果组基于日志搜索，那么在执行顶级日志搜索前会运行搜索以返回组成员。

计算机组通常在日志搜索中与 IN 子句一起使用，如以下示例所示：

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，通过将计算机的别名视为函数以在查询中使用计算机组，如以下示例所示：
> 
>  `UpdateSummary | where Computer in (mycomputergroup)`

## <a name="computer-group-records"></a>计算机组记录
会在通过 Active Directory 或 WSUS 创建的每个计算机组成员身份的 OMS 存储库中创建记录。  这些记录的类型为 **ComputerGroup**，并且具有下表中的属性。  不会基于日志搜索为计算机组创建记录。

| 属性 | 说明 |
|:--- |:--- |
| 类型 |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| 计算机 |成员计算机的名称。 |
| 组 |组名称。 |
| GroupFullName |包括源和源名称的组的完整路径。 |
| GroupSource |从中收集组的源。 <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |从中收集组的源名称。  对于 Active Directory，这是域名。 |
| ManagementGroupName |SCOM 代理的管理组名称。  对于其他代理，这是 AOI-\<工作区 ID\> |
| TimeGenerated |创建或更新计算机组的日期和时间。 |

## <a name="next-steps"></a>后续步骤
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。  


