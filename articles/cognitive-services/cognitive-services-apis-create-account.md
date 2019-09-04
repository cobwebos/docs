---
title: 在 Azure 门户中创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 通过在 Azure 门户中创建并订阅资源，开始使用 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274656"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 门户创建认知服务资源

使用此快速入门，通过 Azure 门户创建 Azure 认知服务资源。 成功创建认知服务资源后，你将获得可用于对应用程序进行身份验证的终结点和密钥。

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 订阅-[免费创建一个](https://azure.microsoft.com/free/)。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>新建 Azure 认知服务资源

在创建认知服务资源之前，必须具有 Azure 资源组才能包含资源。 创建新资源时，可以选择创建新的资源组或使用现有资源组。 本文介绍如何创建新资源组。

1. 登录 [Azure 门户](https://portal.azure.com)，然后单击“创建资源”。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 如前文所述，可以通过两种方式创建认知服务资源：使用多服务资源或单一服务资源。

    #### <a name="multi-service-resourcetabmultiservice"></a>[多服务资源](#tab/multiservice)

    若要创建多服务资源，请在搜索栏中输入 "**认知服务**"。

    ![搜索“认知服务”](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    在 "认知服务" 页上，选择 "**创建**"。

    ![创建认知服务帐户](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[单服务资源](#tab/singleservice)

    若要查看所有可用认知服务，请选择“Azure 市场”下的“AI + 机器学习”。 如果看不到你感兴趣的服务，请单击“查看全部”，并滚动到“认知服务”。 单击 "**查看更多**" 查看认知服务的整个目录。

    一旦处于感兴趣的服务上，单击 "**创建**"。
    
    ![选择认知服务 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. 在“创建”页中提供以下信息：

    #### <a name="multi-service-resourcetabmultiservice"></a>[多服务资源](#tab/multiservice)

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 例如， *MyCognitiveServicesResource*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **Location** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 请记住你的 Azure 位置，因为在调用 Azure 认知服务时可能需要用到它。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    单击“创建”。

    #### <a name="single-service-resourcetabsingleservice"></a>[单服务资源](#tab/singleservice)

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 例如， *TextAnalyticsResource*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **Location** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 请记住你的 Azure 位置，因为在调用 Azure 认知服务时可能需要用到它。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen.png)

    单击“创建”。

    ***

## <a name="get-the-keys-for-your-resource"></a>获取资源的密钥

成功创建资源后，屏幕的右上方会出现一个弹出通知。 在通知中，单击 "**前往资源**" 以查看创建的认知服务资源。 

![中转到认知服务资源](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

从打开的 "快速入门" 窗格中，可以访问终结点和密钥。

![获取密钥和终结点](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定价层和计费

定价层（以及您收取的费用）基于使用身份验证信息发送的事务数。 每个定价层指定：
* 每秒允许的最大事务数（TPS）。
* 在定价层中启用的服务功能。
* 预定义数量的事务的成本。 超出此数量将导致服务的[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中指定额外的费用。

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
