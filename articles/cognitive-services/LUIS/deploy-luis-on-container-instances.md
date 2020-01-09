---
title: 在 Azure 容器实例上部署 LUIS 容器
titleSuffix: Azure Cognitive Services
description: 将 LUIS 容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689457"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>将语言理解（LUIS）容器部署到 Azure 容器实例

了解如何将认知服务[LUIS](luis-container-howto.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建异常探测器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力转向管理基础结构，而不是专注于应用程序开发。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

LUIS 容器需要在运行时中请求的 `.gz` 模型文件。 容器必须能够通过卷装入容器实例访问此模型文件。 有关创建 Azure 文件共享的信息，请参阅[创建文件共享](../../storage/files/storage-how-to-create-file-share.md)。 记下 Azure 存储帐户名称、密钥和文件共享名称，因为稍后需要用到它们。

### <a name="export-and-upload-packaged-luis-app"></a>导出和上传打包的 LUIS 应用

若要将 LUIS 模型（打包应用）上传到 Azure 文件共享，需要<a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">先<span class="docon docon-navigate-external x-hidden-focus"></span>从 LUIS 门户中将其导出</a>。 在 Azure 门户中，导航到存储帐户资源的 "**概述**" 页，然后选择 "**文件共享**"。 选择最近创建的文件共享名称，然后选择 "**上传**" 按钮。

> [!div class="mx-imgBorder"]
> ![上传到文件共享](media/luis-how-to-deploy-to-aci/upload-file-share.png)

上传 LUIS 模型文件。

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
