---
title: Azure Monitor 日志查询中的计算机组 | Microsoft Docs
description: 使用 Azure Monitor 中的计算机组可为一组特定的计算机设定日志查询的范围。  本文介绍用于创建计算机组的不同方法以及如何在日志查询中使用这些方法。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 9ef0f2810252b73921fc0a72f2e523262c760bab
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932660"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Azure Monitor 日志查询中的计算机组
使用 Azure Monitor 中的计算机组可为一组特定的计算机设定[日志查询](../log-query/log-query-overview.md)的范围。  每个组使用定义的查询或通过从不同源导入组填充计算机。  当日志查询中包括组时，结果仅限于与组中的计算机匹配的记录。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>创建计算机组
可以使用下表中的任一方法在 Azure Monitor 中创建计算机组。  在以下各节中提供了每个方法的详细信息。 

| 方法 | 描述 |
|:--- |:--- |
| 日志查询 |创建将返回计算机列表的日志查询。 |
| 日志搜索 API |使用日志搜索 API 基于日志查询结果以编程方式创建计算机组。 |
| Active Directory |自动扫描属于 Active Directory 域成员的任何代理计算机的组成员身份，并在 Azure Monitor 中为每个安全组创建一个组。 （仅限 Windows 计算机）|
| Configuration Manager | 从 System Center Configuration Manager 中导入集合并在 Azure Monitor 中为每个集合创建一个组。 |
| Windows Server Update Services |为目标组自动扫描 WSUS 服务器或客户端，并在 Azure Monitor 中为每个组创建一个组。 |

### <a name="log-query"></a>日志查询
从日志查询创建的计算机组包含由你定义的查询返回的所有计算机。  每次使用计算机组时都会运行此查询，以反映创建组之后所做的任何更改。  

可以将任何查询用于计算机组，但它必须通过使用 `distinct Computer` 返回一组不同的计算机。  下面是可以用于计算机组的一个典型示例查询。

    Heartbeat | where Computer contains "srv" | distinct Computer

在 Azure 门户中，可以使用以下过程从日志搜索创建计算机组。

1. 在 Azure 门户中，单击“Azure Monitor”菜单中的“日志”。
1. 创建并运行一个查询，以返回组中所需的计算机。
1. 单击屏幕顶部的“保存”。
1. 将“另存为”更改为“函数”，然后选择“将此查询另存为计算机组”。
1. 为表中所述计算机组的每个属性提供值，然后单击“保存”。

下表介绍了用于定义计算机组的属性。

| properties | 描述 |
|:---|:---|
| 名称   | 要在门户中显示的查询名称。 |
| 函数别名 | 查询中用于标识计算机组的唯一别名。 |
| 类别       | 用于在门户中对查询进行组织的类别。 |


### <a name="active-directory"></a>Active Directory
将 Azure Monitor 配置为导入 Active Directory 组成员身份时，它将使用 Log Analytics 代理分析任何加入域的 Windows 计算机的组成员身份。  系统会在 Azure Monitor 中为 Active Directory 中的每个安全组创建一个计算机组，并将每台 Windows 计算机添加到与其所属安全组对应的计算机组。  此成员身份每 4 小时持续更新一次。  

> [!NOTE]
> 导入的 Active Directory 组仅包含 Windows 计算机。

通过 Azure 门户上 Log Analytics 工作区的“高级设置”配置 Azure Monitor，以导入 Active Directory 安全组。  依次选择“计算机组”、“Active Directory”和“从计算机导入 Active Directory 组成员身份”。  无需进一步配置。

![Active Directory 中的计算机组](media/computer-groups/configure-activedirectory.png)

导入组后时，菜单将列出检测到组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

### <a name="windows-server-update-service"></a>Windows Server Update Service
将 Azure Monitor 配置为导入 WSUS 组成员身份时，它将使用 Log Analytics 代理分析任何计算机的目标组成员身份。  如果使用客户端目标，连接到 Azure Monitor 并且属于任意 WSUS 目标组的所有计算机的组成员身份会导入到 Azure Monitor。 如果使用服务器端目标，Log Analytics 代理应安装在 WSUS 服务器上，以便将组成员身份信息导入到 Azure Monitor。  此成员身份每 4 小时持续更新一次。 

通过 Azure 门户上 Log Analytics 工作区的“高级设置”配置 Azure Monitor，以导入 WSUS 组。  依次选择“计算机组”、“WSUS”和“导入 WSUS 组成员身份”。  无需进一步配置。

![WSUS 中的计算机组](media/computer-groups/configure-wsus.png)

导入组后时，菜单将列出检测到组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
当配置 Azure Monitor 来导入 Configuration Manager 集合成员身份时，它将为每个集合创建计算机组。  每隔 3 小时会检索一次集合成员身份信息，以使计算机组保持最新。 

必须[将 Configuration Manager 连接到 Azure Monitor](collect-sccm.md) 才能导入 Configuration Manager 集合。  

![SCCM 中的计算机组](media/computer-groups/configure-sccm.png)

导入集合后，菜单将列出具有检测到的组成员身份的计算机数以及导入的组数。  可以单击任一链接以返回包含此信息的 **ComputerGroup** 记录。

## <a name="managing-computer-groups"></a>管理计算机组
可以查看通过日志查询或通过 Azure 门户上 Log Analytics 工作区“高级设置”中的日志搜索 API 创建的计算机组。  选择“计算机组”，然后选择“已保存的组”。  

单击“删除”列中的“x”可删除计算机组。  单击组的“查看成员”图标可运行返回其成员的组的日志搜索。  无法修改计算机组，而是必须删除该组，然后使用修改的设置重新创建它。

![已保存的计算机组](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>在日志查询中使用计算机组
通过将计算机组的别名视为函数，可在查询中使用从日志查询创建的计算机组，通常使用以下语法：

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




## <a name="computer-group-records"></a>计算机组记录
会在通过 Active Directory 或 WSUS 创建的每个计算机组成员身份的 Log Analytics 工作区中创建记录。  这些记录的类型为 **ComputerGroup**，并且具有下表中的属性。  不会基于日志查询为计算机组创建记录。

| properties | 描述 |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |成员计算机的名称。 |
| `Group` |组名称。 |
| `GroupFullName` |包括源和源名称的组的完整路径。 |
| `GroupSource` |从中收集组的源。 <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |从中收集组的源名称。  对于 Active Directory，这是域名。 |
| `ManagementGroupName` |SCOM 代理的管理组名称。  对于其他代理，这是 AOI-\<工作区 ID\> |
| `TimeGenerated` |创建或更新计算机组的日期和时间。 |

## <a name="next-steps"></a>后续步骤
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。  

