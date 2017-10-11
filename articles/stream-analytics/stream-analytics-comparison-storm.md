---
title: "分析平台：Apache Storm 与流分析之间的比较 | Microsoft 文档"
description: "使用 Apache Storm 与流分析之间的比较获取有关选择云分析平台的指导。 了解功能和区别。"
keywords: "分析平台, 分析平台, 云分析平台, storm 比较"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: 97044cb5d7b0b3fcb3b85328df618a265bc59b61
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>选择流分析平台：Apache Storm 与 Azure 流分析的比较
Azure 提供了多种用于流数据分析的解决方案：[Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)和 [Azure HDInsight 上的 Apache Storm](https://azure.microsoft.com/services/hdinsight/apache-storm/)。 这两个分析平台都具备 PaaS 解决方案的优势。 但这些平台的功能以及配置和管理方式却存在一些显著差异。 

本文提供对功能的并排比较，帮助你选择 Apache Storm 或 Azure 流分析作为云分析平台。 

## <a name="general-features"></a>常规功能

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>是否是开放源？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
不可以。 Azure 流分析是一种 Microsoft 专有产品/服务。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
是的。 Apache Storm 是一种由 Apache 授权的技术。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>是否由 Microsoft 提供支持？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
是 </p>
            </td>
            <td width="246" valign="top">
                <p>
是 </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>硬件要求</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
无。 Azure 流分析是一种 Azure 服务。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
无。 Apache Storm 是一种 Azure 服务。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>顶层单位</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
用户可部署和监视流式处理作业。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
用户可部署和监视整个群集，而群集可以托管多个 Storm 作业和其他工作负荷（包括批处理）。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>定价</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
按照处理的数据量以及作业每小时运行所需的流式处理单元数来定价。 
                </p>
                    <p>有关详细信息，请参阅<a href="http://azure.microsoft.com/pricing/details/stream-analytics/">流分析定价</a>。</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
采购单位根据群集计算，收费则根据群集的运行时间来计算，与部署的作业无关。
                </p>
                <p>
有关详细信息，请参阅 <a href="http://azure.microsoft.com/pricing/details/hdinsight/">HDInsight 定价</a>。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>创作

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能：是否支持 SQL DSL？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
是的。 流分析提供类似于 SQL 的语言，以便创建转换。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
不能。 用户需使用 Java 或 C# 编写代码，或使用 Trident API。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能：临时运算符？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
默认支持开窗聚合和临时联接。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
临时运算符必须由用户来实现。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>开发体验</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
用户可通过 Azure 门户使用派生自实时流的示例数据，创建、调试和监视作业。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
对于 .NET，用户可通过 Visual Studio 进行开发、调试和监视。 对于 Java 或其他语言，用户可使用自己选择的 IDE。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>调试支持</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
可借助基本作业状态和操作日志进行调试。 流分析目前不允许用户指定日志中包含哪些内容或多少内容（即详细模式）。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
提供详细日志。 用户可通过 Visual Studio 访问日志，或通过登录到群集来直接访问日志。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>是否支持使用自定义代码的可扩展性？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
部分支持 JavaScript UDF。 有关详细信息，请参阅 <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">JavaScript UDF 集成</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
是的。 用户可使用 C#、Java 或 Storm 上支持的任何其他语言编写自定义代码。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>数据源（输入）和输出 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>输入数据源</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Azure 事件中心和 Azure Blob 存储。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
连接器适用于 Azure 事件中心、Azure 服务总线、Kafka 等。 用户可使用自定义代码创建其他连接器。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>输入数据格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro、JSON、CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
用户可使用自定义代码来实现任何格式。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>输出</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
流式处理作业可以有多个输出。 支持的输出：Azure 事件中心、Azure Blob 存储、Azure 表存储、Azure SQL DB 和 Power BI。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm 在拓扑中支持多种输出，每种输出都可以具有用于下游处理的自定义逻辑。 Storm 包括适用于 Power BI、Azure 事件中心、Azure Blob 存储、Azure Cosmos DB、SQL 和 HBase 的连接器。 用户可使用自定义代码创建其他连接器。    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>数据编码格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
必须使用 UTF-8 对数据进行格式化。
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
用户可使用自定义代码来实现任何数据编码格式。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>管理和操作 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>作业部署模型</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure 门户、PowerShell 和 REST API。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Azure 门户、PowerShell、Visual Studio 和 REST API。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>监视（统计信息、计数器等）</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
监视是使用 Azure 门户和 REST API 实现的。 用户还可以配置 Azure 警报。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
监视是使用 Storm UI 和 REST API 实现的。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>可伸缩性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
可伸缩性取决于每个作业的流式处理单位 (SU) 数目。 每个流式处理单位的处理速度高达 1 MB/秒，最大值为 50 个单位。 有关详细信息，请参阅<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">通过扩展增大吞吐量</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
可伸缩性取决于 HDInsight Storm 群集中的节点数。 节点数的上限由用户的 Azure 配额来定义。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>数据处理限制</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
用户可以提高或降低流式处理单位数来提高数据处理速度（上限为 1 GB/秒）或优化成本。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
用户可以增大或缩小群集。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>停止/恢复</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
停止，并从上次停止的位置恢复。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
停止，并根据水印从上次停止的位置恢复。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>服务和框架更新</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
自动修补，不停机。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
自动修补，不停机。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>通过确保服务高度可用并确保 SLA 来保证业务连续性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>SLA：99.9% 的运行时间</li>
                <li>从故障自动恢复</li>
                <li>内置有状态临时运算符恢复功能</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA：99.9% 的 Storm 群集运行时间。 
                </p>
                <p>
Apache Storm 是一种容错性流式处理平台。 但是，用户必须负责确保流式处理作业不间断运行。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>高级功能 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>延迟到达和无序事件处理</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
内置可配置策略可以重新排序、删除事件或调整事件时间。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
用户必须实现相关逻辑来处理这种情况。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>引用数据</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure Blob 存储提供引用数据，内存中缓存最大大小为 100 MB。 引用数据由服务刷新。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
数据大小没有限制。 连接器适用于 HBase、Azure Cosmos DB、SQL Server 和 Azure。 用户可使用自定义代码创建其他连接器。 必须使用自定义代码刷新引用数据。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>机器学习集成</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
可在作业创建过程中将已发布的 Azure 机器学习模型配置为各种函数。 有关详细信息，请参阅<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">使用机器学习函数进行缩放</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
通过 Storm Bolt 提供。
                </p>
            </td>
        </tr>
    </tbody>
</table>
