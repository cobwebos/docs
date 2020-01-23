---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122750"
---
## <a name="create-a-personalizer-azure-resource"></a>创建个性化体验创建服务 Azure 资源

使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于个性化体验创建服务的资源。 你还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `PERSONALIZER_RESOURCE_KEY`（代表资源键）。
* `PERSONALIZER_RESOURCE_ENDPOINT`（代表资源终结点）。

在 Azure 门户中，可以从“快速入门”页中获取密钥和终结点值  。
