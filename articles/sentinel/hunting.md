---
title: Azure Sentinel 中的搜寻功能 |Microsoft Docs
description: 本文介绍如何使用 Azure Sentinel 搜寻功能。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 54ddf6818b95a4037188ab222501ddfa69b28149
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587892"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>利用 Azure Sentinel 寻找威胁

如果你是一位调查人员，想要主动应对安全威胁，Azure 可以通过强大的搜索和查询工具来寻找组织数据源的安全威胁。 但您的系统和安全设备生成的数据山上很难分析和筛选成有意义的事件。 为了帮助安全分析师主动了解你的安全应用程序未检测到的新异常，Azure Sentinel 的内置搜索查询可指导你请求正确的问题，以便在你的网络上发现已有的数据中的问题。 

例如，一个内置查询提供有关在你的基础结构上运行的最罕见的进程的数据-你不希望每次运行时都出现警报，但这可能是完全合法的，但你可能想要查看ere 是异常的。 



使用 Azure Sentinel 搜寻，可以利用以下功能：

- 内置查询：若要开始使用，起始页提供了预加载的查询示例，这些示例旨在帮助你开始使用表和查询语言。 这些内置的搜索查询是由 Microsoft 安全研究人员不断地开发的，添加新的查询，并微调现有查询，为你提供查找新检测并找出开始查找的位置的入口点新攻击的开头。 

- 使用 IntelliSense 的强大查询语言：构建在查询语言之上，为你提供在下一级别中进行搜寻所需的灵活性。

- 创建你自己的书签：在搜索过程中，你可能会遇到匹配或发现、仪表板或活动，它们看起来异常或可疑。 为了标记这些项以便以后可以返回到这些项，请使用书签功能。 书签允许稍后保存项，用于创建要调查的事件。 有关书签的详细信息，请参阅[在搜寻中使用书签](hunting.md)。
- 使用笔记本自动进行调查：笔记本类似于循序渐进的行动手册，你可以构建这些行动手册来演练调查和查寻的步骤。  笔记本封装可重复使用的操作手册中的所有搜寻步骤，可与组织中的其他人共享。 
- 查询存储的数据：可在要查询的表中访问数据。 例如，您可以查询进程创建、DNS 事件和许多其他事件类型。

- 社区链接：利用更多社区的强大功能查找其他查询和数据源。
 
## <a name="get-started-hunting"></a>开始搜寻

1. 在 Azure Sentinel 门户中，单击 "**搜寻**"。
  ![Azure Sentinel 开始搜寻](media/tutorial-hunting/hunting-start.png)

2. 打开**搜寻**页面时，所有搜寻查询都显示在一个表中。 此表列出了由 Microsoft 的安全分析师团队编写的所有查询，以及你创建或修改的任何其他查询。 每个查询都提供了它 hunts 的内容以及它在其上运行的数据类型的说明。 这些模板按其各种策略分组，右侧的图标将对威胁类型进行分类，例如，初始访问、暂留和渗透。 您可以使用任意字段筛选这些搜寻查询模板。 可以将任何查询保存到收藏夹。 通过将查询保存到收藏夹，每次访问该**搜寻**页面时都会自动运行该查询。 你可以创建自己的搜寻查询或克隆并自定义现有的搜寻查询模板。 
 
2. 单击 "搜寻查询详细信息" 页中的 "**运行查询**"，无需离开搜寻页面即可运行任何查询。  匹配项的数目将显示在表中。 查看搜寻查询及其匹配项的列表。 检查与匹配项关联的 kill 链中的哪个阶段。

3. 在 "查询详细信息" 窗格中快速查看基础查询，或单击 "**查看查询结果**" 以在 Log Analytics 中打开该查询。 在底部，查看查询的匹配项。

4.  单击该行，然后选择 "**添加书签**" 来添加要调查的行-您可以对看起来可疑的任何内容执行此操作。 

5. 然后，返回**到主搜索**页面，然后单击 "**书签**" 选项卡以查看所有可疑活动。 

6. 选择一个书签，然后单击 "**调查**" 以打开调查体验。 可以筛选书签。 例如，如果你正在调查市场活动，则可以为市场活动创建标记，然后根据该市场活动筛选所有书签。

1. 在您发现了哪个搜寻查询为可能的攻击提供了较高价值的见解后，您还可以根据您的查询创建自定义检测规则，并将这些见解作为警报显示在安全事件响应程序中。

 

## <a name="query-language"></a>查询语言 

Azure Sentinel 中的搜寻基于 Kusto 查询语言。 有关查询语言和支持的运算符的详细信息，请参阅[查询语言参考](https://docs.loganalytics.io/docs/Language-Reference/)。

## <a name="public-hunting-query-github-repository"></a>公共搜寻查询 GitHub 存储库

查看[搜寻查询存储库](https://github.com/Azure/Orion)。 参与和使用客户共享的示例查询。

 

## <a name="sample-query"></a>示例查询

典型查询以表名称开头，后面是由 \|分隔的一系列运算符。

在上面的示例中，从表名称 SecurityEvent 开始，并根据需要添加了管道元素。

1. 定义时间筛选器，以便仅查看前七天的记录。

2. 在查询中添加筛选器，以仅显示事件 ID 4688。

3. 在命令行的查询中添加筛选器，以仅包含 cscript.exe 的实例。

4. 仅投影感兴趣的列，并将结果限制为1000，然后单击 "**运行查询**"。
5. 单击绿色三角形并运行查询。 您可以测试该查询，并运行它以查找异常行为。

## <a name="useful-operators"></a>有用的运算符

查询语言功能强大并且具有许多可用运算符，这里列出了一些有用的运算符：

**where** -将表筛选到满足谓词的行的子集。

**汇总**-生成聚合输入表内容的表。

**联接**-合并两个表中的行，通过匹配每个表中指定列的值形成一个新表。

**count** -返回输入记录集中的记录数。

**top** -返回按指定列排序的前 N 个记录。

**limit** -返回指定的行数。

**项目**-选择要包含、重命名或删除的列，并插入新的计算列。

**扩展**-创建计算列并将其追加到结果集。

**makeset** -返回 Expr 在组中采用的非重复值集的动态（JSON）数组

**find** -查找匹配一组表中的谓词的行。

## <a name="save-a-query"></a>保存查询

你可以创建或修改查询并将其另存为你自己的查询，或与同一租户中的用户共享。

   ![保存查询](./media/tutorial-hunting/save-query.png)

创建新的搜寻查询：

1. 单击 "**新建查询**"，然后选择 "**保存**"。
2. 填写所有空字段，然后选择 "**保存**"。

   ![新建查询](./media/tutorial-hunting/new-query.png)

克隆和修改现有搜寻查询：

1. 选择要修改的表中的搜寻查询。
2. 选择要修改的查询行中的省略号（...），然后选择 "**克隆查询**"。

   ![克隆查询](./media/tutorial-hunting/clone-query.png)
 

3. 修改查询，然后选择 "**创建**"。

   ![自定义查询](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Azure Sentinel 运行搜寻调查。 要详细了解 Azure Sentinel，请参阅以下文章：


- [使用笔记本运行自动搜寻活动](notebooks.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
