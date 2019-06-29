---
title: 运行 Azure 容器实例
titleSuffix: Text Analytics - Azure Cognitive Services
description: 将使用情绪分析图像的文本分析容器部署到 Azure 容器实例，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454979"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>将情绪分析容器部署到 Azure 容器实例 (ACI)

了解如何部署认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器与到 Azure 的情绪分析映像[容器实例](https://docs.microsoft.com/azure/container-instances/)(ACI)。 此过程其中演示了如何创建文本分析的资源、 关联的情绪分析图像和能够练习从浏览器两个此业务流程的创建。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

## <a name="prerequisites"></a>必备组件

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>后续步骤 

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接的服务](../vs-text-connected-service.md)
