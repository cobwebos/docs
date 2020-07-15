---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144225"
---
## <a name="set-up"></a>设置

### <a name="create-a-translator-resource"></a>创建“翻译”资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建用于“翻译”的资源。 你还可以：

* 查看 [Azure 门户](https://portal.azure.com/)中的现有资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - “翻译”资源的订阅密钥。
* `TRANSLATOR_TEXT_ENDPOINT` -“翻译”的全局终结点。 改用 `https://api.cognitive.microsofttranslator.com/`
