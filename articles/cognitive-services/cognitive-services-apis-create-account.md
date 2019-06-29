---
title: 在 Azure 门户中创建认知服务帐户
titlesuffix: Azure Cognitive Services
description: 如何在 Azure 门户中创建 Azure 认知服务 API 帐户。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445855"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>创建认知服务帐户使用 Azure 门户

在此快速入门中，将了解如何注册 Azure 认知服务并创建具有单一服务或多服务订阅的帐户。 这些服务由 Azure [资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示，允许连接到一个或多个 Azure 认知服务 API。

## <a name="prerequisites"></a>必备组件

* 有效的 Azure 订阅。 免费[创建一个帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>创建新的 Azure 认知服务资源

创建资源时之前, 必须具有 Azure 资源组。 每个认知服务帐户 （和其关联的 Azure 资源） 必须属于 Azure 资源组。 创建帐户时，必须创建新的资源组，或使用现有的选项。 本文介绍如何创建新资源组。

1. 登录 [Azure 门户](https://portal.azure.com)，然后单击“创建资源”  。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 您可以按以下方式找到具有可用的认知服务：
    * 使用搜索栏，然后输入你想要订阅的服务的名称。
        * 若要创建多服务订阅的资源，请输入**认知服务**在搜索栏中，然后选择**认知服务**资源。

        ![搜索“认知服务”](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * 若要查看所有可用的认知服务，请选择**AI + 机器学习**下**Azure Marketplace**。 如果看不到感兴趣的服务，单击**查看所有**并滚动到**认知服务**。 单击**详细**若要查看的整个目录中的认知服务 Api。
    
        ![选择认知服务 API](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. 在“创建”页中提供以下信息： 

    > [!IMPORTANT]
    > 请记住你的 Azure 位置，因为调用 Azure 认知服务时可能会需要它。

    |    |    |
    |--|--|
    | **Name** | 认知服务资源的描述性名称。 建议使用描述性的名称，例如“MyCognitiveServicesAccount”  。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 [Azure 资源组](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>获取订阅密钥

创建资源后，如果已固定该资源，则可以从 Azure 仪表板对其进行访问。 否则，可以在“资源组”中查找该资源  。 选择所需的资源之后, 中，你可以获取的密钥通过选择**密钥**下**资源管理**。

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清理资源

如果你想要清理和删除认知服务订阅，则可以删除资源或资源组。 删除资源组也会删除与资源组相关联的任何其他资源。

若要使用 Azure 门户删除资源组，请执行以下操作：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。 
2. 找到要删除的资源组并右键单击列表右侧的多个按钮 （...）。
3. 选择“删除资源组”并进行确认。 

## <a name="see-also"></a>另请参阅

* [对 Azure 认知服务的请求进行身份验证](authentication.md)
* [Azure 认知服务有哪些？](Welcome.md)
* [自然语言支持](language-support.md)
* [Docker 容器支持](cognitive-services-container-support.md)
