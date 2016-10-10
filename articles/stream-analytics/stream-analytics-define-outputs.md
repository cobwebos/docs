<properties
	pageTitle="流分析输出：存储、分析选项 | Azure"
	description="了解有关设定流分析数据输出选项（包括 Power BI）目标，用于分析结果的信息。"
	keywords="数据转换、分析结果、数据存储选项"
	services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
	documentationCenter="" 
	authors="jeffstokes72"
	manager="jhubbard" 
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/29/2016"
	ms.author="jeffstok"/>

# 流分析输出：存储、分析选项

创作流分析作业时，需考虑如何使用生成的数据。如何查看流分析作业的结果？流分析作业的结果存储在何处？

为了启用多种应用程序模式，Azure 流分析提供了不同的选项来存储输出和查看分析结果。这样可以轻松地查看作业输出，并可灵活地使用和存储作业输出，以便进行数据仓库操作和其他操作。必须先存在作业中配置的输出，然后才能启动作业并开始事件的流动。例如，如果你使用 Blob 存储作为输出，该作业将不会自动创建存储帐户。在启动 ASA 作业之前，需要由用户创建该存储帐户。

## Azure 数据湖存储

流分析支持 [Azure 数据湖存储](https://azure.microsoft.com/services/data-lake-store/)。此存储可让你存储任何大小、类型和引入速度的数据，以便进行运行和探索分析。目前只有 Azure 经典门户支持创建和配置数据湖存储输出。此外，流分析需要经过授权，才能访问数据湖存储。[数据湖输出一文](stream-analytics-data-lake-output.md)中讨论了有关授权以及如何注册数据湖存储预览版（如有需要）的详细信息。

下表列出了属性名称和创建数据湖存储输出所需的属性说明。

<table>
<tbody>
<tr>
<td><B>属性名称</B></td>
<td><B>说明</B></td>
</tr>
<tr>
<td>输出别名</td>
<td>该名称是在查询中使用的友好名称，用于将查询输出定向到此数据湖存储。</td>
</tr>
<tr>
<td>帐户名</td>
<td>Data Lake 存储帐户的名称（你正在向该存储帐户发送输出）。你将看到数据湖存储帐户的下拉列表，登录门户的用户可访问该下拉列表。</td>
</tr>
<tr>
<td>路径前缀模式 [<I>可选</I>]</td>
<td>用于对指定数据湖存储帐户中的文件进行编写的文件路径。<BR>{date}、{time}<BR>示例 1：folder1/logs/{date}/{time}<BR>示例 2：folder1/logs/{date}</td>
</tr>
<tr>
<td>日期格式 [<I>可选</I>]</td>
<td>如果在前缀路径中使用日期令牌，你可以选择组织文件所采用的日期格式。示例：YYYY/MM/DD</td>
</tr>
<tr>
<td>时间格式 [<I>可选</I>]</td>
<td>如果在前缀路径中使用时间令牌，你可以选择组织文件所采用的时间格式。目前唯一支持的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>输出数据的序列化格式。支持 JSON、CSV 和 Avro。</td>
</tr>
<tr>
<td>编码</td>
<td>如果是 CSV 或 JSON 格式，则必须指定一种编码格式。目前只支持 UTF-8 这种编码格式。</td>
</tr>
<tr>
<td>分隔符</td>
<td>仅适用于 CSV 序列化。流分析支持大量的常见分隔符以对 CSV 数据进行序列化。支持的值为逗号、分号、空格、制表符和竖线。</td>
</tr>
<tr>
<td>格式</td>
<td>仅适用于 JSON 序列化。分隔行指定了通过新行分隔各个 JSON 对象，从而格式化输出。数组指定输出将被格式化为 JSON 对象的数组。</td>
</tr>
</tbody>
</table>

### 续订数据湖存储授权

如果自作业创建后或上次身份验证后更改了密码，则需要重新对 Data Lake Store 帐户进行身份验证。

![Azure Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)


## SQL 数据库

可以将 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)用作本质上为关系型数据的输出，也可以将其用于所依赖的内容在关系数据库中托管的应用程序。流分析作业将写入到 Azure SQL 数据库的现有表中。请注意表架构必须与字段及其正从作业输出的类型完全匹配。[Azure SQL 数据仓库](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)也可以通过 SQL 数据库输出选项指定为输出（此项为预览功能）。下表列出了属性名称和用于创建 SQL 数据库输出的属性说明。

| 属性名称 | 说明 |
|---------------|-------------|
| 输出别名 | 该名称是在查询中使用的友好名称，用于将查询输出定向到此数据库。 |
| 数据库 | 数据库的名称（你正在向该数据库发送输出） |
| 服务器名称 | SQL 数据库服务器名称 |
| 用户名 | 有权写入到数据库的用户名 |
| 密码 | 用于连接到数据库的密码 |
| 表 | 将写入输出的表名称。表名称区分大小写，并且该表架构应与字段数量以及作业输出正在生成的字段类型完全匹配。 |

> [AZURE.NOTE] 目前，流分析中的作业输出支持 Azure SQL 数据库产品/服务。但是，不支持带有附加数据库，运行 SQL Server 的 Azure 虚拟机。这在将来的版本中可能会有所改变。

## Blob 存储

Blob 存储提供了一个种经济高效且可扩展的解决方案，用于在云中存储大量非结构化数据。如需 Azure Blob 存储及其用法的简介，请参阅文档：[如何使用 Blob](../storage/storage-dotnet-how-to-use-blobs.md)。

下表列出了属性名称和用于创建 blob 输出的属性说明。

<table>
<tbody>
<tr>
<td>属性名称</td>
<td>说明</td>
</tr>
<tr>
<td>输出别名</td>
<td>该名称是在查询中使用的友好名称，用于将查询输出定向到此 blob 存储。</td>
</tr>
<tr>
<td>存储帐户</td>
<td>存储帐户的名称（你正在向该存储帐户发送输出）。</td>
</tr>
<tr>
<td>存储帐户密钥</td>
<td>与存储帐户关联的密钥。</td>
</tr>
<tr>
<td>存储容器</td>
<td>容器对存储在 Microsoft Azure Blob 服务中的 blob 进行逻辑分组。将 blob 上载到 Blob 服务时，必须为该 blob 指定一个容器。</td>
</tr>
<tr>
<td>路径前缀模式 [可选]</td>
<td>用于对指定容器中的 blob 进行编写的文件路径。<BR>在路径中，你可以选择使用以下 2 个变量的一个或多个实例指定 blob 写入的频率：<BR>{date}、{time}<BR>示例 1：cluster1/logs/{date}/{time}<BR>示例 2：cluster1/logs/{date}</td>
</tr>
<tr>
<td>日期格式 [可选]</td>
<td>如果在前缀路径中使用日期令牌，你可以选择组织文件所采用的日期格式。示例：YYYY/MM/DD</td>
</tr>
<tr>
<td>时间格式 [可选]</td>
<td>如果在前缀路径中使用时间令牌，你可以选择组织文件所采用的时间格式。目前唯一支持的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>输出数据的序列化格式。支持 JSON、CSV 和 Avro。</td>
</tr>
<tr>
<td>编码</td>
<td>如果是 CSV 或 JSON 格式，则必须指定一种编码格式。目前只支持 UTF-8 这种编码格式。</td>
</tr>
<tr>
<td>分隔符</td>
<td>仅适用于 CSV 序列化。流分析支持大量的常见分隔符以对 CSV 数据进行序列化。支持的值为逗号、分号、空格、制表符和竖线。</td>
</tr>
<tr>
<td>格式</td>
<td>仅适用于 JSON 序列化。分隔行指定了通过新行分隔各个 JSON 对象，从而格式化输出。数组指定输出将被格式化为 JSON 对象的数组。</td>
</tr>
</tbody>
</table>

## 事件中心

[事件中心](https://azure.microsoft.com/services/event-hubs/)是具有高扩展性的发布-订阅事件引入器。事件中心每秒可收集数百万个事件。当流分析作业的输出将要成为另一个流式处理作业的输入时，可以将事件中心用作输出。

将事件中心数据流配置成输出时，需要使用几个参数。

| 属性名称 | 说明 |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 输出别名 | 该名称是在查询中使用的友好名称，用于将查询输出定向到此事件中心。 |
| 服务总线命名空间 | 服务总线命名空间是包含一组消息传递实体的容器。当你创建新的事件中心后，你还创建了服务总线命名空间 |
| 事件中心 | 事件中心输出的名称 |
| 事件中心策略名称 | 可以在事件中心的“配置”选项卡上创建的共享访问策略。每个共享访问策略都会有名称、所设权限以及访问密钥 |
| 事件中心策略密钥 | 用于验证访问服务总线命名空间的共享访问密钥 |
| 分区键列 [可选] | 此列包含事件中心输出的分区键。 |
| 事件序列化格式 | 输出数据的序列化格式。支持 JSON、CSV 和 Avro。 |
| 编码 | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式 |
| 分隔符 | 仅适用于 CSV 序列化。流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 | 仅适用于 JSON 类型。分隔行指定了通过新行分隔各个 JSON 对象，从而格式化输出。数组指定输出将被格式化为 JSON 对象的数组。 |

## Power BI

[Power BI](https://powerbi.microsoft.com/) 可以用作流分析作业的输出，以便提供丰富的分析结果可视化体验。此功能可用于操作仪表板、生成报告以及进行指标驱动型报告。

### 向 Power BI 帐户授权

1.	当 Power BI 被选为 Azure 管理门户中的输出时，会提示你向现有的 Power BI 用户授权或创建新的 Power BI 帐户。

    ![向 Power BI 用户授权](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2.	如果你还没有帐户，请创建一个新帐户，然后单击“立即授权”。将显示如下所示的屏幕。

    ![Azure 帐户的 Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3.	在此步骤中，提供用于授权 Power BI 输出的工作或学校帐户。如果你还没有注册 Power BI，请选择“立即注册”。用于 Power BI 的工作或学校帐户可能不同于你当前登录时所用的 Azure 订阅帐户。

### 配置 Power BI 输出属性

Power BI 帐户身份验证完成后，你可以为自己的 Power BI 输出配置属性。下表列出了属性名称以及配置 Power BI 输出的说明。

| 属性名称 | 说明 |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 输出别名 | 该名称是在查询中使用的友好名称，用于将查询输出定向到此 PowerBI 输出。 |
| 组工作区 | 若要与其他 Power BI 用户启用共享数据，可以在 Power BI 帐户中选择组。如果不想写入组，可以选择“我的工作区”。更新现有组需要对 Power BI 重新进行身份验证。 | 
| 数据集名称 | 提供数据集名称，供 Power BI 输出使用。 |
| 表名称 | 在 Power BI 输出的数据集下提供表名称。目前，流分析作业的 Power BI 输出只能在数据集中设置一个表。 |

有关配置 Power BI 输出和仪表板的演练，请参阅 [Azure 流分析和 Power BI](stream-analytics-power-bi-dashboard.md) 一文。

> [AZURE.NOTE] 不要在 Power BI 仪表板中显式创建数据集和表。当作业启动并且开始将输出抽取到 Power BI 中时，会自动填充数据集和表。请注意，如果作业查询没有生成任何结果，则不会创建数据集和表。另请注意，如果 Power BI 已经有一个数据集和表，且与流分析作业中提供的数据集和表名称相同，则会覆盖现有的数据。

### 续订 Power BI 授权

如果自作业创建后或上次身份验证后更改了密码，你需要重新对 Power BI 帐户进行身份验证。如果在 Azure Active Directory (AAD) 租户上配置了 Multi-Factor Authentication (MFA)，还需要每 2 周续订一次 Power BI 授权。此问题的症状是没有作业输出，并且操作日志存在“验证用户错误”：

  ![Power BI 刷新令牌错误](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

要解决此问题，请停止正在运行的作业并转到你的 Power BI 输出。单击“续订授权”链接，并在“上次停止时间”重新启动你的工作以避免数据丢失。

  ![Power BI 续订授权](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## 表存储

[Azure 表存储](/documentation/articles/storage-introduction/)提供了具有高可用性且可大规模缩放的存储，因此应用程序可以自动缩放以满足用户需求。表存储是 Microsoft 推出的 NoSQL 键/属性存储，适用于对架构的约束性较少的结构化数据。Azure 表存储可用于持久地存储数据，方便进行高效的检索。

下表列出了属性名称和用于创建表输出的属性说明。

| 属性名称 | 说明 |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 输出别名 | 该名称是在查询中使用的友好名称，用于将查询输出定向到此表存储。 |
| 存储帐户 | 存储帐户的名称（你正在向该存储帐户发送输出）。 |
| 存储帐户密钥 | 与存储帐户关联的访问密钥。 |
| 表名称 | 表的名称如果表不存在，则会创建表。 |
| 分区键 | 包含分区键的输出列的名称。分区键是给某个定表中分区的唯一标识，分区键构成了实体主键的第一部分。分区键是一个最大为 1 KB 的字符串值。 |
| 行键 | 包含行键的输出列的名称。行键是某个给定分区中实体的唯一标识符。行键构成了实体主键的第二部分。行键是一个最大为 1 KB 的字符串值。 |
| 批大小 | 批处理操作的记录数。通常情况下，默认值对于大多数作业来说已经足够。若要修改此设置，请参阅[表批处理操作规范](https://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)以获取详细信息。 |

## 服务总线队列

[服务总线队列](https://msdn.microsoft.com/zh-cn/library/azure/hh367516.aspx)为一个或多个竞争使用方提供先入先出 (FIFO) 消息传递方式。通常情况下，接收方会按照消息添加到队列中的临时顺序来接收并处理消息，并且每条消息仅由一个消息使用方接收并处理。

下表列出了属性名称和用于创建队列输出的属性说明。

| 属性名称 | 说明 |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 输出别名 | 该名称是在查询中使用的友好名称，用于将查询输出定向到此服务总线队列。 |
| 服务总线命名空间 | 服务总线命名空间是包含一组消息传递实体的容器。 |
| 队列名称 | 服务总线队列的名称。 |
| 队列策略名称 | 在创建队列时，你还可以在“队列配置”选项卡上创建共享的访问策略。每个共享访问策略都会有名称、所设权限以及访问密钥。 |
| 队列策略密钥 | 用于验证访问服务总线命名空间的共享访问密钥 |
| 事件序列化格式 | 输出数据的序列化格式。支持 JSON、CSV 和 Avro。 |
| 编码 | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式 |
| 分隔符 | 仅适用于 CSV 序列化。流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 | 仅适用于 JSON 类型。分隔行指定了通过新行分隔各个 JSON 对象，从而格式化输出。数组指定输出将被格式化为 JSON 对象的数组。 |

## 服务总线主题

服务总线队列提供的是一对一，从发送方到接收方的通信方法，而[服务总线主题](https://msdn.microsoft.com/zh-cn/library/azure/hh367516.aspx)提供的则是一对多形式的通信。

下表列出了属性名称和用于创建表输出的属性说明。

| 属性名称 | 说明 |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 输出别名 | 这个名称是在查询中使用的友好名称，用于将查询输出定向到此服务总线主题。 |
| 服务总线命名空间 | 服务总线命名空间是包含一组消息传递实体的容器。当你创建新的事件中心后，你还创建了服务总线命名空间 |
| 主题名称 | 主题是消息传送实体，类似于事件中心和队列。之所以设计队列，是为了从多个不同的设备和服务收集事件流。在创建主题时，还会为其提供特定的名称。发送到主题的消息在创建订阅后才会提供给用户，因此请确保主题下存在一个或多个订阅 |
| 主题策略名称 | 创建主题时，还可以在“主题配置”选项卡上创建共享的访问策略。每个共享访问策略都会有名称、所设权限以及访问密钥 |
| 主题策略密钥 | 用于验证访问服务总线命名空间的共享访问密钥 |
| 事件序列化格式 | 输出数据的序列化格式。支持 JSON、CSV 和 Avro。 |
| 编码 | 如果是 CSV 或 JSON 格式，则必须指定一种编码格式。目前只支持 UTF-8 这种编码格式 |
| 分隔符 | 仅适用于 CSV 序列化。流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。支持的值为逗号、分号、空格、制表符和竖线。 |

## DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 是完全托管的 NoSQL 文档数据库服务，提供针对无架构数据的查询和事务、可预测且可靠的性能，以及快速开发。

下表列出了用于创建 DocumentDB 输出的属性名称和属性说明。

<table>
<tbody>
<tr>
<td>属性名称</td>
<td>说明</td>
</tr>
<tr>
<td>帐户名</td>
<td>DocumentDB 帐户的名称。这也可以是该帐户的终结点。</td>
</tr>
<tr>
<td>帐户密钥</td>
<td>DocumentDB 帐户的共享访问密钥。</td>
</tr>
<tr>
<td>数据库</td>
<td>DocumentDB 数据库名称。</td>
</tr>
<tr>
<td>集合名称模式</td>
<td>要使用的集合的集合名称模式。可以使用可选的 {partition} 令牌（其中分区从 0 开始）构造集合名称格式。<BR>例如以下是有效的输入：<BR>MyCollection{partition}<BR>MyCollection<BR>请注意，集合必须在启动流分析作业之前存在并且不会自动创建。</td>
</tr>
<tr>
<td>分区键</td>
<td>输出事件中的字段的名称，该字段用于指定跨集合分区输出的键。</td>
</tr>
<tr>
<td>文档 ID</td>
<td>输出事件中的字段的名称，该字段用于指定插入或更新操作所基于的主键。</td>
</tr>
</tbody>
</table>


## 获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/zh-CN/home?forum=AzureStreamAnalytics)

## 后续步骤
我们已经向你介绍了流分析，这是一种托管服务，适用于对物联网的数据进行流式分析。若要了解有关此服务的详细信息，请参阅：

- [Azure 流分析入门](/documentation/articles/stream-analytics-get-started/)
- [缩放 Azure 流分析作业](/documentation/articles/stream-analytics-scale-jobs/)
- [Azure 流分析查询语言参考](https://msdn.microsoft.com/zh-cn/library/azure/dn834998.aspx)
- [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/zh-cn/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: /documentation/articles/stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: /documentation/articles/stream-analytics-scale-jobs/
[stream.analytics.introduction]: /documentation/articles/stream-analytics-introduction/
[stream.analytics.get.started]: /documentation/articles/stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=Mooncake_0926_2016-->