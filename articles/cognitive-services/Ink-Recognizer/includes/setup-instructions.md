---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304093"
---
>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](../../cognitive-services-apis-create-account.md)为墨迹识别器创建资源。

创建资源后，通过打开 [Azure 门户](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)上的资源并单击“快速入门”来获取终结点和密钥。

创建两个[环境变量](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)：

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 用于对请求进行身份验证的订阅密钥。 

* `INK_RECOGNITION_ENDPOINT` - 资源的终结点。 它将如下所示： <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
