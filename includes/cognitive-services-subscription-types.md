---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461508"
---
## <a name="azure-cognitive-service-subscription-types"></a>Azure 认知服务订阅类型

> [!NOTE]
> 订阅所有者可以通过应用 [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)，分配“不允许的资源类型”策略定义并指定“Microsoft.CognitiveServices/accounts”作为目标资源类型来禁止为资源组和订阅创建认知服务帐户  。

可以通过两个不同的订阅来访问 Azure 认知服务：多服务订阅或一个单一服务。 这些订阅可以同时连接到单个服务或多个服务。

### <a name="multi-service-subscription"></a>多服务订阅

>[!WARNING]
> 目前，以下服务  不支持多服务密钥：QnA Maker、 语音服务、 自定义影像和异常情况检测程序。

Azure 认知服务的多服务订阅，可以使用单个订阅和 Azure 资源的大部分 Azure 认知服务，并将合并从你使用的服务计费。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)了解其他信息。

### <a name="single-service-subscription"></a>单服务订阅

对单个服务，如计算机视觉或语音服务的订阅。 单服务订阅仅限于该资源。 
