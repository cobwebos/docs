---
title: Azure 资源 - QnA 制造商
description: QnA Maker 使用多个 Azure 源，每个源具有不同的用途。 了解它们是如何单独使用的，您可以规划和选择正确的定价层，或知道何时更改定价层。 了解它们的组合使用方式，可以在问题发生时查找和修复问题。
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 1bd491ecbd878cb7bb05a7eaa5712c75653f2cba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804293"
---
# <a name="azure-resources-for-qna-maker"></a>用于 QnA 制造商的 Azure 资源

QnA Maker 使用多个 Azure 源，每个源具有不同的用途。 了解它们是如何单独使用的，您可以规划和选择正确的定价层，或知道何时更改定价层。 了解它们_的组合使用方式_，可以在问题发生时查找和修复问题。

## <a name="resource-planning"></a>资源规划

当您首次开发 QnA Maker 知识库时，在原型阶段，通常拥有用于测试和生产的单个 QnA Maker 资源。

进入项目的开发阶段时，应考虑：

* 您的知识库系统将持有多少种语言
* 您需要在/从中提供多少区域
* 系统将保存每个域中的文档数

计划让单个 QnA Maker 资源保存具有相同语言、相同区域和相同主题域组合的所有知识库。

## <a name="pricing-tier-considerations"></a>定价层注意事项

通常，你需要考虑三个参数：

* **需要的服务吞吐量**：
    * 根据需要为应用服务选择合适的[应用计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。 可以[纵向扩展](https://docs.microsoft.com/azure/app-service/manage-scale-up)或收缩应用。
    * 这还应影响 Azure**认知搜索**SKU 选择，[请参阅此处](https://docs.microsoft.com/azure/search/search-sku-tier)的更多详细信息。 此外，您可能需要调整具有副本的认知搜索[容量](../../../search/search-capacity-planning.md)。

* **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，您可以根据不同主题域的数量决定所需的知识库数量。 一旦主题域（对于单一语言）应位于一个知识库中。

    可以在特定的层中发布 N-1 个知识库，其中，N 是该层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

    例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引用于所有知识库以进行创作和测试。

* **源文档的数量**：QnA Maker 管理服务的免费 SKU 将可以通过门户和 API 管理的文档数限制为 3（每个文档的大小限制为 1 MB）。 标准 SKU 对于可以管理的文档数没有限制。 有关更多详细信息，请参阅[此处](https://aka.ms/qnamaker-pricing)。

下表提供了一些概要准则。

|                        | QnA Maker 管理 | 应用服务 | Azure 认知搜索 | 限制                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 试验        | 免费 SKU             | 免费层   | 免费层    | 分别最多发布 2 KB、50 MB 大小  |
| 开发/测试环境   | 标准 SKU         | Shared      | Basic        | 分别最多发布 14 KB、2 GB 大小    |
| 生产环境 | 标准 SKU         | Basic       | 标准     | 分别最多发布 49 KB、25 GB 大小 |

## <a name="when-to-change-a-pricing-tier"></a>何时更改定价层

|升级|原因|
|--|--|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)QnA 制造商管理 SKU|您希望在知识库中有更多的 QnA 对或文档源。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)应用服务 SKU 并检查认知搜索层并[创建认知搜索副本](../../../search/search-capacity-planning.md)|您的知识库需要从客户端应用（如聊天机器人）提供更多请求。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)Azure 认知搜索服务|你计划拥有许多知识库。|

通过在[Azure 门户中更新应用服务来](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)获取最新的运行时更新。

## <a name="resource-naming-considerations"></a>资源命名注意事项

QnA Maker 资源（如`qna-westus-f0-b`）的资源名称也用于命名其他资源。

Azure 门户创建窗口允许您创建 QnA Maker 资源，并为其他资源选择定价层。

> [!div class="mx-imgBorder"]
> ![用于创建 QnA Maker 资源的 Azure 门户屏幕截图](../media/concept-azure-resource/create-blade.png)

创建资源后，它们具有相同的名称，但可选的应用程序见解资源除外，这些资源将字符后发布到名称。

> [!div class="mx-imgBorder"]
> ![Azure 门户资源列表的屏幕截图](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 创建新资源组时创建 QnA Maker 资源。 这允许您在按资源组搜索时查看与 QnA Maker 资源关联的所有资源。

> [!TIP]
> 使用命名约定指示资源或资源组名称中的定价层。 当您收到创建新知识库或添加新文档时的错误时，认知搜索定价层限制是一个常见问题。

## <a name="resource-purposes"></a>资源用途

使用 QnA Maker 创建的每个 Azure 资源都有特定用途：

* QnA Maker 资源
* 认知搜索资源
* 应用服务
* 应用计划服务
* 应用程序洞察服务


### <a name="cognitive-search-resource"></a>认知搜索资源

[认知搜索](../../../search/index.yml)资源用于：

* 存储 QnA 对
* 在运行时提供 QnA 对的初始排名（排名#1）

#### <a name="index-usage"></a>索引使用情况

资源保留一个索引作为测试索引，其余索引与每个已发布的知识库相关联。

一个资源定价，以容纳15个索引，将持有14个已发布的知识库，一个索引用于测试所有的知识库。 此测试索引按知识库进行分区，以便使用交互式测试窗格的查询将使用测试索引，但仅返回与特定知识库关联的特定分区的结果。

#### <a name="language-usage"></a>语言用法

在 QnA Maker 资源中创建的第一个知识库用于确定认知搜索资源及其所有索引_的单一_语言集。 对于 QnA Maker 服务，您只能设置_一种语言_设置。

### <a name="qna-maker-resource"></a>QnA Maker 资源

QnA Maker 资源提供对创作和发布 API 以及运行时 QnA 对的第二排名层 （NLP）（排名#2）的访问。

第二个排名应用智能筛选器，其中可以包含元数据和后续提示。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA 制造商资源配置设置

在[QnA Maker 门户](https://qnamaker.ai)中创建新的知识库时，**语言**设置是在资源级别应用的唯一设置。 在为资源创建第一个知识库时，请选择该语言。

### <a name="app-service-and-app-service-plan"></a>应用服务和应用服务计划

客户端应用程序使用[应用服务](../../../app-service/index.yml)通过运行时终结点访问已发布的知识库。

要查询已发布的知识库，所有已发布的知识库都使用相同的 URL 终结点，但在路由中指定**知识库 ID。**

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[应用程序见解](../../../azure-monitor/app/app-insights-overview.md)用于收集聊天日志和遥测数据。 查看常见的[Kusto 查询](../how-to/get-analytics-knowledge-base.md)，了解有关服务的信息。

## <a name="share-services-with-qna-maker"></a>与 QnA 制造商共享服务

QnA Maker 创建多个 Azure 资源。 要减少管理并从成本分摊中获益，请使用下表了解您可以共享和不能共享的内容：

|服务|共享|原因|
|--|--|--|
|认知服务|X|设计上不可能|
|应用服务计划|✔|为应用服务计划分配的固定磁盘空间。 如果共享同一应用服务计划的其他应用使用大量磁盘空间，则 QnAMaker 应用服务实例将遇到问题。|
|应用服务|X|设计上不可能|
|Application Insights|✔|可以共享|
|搜索服务|✔|1.`testkb`是 QnAMaker 服务的保留名称;不能被别人使用。<br>2. 名称`synonym-map`的 Synonym 地图保留为 QnAMaker 服务。<br>3. 已发布的知识库的数量受搜索服务层的限制。 如果有可用的免费索引，其他服务可以使用它们。|

### <a name="using-a-single-cognitive-search-service"></a>使用单个认知搜索服务

如果通过门户创建 QnA 服务及其依赖项（如搜索），则为您创建搜索服务并链接到 QnA Maker 服务。 创建这些资源后，可以更新应用服务设置以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

[了解如何配置](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)QnA Maker 以使用不同于作为 QnA Maker 资源创建过程一部分创建的认知服务资源。

## <a name="management-service-region"></a>管理服务区域

QnA Maker 的管理服务仅用于 QnA 制造商门户和初始数据处理。 此服务仅在**美国西部**区域提供。 此美国西部服务中不存储任何客户数据。

## <a name="keys-in-qna-maker"></a>QnA 制造商中的密钥

QnA Maker 服务处理两种类型的密钥：创作与 App 服务中托管的运行时一起使用的**密钥**和**查询终结点密钥**。

如果您正在寻找**订阅密钥**[，术语已更改](#subscription-keys)。

通过 API 向服务发出请求时使用这些密钥。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

|名称|位置|目的|
|--|--|--|
|创作密钥|[Azure 门户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|这些密钥用来访问 [QnA Maker 管理服务 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 这些 API 允许您编辑知识库中的问题和答案，并发布您的知识库。 当您创建新的 QnA Maker 服务时，将创建这些密钥。<br><br>在 **"密钥**"页上的 **"认知服务**"资源上找到这些键。|
|查询终结点键|[QnA Maker 门户](https://www.qnamaker.ai)|这些键用于查询已发布的知识库终结点，以获得用户问题的响应。 您通常在聊天机器人或连接到 QnA Maker 服务的客户端应用程序代码中使用此查询终结点。 这些密钥是在发布 QnA Maker 知识库时创建的。<br><br>在 **"服务设置"** 页中找到这些键。 从下拉菜单右上角的用户菜单中查找此页面。|

### <a name="subscription-keys"></a>订阅密钥

创作和查询终结点键的术语是更正术语。 上一个术语是**订阅密钥**。 如果看到引用订阅密钥的其他文档，这些文档等效于创作和查询终结点密钥（在运行时使用）。

您必须知道密钥是访问、知识库管理或知识库查询，才能知道需要查找哪个密钥。

## <a name="next-steps"></a>后续步骤

* 了解 QnA 制造商[知识库](knowledge-base.md)
* 了解[知识库生命周期](development-lifecycle-knowledge-base.md)
* 审查服务和知识库[限制](../limits.md)

