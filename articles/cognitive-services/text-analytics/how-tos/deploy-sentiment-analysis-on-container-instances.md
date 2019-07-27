---
title: 运行 Azure 容器实例-文本分析
titleSuffix: Azure Cognitive Services
description: 使用情绪分析映像将文本分析容器部署到 Azure 容器实例, 并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552531"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>将情绪分析容器部署到 Azure 容器实例

了解如何通过情绪分析映像将认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 该过程演示了创建文本分析资源的方法、创建关联的情绪分析映像的方法，以及在浏览器中练习前两项的相关业务流程的功能。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

## <a name="prerequisites"></a>系统必备

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>后续步骤 

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接服务](../vs-text-connected-service.md)
