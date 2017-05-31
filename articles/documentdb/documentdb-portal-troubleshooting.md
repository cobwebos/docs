---
title: "Azure Cosmos DB 门户问题故障排除 | Microsoft Docs"
description: "找出并解决 Azure Cosmos DB Azure 门户中的问题。"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 36ad9bf4-2617-4347-ba29-edebf62fc3ec
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 307b1d0edc1aa76654cc96c71cccc39c2a11d40c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-portal-troubleshooting-tips"></a>Azure Cosmos DB 门户故障排除提示
本文介绍如何在 Azure 门户中解决 Azure Cosmos DB 问题。 

## <a name="resources-are-missing"></a>缺少资源
**症状**：门户边栏选项卡中缺少数据库或集合。

**解决方案**：减少应用程序使用量，以在集合的最大吞吐量配额下运行。 

**说明**：门户是一种应用程序，就像任何其他应用程序一样，对 Cosmos DB 数据库和集合进行调用。 如果当前由于从单独的应用程序进行调用，请求受到限制，门户可能也同样受到限制，导致资源未显示在门户中。 若要解决此问题，需解决高吞吐量使用率的原因，然后刷新门户边栏选项卡。 有关如何测量和降低吞吐量使用率的信息，请参阅[性能提示](documentdb-performance-tips.md)一文的[吞吐量](documentdb-performance-tips.md#throughput)部分。

## <a name="pages-or-blades-wont-load"></a>页面或边栏选项卡无法加载
**症状**：门户中的页面和边栏选项卡未显示。

**解决方案**：减少应用程序使用量，以在集合的最大吞吐量配额下运行。 

**说明**：门户是一种应用程序，就像任何其他应用程序一样，对 Cosmos DB 数据库和集合进行调用。 如果当前由于从单独的应用程序进行调用，请求受到限制，门户可能也同样受到限制，导致资源未显示在门户中。 若要解决此问题，需解决高吞吐量使用率的原因，然后刷新门户边栏选项卡。 有关如何测量和降低吞吐量使用率的信息，请参阅[性能提示](documentdb-performance-tips.md)一文的[吞吐量](documentdb-performance-tips.md#throughput)部分。

## <a name="add-collection-button-is-disabled"></a>“添加集合”按钮处于禁用状态
**症状**：在“数据库”边栏选项卡上，“添加集合”按钮处于禁用状态。

**说明**：如果 Azure 订阅已与权益信用额度（如 MSDN 订阅提供的免费信用额度）关联，并且已使用这个月的所有信用额度，则无法在 Cosmos DB 中创建任何其他集合。

**解决方案**：从帐户中删除支出限制。

1. 在 Azure 门户中，单击跳转栏中的“订阅”，再单击与 Cosmos DB 数据库关联的订阅，然后在“订阅”边栏选项卡中，单击“管理”。 
    ![Azure Cosmos DB 提供多个妥善定义的（宽松）一致性模型供你选择](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. 在新的浏览器窗口中，将看到已没有剩余的信用额度。 单击“删除支出限制”按钮可只删除当前计费周期的支出限制或无限期地删除支出限制。 然后完成向导以添加或确认信用卡信息。 
    ![Azure Cosmos DB 提供多个妥善定义的（宽松）一致性模型供你选择](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## <a name="query-explorer-completes-with-errors"></a>查询浏览器完成时出错
请参阅[查询浏览器故障排除](documentdb-query-collections-query-explorer.md#troubleshoot)。

## <a name="no-data-available-in-monitoring-tiles"></a>监视磁贴中未提供数据
请参阅[监视磁贴故障排除](documentdb-monitor-accounts.md#troubleshooting)。

## <a name="no-documents-returned-in-document-explorer"></a>文档资源管理器中未返回任何文档
请参阅[文档资源管理器故障排除](documentdb-view-json-document-explorer.md#troubleshoot)。

## <a name="next-steps"></a>后续步骤
如果在门户中仍遇到问题，请发送电子邮件至 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) 寻求帮助，或者通过依次单击“浏览”、“帮助 + 支持”、“创建支持请求”来提出支持请求。


