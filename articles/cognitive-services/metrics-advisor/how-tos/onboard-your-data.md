---
title: 将数据馈送载入指标顾问
titleSuffix: Azure Cognitive Services
description: 如何开始将数据馈送载入到指标顾问。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043185"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>操作说明：将指标数据载入指标顾问

请参阅本文，了解如何将数据载入指标顾问。 

## <a name="data-schema-requirements-and-configuration"></a>数据架构要求和配置

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>避免加载部分数据

部分数据由指标审查程序和数据源中存储的数据之间的不一致引起。 在指标顾问完成数据源更新后，可能会发生这种情况。 指标顾问只从给定数据源提取一次数据。

例如，如果指标已载入用于监视的指标审查。 指标顾问在时间戳 A 成功地获取指标数据，并对其执行异常检测。 但是，如果该特定时间戳的指标数据在引入数据后已刷新。 不会检索新的数据值。

你可以尝试 [回填](manage-data-feeds.md#backfill-your-data-feed) (稍后介绍) 的历史数据，以缓解不一致情况，但如果已触发这些时间点的警报，则不会触发新的异常警报。 此过程可能会向系统添加更多工作负荷，并且不是自动的。

为了避免加载部分数据，建议采用两种方法：

* 在一个事务中生成数据：

    确保在同一时间戳的所有维度组合的指标值存储到一个事务中的数据源。 在上面的示例中，请等待所有数据源中的数据准备就绪，然后将其加载到一个事务中的指标顾问中。 指标顾问可以定期轮询数据馈送，直到成功 (或部分) 检索数据。

* 为 **引入时间偏移量** 参数设置适当的值，以延迟数据引入：

    设置数据馈送的 " **引入时间偏移量** " 参数，以延迟引入，直到数据完全准备就绪。 对于不支持 Azure 表存储等事务的某些数据源，这可能很有用。 有关详细信息，请参阅 [高级设置](manage-data-feeds.md#advanced-settings) 。

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>使用基于 web 的工作区添加数据馈送

登录到指标顾问门户并选择工作区后，请单击 " **开始**"。 然后，在工作区的主页上，单击左侧菜单中的 " **添加数据馈送** "。

### <a name="add-connection-settings"></a>添加连接设置

接下来，你将输入一组用于连接时序数据源的参数。 
* **源类型**：用于存储时序数据的数据源的类型。
* **粒度**：时间序列数据中连续数据点之间的间隔。 当前指标顾问支持：每年、每月、每周、每日、每小时和自定义。 自定义选项支持的最小间隔为60秒。
  * **秒**： *GranularityName* 设置为 *自定义*的秒数。
* **自 (UTC 以来引入数据) **：数据引入的基线开始时间。 *startOffsetInSeconds* 通常用于添加偏移量以帮助提高数据一致性。

接下来，您需要指定数据源的连接信息，以及用于将数据转换为所需架构的自定义查询。 有关其他字段以及连接不同数据源类型的详细信息，请参阅 [添加来自不同数据源的数据馈送](../data-feeds-from-different-sources.md)。

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>验证并获取架构

设置连接字符串和查询字符串后，选择 "验证"，然后选择 " **获取架构** " 来验证连接，并运行查询以从数据源中获取数据架构。 通常，这会花费几秒钟的时间，具体取决于你的数据源连接。 如果此步骤中出现错误，请确认：

* 你的连接字符串和查询是正确的。
* 如果有防火墙设置，指标顾问实例能够连接到数据源。

### <a name="schema-configuration"></a>架构配置

加载数据架构后，请选择相应的字段。

如果省略数据点的时间戳，则指标顾问将在数据点引入时使用时间戳。 对于每个数据馈送，最多可以将一个列指定为时间戳。 如果您收到一条消息，指出不能将列指定为时间戳，请检查您的查询或数据源，并检查查询结果中是否有多个时间戳，而不仅是预览数据。 执行数据引入时，指标顾问每次只能使用一个块 (（例如一天，一小时），根据给定源的时间序列数据) 的粒度。

|选项  |说明  |说明  |
|---------|---------|---------|
| **显示名称** | 要在工作区中显示的名称，而不是原始列名称。 | |
|**Timestamp**     | 数据点的时间戳。 如果省略，则指标顾问将在数据点引入时使用时间戳。 对于每个数据馈送，最多可以将一个列指定为时间戳。        | 可选。 最多只能指定一列。 如果 **无法将列指定为时间戳** 错误，请检查查询或数据源中是否存在重复的时间戳。      |
|度量      |  数据馈送中的数值。 对于每个数据馈送，您可以指定多个度量值，但至少应选择一列作为度量值。        | 应至少指定一列。        |
|**维度**     | 分类值。 不同值的组合标识特定的一维时序，例如：国家/地区、语言、租户。 您可以选择零个或多个列作为维度。 注意：选择非字符串列作为维度时要格外小心。 | 可选。        |
|**忽略**     | 忽略所选列。        | 可选。 请参阅以下文本。       |

如果希望忽略列，建议更新查询或数据源以排除这些列。 您还可以使用 **ignore 列** 忽略列，然后在特定列上 **忽略** 。 如果某列应为维度，并且被错误地设置为 "已 *忽略*"，则指标顾问可能会最终引入部分数据。 例如，假设查询中的数据如下所示：

| 行 ID | 时间戳 | 国家/地区 | 语言 | 收入 |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | 中国 | ZH-CN | 10000 |
| 2 | 2019/11/10 | 中国 | EN-US | 1000 |
| 3 | 2019/11/10 | US | ZH-CN | 12000 |
| 4 | 2019/11/11 | US | EN-US | 23000 |
| ... | ...| ... | ... | ... |

如果 *国家/地区* 是维度， *语言* 设置为 " *忽略*"，则第一个和第二个行将具有相同的维度。 指标顾问将任意使用两行中的一个值。 在这种情况下，指标顾问不会聚合行。

### <a name="automatic-roll-up-settings"></a>自动汇总设置

> [!IMPORTANT]
> 如果你想要启用根本原因分析和其他诊断功能，则需要配置 **自动汇总设置** 。 启用后，不能更改自动汇总设置。

指标顾问可以在引入过程中自动对每个维度上的 SUM、MAX、MIN) 执行聚合 (，然后生成一个将用于根事例分析和其他诊断功能的层次结构。 

请考虑下列情形：

* *我不需要为数据包含汇总分析。*

    不需要使用指标顾问汇总。

* *我的数据已汇总，维度值由以下内容表示： NULL 或为空 (默认值) ，仅为 NULL，其他。*

    此选项意味着度量值顾问无需汇总数据，因为行已经求和。 例如，如果选择 " *仅空*"，则会将以下示例中的第二个数据行视为所有国家和语言的聚合，并使用 *en-us*;对于 *国家/地区* 有空值的第四个数据行，将被视为普通行，这可能表示数据不完整。
    
    | 国家/地区 | 语言 | 收入 |
    |---------|----------|--------|
    | 中国   | ZH-CN    | 10000  |
    |  (NULL)   | EN-US    | 999999 |
    | US      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *我需要指标顾问通过计算 Sum/Max/Min/Avg/Count 并将其表示为 <some string>*

    某些数据源（如 Cosmos DB 或 Azure Blob 存储）不支持某些计算，例如 *group by* 或 *cube*。 指标顾问提供了汇总选项，以在引入过程中自动生成数据多维数据集。
    此选项意味着您需要使用指标顾问来计算使用您选择的算法的汇总，并使用指定的字符串表示指标顾问中的汇总。 这不会更改数据源中的任何数据。
    例如，假设你有一组时间系列，它代表维度 (国家/地区) 的销售指标。 对于给定的时间戳，它可能如下所示：


    | 国家/地区       | 区域           | Sales |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | United States | Montana          | 100   |


    通过 *Sum*启用自动汇总后，指标顾问将计算维度组合，并在数据引入期间对指标求和。 结果可能是：

    | 国家/地区       | 区域           | Sales |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | Null          | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | Null          | British Columbia | 500   |
    | United States | Montana          | 100   |
    | Null          | Montana          | 100   |
    | Null          | Null             | 700   |
    | Canada        | Null             | 600   |
    | United States | Null             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` 表示加拿大的销售总额 (所有区域) 为600。

    下面是 SQL 语言中的转换。

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    使用自动汇总功能之前，请考虑以下事项：

    * 如果要使用 *SUM* 聚合数据，请确保在每个维度中的度量值都是累加的。 下面是一些 *非加法* 指标的示例：
      * 基于分数的指标。 这包括比率、百分比等。例如，不应添加每种状态的失业率率来计算整个国家/地区的失业率费率。
      * 维度重叠。 例如，你不应将中的人数添加到每个运动中来计算喜欢运动的人员数，因为它们之间存在重叠，一个人可以喜欢多个运动。
    * 为了确保整个系统的运行状况，多维数据集的大小受到限制。 目前限制为1000000。 如果数据超过此限制，则对该时间戳的引入将会失败。

## <a name="advanced-settings"></a>高级设置

可以通过多个高级设置以自定义方式（如指定摄取偏移或并发）启用数据引入。 有关详细信息，请参阅数据馈送管理一文中的 [高级设置](manage-data-feeds.md#advanced-settings) 部分。

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>指定数据馈送的名称并检查引入进度
 
为数据馈送提供自定义名称，该名称将显示在工作区中。 然后单击 " **提交**"。 在 "数据馈送详细信息" 页中，可以使用 "引入进度栏" 查看状态信息。

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="引入进度栏" lightbox="../media/datafeeds/ingestion-progress.png":::


检查引入失败的详细信息： 

1. 单击 " **显示详细信息**"。
2. 单击 " **状态** "，然后选择 " **失败** " 或 " **错误**"。
3. 将鼠标悬停在失败的引入上，并查看显示的详细信息消息。

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="引入进度栏" 来重新加载引入的进度。 数据引入完成后，可随时单击度量值并检查异常情况检测结果。

## <a name="next-steps"></a>后续步骤
- [管理数据馈送](manage-data-feeds.md)
- [不同数据源的配置](../data-feeds-from-different-sources.md)
- [配置指标并微调检测配置](configure-metrics.md)
