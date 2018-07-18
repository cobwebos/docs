---
title: 在 Azure Log Analytics 中收集自定义日志 | Microsoft Docs
description: Log Analytics 可以从 Windows 和 Linux 计算机上的文本文件中收集事件。  本文介绍如何定义新的自定义日志，以及这些日志在 Log Analytics 工作区中创建的记录的详细信息。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: c533d54a804ccc624246f54940ccf269361cdd7a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128667"
---
# <a name="custom-logs-in-log-analytics"></a>Log Analytics 中的自定义日志
Log Analytics 中的自定义日志数据源可以从 Windows 和 Linux 计算机上的文本文件中收集事件。 许多应用程序将信息记录到文本文件，而不是标准日志记录服务（例如 Windows 事件日志或 Syslog）。  收集后，使用 Log Analytics 的[自定义字段](log-analytics-custom-fields.md)功能可将日志中的每个记录解析到各个字段中。

![自定义日志收集](media/log-analytics-data-sources-custom-logs/overview.png)

要收集的日志文件必须符合以下条件。

- 每行日志必须有单个条目，或在每个条目的开头使用时间戳，时间戳可匹配以下任一种格式。

    YYYY-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Mon DD, YYYY HH:MM:SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- 日志文件不允许会以新条目覆盖文件的循环日志记录或日志轮换。
- 日志文件必须使用 ASCII 或 UTF-8 编码。  不支持其他格式，如 UTF-16。

>[!NOTE]
>如果日志文件中存在重复项，Log Analytics 将收集这些项。  但是，搜索结果将不一致，其中过滤结果显示的事件比结果计数更多。  重要的是，你要验证日志以确定创建它的应用程序是否是导致该行为的原因，并在可能的情况下对其进行处理，然后再创建自定义日志收集定义。  
>
  
## <a name="defining-a-custom-log"></a>定义自定义日志
使用以下步骤定义自定义日志文件。  请在本文末尾查看添加自定义日志的演示示例。

### <a name="step-1-enable-custom-logs-preview"></a>步骤 1。 启用自定义日志预览
1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 订阅窗格中选择一个工作区，再选择“OMS 门户”磁贴。<br><br> ![“日志搜索”按钮](media/log-analytics-data-sources-custom-logs/azure-portal-01.png)<br><br> 
3. 在重定向到 OMS 门户后，单击页面右上角的“设置”磁贴。<br><br> ![OMS 门户“设置”选项](media/log-analytics-data-sources-custom-logs/oms-portal-settings-option.png)<br><br> 
4. 从“设置”页面，选择“预览功能”，并在页面上选择“启用”自定义日志。    

### <a name="step-2-open-the-custom-log-wizard"></a>步骤 2. 打开自定义日志向导
自定义日志向导在 Azure 门户中运行，使用它可以定义要收集的新自定义日志。

1. 在 Azure 门户中，选择“Log Analytics”> 你的工作区 >“高级设置”。
2. 单击“数据” > “自定义日志”。
3. 默认情况下，所有配置更改均会自动推送到所有代理。  对于 Linux 代理，配置文件会发送到 Fluentd 数据收集器。  如果想在每个 Linux 代理上手动修改此文件，则取消选中“将下面的配置应用到我的 Linux 计算机”框即可。
4. 单击“添加+”，打开自定义日志向导。

### <a name="step-3-upload-and-parse-a-sample-log"></a>步骤 3. 上传和分析示例日志
首先上载自定义日志示例。  该向导将分析并显示此文件中的条目，以便进行验证。  Log Analytics 将使用指定的分隔符标识每个记录。

“换行”是默认分隔符，用于每行包含单个条目的日志文件。  如果行以日期和时间开头且格式符合要求，则可以指定“时间戳”分隔符，它可支持跨多行的条目。

如果使用时间戳分隔符，则存储在 Log Analytics 中的每个记录的 TimeGenerated 属性将填充为日志文件中为该条目指定的日期/时间。  如果使用换行分隔符，则 TimeGenerated 将填充为 Log Analytics 收集此条目的日期和时间。


1. 单击“浏览”，浏览到示例文件。  请注意，此按钮在某些浏览器中可能标记为“选择文件”。
2. 单击“下一步”。
3. 自定义日志向导将上传文件，并列出其标识的记录。
4. 更改用于标识新记录的分隔符。根据日志文件中的记录，选择标识效果最好的分隔符。
5. 单击“下一步”。

### <a name="step-4-add-log-collection-paths"></a>步骤 4. 添加日志集合路径
必须在可查找自定义日志的代理上定义一个或多个路径；  可以提供日志文件的特定路径和名称，也可以使用通配符为名称指定路径。  这样，应用程序就可以每天创建新文件，或者在某个文件达到一定大小时创建新文件。  还可以为单个日志文件提供多个路径。

例如，应用程序可能会每天创建日志文件，其日期包含在名称中，例如 log20100316.txt。 此类日志的模式可能是 *log\*.txt*，它将按照应用程序命名方案应用于任何日志文件。

下表提供了有效模式示例，用来指定不同的日志文件。 

| 说明 | 路径 |
|:--- |:--- |
| Windows 代理上的 *C:\Logs* 中带 .txt 扩展名的所有文件 |C:\Logs\\\*.txt |
| Windows 代理上的 *C:\Logs* 中名称以 log 开头并带 .txt 扩展名的所有文件 |C:\Logs\log\*.txt |
| Linux 代理上的 */var/log/audit* 中带 .txt 扩展名的所有文件 |/var/log/audit/*.txt |
| Linux 代理上的 */var/log/audit* 中名称以 log 开头并带 .txt 扩展名的所有文件 |/var/log/audit/log\*.txt |

1. 选择 Windows 或 Linux，指定正在添加的路径格式。
2. 键入路径，并单击 **+** 按钮。
3. 其他任何路径请重复此步骤。

### <a name="step-5-provide-a-name-and-description-for-the-log"></a>步骤 5。 提供日志名称及描述
指定的名称将用于上述日志类型。  它始终以 _CL 结尾，与自定义日志区分开来。

1. 为日志键入名称。  系统会自动提供 **\_CL** 后缀。
2. 添加可选“说明”。
3. 单击“下一步”，保存自定义日志的定义。

### <a name="step-6-validate-that-the-custom-logs-are-being-collected"></a>步骤 6. 验证是否正在收集自定义日志
新自定义日志的初始数据可能需要一个小时才能在 Log Analytics 中出现。  它将从指定路径的日志中，收集在自定义日志的定义时间之后生成的条目。  它不会在自定义日志创建过程中保留上传的条目，但是它将收集它找到的日志文件中的现有条目。

Log Analytics 开始从自定义日志收集后，它的记录就可用于日志搜索。  将为自定义日志指定的名称用作查询的“类型”。

> [!NOTE]
> 如果搜索缺少 RawData 属性，则可能需要关闭并重新打开浏览器。
>
>

### <a name="step-7-parse-the-custom-log-entries"></a>步骤 7. 分析自定义日志条目
全部日志条目将存储在名为 **RawData** 的单个属性中。  你很可能希望将每个条目中信息的不同部分分离到存储在记录中的单个属性中。  使用 Log Analytics 的[自定义字段](log-analytics-custom-fields.md)功能执行此操作。

此处不提供分析自定义日志条目的详细步骤。  请参考[自定义字段](log-analytics-custom-fields.md)文档，获取此信息。

## <a name="removing-a-custom-log"></a>删除自定义日志
在 Azure 门户中使用以下过程删除以前定义的自定义日志。

1. 从工作区的“高级设置”中的“数据”菜单中选择“自定义日志”，列出所有自定义日志。
2. 单击要删除的自定义日志旁边的“删除”。


## <a name="data-collection"></a>数据收集
Log Analytics 大概每隔 5 分钟就会从每个自定义日志中收集新条目。  代理将记录其在每个日志文件中的位置。  如果代理在一段时间内处于脱机状态，Log Analytics 将从其上次脱机的位置收集条目，即使这些条目创建于代理脱机期间。

日志条目的全部内容写入到名为 **RawData** 的单个属性中。  创建自定义日志后，通过定义[自定义字段](log-analytics-custom-fields.md)，可以将此解析到可单独分析和搜索的多个属性中。

## <a name="custom-log-record-properties"></a>自定义日志记录属性
自定义日志记录的类型与提供的日志名称一致，且具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| TimeGenerated |Log Analytics 收集记录的日期和时间。  如果日志使用基于时间的分隔符，则此时间是从条目中收集的时间。 |
| SourceSystem |从中收集记录的代理类型。 <br> OpsManager – Windows 代理，直接连接或 System Center Operations Manager <br> Linux - 所有 Linux 代理 |
| RawData |收集的条目的完整文本。 |
| ManagementGroupName |System Center Operations Manager 代理的管理组名称。  对于其他代理，这是 AOI-\<工作区 ID\> |

## <a name="log-searches-with-custom-log-records"></a>使用自定义日志记录进行日志搜索
与任何其他数据源中的记录一样，自定义日志的记录存储在 Log Analytics 工作区中。  它们将具有与定义日志时提供的名称匹配的类型，因此可以在搜索中使用类型属性来检索从特定的日志收集的记录。

下表提供了日志搜索的不同示例，它们从自定义日志中检索记录。

| 查询 | 说明 |
|:--- |:--- |
| MyApp_CL |名为 MyApp_CL 的自定义日志的所有事件。 |
| MyApp_CL &#124; where Severity_CF=="error" |来自名为 MyApp_CL 的自定义日志，且 *Severity_CF* 自定义字段值为 *error* 的所有事件。 |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>添加自定义日志的演示示例
以下部分是创建自定义日志的演示示例。  收集的示例日志在每行有单个条目，以日期和时间开头，然后是逗号分隔的代码、状态和消息字段。  几个示例条目如下所示。

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>上传和分析示例日志
我们提供一个日志文件，可以看到它收集的事件。  在这种情况下，换行是有效的分隔符。  如果日志中的单个条目跨过多行，则需要使用时间戳分隔符。

![上传和分析示例日志](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>添加日志集合路径
日志文件位于 *C:\MyApp\Logs*。  每天创建一个新文件，名称为包括日期的 *appYYYYMMDD.log* 模式。  此日志的有效模式是 *C:\MyApp\Logs\\\*.log*。

![日志集合路径](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>提供日志名称及描述
我们使用名称“MyApp_CL”，然后键入“说明”。

![日志名称](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>验证是否正在收集自定义日志
我们使用查询“Type=MyApp_CL”，从收集的日志返回所有记录。

![没有自定义字段的日志查询](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>分析自定义日志条目
我们使用自定义字段来定义“EventTime”、“Code”、“Status”和“Message”字段，这样就可以看到查询返回的记录中的差异。

![具有自定义字段的日志查询](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>后续步骤
* 使用[自定义字段](log-analytics-custom-fields.md)将自定义日志中的条目解析为单个字段。
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。
