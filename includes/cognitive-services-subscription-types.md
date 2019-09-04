---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274661"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure 认知服务资源类型

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
可以通过两种不同的资源访问 Azure 认知服务：多服务资源或单一服务资源。 这些订阅允许同时连接到单个认知服务或多个认知服务。

### <a name="multi-service-resource"></a>多服务资源

订阅多服务认知服务资源：
* 允许你将单个 Azure 资源用于大多数 Azure 认知服务。
* 获取可用于多个 Azure 认知服务的单个密钥。
* 合并你使用的服务中的计费。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)了解其他信息。

>[!WARNING]
> 目前，以下服务不支持多服务密钥：QnA Maker、语音服务、自定义视觉和异常探测器。

### <a name="single-service-resource"></a>单服务资源

订阅单个服务认知服务资源：
* 允许您使用指定的认知服务（例如计算机视觉或语音服务）创建资源。
* 获取特定于你为其创建资源的认知服务的密钥。