---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a936afc611462b6ec3c3de9021d517ccf66f666b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586474"
---
## <a name="set-up"></a>设置

### <a name="create-a-translator-resource"></a>创建“翻译”资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建用于“翻译”的资源。 你还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services)。 注册之后，它就可以在 Azure 网站上使用了。
* 查看 [Azure 门户](https://portal.azure.com/)中的现有资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - “翻译”资源的订阅密钥。
* `TRANSLATOR_TEXT_ENDPOINT` -“翻译”的全局终结点。 改用 `https://api.cognitive.microsofttranslator.com/`
