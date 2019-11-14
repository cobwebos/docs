---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 2fe3104d61b5fe2fbf9624ed2fd4fdb2de5686a2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750122"
---
获取用于对应用程序进行身份验证的密钥和终结点。 在本地计算机上使用 [Azure 门户](../../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../../cognitive-services-apis-create-account-cli.md) 创建用于文本分析的资源。 还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源

从试用订阅或资源获取密钥和终结点后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。 为密钥创建名为 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 的变量，为终结点创建名为 `TEXT_ANALYTICS_ENDPOINT` 的变量。