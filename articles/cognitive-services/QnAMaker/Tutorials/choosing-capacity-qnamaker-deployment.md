---
title: 部署的资源容量 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 有关选择 QnA Maker 部署的容量的指南
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 01548cf2de8db8f4dc9984598a5e5544bf97fd49
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432641"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>选择 QnA Maker 部署的容量

QnA Maker 服务依赖于三个 Azure 资源：
1.  应用服务（适用于运行时）
2.  Azure 搜索（用于存储问与答）
3.  App Insights（可选，用于存储聊天日志和遥测数据）

在创建 QnA Maker 服务之前，应当决定上述服务的哪个层适合你。 

通常，你需要考虑三个参数：
1. **你需要的服务吞吐量**：根据需要为你的应用服务选择合适的[应用计划](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/)。 可以[纵向扩展](https://docs.microsoft.com/azure/app-service/web-sites-scale)或收缩应用。 这应当也会影响你的 Azure 搜索 SKU 选择，请参阅[此处](https://docs.microsoft.com/azure/search/search-sku-tier)的更多详细信息。

2. **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/en-in/pricing/details/search/)。 可以在特定的层中发布 N-1 个知识库，其中，N 是该层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

3. **源文档的数量**：QnA Maker 管理服务的免费 SKU 将可以通过门户和 API 管理的文档数限制为 3（每个文档的大小限制为 1 MB）。 标准 SKU 对于可以管理的文档数没有限制。 有关更多详细信息，请参阅[此处](https://aka.ms/qnamaker-pricing)。

下表提供了一些概要准则。

|                        | QnA Maker 管理 | 应用服务 | Azure 搜索 | 限制                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 试验        | 免费 SKU             | 免费层   | 免费层    | 分别最多发布 2 KB、50 MB 大小  |
| 开发/测试环境   | 标准 SKU         | 共享      | 基本        | 分别最多发布 14 KB、2 GB 大小    |
| 生产环境 | 标准 SKU         | 基本       | 标准     | 分别最多发布 49 KB、25 GB 大小 |

若要对 QnA Maker 堆栈进行升级，请参阅[升级 QnA Maker 服务](../How-To/upgrade-qnamaker-service.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [升级 QnA Maker 服务](../How-To/upgrade-qnamaker-service.md)
