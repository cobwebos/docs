---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334255"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure 认知服务资源类型

> [!NOTE]
> 订阅所有者可以通过应用[Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)、分配 "不允许的资源类型" 策略定义来禁用资源组和订阅的认知服务资源的创建, 并指定**Cognitiveservices account/accounts**作为目标资源类型。

可以通过两种不同的资源访问 Azure 认知服务:多服务资源或单一服务资源。 通过这些订阅，可以同时连接到单个服务或多个服务。

### <a name="multi-service-resource"></a>多服务资源

>[!WARNING]
> 目前，以下服务  不支持多服务密钥：QnA Maker、语音服务、自定义视觉和异常探测器。

订阅多服务认知服务资源:
* 允许你将单个 Azure 资源用于大多数 Azure 认知服务。
* 合并你使用的服务中的计费。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)了解其他信息。

### <a name="single-service-resource"></a>单服务资源

将单一服务资源 (例如计算机视觉或语音服务) 限制为指定的服务。