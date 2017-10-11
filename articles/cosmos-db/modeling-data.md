---
title: "一种 NoSQL 数据库的文档数据建模 |Microsoft 文档"
description: "了解有关建模的 NoSQL 数据库的数据"
keywords: "对数据进行建模"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig1
documentationcenter: 
ms.assetid: 69521eb9-590b-403c-9b36-98253a4c88b5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2016
ms.author: arramac
ms.openlocfilehash: 16c387fe574243544cf54cf283c7713ddcaa1942
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="modeling-document-data-for-nosql-databases"></a>对文档的 NoSQL 数据库的数据进行建模
虽然但免架构的数据库，如 Azure Cosmos DB，使其非常轻松地实现对数据模型更改你应仍花一些时间来考虑有关你的数据。 

数据将如何存储？ 你的应用程序将如何检索和查询数据？ 是你的应用程序粗，读取或写入大量？ 

在阅读本文后, 你将能够回答以下问题：

* 如何应考虑文档数据库中的文档？
* 为什么应关注和数据建模是什么？ 
* 是如何在文档数据库建模数据到关系数据库不同？
* 如何速成版非关系数据库中的数据关系？
* 当嵌入数据和当链接到数据？

## <a name="embedding-data"></a>嵌入数据
启动对文档存储，如 Azure Cosmos DB 中的数据进行建模时尝试将你的实体视为**自包含的文档**在 JSON 中表示。

我们深入了解之前太更进一步，让我们收回几个步骤并看一下如何我们可能模型中关系数据库中，主题中的许多人都已熟悉的一些东西。 下面的示例演示如何将个人存储在关系数据库中。 

![关系数据库模型](./media/documentdb-modeling-data/relational-data-model.png)

在使用关系数据库，我们已讲授年规范化，规范化，规范化。

规范化数据通常包括一个实体，如人猜出，以及将它分解到离散的数据段。 在上面的示例中，一个人可以有多个联系人的详细信息记录，以及多个地址记录。 我们甚至更进一步，并通过进一步提取常见将分解联系人详细信息字段喜欢类型。 地址相同，此处每个记录具有之类的类型*主页*或*业务* 

对数据进行规范化是到引导内部**避免存储冗余数据**在每个记录，而是引用数据。 在此示例中，若要读取的人员，其所有联系人详细信息和地址，需要使用联接来有效地在运行时中聚合数据。

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

其联系人详细信息和地址使用更新的人员要求在多个单独的表之间的写入操作。 

现在让我们看看如何我们将模型与文档数据库中的自包含实体相同的数据。

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {            
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email: "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

使用上述方法我们现在有**非规范化**人员记录 where 我们**嵌入**给此人，他们的联系人详细信息和地址，例如相关中，向单个 JSON 文档的所有信息。
此外，因为我们要不局限于固定架构我们可以灵活地执行诸如完全具有不同形状的联系人详细信息。 

从数据库中检索完整人员记录现单个读取操作针对单个集合和单个文档。 更新用户记录，其联系人详细信息和地址，也是针对单个文档的单个写入操作。

通过反规范化的数据，你的应用程序可能需要发出较少的查询和更新以完成常见操作。 

### <a name="when-to-embed"></a>何时嵌入
一般情况下，使用嵌入的数据模型时：

* 有**包含**实体之间的关系。
* 有**一个到一些**实体之间的关系。
* 没有嵌入的数据，**很少更改**。
* 那里嵌入的数据不会增长**不受约束**。
* 没有嵌入的数据是**整型**到文档中的数据。

> [!NOTE]
> 通常非规范化的数据模型提供了更好地**读取**性能。
> 
> 

### <a name="when-not-to-embed"></a>何时不嵌入
虽然文档数据库中的经验法则是使非规范化的所有内容，并将嵌入到单个文档中的所有数据，这可能导致某些情况下，应当避免。

需要此 JSON 代码段。

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

这可能是嵌入的注释的 post 实体将如下所示如果我们已建模的典型的博客或 CMS，系统。 此示例的问题是注释数组是**unbounded**，这意味着可以有任何单个 post 的注释的数目没有 （实际） 限制。 当文档的大小可能会显著增长，这将成为问题。

文档的大小随着通过网络，以及读取和更新文档中的，在大规模情况下，传输数据的能力将受到影响。

在这种情况下，则可以更好的做法，请考虑以下模型。

    Post document:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment documents:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

此模型具有最新的三个注释中内嵌的 post 本身，这是带有固定数组绑定这一次。 其他备注中分组到批次的 100 注释中并存储在单独的文档。 批的大小已被选作 100，因为我们虚构的应用程序允许用户一次加载 100 的注释。  

嵌入的数据不是一个不错的主意的另一种是嵌入的数据通常使用跨文档，并且将经常发生变化时。 

需要此 JSON 代码段。

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

这可能表示人员的常用组合。 我们已选择将向每个项目组合文档中的常用信息。 在环境中相关的数据其中频繁地更改，如股票交易应用程序，嵌入频繁更改的数据将意味着您正在不断更新每个项目组合文档每次卖常用。

股票*zaza*可能被卖许多数百次在单个日期和数千个用户可能具有*zaza*上其产品组合。 与数据模型类似上面我们将需要更新很多时候的数千个组合文档导致系统每日，不会非常很好地扩展。 

## <a id="Refer"></a>引用数据
因此，嵌入数据可以很好地适用于许多情况下，但很明显时反规范你的数据将导致更多问题不是值得有方案。 因此我们现在该怎么办？ 

关系数据库不是你可以在其中创建实体之间的关系的唯一位置。 在文档数据库可以在实际上与其他文档中的数据相关的一个文档中具有信息。 现在，我将不提倡甚至一分钟，我们生成系统会更好地适用于 Azure Cosmos DB 中的关系数据库或任何其他文档数据库，但简单关系感觉没问题和可能非常有用。 

在我们选择了前面使用的常用组合从示例下面的 JSON 但这次我们引用上而不是将其嵌入项目组合的常用项。 在常用的项更改整天频繁仅需要更新的文档时，这种方式是单个的常用文档。 

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }


尽管这种方法立即缺点是你的应用程序是否需要以显示有关被保留时显示一个人的组合; 每个股票信息在这种情况下，你需要对数据库加载每个常用文档的信息进行多个行程。 此处我们进行了改进的写入操作，这发生这种情况经常在一天，但反过来泄露可能对此特定系统的性能产生的影响更少的读取操作的效率的决定。

> [!NOTE]
> 规范化数据模型**可能需要多个往返**到服务器。
> 
> 

### <a name="what-about-foreign-keys"></a>外键呢？
因为当前没有约束的概念外, 键或否则为在文档中有任何间文档关系实际上是"弱链接"和数据库本身将不会验证。 如果你想要确保文档引用实际存在的数据，然后你需要在你的应用程序，或通过使用服务器端触发器或存储的过程在 Azure Cosmos 数据库上执行此操作。

### <a name="when-to-reference"></a>何时引用
一般情况下，使用规范化的数据模型时：

* 表示**一到多**关系。
* 表示**多对多**关系。
* 相关数据**频繁更改**。
* 引用的数据可能是**unbounded**。

> [!NOTE]
> 通常规范化提供更好地**编写**性能。
> 
> 

### <a name="where-do-i-put-the-relationship"></a>其中将放关系？
关系的增长将帮助确定要将引用存储的文档中。

如果我们看一下下面模型发布服务器和丛书的 JSON。

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

如果每个发布服务器丛书数量较少时使用有限的增长，然后将存储在发布服务器文档内部的书籍引用可能会有用。 但是，如果不受限制的每个发布服务器的丛书数，然后此数据模型将会导致于可变、 不断增加的数组，如上面的示例发布服务器文档中所示。 

切换位菜单和工具栏将导致一种模型，仍表示相同的数据，但现在会避免这些大型的可变集合。

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents: 
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

在上面的示例中，我们已在发布服务器文档中删除不受限制的集合。 改为我们刚刚使用对发布服务器上每个书籍文档的引用。

### <a name="how-do-i-model-manymany-relationships"></a>如何模型多对多关系？
关系数据库中*多： 多*关系关系通常模型化联接表，只需将记录从其他表联接起来。 

![联接表](./media/documentdb-modeling-data/join-table.png)

你可能想要复制的相同的内容，使用的文档并生成类似于以下的数据模型。

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

这将起作用。 但是，加载其丛书任一的作者或使用其作者，加载一本书将始终需要对数据库的至少两个其他查询。 联接的文档，然后将另一个查询来获取实际的文档被联接的一个查询。 

如果所有执行此联接表粘附在一起的数据的两个段，然后为何不将其删除完全？
请考虑以下方面。

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

现在，如果我遇到了以下作者，立即知道他们所写的丛书和相反如果我遇到了以下书籍文档加载我将知道作者的 id。 这将保存你的应用程序必须作出的服务器数往返针对联接表减少该中间查询。 

## <a id="WrapUp"></a>混合数据模型
我们现在已经了解嵌入 （或反规范） 和引用 （或正则化） 数据，每个具有其自负并且每个具有会危害，正如我们所看到。 

它没有始终可以是以下之一或过有点混淆事项。 

根据你的应用程序使用特定的模式和工作负荷可能有混合嵌入其中的情况下引用的数据有意义和无法具有较少服务器的简单应用程序逻辑的同时，仍保持较高的性能往返过程。

请考虑以下 JSON。 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

此处我们已 （通常） 后接嵌入的模型，其中从其他实体的数据嵌入在顶级文档中，但引用的其他数据。 

如果你看一下簿文档，我们可以看到一些有趣的字段时我们将看看作者的数组。 没有*id*字段即回头参考作者文档中，标准做法在规范化模型中，我们使用的字段，但然后我们还具有*名称*和*thumbnailUrl*。 我们无法仍然使用*id*保留应用程序来获取任何其他信息和它需要从各自的作者文档使用的"链接"，但因为我们的应用程序显示的作者的名称和一个缩略图显示每本书我们可以保存一次往返过程到每个列表中的书籍服务器由反规范**某些**作者的数据。

当然，如果作者的名称更改，或者想要更新我们将不得不转更新其照片每本书它们不断发布，但是对于我们的应用程序，基于作者不要非常频繁更改其名称的假设这是一个可接受的设计决策。  

在示例中有**预先计算聚合**若要保存昂贵的处理在读取操作的值。 在示例中，某些作者文档中嵌入的数据是在运行时计算的数据。 每次发布新书籍时，创建簿文档**和**countOfBooks 字段设置为基于特定作者存在的书籍文档数的计算值。 此优化，都很不错读取大量的系统中我们可以承受在写入进行计算，以优化读取。

因为 Azure Cosmos DB 支持，让具有预先计算字段的模型的能力可**多文档事务**。 许多非 Sql 存储区无法跨文档执行事务，并因此提倡设计决策，如"始终嵌入的所有内容"，由于此限制。 使用 Azure Cosmos DB，可以使用服务器端触发器或存储的过程，插入丛书和更新所有在 ACID 事务中的作者。 现在，你不**具有**嵌入中的所有内容到一个文档只是为了确保你的数据保持一致。

## <a name="NextSteps"></a>后续步骤
这篇文章中的最大优点是了解免架构世界中的数据建模是先前一样重要。 

就像没有单个方法来表示一种屏幕上的数据，没有你的数据建模只有一种方法。 您需要了解你的应用程序以及它将生成使用，并处理数据。 然后，通过应用此处提供的准则的一些你可以设置有关创建满足你的应用程序的即时的模型。 当你的应用程序需要进行更改时，你可以利用的免架构数据库以接受更改并轻松地发展你的数据模型的灵活性。 

若要了解有关 Azure Cosmos DB 的详细信息，请参阅服务的[文档](https://azure.microsoft.com/documentation/services/cosmos-db/)页。 

若要了解如何到分片到多个分区数据引用的[Azure Cosmos DB 中的分区数据](documentdb-partition-data.md)。 
