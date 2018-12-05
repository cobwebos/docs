---
title: Azure Cosmos DB 设计模式：社交媒体应用 |Microsoft Docs
description: 利用 Azure Cosmos DB 的存储灵活性和其他 Azure 服务了解社交网络的设计模式。
keywords: 社交媒体应用
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: maquaran
ms.openlocfilehash: 6c2911ac65b95ea0a705944fdd8fb9288af28498
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165673"
---
# <a name="going-social-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 进行社交

生活在大规模互连的社会中，这意味着有时候你也成了社交网络中的一部分。 使用社交网络与朋友、同事和家人保持联系，有时还会与有共同兴趣的人分享我们的激情。

作为工程师或开发者，你可能想知道这些网络如何存储和互连数据。 或者，你甚至可能承担着为特定利基市场创建或构建新社交网络的任务。 这时就会产生一个大问题：所有这些数据是如何存储的？

假设你正在创建一个新型时尚的社交网络，用户可以在此网络中发布带有相关媒体（如图片、视频，甚至音乐）的文章。 用户可以对帖子发表评论并打分以进行评级。 主网站登录页上将提供用户可见并可与之交互的帖子源。 这种方法听起来并不复杂，但为了简单起见，我们就此止步。 （可自行深入了解受关系影响的自定义用户馈送，但这超出了本文的目标。）

那么，如何确定存储这些数据的方式以及位置？

你可能有使用 SQL 数据库的经验，或者了解[数据的关系模型](https://en.wikipedia.org/wiki/Relational_model)。 可开始绘制如下内容：

![说明相对关系模型的关系图](./media/social-media-apps/social-media-apps-sql.png)

非常标准且美观的数据结构，但却不可扩展。

不要误会，我一直致力于开发 SQL 数据库。 它们很棒，但就像每种模式、实践方法和软件平台一样，它并不适用于所有场景。

为什么在此方案中 SQL 不是最佳选择？ 我们来看一下单个帖子的结构。 如果我想在网站或应用程序中显示帖子，可能不得不执行查询...通过联接八个表 (!) 来仅仅显示单个帖子。 现在请想象一下：一系列帖子动态地加载并显示在屏幕上。你可能就明白我的意思了。

你可以使用一个功能足够强大的超大 SQL 实例来解决数以千计的查询，其中通过许多联接来呈现内容。 但当已经有一个更简单的解决方案存在时，为什么还要选择这种呢？

## <a name="the-nosql-road"></a>NoSQL 加载

本文将指导如何使用 Azure 的 NoSQL 数据库 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 经济高效地对社交平台的数据进行建模。 还将介绍如何使用其他 Azure Cosmos DB 功能，如 [Gremlin API](../cosmos-db/graph-introduction.md)。 使用 [NoSQL](https://en.wikipedia.org/wiki/NoSQL) 方法以 JSON 格式存储数据并应用[非规范化](https://en.wikipedia.org/wiki/Denormalization)，就可以将以前的复杂帖子转换为单个[文档](https://en.wikipedia.org/wiki/Document-oriented_database)：

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

可以使用单个查询获得，且无需联接。 此查询非常简单直观，且在预算方面，它所需要的资源更少，但得到的结果更好。

Azure Cosmos DB 的自动索引功能可确保为所有属性都编制索引。 自动索引甚至可以进行[自定义](index-policy.md)。 通过此无架构方法，可存储具有不同动态结构的文档。 也许明天你想让帖子有一个与其相关的类别或井号标签列表？ Cosmos DB 将使用添加的属性处理新文档，而无需我们进行额外的工作。

可以将对帖子的评论视为具有父属性的其他帖子。 （这可以简化对象映射。）

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

并且所有社交互动都可以作为计数器存储在单个对象上：

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

创建源只不过是创建文档的问题，文档可按给定的相关顺序保留帖子 ID 列表：

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

可拥有“最新”的流，其中包含按创建日期排序的帖子。 或者可拥有一个“最热门”的流，其中包含过去 24 小时内点赞数较多的帖子。 甚至可以根据关注者和兴趣等逻辑为每个用户实现自定义流。 它仍然是一个帖子列表。 虽然如何生成这些列表还是一个问题，但读取性能不会受到阻碍。 在获得其中一个列表之后，使用 [IN 运算符](how-to-sql-query.md#WhereClause)向 Cosmos DB 发布单个查询以一次性获取帖子的所有页面。

可以使用 [Azure 应用服务](https://azure.microsoft.com/services/app-service/) 的后台进程 - [Web 作业](../app-service/web-sites-create-web-jobs.md) - 来构建源流。 创建一个帖子后，可以通过使用 [Azure 存储](https://azure.microsoft.com/services/storage/)[队列](../storage/queues/storage-dotnet-how-to-use-queues.md)和 Web 作业（通过 [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 触发）触发后台处理，从而根据自己的自定义逻辑实现流内的帖子传播。

通过使用这种相同的技术创建最终一致性环境还可以以延迟方式处理评分和点赞。

至于关注者，则需要有更多的技巧来处理。 Cosmos DB 具有文档大小限制，而且读取/写入大型文档会影响应用程序的可伸缩性。 因此，可考虑使用以下结构，以文档形式存储关注者：

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

此结构可能适用于拥有数千位关注者的用户。 但如果有名人加入排名，此方法会生成大型文档，并可能最终达到文档大小上限。

为了解决此问题，可以使用一种混合方法。 可以在用户统计信息文档中存储关注者人数：

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

可以使用 Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md) 存储实际的关注者图形，以创建每位用户的[顶点](http://mathworld.wolfram.com/GraphVertex.html)和[边缘](http://mathworld.wolfram.com/GraphEdge.html)，从中反映出“A 关注 B”关系。 使用 Gremlin API 不仅可以获取某位用户的关注者，而且还能创建更复杂的查询以推荐具有共同点的用户。 如果在图形中添加用户喜欢或感兴趣的内容类别，就可以开始布置智能内容发现、推荐关注用户感兴趣的内容和查找具有共同点的用户等体验。

“用户统计信息”文档仍可用来在 UI 中创建卡或快速个人资料预览。

## <a name="the-ladder-pattern-and-data-duplication"></a>“阶梯”模式和数据重复

你可能已注意到，在引用帖子的 JSON 文档中，某个用户出现了多次。 你猜得没错，这种重复意味着在此非范式下，描述用户的信息可能在多处显示。

若要允许更快速地查询，请引发数据重复。 此负面影响的问题在于，如果某些操作导致用户的数据发生更改，那么需要查找该用户曾经执行过的所有活动并对这些活动全部进行更新。 听上去不太实用，对不对？

通过识别用户的关键属性解决该问题。对于每个活动，都会在应用程序中显示此属性。 如果在应用程序中直观显示一个帖子并仅显示创建者的姓名和照片，那么为什么还要在“createdBy”属性中存储用户的所有数据呢？ 如果对于每一条评论都只显示用户的照片，那么的确不需要该用户的其余信息。 这就会涉及到我所称的“阶梯模式”。

我们以用户信息为例：

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

通过查看此信息，可以快速检测出哪些是重要的信息，哪些不是，从而就会创建一个“阶梯”：

![阶梯模式关系图](./media/social-media-apps/social-media-apps-ladder.png)

最简单的一步称为 UserChunk，这是标识用户的最小信息块并可用于数据重复。 通过减少重复数据的大小直到只留下要“显示”的信息，可以降低大规模更新的可能性。

中间步骤被称为用户。 这是会在 Cosmos DB 上的大多数依赖性能的查询上使用的完整数据，也是最常访问和最重要的数据。 它包括由 UserChunk 表示的信息。

最复杂的一步是扩展用户。 它包括重要用户信息和其他不需要快速读取或最终使用的数据，如登录过程。 此数据可以存储在 Cosmos DB 外、Azure SQL 数据库或 Azure 存储表中。

为什么要拆分用户，甚至将此信息存储在不同的位置？ 从性能角度考虑，文档越大，查询成本将越高。 保持文档精简，使其包含为社交网络执行所有依赖性能的查询的正确信息。 存储最终方案的其他额外信息，例如用于使用情况分析和大数据计划的完整配置文件编辑、登录和数据挖掘。 不必关心数据挖掘的数据收集过程是否较慢，因为它在 Azure SQL 数据库上运行。 尽管你的用户拥有快速而精简的体验，但你仍会感到担心。 Cosmos DB 中存储的用户如下列代码所示：

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

贴子内容如下所示：

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

当区块属性受影响需要进行编辑时，可轻松找到受影响的文档。 只需使用指向编制索引属性的查询，例如 `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`，然后更新区块。

## <a name="the-search-box"></a>搜索框

幸运的是，用户将生成许多内容。 并且你应能够提供搜索和查找可能在其内容流中不直接显示的内容的能力，也许是由于未关注创建者，或者也许是因为只是想要尽力找到六个月之前发布的旧帖子。

由于使用的是 Azure Cosmos DB，因此可使用 [Azure 搜索](https://azure.microsoft.com/services/search/) 在几分钟内轻松实现搜索引擎，而无需键入任何代码（搜索进程和 UI 除外）。

为什么此过程如此简单？

Azure 搜索可实现它们称之为[索引器](https://msdn.microsoft.com/library/azure/dn946891.aspx)的内容，这是在数据存储库中挂钩的后台处理程序，可以自动添加、更新或删除索引中的对象。 它们支持 [Azure SQL 数据库索引器](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/)、[Azure Blobs 索引器](../search/search-howto-indexing-azure-blob-storage.md)和 [Azure Cosmos DB 索引器](../search/search-howto-index-documentdb.md)。 从 Cosmos DB 到 Azure 搜索的信息转换非常简单。 这两种技术都以 JSON 格式存储信息，因此只需[创建索引](../search/search-create-index-portal.md)并从要编制索引的文档中映射属性。 就这么简单！ 根据数据大小，可在几分钟内通过云基础结构中的最佳搜索即服务解决方案搜索所有内容。

有关 Azure 搜索的详细信息，请访问 [Hitchhiker’s Guide to Search（搜索漫游指南）](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)。

## <a name="the-underlying-knowledge"></a>基础知识

存储所有此内容（每天会不断增加）后，可能会思考这样一个问题：我可以使用所有来自用户的此信息流做些什么？

答案非常简单：将其投入使用并从中进行学习。

但是，可以学到什么？ 一些简单的示例包括[情绪分析](https://en.wikipedia.org/wiki/Sentiment_analysis)、基于用户偏好的内容建议，甚至自动执行内容审查器。内容审查器可确保通过社交网络发布的内容对该系列均安全。

由于想要深入了解，可能会认为自己需要更多数学科学方面的知识才能提取出简单数据库和文件之外的这些模式和信息，其实不然。

[Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)（[Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx) 的一部分）是一项全面托管的云服务，使你可以在简单的拖放界面中使用算法创建工作流、为 [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) 中自己的算法编码，或使用一些已生成或现成的 API，如：[文本分析](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2)、[内容审查器或[建议](https://gallery.azure.ai/Solution/Recommendations-Solution)。

若要完成上述任何一种机器学习方案，可使用 [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) 从不同源获取信息。 还可使用 [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) 来处理信息并生成可由 Azure 机器学习处理的输出。

另一个可用的选项是使用 [Azure 认知服务](https://www.microsoft.com/cognitive-services) 分析用户内容：不仅可以更好地理解它们（通过使用 [文本分析 API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api) 分析编写的内容），而且还可以使用[计算机视觉 API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) 检测不需要的内容或成人内容，并采取相应的措施。 认知服务包括大量不需要使用任何一种机器学习知识的现成的可用解决方案。

## <a name="a-planet-scale-social-experience"></a>全球范围内的社交体验

最后，还必须说明一个非常重要的项目：可伸缩性。 设计体系结构时，每个组件都应该自行缩放。 你最终将需要处理更多数据，或者希望拥有更大的地理覆盖范围。 幸运的是，使用 Cosmos DB 完成这两项任务是一种统包体验。

Cosmos DB 支持现成可用的[动态分区](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/)。 它会根据给定的分区键自动创建分区，分区键在文档中定义为属性。 定义正确的分区键操作必须在设计时完成。 有关详细信息，请参阅[选择正确的分区键](partitioning-overview.md#choose-partitionkey)文章。

对于社交体验，必须将分区策略与查询和写入方式保持一致。 （例如，推荐在同一分区内进行读取，并通过在多个分区上分散写入来避免“热点”。）某些选项为：基于临时键的分区（日/月/周）、按内容类别、按地理区域，或按用户。 这一切都取决于查询数据并在社交体验中显示数据的方式。

Cosmos DB 以透明方式在所有分区中运行查询（包括[聚合](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)），因此无需在数据增长过程中添加任何逻辑。

一段时间后，最终流量会增加，资源消耗（通过 [RU](request-units.md) 即“请求单位”进行度量）也会增加。 随着用户群体的增长，你将更频繁地进行读取和写入操作。 用户群体将开始创建和阅读更多内容。 因此，缩放吞吐量的能力至关重要。 增加 RU 非常容易。 可以通过在 Azure 门户中单击几次或[通过 API 发出命令](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer)来实现。

![扩展和定义分区键](./media/social-media-apps/social-media-apps-scaling.png)

如果情况不断好转会怎样？ 假设来自其他区域、国家/地区或大洲的用户注意到你的平台并开始使用。 真是太棒了！

可是等等！ 你很快就会发现他们使用平台的体验并不是最佳体验。 他们距离你的运营区域太远，会出现非常严重的延迟情况。 你显然不希望他们放弃使用。 要是有一种简单的方法可以扩大全球覆盖范围呢？ 方法就在这里！

通过 Cosmos DB，只需单击数次即可通过透明方式[全局复制数据](../cosmos-db/tutorial-global-distribution-sql-api.md)，并从[客户端代码](../cosmos-db/tutorial-global-distribution-sql-api.md)中自动选择可用区域。 此进程还意味着可以拥有[多个故障转移区域](high-availability.md)。

全局复制数据时，需确保客户端可以利用该数据。 如果正在使用 Web 前端或从移动客户端访问 API，则可以部署 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)并在所有所需区域克隆 Azure 应用服务（方法是通过使用性能配置支持扩展的全球覆盖范围）。 客户端访问前端或 API 时，将被路由到最近的应用服务，而该应用服务将连接到本地的 Cosmos DB 副本。

![将全球覆盖范围添加到社交平台](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>结束语

本文阐述了使用低成本服务完全在 Azure 上创建社交网络的替代方案。 它通过鼓励使用多层存储解决方案和称为“阶梯”的数据分发来提供结果。

![社交网络中各 Azure 服务之间的交互关系图](./media/social-media-apps/social-media-apps-azure-solution.png)

事实上，对于此类方案并没有万能方法。 需结合各种卓越的服务共同创建，才能提供绝佳的体验：Azure Cosmos DB 的速度和自由性，可用于提供绝佳的社交应用程序；Azure 搜索等一流搜索解决方案，可用于提供幕后的智能操作；Azure 应用服务的灵活性，不仅可以托管与语言无关的应用程序，甚至还可以托管功能强大的后台处理程序；Azure 存储和 Azure SQL 数据库的可扩展性，可用于存储大量数据；Azure 机器学习的分析功能，可创建能够为进程提供反馈，并且有助于我们向合适的用户提供合适的内容的知识和智能。

## <a name="next-steps"></a>后续步骤

若要了解有关 Cosmos DB 用例的详细信息，请参阅 [Cosmos DB 常见用例](use-cases.md)。
