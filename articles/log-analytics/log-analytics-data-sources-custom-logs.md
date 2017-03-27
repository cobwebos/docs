---
title: "收集 OMS Log Analytics 中的自定义日志 | Microsoft Docs"
description: "Log Analytics 可以从 Windows 和 Linux 计算机上的文本文件中收集事件。  本文介绍如何定义新的自定义日志，以及这些日志在 OMS 存储库中创建的记录的详细信息。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: a9c70810c4f731b2d8b395873fa6b94db78306aa


---
# <a name="custom-logs-in-log-analytics"></a>Log Analytics 中的自定义日志
Log Analytics 中的自定义日志数据源可以从 Windows 和 Linux 计算机上的文本文件中收集事件。 许多应用程序将信息记录到文本文件，而不是标准日志记录服务（例如 Windows 事件日志或 Syslog）。  收集后，使用 Log Analytics 的[自定义字段](log-analytics-custom-fields.md)功能可将日志中的每个记录解析到各个字段中。

![自定义日志收集](media/log-analytics-data-sources-custom-logs/overview.png)

要收集的日志文件必须符合以下条件。

* 日志每行必须具有单个条目，或在每个条目的开头使用时间戳匹配以下格式之一。
  
    YYYY-MM-DD HH:MM:SS <br>
    M/D/YYYY HH:MM:SS AM/PM <br>
    Mon DD,YYYY HH:MM:SS
* 日志文件不允许循环更新，即不允许文件被新条目覆盖。 

## <a name="defining-a-custom-log"></a>定义自定义日志
使用以下步骤定义自定义日志文件。  滚动到本文末尾，查看添加自定义日志的演示示例。

### <a name="step-1-open-the-custom-log-wizard"></a>步骤 1。 打开自定义日志向导
自定义日志向导在 OMS 门户中运行，可以定义要收集的新自定义日志。

1. 在 OMS 门户中，转至“**设置**”。
2. 依次单击“**数据**”、“**自定义日志**”。
3. 默认情况下，所有配置更改均会自动推送到所有代理。  对于 Linux 代理，配置文件发送到 Fluentd 数据收集器。  如果想在每个 Linux 代理上手动修改此文件，则取消选中“*将下面的配置应用到我的 Linux 计算机*”框即可。
4. 单击“**添加**”以打开自定义日志向导。

### <a name="step-2-upload-and-parse-a-sample-log"></a>步骤 2. 上载和分析示例日志
首先上载自定义日志示例。  该向导将分析并显示此文件中的条目，以便进行验证。  Log Analytics 将使用你指定的分隔符标识每个记录。

**换行**是默认分隔符，用于每行具有单个条目的日志文件。  如果行以可用的日期和时间格式之一开头，则可以指定**时间戳**分隔符，它可支持跨多行的条目。 

如果使用时间戳分隔符，则存储在 OMS 中的每个记录的 TimeGenerated 属性将填充为在日志文件中为相应条目指定的日期/时间。  如果使用换行分隔符，则 TimeGenerated 将填充为 Log Analytics 收集此条目的日期和时间。 

> [!NOTE]
> Log Analytics 当前将时间戳分隔符用作 UTC 来处理从日志收集的日期/时间。  这一做法将很快更改为使用代理上的时区。 
> 
> 

1. 单击“**浏览**”并浏览到示例文件。  请注意，此按钮在某些浏览器中可能标记为“**选择文件**”。
2. 单击“资源组名称” 的 Azure 数据工厂。 
3. 自定义日志向导将上载文件，并列出其标识的记录。
4. 更改用于标识新记录的分隔符，选择最能标识日志文件中记录的分隔符。
5. 单击“资源组名称” 的 Azure 数据工厂。

### <a name="step-3-add-log-collection-paths"></a>步骤 3. 添加日志集合路径
你必须在可查找自定义日志的代理上定义一个或多个路径。  可以提供日志文件的特定路径和名称，也可以使用通配符为名称指定路径。  这样可支持每天或当某个文件达到一定大小时创建新文件的应用程序。  此外还可以为单个日志文件提供多个路径。

例如，应用程序可能会每天创建日志文件，将日期包括在如 log20100316.txt 的名称中。 此类日志的模式可能是 *log\*.txt*，它将按照应用程序命名方案应用于任何日志文件。

下表提供了指定不同日志文件的有效模式示例。 

| 说明 | 路径 |
|:--- |:--- |
| Windows 代理的 *C:\Logs* 中具有 .txt 扩展名的所有文件 |C:\Logs\\\*.txt |
| Windows 代理上的 *C:\Logs* 中具有以 log 开头的名称和 .txt 扩展名的所有文件 |C:\Logs\log\*.txt |
| Linux 代理上的 */var/log/audit* 中具有 .txt 扩展名的所有文件 |/var/log/audit/*.txt |
| Linux 代理上的 */var/log/audit* 中以 log 开头的名称和 .txt 扩展名的所有文件 |/var/log/audit/log\*.txt |

1. 选择 Windows 或 Linux 以指定正在添加的路径格式。
2. 键入路径，然后单击 **+** 按钮。
3. 为任何其他路径重复此步骤。

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>步骤 4。 提供日志名称及描述
你指定的名称将用于日志类型，如上所述。  它将始终以 _CL 结尾，以将其与自定义日志区分开来。

1. 为日志键入名称。  系统将自动提供 **\_CL** 后缀。
2. 添加可选**说明**。
3. 单击“**下一步**”以保存自定义日志的定义。

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>步骤 5。 验证是否正在收集自定义日志
新自定义日志的初始数据可能需要一个小时才能出现在 Log Analytics 中。  从定义自定义日志起，即开始从你指定的路径中找到的日志收集条目。  它不会在自定义日志创建过程中保留上载的条目，但是它将收集它找到的日志文件中的现有条目。

Log Analytics 开始从自定义日志收集后，它的记录将可用于日志搜索。  将为自定义日志指定的名称用作查询中的**类型**。

> [!NOTE]
> 如果搜索缺少 RawData 属性，则可能需要关闭并重新打开浏览器。
> 
> 

### <a name="step-6-parse-the-custom-log-entries"></a>步骤 6. 分析自定义日志条目
全部日志条目将存储在名为 **RawData** 的单个属性中。  你很可能希望将每个条目中的信息的不同部分分离到存储在记录中的单个属性中。  使用 Log Analytics 的[自定义字段](log-analytics-custom-fields.md)功能执行此操作。

有关分析自定义日志条目的详细步骤未在此处提供。  请参考[自定义字段](log-analytics-custom-fields.md)文档，获取此信息。

## <a name="disabling-a-custom-log"></a>禁用自定义日志
一旦已创建自定义日志定义就无法将其删除，但通过删除它的所有集合路径可将其禁用。

1. 在 OMS 门户中，转至“**设置**”。
2. 依次单击“**数据**”、“**自定义日志**”。
3. 单击自定义日志定义旁边的“**详细信息**”来禁用。
4. 删除自定义日志定义的全部集合路径。

## <a name="data-collection"></a>数据收集
Log Analytics 约每隔 5 分钟就会从每个自定义日志中收集新条目。  代理将在从中进行收集的每个日志文件中记录其位置。  如果代理在一段时间内处于脱机状态，则 Log Analytics 将从其上次脱机的位置收集条目，即使这些条目是在代理脱机期间创建的。

日志条目的全部内容写入到名为 **RawData** 的单个属性中。  创建自定义日志后，通过定义[自定义字段](log-analytics-custom-fields.md)，你可以将此解析到可单独分析和搜索的多个属性中。

## <a name="custom-log-record-properties"></a>自定义日志记录属性
自定义日志在下表中记录具有你提供的日志名称的类型及其属性。

| 属性 | 说明 |
|:--- |:--- |
| TimeGenerated |Log Analytics 收集记录的日期和时间。  如果日志使用基于时间的分隔符，则此时间是从条目中收集的时间。 |
| SourceSystem |从中收集记录的代理类型。 <br> OpsManager – Windows 代理，直接连接或通过 SCOM 连接 <br> Linux - 所有 Linux 代理 |
| RawData |收集的条目的完整文本。 |
| ManagementGroupName |SCOM 代理的管理组名称。  对于其他代理，这是 AOI-\<工作区 ID\> |

## <a name="log-searches-with-custom-log-records"></a>使用自定义日志记录进行日志搜索
与任何其他数据源中的记录一样，自定义日志的记录存储在 OMS 存储库中。  它们将具有与定义日志时提供的名称匹配的类型，因此可以在搜索中使用类型属性来检索从特定的日志收集的记录。

下表提供了从自定义日志检索记录的不同日志搜索的示例。

| 查询 | 说明 |
|:--- |:--- |
| Type=MyApp_CL |名为 MyApp_CL 的自定义日志的所有事件。 |
| Type=MyApp_CL Severity_CF=error |名为 *Severity_CF* 的自定义字段中，名为 MyApp_CL、值为 *error* 的自定义日志的所有事件。 |

## <a name="sample-walkthrough-of-adding-a-custom-log"></a>添加自定义日志的演示示例
以下部分是自定义日志创建演示示例。  收集的示例日志在每行具有单个条目，以日期和时间开头，然后是逗号分隔的代码、状态和消息字段。  几个示例条目如下所示。

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>上载和分析示例日志
我们提供其中一个日志文件，然后可以看到它将收集的事件。  在这种情况下，换行是有效的分隔符。  如果日志中的单个条目无法跨过多行，则需要使用时间戳分隔符。

![上载和分析示例日志](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>添加日志集合路径
日志文件将位于 *C:\MyApp\Logs*。  每天将创建一个新文件，名称为包括日期的 *appYYYYMMDD.log* 模式。  此日志的有效模式是 *C:\MyApp\Logs\\\*.log*。

![日志集合路径](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>提供日志名称及描述
我们使用 *MyApp_CL* 名称，然后键入**说明**。

![日志名称](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>验证是否正在收集自定义日志
我们使用 *Type=MyApp_CL* 的查询，以从收集的日志返回所有记录。

![没有自定义字段的日志查询](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>分析自定义日志条目
我们使用自定义字段来定义 *EventTime*、*Code*、*Status* 和 *Message* 字段，这样就可以看到由查询返回的记录中的差异。

![具有自定义字段的日志查询](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>后续步骤
* 使用[自定义字段](log-analytics-custom-fields.md)来将自定义日志中的条目解析为单个字段。
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 




<!--HONumber=Jan17_HO4-->


