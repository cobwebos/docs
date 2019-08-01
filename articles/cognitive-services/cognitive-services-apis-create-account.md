---
title: 在 Azure 门户中创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 通过在 Azure 门户中创建并订阅资源, 开始使用 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: af01c0c2586ce7df1902a0bcc502c6fd06a5215d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697907"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 门户创建认知服务资源

使用本快速入门教程开始使用 Azure 门户的 Azure 认知服务。 认知服务由你在 Azure 订阅中创建的 Azure[资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示。 创建资源后, 请使用生成的密钥和终结点对应用程序进行身份验证。 

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 订阅-[免费创建一个](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>新建 Azure 认知服务资源

在创建认知服务资源之前, 必须具有 Azure 资源组才能包含资源。 创建新资源时, 可以选择创建新的资源组或使用现有资源组。 本文介绍如何创建新资源组。

1. 登录 [Azure 门户](https://portal.azure.com)，然后单击“创建资源”  。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 可以通过以下方式查找可用的认知服务：
    * 使用搜索栏，并输入要订阅的服务的名称。
        * 若要创建多服务资源, 请在搜索栏中输入**认知服务**, 然后选择**认知服务**资源。

        ![搜索“认知服务”](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * 若要查看所有可用认知服务，请选择“Azure 市场”下的“AI + 机器学习”   。 如果看不到你感兴趣的服务，请单击“查看全部”，并滚动到“认知服务”   。 单击“更多”  以查看认知服务 API 的整个目录。
    
        ![选择认知服务 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. 在“创建”页中提供以下信息：

    > [!IMPORTANT]
    > 请记住你的 Azure 位置，因为在调用 Azure 认知服务时可能需要用到它。

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 例如*MyCognitiveServicesAccount*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **Location** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 [Azure 资源组](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>获取资源的密钥

创建资源后，如果已固定该资源，则可以从 Azure 仪表板对其进行访问。 否则，可以在“资源组”中查找该资源  。 在选择了资源后，可以通过选择“资源管理”下的“密钥”获取所需的密钥   。

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定价层和计费

定价层 (以及您收取的费用) 基于使用身份验证信息发送的事务数。 每个定价层指定:
* 每秒允许的最大事务数 (TPS)。
* 在定价层中启用的服务功能。
* 预定义事务量的成本。 超出此数量将导致服务的[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中指定额外的费用。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组也会删除组中包含的任何其他资源。

若要使用 Azure 门户删除资源组，请执行以下操作：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。 
2. 找到要删除的资源组，并右键单击列表右侧的“更多”按钮 (...)。
3. 选择“删除资源组”并进行确认。 

## <a name="see-also"></a>请参阅

* [对 Azure 认知服务的请求进行身份验证](authentication.md)
* [什么是 Azure 认知服务？](Welcome.md)
* [自然语言支持](language-support.md)
* [Docker 容器支持](cognitive-services-container-support.md)
