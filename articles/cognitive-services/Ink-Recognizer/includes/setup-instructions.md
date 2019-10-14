---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996858"
---
>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](../../cognitive-services-apis-create-account.md)为墨迹识别器创建资源。 

* 也可以免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册后，该密钥和终结点将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上可用。

创建资源后，通过打开 [Azure 门户](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)上的资源并单击“快速入门”  来获取终结点和密钥。

创建两个[环境变量](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)：

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 资源的终结点。 它将如下所示： <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` - 用于对请求进行身份验证的订阅密钥。   
