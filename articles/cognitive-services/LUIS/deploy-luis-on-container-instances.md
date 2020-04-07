---
title: 在 Azure 容器实例上部署 LUIS 容器
titleSuffix: Azure Cognitive Services
description: 将 LUIS 容器部署到 Azure 容器实例，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: f1a0a08351a03e46d6c3a1e82b68ecea6e36c015
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757246"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>将语言理解 (LUIS) 容器部署到 Azure 容器实例

了解如何将认知服务[LUIS](luis-container-howto.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建异常探测器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

LUIS 容器需要在运行时中拉取的 `.gz` 模型文件。 容器必须能够通过容器实例中的卷装载来访问此模型文件。 有关如何创建 Azure 文件共享的信息，请参阅[创建文件共享](../../storage/files/storage-how-to-create-file-share.md)。 记下 Azure 存储帐户名称、密钥和文件共享名称，因为稍后需要用到它们。

### <a name="export-and-upload-packaged-luis-app"></a>导出和上传打包的 LUIS 应用

若要将 LUIS 模型（打包的应用）上传到 Azure 文件共享，需要首先<a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">将其从 LUIS 门户导出<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在 Azure 门户中，导航到存储帐户资源的“概览”**** 页，然后选择“文件共享”。****。 选择最近创建的文件共享名称，然后选择“上传”**** 按钮。

> [!div class="mx-imgBorder"]
> ![上传到文件共享](media/luis-how-to-deploy-to-aci/upload-file-share.png)

上传 LUIS 模型文件。

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
