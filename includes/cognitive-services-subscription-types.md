---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: d5ed36700e2aea623fb259816e5baddff5215361
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "70381757"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
可以通过两种不同的资源访问 Azure 认知服务：多服务资源或单服务资源。

* 多服务资源：
    * 使用单个密钥和终结点访问多个 Azure 认知服务。
    * 合并来自你使用的服务的计费。
* 单服务资源：
    * 对于创建的每个服务，使用唯一密钥和终结点访问单个 Azure 认知服务。 
    * 使用免费层试用服务。   
