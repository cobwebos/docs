---
title: 快速入门：使用 SDK 创建和使用学习循环 - 个性化体验创建服务
description: 本快速入门介绍如何使用客户端 SDK 创建和管理知识库。
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524863"
---
# <a name="quickstart-personalizer-client-library"></a>快速入门：个性化体验创建服务客户端库

在本快速入门中使用个性化体验创建服务显示个性化内容。

个性化体验创建服务客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。

 * 排名 API - 根据你提供的有关内容和上下文的实时信息，从内容项中选择最佳项。
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
>[个性化体验创建服务的工作原理](how-personalizer-works.md)

* [什么是个性化体验创建服务？](what-is-personalizer.md)
* [可在哪种场合下使用个性化体验创建服务？](where-can-you-use-personalizer.md)
* [故障排除](troubleshooting.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) 上找到此示例的源代码。
