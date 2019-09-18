---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393810"
---
## <a name="set-up"></a>设置

### <a name="create-a-translator-text-resource"></a>创建文本翻译资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建用于文本翻译的资源。 还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services)。 注册之后，它就可以在 Azure 网站上使用了。
* 查看 [Azure 门户](https://portal.azure.com/)中的现有资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - 文本翻译资源的订阅密钥。
* `TRANSLATOR_TEXT_ENDPOINT` - 文本翻译的全局终结点。 使用 `https://api.cognitive.microsofttranslator.com/`。
