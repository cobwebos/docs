---
title: 运行 Azure 容器实例
titleSuffix: Azure Cognitive Services
description: 将使用情绪分析图像的文本分析容器部署到 Azure 容器实例，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9f174d54fcc74eed613eb69412bc0e515f15897b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711721"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>情绪分析容器部署到 Azure 容器实例

了解如何部署认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器与到 Azure 的情绪分析映像[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程其中演示了如何创建文本分析的资源、 关联的情绪分析图像和能够练习从浏览器两个此业务流程的创建。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

## <a name="prerequisites"></a>必备组件

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>后续步骤 

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接的服务](../vs-text-connected-service.md)
