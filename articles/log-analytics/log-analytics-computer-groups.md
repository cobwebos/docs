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
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 17a59a38b6a445a7f42df171a711669f95fc84c2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics 日志搜索中的计算机组

Log Analytics 中的计算机组允许为一组特定的计算机设定[日志搜索](log-analytics-log-search-new.md)的范围。  每个组使用定义的查询或通过从不同源导入组填充计算机。  当日志搜索中包括组时，结果仅限于与组中的计算机匹配的记录。

## <a name="creating-a-computer-group"></a>创建计算机组
可以使用下表中的任一方法在 Log Analytics 中创建计算机组。  在以下各节中提供了每个方法的详细信息。 

| 方法 | 说明 |
|:--- |:--- |
| 日志搜索 |创建将返回计算机列表的日志搜索。 |
| 日志搜索 API |使用日志搜索 API 基于日志搜索结果以编程方式创建计算机组。 |
| Active Directory |自动扫描属于 Active Directory 域成员的任何代理计算机的组成员身份，并在 Log Analytics 中为每个安全组创建一个组。 |
| 配置管理器 | 从 System Center Configuration Manager 中导入集合并在 Log Analytics 中为每个集合创建一个组。 |
| Windows Server Update Services |为目标组自动扫描 WSUS 服务器或客户端，并在 Log Analytics 中为每个组创建一个组。 |

### <a name="log-search"></a>日志搜索
从日志搜索创建的计算机组包含由你定义的查询返回的所有计算机。  每次使用计算机组时都会运行此查询，以反映创建组之后所做的任何更改。  

可以将任何查询用于计算机组，但它必须通过使用 `distinct Computer` 返回一组不同的计算机。  下面是可以用于计算机组的一个典型示例搜索。

    Heartbeat | where Computer contains "srv" | distinct Computer

下表介绍了用于定义计算机组的属性。

| 属性 | 说明 |
|:---|:---|
| 显示名称   | 要在门户中显示的搜索的名称。 |
| 类别       | 在门户中对搜索进行组织的类别。 |
| 查询          | 针对计算机组的查询。 |
| 函数别名 | 查询中用于标识计算机组的唯一别名。 |

在 Azure 门户中，可以使用以下过程从日志搜索创建计算机组。

2. 打开“日志搜索”，然后单击屏幕顶部的“保存的搜索”。
3. 单击“添加”并为计算机组的每个属性提供值。
4. 选择“将此查询保存为计算机组”并单击“确定”。


在 OMS 门户中，可以使用以下过程从日志搜索创建计算机组。

1. 打开“日志搜索”并为计算机组创建日志搜索。  
2. 单击屏幕顶部的“保存”按钮。
3. 选择“是”将此查询另存为计算机组。
5. 为计算机组的每个属性提供值。 


>[!NOTE]
> 如果工作区仍然使用[旧的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则可以使用相同的过程来创建计算机组，但必须使用旧查询语言的语法。


### <a name="log-search-api"></a>日志搜索 API
使用日志搜索 API 创建的计算机组与使用日志搜索创建的搜索相同。  有关使用日志搜索 API 创建计算机组的详细信息，请参阅 [Log Analytics 日志搜索 REST API 中的计算机组](log-analytics-log-search-api.md#computer-groups)。

### <a name="active-directory"></a>Active Directory
在将 Log Analytics 配置为导入 Active Directory 组成员身份时，它将使用 OMS 代理分析任何加入域的计算机的组成员身份。  在 Log Analytics 中为 Active Directory 中的每个安全组创建一个计算机组，并且会将每台计算机添加到与其所属安全组对应的计算机组。  此成员身份每 4 小时持续更新一次。  

在 Azure 门户中，从 Log Analytics“高级设置”中配置 Log Analytics 以导入 Active Directory 安全组。  依次选择“计算机组”、“Active Directory”和“从计算机导入 Active Directory 组成员身份”。  无需进一步的配置。

![Active Directory 中的计算机组](media/log-analytics-computer-groups/configure-activedirectory.png)

导入组后时，菜单将列出检测到组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

### <a name="windows-server-update-service"></a>Windows Server Update Service
在将 Log Analytics 配置为导入 WSUS 组成员身份时，它将使用 OMS 代理分析任何计算机的目标组成员身份。  如果使用客户端目标，连接到 OMS 并且属于任意 WSUS 目标组的所有计算机的组成员身份会导入到 Log Analytics。 如果使用服务器端目标，OMS 代理应安装在 WSUS 服务器上，以使组成员身份信息导入到 OMS。  此成员身份每 4 小时持续更新一次。 

在 Azure 门户中，从 Log Analytics“高级设置”中配置 Log Analytics 以导入 WSUS 组。  依次选择“计算机组”、“WSUS”和“导入 WSUS 组成员身份”。  无需进一步的配置。

![WSUS 中的计算机组](media/log-analytics-computer-groups/configure-wsus.png)

导入组后时，菜单将列出检测到组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
当配置 Log Analytics 来导入 Configuration Manager 集合成员身份时，它将为每个集合创建计算机组。  每隔 3 小时会检索一次集合成员身份信息，以使计算机组保持最新。 

必须[将 Configuration Manager 连接到 Log Analytics](log-analytics-sccm.md)，然后才能导入 Configuration Manager 集合。  然后，可以从 Azure 门户中的 Log Analytics“高级设置”配置导入。  依次选择“计算机组”、“SCCM”、“导入 Configuration Manager 集合成员身份”。  无需进一步的配置。

![SCCM 中的计算机组](media/log-analytics-computer-groups/configure-sccm.png)

导入集合后，菜单将列出具有检测到的组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

## <a name="managing-computer-groups"></a>管理计算机组
可以查看在 Azure 门户中通过日志搜索或通过 Log Analytics“高级设置”中的日志搜索 API 创建的计算机组。  选择“计算机组”，然后选择“已保存的组”。  

单击“删除”列中的“x”可删除计算机组。  单击组的“查看成员”图标可运行返回其成员的组的日志搜索。  无法修改计算机组，而是必须删除该组，然后使用修改的设置重新创建它。

![已保存的计算机组](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>在日志搜索中使用计算机组
通过将计算机组的别名视为函数，可在查询中使用从日志搜索中创建的计算机组，通常使用以下语法：

  `Table | where Computer in (ComputerGroup)`

例如，可以使用以下语法仅返回名为 mycomputergroup 的计算机组中的计算机的 UpdateSummary 记录。
 
  `UpdateSummary | where Computer in (mycomputergroup)`


导入的计算机组及其包含的计算机存储在 ComputerGroup 表中。  例如，以下查询会从 Active Directory 返回域计算机组中的计算机列表。 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

以下查询将仅针对域计算机中的计算机返回 UpdateSummary 记录。

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```



  

>[!NOTE]
> 如果工作区仍然使用[旧的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则在日志搜索中请使用以下语法来引用计算机组。  指定“类别”是可选的，并且仅当在不同类别中具有同名的计算机组时才需要。 
>
>    `$ComputerGroups[Category: Name]`
>
>计算机组通常在日志搜索中与 IN 子句一起使用，如以下示例所示：
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



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

