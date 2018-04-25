---
title: Microsoft Power BI 工作区集合入门
description: Power BI 工作区集合是一项 Azure 服务，应用程序开发人员可通过它将交互式 Power BI 报表添加到自己的应用程序中。
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: ce8638bab55b18ec4f15ed9fc11f3256659bfbd9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI 工作区集合入门

**Power BI 工作区集合**是一项 Azure 服务，应用程序开发人员可通过它将交互式 Power BI 报表添加到自己的应用程序中。 **Power BI 工作区集合**可配合现有应用程序使用，无需重新设计或更改用户登录方式。

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议你规划到 Power BI Embedded 的迁移以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

**Microsoft Power BI 工作区集合**的资源通过 [Azure 资源管理器 API](https://msdn.microsoft.com/library/mt712306.aspx) 进行预配。 在这种情况下，预配的资源为 **Power BI 工作区集合**。

![Power BI 工作区集合的常规流](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>创建工作区集合

**工作区集合** 是顶层的 Azure 资源，是要嵌入到应用程序的内容容器。 可采用两种方式创建工作区集合：

* 使用 Azure 门户手动创建
* 使用 Azure 资源管理器 API 以编程方式创建

以下让我们逐步完成使用 Azure 门户构建工作区集合的步骤。

1. 打开 **Azure 门户** ([http://portal.azure.com](http://portal.azure.com)) 并登录到其中。
2. 在顶部面板上选择“+ 新建”。
   
   ![Azure 门户中的“+ 新建”](media/get-started/create-workspace-1.png)
3. 在“数据 + 分析”下选择“Power BI 工作区集合”。
4. 如果已经有了 Power BI 工作区集合订阅，则请在入门消息底部选择“创建工作区集合”。

5. 在“工作区集合”上输入所需的信息。
   
   ![创建工作区集合](media/get-started/create-workspace-2.png)
1. 选择**创建**。

工作区集合需要一些时间进行预配。 完成后会转到“工作区集合”。

   ![Azure 门户中的工作区集合](media/get-started/create-workspace-3.png)

“创建”结果包含调用创建工作区并向其部署内容的 API 所需的信息。

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>查看 Power BI API 访问密钥

调用 Power BI REST API 所需的最重要信息之一是 **访问密钥**。 这些访问密钥用于生成对 API 请求进行身份验证的 **应用程序令牌** 。 若要查看访问密钥，请单击“设置”上的“访问密钥”。 有关应用令牌的详细信息，请参阅[通过 Power BI 工作区集合进行身份验证和授权](app-token-flow.md)。

   ![Azure 门户的“工作区集合”设置中的访问密钥](media/get-started/access-keys.png)

你会发现有两个密钥。

   ![访问密钥中的两个密钥](media/get-started/access-keys-2.png)

复制这两个密钥并将它们安全存储在应用程序中。 重要的是，要像对待密码一样对待这些密钥，因为它们可以提供工作区集合中所有内容的访问权限。

虽然列出了两个密钥，但是在特定时间只需要一个密钥。 提供第二个密钥以便定期重新生成密钥，且无需中断对服务的访问。

现在应用程序具有一个 Power BI 实例和两个 **访问密钥**，可以将报表导入自己的应用程序中。 在了解如何导入报表之前，下一节介绍了如何创建要嵌入到应用程序中的 Power BI 数据集和报表。

## <a name="working-with-workspaces"></a>使用工作区

创建工作区集合后，需要创建一个工作区来容纳报表和数据集。 若要创建工作区，需使用 [POST 工作区 REST API](https://msdn.microsoft.com/library/azure/mt711503.aspx)。

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>使用 Power BI Desktop 创建要嵌入到应用程序中的 Power BI 数据集和报表

至此，已创建应用程序的 Power BI 实例，并有了访问密钥，接下来需要创建要嵌入的 Power BI 数据集和报表。 可以使用 **Power BI Desktop**创建数据集和报表。 可以免费下载 [Power BI Desktop](https://go.microsoft.com/fwlink/?LinkId=521662)。 或者，若要快速开始使用，可以下载 [零售分析示例 PBIX](http://go.microsoft.com/fwlink/?LinkID=780547)。

> [!NOTE]
> 若要了解有关如何使用 **Power BI Desktop**的详细信息，请参阅 [Power BI Desktop 入门](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop)。

在 **Power BI Desktop** 中，可以通过将数据的副本导入 **Power BI Desktop** 来连接数据源，或者使用 **DirectQuery** 直接连接数据源。

以下是使用**导入**和 **DirectQuery** 之间的区别。

| 导入 | DirectQuery |
| --- | --- |
| 将表、列 *和数据* 导入或复制到 **Power BI Desktop**。 使用可视化效果时，**Power BI Desktop** 将查询数据的副本。 若要查看对基础数据所做的更改，必须刷新，或重新导入完整的当前数据集。 |仅将 *表和列* 导入或复制到 **Power BI Desktop**。 使用可视化效果时，**Power BI Desktop** 将查询基础数据源，这意味着你始终可以查看当前数据。 |

有关连接到数据源的详细信息，请参阅 [连接到数据源](connect-datasource.md)。

在 **Power BI Desktop**中保存工作后，将创建一个 PBIX 文件。 此文件包含报表。 此外，如果导入数据，则 PBIX 将包含完整数据集；而如果使用 **DirectQuery**，PBIX 将只包含数据集架构。 使用 [Power BI 导入 API](https://msdn.microsoft.com/library/mt711504.aspx)以编程方式将 PBIX 部署到工作区。

> [!NOTE]
> Power BI 工作区集合还有其他一些 API，用于更改数据集所指向的服务器和数据库，以及设置数据集用于连接数据库的服务帐户凭据。 请参阅 [Post SetAllConnections（发布 SetAllConnections）](https://msdn.microsoft.com/library/mt711505.aspx)和 [Patch Gateway Datasource（修补网关数据源）](https://msdn.microsoft.com/library/mt711498.aspx)。

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>使用 API 创建 Power BI 数据集和报表

### <a name="datasets"></a>数据集

可以使用 REST API 在 Power BI 工作区集合中创建数据集。 然后即可将数据推送到数据集中。 这样即可在不需要 Power BI Desktop 的情况下处理数据。 有关详细信息，请参阅 [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx)（发布数据集）。

### <a name="reports"></a>报告

可以通过 JavaScript API 直接在应用程序中基于数据集创建报表。 有关详细信息，请参阅[在 Power BI 工作区集合中基于数据集创建新报表](create-report-from-dataset.md)。

## <a name="see-also"></a>另请参阅

[示例入门](get-started-sample.md)  
[在 Power BI 工作区集合中进行身份验证和授权](app-token-flow.md)  
[嵌入报表](embed-report.md)  
[在 Power BI 工作区集合中基于数据集创建新报表](create-report-from-dataset.md)
[保存报表](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript 嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

有更多问题？ [尝试 Power BI 社区](http://community.powerbi.com/)

