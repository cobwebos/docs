---
title: Azure 资源-QnA Maker
description: QnA Maker 使用多个 Azure 源，每个都有不同的用途。 了解如何单独使用这些功能，可以规划和选择正确的定价层，或者知道何时更改定价层。 了解如何结合使用它们，可以在出现问题时查找并解决问题。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 2b3a06122ce6123cd8edcedf5dfbf38c3c12218a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843483"
---
# <a name="azure-resources-for-qna-maker"></a>适用于 QnA Maker 的 Azure 资源

QnA Maker 使用多个 Azure 源，每个都有不同的用途。 了解如何单独使用这些功能，可以规划和选择正确的定价层，或者知道何时更改定价层。 了解如何_结合_使用它们，可以在出现问题时查找并解决问题。

## <a name="resource-planning"></a>资源规划

当你首次在原型阶段开发 QnA Maker 知识库时，有一种是用于测试和生产的单个 QnA Maker 资源。

在移动到项目的开发阶段时，应考虑以下事项：

* 您的知识库系统将包含多少种语言
* 你需要知识库的可用区域数量
* 系统将包含的每个域中的文档数

计划使用单个 QnA Maker 资源包含具有相同语言、同一区域和相同使用者域组合的所有知识库。

## <a name="pricing-tier-considerations"></a>定价层注意事项

通常，你需要考虑三个参数：

* **你需要的服务吞吐量**：根据需要为你的应用服务选择合适的[应用计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。 可以[纵向扩展](https://docs.microsoft.com/azure/app-service/manage-scale-up)或收缩应用。 这还会影响 Azure 认知搜索 SKU 选择，有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/search/search-sku-tier)。

* **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，根据不同的使用者域的数量决定所需的知识库数。 一旦使用者域（适用于一种语言）应在一个知识库中。

    可以在特定的层中发布 N-1 个知识库，其中，N 是该层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

    例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引用于所有知识库以进行创作和测试。

* **源文档的数量**：QnA Maker 管理服务的免费 SKU 将可以通过门户和 API 管理的文档数限制为 3（每个文档的大小限制为 1 MB）。 标准 SKU 对于可以管理的文档数没有限制。 有关更多详细信息，请参阅[此处](https://aka.ms/qnamaker-pricing)。

下表提供了一些概要准则。

|                        | QnA Maker 管理 | 应用服务 | Azure 认知搜索 | 限制                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 试验        | 免费 SKU             | 免费层   | 免费层    | 分别最多发布 2 KB、50 MB 大小  |
| 开发/测试环境   | 标准 SKU         | 共享      | 基本        | 分别最多发布 14 KB、2 GB 大小    |
| 生产环境 | 标准 SKU         | 基本       | 标准     | 分别最多发布 49 KB、25 GB 大小 |

## <a name="when-to-change-a-pricing-tier"></a>何时更改定价层

|升级|原因|
|--|--|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)QnA Maker 管理 SKU|你需要在知识库中具有更多 QnA 集或文档源。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)应用服务 SKU|您的知识库需要提供客户端应用程序的更多请求，例如聊天机器人。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)Azure 认知搜索服务|你计划有多个知识库。|

通过[在 Azure 门户中更新应用服务](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)来获取最新的运行时更新。

## <a name="resource-naming-considerations"></a>资源命名注意事项

QnA Maker 资源的资源名称（如 `qna-westus-f0-b`）还用于命名其他资源。

使用 "Azure 门户创建" 窗口，可以创建 QnA Maker 资源，并为其他资源选择定价层。

> [!div class="mx-imgBorder"]
> QnA Maker 资源创建的 Azure 门户 ![屏幕截图](../media/concept-azure-resource/create-blade.png)

创建资源后，它们具有相同的名称，可选的 Application Insights 资源除外，该资源将字符 postpends 到名称。

> [!div class="mx-imgBorder"]
> Azure 门户资源列表的 ![屏幕快照](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 创建 QnA Maker 资源时，请创建新的资源组。 这样，你就可以在按资源组搜索时查看与 QnA Maker 资源关联的所有资源。

> [!TIP]
> 使用命名约定在资源或资源组的名称中指示定价层。 如果在创建新知识库或添加新文档时收到错误，则认知搜索定价层限制是常见问题。

## <a name="resource-purposes"></a>资源目的

使用 QnA Maker 创建的每个 Azure 资源都有特定用途：

* QnA Maker 资源
* 认知搜索资源
* 应用服务
* 应用计划服务
* Application Insights 服务


### <a name="cognitive-search-resource"></a>认知搜索资源

[认知搜索](../../../search/index.yml)资源用于：

* 存储 QnA 集
* 在运行时提供 QnA 集的初始排名（ranker #1）

#### <a name="index-usage"></a>索引使用情况

资源保留一个索引作为测试索引，其余索引与每个已发布的知识库关联。

定价为包含15个索引的资源将保留14个发布的知识库，而一个索引用于测试所有知识库。 此测试索引按知识库分区，以便使用 "交互式测试" 窗格的查询将使用测试索引，但仅从与特定知识库关联的特定分区返回结果。

#### <a name="language-usage"></a>语言用法

在 QnA Maker 资源中创建的第一个知识库用于确定认知搜索资源及其所有索引的_单一_语言集。 只能为 QnA Maker 服务_设置一种语言_。

### <a name="qna-maker-resource"></a>QnA Maker 资源

QnA Maker 资源提供对创作和发布 Api 的访问，以及运行时 QnA 集的基于自然语言处理（NLP）的第二排名层（ranker #2）。

第二个排名适用于智能筛选器，这些筛选器可以包含元数据和跟进提示。

### <a name="app-service-and-app-service-plan"></a>应用服务和应用服务计划

[应用服务](../../../app-service/index.yml)由客户端应用程序用于通过运行时终结点访问已发布的知识库。

若要查询已发布的知识库，所有发布的知识库均使用相同的 URL 终结点，但在路由中指定**知识库 ID** 。

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md)用于收集聊天日志和遥测数据。 查看常见的[Kusto 查询](../how-to/get-analytics-knowledge-base.md)以获取有关服务的信息。

## <a name="share-services-with-qna-maker"></a>与 QnA Maker 共享服务

QnA Maker 创建多个 Azure 资源。 若要减少成本共享的管理和权益，请使用下表来了解可以和不能共享的内容：

|服务|共享|原因|
|--|--|--|
|认知服务|X|不能通过设计|
|应用服务计划|✔|为应用服务计划分配的固定磁盘空间。 如果共享同一应用服务计划的其他应用使用了大量磁盘空间，QnAMaker 应用服务实例将会遇到问题。|
|应用服务|X|不能通过设计|
|Application Insights|✔|可以共享|
|搜索服务|✔|1. `testkb` 是 QnAMaker 服务的保留名称;其他用户不能使用它。<br>2. `synonym-map` 的名称是为 QnAMaker 服务保留的同义词映射。<br>3. 已发布的知识库数受搜索服务层限制。 如果有可用的可用索引，则其他服务可以使用这些索引。|

### <a name="using-a-single-cognitive-search-service"></a>使用单个认知搜索服务

如果通过门户创建 QnA 服务及其依赖项（如搜索），将为你创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

了解[如何将 QnA Maker 配置](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)为使用不同的认知服务资源，而不是创建 QnA Maker 资源创建过程的一部分。

## <a name="management-service-region"></a>管理服务区域

QnA Maker 的管理服务仅用于 QnA Maker 门户和初始数据处理。 此服务仅在**美国西部**区域提供。 此美国西部服务中未存储任何客户数据。

## <a name="keys-in-qna-maker"></a>QnA Maker 中的密钥

QnA Maker 服务处理两种类型的密钥：**创作密钥**和用于在应用服务中托管的运行时的**查询终结点密钥**。

如果你正在寻找**订阅密钥**，[术语已更改](#subscription-keys)。

通过 Api 向服务发出请求时使用这些密钥。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

|名称|位置|用途|
|--|--|--|
|创作密钥|[Azure 门户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|这些密钥用于访问[QnA Maker 管理服务 api](https://go.microsoft.com/fwlink/?linkid=2092179)。 通过这些 Api，您可以编辑知识库中的问题和解答，并发布您的知识库。 这些密钥是在创建新的 QnA Maker 服务时创建的。<br><br>在 "**密钥**" 页上的 "**认知服务**" 资源上查找这些密钥。|
|查询终结点密钥|[QnA Maker 门户](https://www.qnamaker.ai)|这些键用于查询已发布的知识库终结点，以获取用户问题的响应。 通常在聊天机器人中或连接到 QnA Maker 服务的客户端应用程序代码中使用此查询终结点。 这些密钥是在您发布 QnA Maker 知识库时创建的。<br><br>在 "**服务设置**" 页中查找这些密钥。 在下拉菜单上页面右上角的用户菜单中找到此页。|

### <a name="subscription-keys"></a>订阅密钥

术语 "创作" 和 "查询" 终结点是纠正条款。 以前的术语是**订阅密钥**。 如果你看到其他文档引用订阅密钥，则这些文档等效于创作和查询终结点密钥（在运行时中使用）。

您必须知道该密钥所访问的内容、知识库管理或知识库查询，才能知道您需要找到哪个密钥。

## <a name="next-steps"></a>后续步骤

* 了解 QnA Maker[知识库](knowledge-base.md)
* 了解[知识库生命周期](development-lifecycle-knowledge-base.md)
* 查看服务和知识库[限制](../limits.md)

