---
title: Azure Sentinel 中的数据规范化 |Microsoft Docs
description: 本文介绍了 Azure Sentinel 如何从许多不同的源中规范化数据，并详细说明了规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 0c6129a24e6ed083114971df5f254eca54924400
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934581"
---
# <a name="normalization-in-azure-sentinel"></a>Azure Sentinel 中的规范化

本文介绍了 Azure Sentinel 中的数据架构规范化，并说明了包含链接的网络连接和会话架构。

## <a name="what-is-normalization"></a>什么是规范化

一起使用各种数据类型和表会带来挑战。 必须熟悉许多不同的数据类型和架构，才能针对每个数据类型和架构编写和使用一组唯一的分析规则、工作簿和搜寻查询，甚至对于共享共性 (的情况，例如，与) 的防火墙设备相关。 调查和查找所需的不同数据类型之间的相关性也很困难。 Azure Sentinel 提供了一个无缝的体验，用于在统一的规范化视图中处理来自不同源的数据。

Azure Sentinel **通用信息模型** 包含三个方面：

- **规范化的架构** 涵盖了可预测事件类型的常见集，这些类型 () 表，它们易于使用并在 (上构建统一功能，例如，网络表) 。 该架构还包括标准化的列约定，以及值和格式标准化 (标准一致的数据表示形式，如) 的 IP 地址。

- **分析器** 将不同类型的现有数据映射到规范化架构。 根据模型，可以使用函数) 或引入时间在查询时将数据分析为规范化的架构 (。 目前仅支持查询时分析。

- **每个规范化架构的内容** 包括分析规则、交互式工作簿、搜寻查询和其他内容。

### <a name="current-release"></a>当前版本

在此版本中， [规范化的网络连接和会话架构](./normalization-schema.md) (1.0.0) 可供公共预览版使用。 此架构描述网络连接或会话，如防火墙、线路数据、NSG、Netflow、代理系统和 web 安全网关记录的连接。  所选的查询时间分析器和分析规则与架构一起提供，并利用了它。

此架构目前仅可通过查询时分析程序使用 (请参阅分析器部分) 。

您可以将数据分析为其他表示形式，并使用 [OSSEM 实体命名模型](https://ossemproject.com/cdm/entities/intro.html#) 创建与现有和未来的规范化表一致的列。

## <a name="normalized-schema-and-parsing"></a>规范化的架构和分析

### <a name="how-our-normalized-schemas-are-defined"></a>如何定义规范化架构

Azure Sentinel 与 [开源安全事件元数据 (OSSEM) ](https://ossemproject.com/intro.html) 通用信息模型，允许在规范化的表之间进行可预测的实体关联。 OSSEM 是一个社区导向项目，主要侧重于来自不同数据源和操作系统的安全事件日志的文档和标准化。 此外，该项目还提供了一个通用信息模型 (CIM) ，可用于数据建模过程中的数据工程师，以允许安全分析师跨不同数据源查询和分析数据。

[OSSEM CIM](https://ossemproject.com/cdm/intro.html)定义一组实体 (例如： file、HOST、IP、process) ，并为每个此类实体定义一组属性。 此外，CIM 还会定义一组表 (例如，使用这些实体的相关属性的 [网络会话](https://ossemproject.com/cdm/tables/network_session.html) 表) ，允许无缝且可预测的关联。 请注意，实体可以嵌套 (例如，源实体可以包含将具有 name 属性的文件实体) 。

若要了解有关 OSSEM 实体结构的详细信息，请访问 [官方 OSSEM 引用](https://ossemproject.com/cdm/guidelines/entity_structure.html)。

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>在 Azure Sentinel 中如何实现规范化架构

在 Azure Sentinel 中的 OSSEM CIM 实现中，我们将 OSSEM 表示形式投影到 Log Analytics 表格表示形式，无论此表示形式是内置表，还是使用将现有数据映射到此表示形式的查询时间分析程序或函数创建的。 对于表格表示形式，我们将连接 OSSEM 实体名称和属性名称，并将它们统称为单个列名。 例如，包含包含 md5 属性的哈希实体的文件实体的源实体将作为以下 Log Analytics 列实现： SrcFileHashMd5。  (OSSEM 默认使用 *snake_case* ，而 Azure Sentinel 和 Log Analytics 则使用 *PascalCase*。 在 OSSEM 中，这种列 src_file_hash_md5。 ) 

其他自定义字段可能存在于 Azure Sentinel 实现中，原因是特定于 Azure Sentinel 客户的 Log Analytics 平台要求和用例。

### <a name="schema-reference"></a>架构参考

若要了解详细信息，请参阅 [架构参考文档](./normalization-schema.md)和正式的 [OSSEM 项目文档](https://ossemproject.com/cdm/intro.html)。

架构引用还包括值和格式标准化。 源字段（原始字段或已分析数据）可能不是标准格式，也不能使用架构标准的值列表，因此需要将其转换为架构标准表示形式，才能进行完全标准化。

## <a name="parsers"></a>解析

### <a name="what-is-parsing"></a>分析内容

使用可用的已定义规范化表的基本集，您将需要 (分析/映射) 数据转换为这些表。 也就是说，您可以将特定数据从其原始格式提取到规范化架构中的已知列。 在 Azure Sentinel 中进行分析时，将在 **查询时** 进行分析：使用 Kusto 查询语言-KQL) 将数据分析构建为 Log Analytics 用户函数 (将现有 (表中的数据（如 CommonSecurityLog、自定义日志表、syslog) ）转换为规范化的表架构。

另一种分析（在 Azure Sentinel 中尚不受支持）是在 **引入时** ，允许直接将数据收集到规范化表中， () ，因为它引入数据源。 引入时间分析提高了性能，因为直接查询数据模型时无需使用函数。

### <a name="using-query-time-parsers"></a>使用查询时间分析器

#### <a name="installing-a-parser"></a>安装分析器

Azure Sentinel [官方 GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))中提供了可用的查询时间分析。 每个分析器都进行了版本控制，使客户能够轻松地使用和监视未来更新。 安装分析器：

1. 将上述 GitHub 链接中每个相关 KQL 文件中的相关分析器内容复制到剪贴板

1. 在 Azure Sentinel 门户中打开 "日志" 页，将 KQL 文件的内容粘贴到 "日志" 屏幕，然后单击 " **保存**"。

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="安装新的分析器":::

1. 在 "保存" 对话框中，按如下所示填充字段：
    1. **名称**：建议使用以上示例中 (" **函数别名** " 字段中使用的相同值， *CheckPoint_Network_NormalizedParser*) 
    
    1. **另存为**： select **函数**

    1. **函数别名**：应按照上述示例中的 *PRODUCT_Network_NormalizedParser* (命名， *CheckPoint_Network_NormalizedParser*) 。

    1. **类别**：可以选择现有类别或创建新类别 (例如 *NormalizedNetworkSessionsParsers*) 
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="保存分析器":::

为了正确使用分析程序，你还必须安装空的网络架构分析器 (这会创建所有网络会话架构字段的空表格视图) 和网络元分析程序 (，将所有已启用的分析器结合在一起，以便在网络架构) 中的各种源中创建数据的单一视图。 安装这两个分析器的方式与上述步骤类似。

保存查询函数时，可能需要关闭查询资源管理器，然后重新打开它，以反映新函数。

#### <a name="using-the-parsers"></a>使用分析器

启用后，可以使用元分析器在当前启用的所有分析器上查询统一视图。 为此，请在 "Sentinel 日志" 页上，执行以下查询：

:::image type="content" source="./media/normalization/query-parser.png" alt-text="查询分析器":::
 
您还可以通过单击 "查询资源管理器"，在 "Sentinel 日志" 页中使用查询资源管理器访问元分析程序或单独的分析程序：

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="查询资源管理器":::

在右侧窗格中，展开 "已保存的查询" 部分，找到 "NormalizedNetworkParsers" 文件夹 (或创建分析) 时选择的类别名称：

:::image type="content" source="./media/normalization/find-parser.png" alt-text="查找分析器":::

你可以单击每个单独的分析器并查看其使用的基础函数，并 (或通过其别名直接访问它，如上文) 所述。 请注意，某些分析器可以为方便起见，将原始字段并行保留到规范化字段。 可以在分析程序的查询中轻松编辑此项。

#### <a name="customizing-parsers"></a>自定义分析器

您可以重复以上步骤 (在查询资源管理器中查找分析器) ，单击相关的分析器并查看其函数实现。
例如，你可以决定编辑元分析器来添加/删除单个分析器。

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="自定义分析器":::
 
更改函数后，再次单击 "保存"，并使用相同的名称、别名和类别。 将打开替代对话框–按 "确定"：

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="是否确定":::

#### <a name="additional-information"></a>其他信息

详细了解 (查询时间分析实现) 在 Log Analytics 中所 [保存的查询](../azure-monitor/log-query/saved-queries.md) 。


## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 的规范化架构。 有关引用架构本身，请参阅 [Azure Sentinel 数据规范化架构参考](./normalization-schema.md)。

* [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)。 查找关于 Azure 安全性及合规性的博客文章。
