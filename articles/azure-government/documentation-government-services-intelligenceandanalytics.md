---
title: "Azure 政府智能 + 分析 | Microsoft 文档"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 14fb62669e15873770ef9b8470e34db70f1ca2ca
ms.openlocfilehash: 2b89d78e20cccee81c9581e762e4c61bacd84fd4
ms.lasthandoff: 02/15/2017


---
# <a name="azure-government-intelligence--analytics"></a>Azure 政府智能 + 分析
本文概述了 Azure 政府环境的智能和分析服务、变体和注意事项。

## <a name="azure-hdinsight"></a>Azure HDInsight
Linux Standard 上的 HDInsight 已在 Azure 政府正式发布。 可以在<a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>此处</a>看到一个演示如何使用 HDInsight 在 Azure 政府上生成以数据为中心的解决方案的演示。

Linux Premium 上的 HDInsight 即将推出。

### <a name="variations"></a>变体
当前，以下 HDInsight 功能在 Azure 政府中不可用。

* HDInsight 在 Windows 上不可用。
* Azure Data Lake Store 目前在 Azure 政府中不可用。 Azure Blob 存储是目前唯一可用的存储选项。

Log Analytics 的 URL 在 Azure 政府版中是不同的：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| HDInsight 群集 | \*.azurehdinsight.net | \*.azurehdinsight.us |

有关详细信息，请参阅 [Azure HDInsight 公共文档](../hdinsight/hdinsight-hadoop-introduction.md)。

## <a name="power-bi"></a>Power BI
Power BI 美国政府版已作为 Office 365 美国政府社区版订阅的一部分正式发布。 可以在<a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>此处</a>了解 Power BI 美国政府版。 可以在<a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>此处</a>看到一个演示如何使用 Power BI 在 Azure 政府上生成以数据为中心的解决方案的演示。

Power BI Embedded 即将推出。

### <a name="variations"></a>变体

Power BI 的 URL 在美国政府版中是不同的：

| 服务类型 | Power BI 商业版 | Power BI 美国政府版 |
| --- | --- | --- |
| Power BI URL | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 博客</a>。

