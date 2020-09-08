---
title: 快速入门：使用 SDK 创建和使用学习循环 - 个性化体验创建服务
description: 本快速入门介绍如何使用个性化体验创建服务客户端库创建和管理知识库。
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: dccdec0888f2968fb7089c4ff80c9338215de135
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055945"
---
# <a name="quickstart-personalizer-client-library"></a>快速入门：个性化体验创建服务客户端库

在本快速入门中使用个性化体验创建服务显示个性化内容。

个性化体验创建服务客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。

 * 排名 API - 根据你提供的有关内容和上下文的实时信息，从操作中选择最佳项。
 * 奖励 API - 根据业务需要确定奖励分数，然后使用此 API 将其发送给个性化体验创建服务。 该分数可以是单个值（例如，1 表示“好”，0 表示“差”），也可以是你根据业务需求创建的算法。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [个性化体验创建服务的工作原理](how-personalizer-works.md)
> [使用个性化体验创建服务的场合](where-can-you-use-personalizer.md)
