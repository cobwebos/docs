---
title: 设置 QnA Maker 服务 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967673"
---
# <a name="create-a-qna-maker-service"></a>创建 QnA Maker 服务

在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。

## <a name="create-a-new-service"></a>创建新服务

此过程部署几个 Azure 资源。 这些资源共同管理知识库内容，并通过终结点提供问答功能。

1. 登录到 Azure 门户并[创建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)资源。

1. 阅读条款和条件后, 选择 "**创建**"。

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，选择适当的层和区域。

    ![新建 QnA Maker 服务 - 定价层和区域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 使用唯一名称填写**名称**以标识此 QnA Maker 服务。 此名称还标识知识库将关联到的 QnA Maker 终结点。
    * 选择将部署 QnA Maker 资源的**订阅**。
    * 为 QnA Maker 管理服务 (门户和管理 Api) 选择**定价层**。 有关 SKU 定价的详细信息，请参见[此处](https://aka.ms/qnamaker-pricing)。
    * 创建新的**资源组**（推荐）或使用现有的资源组来在其中部署此 QnA Maker 资源。 QnA Maker 创建多个 Azure 资源;创建用于保存这些资源的资源组时, 可以通过资源组名称轻松查找、管理和删除这些资源。
    * 选择**资源组位置**。
    * 选择 Azure 搜索服务的**搜索定价层**。 如果看到“免费层”选项显示为灰色，则表示你已在订阅中部署了免费 Azure 搜索层。 在这种情况下，将需要从基本 Azure 搜索层开始。 在[此处](https://azure.microsoft.com/pricing/details/search/)查看 Azure 搜索定价的详细信息。
    * 选择要部署 Azure 搜索数据的**搜索位置**。 必须存储客户数据的限制将通知你为 Azure 搜索选择的位置。
    * 在**应用名称**中为应用服务命名。
    * 默认情况下，应用服务默认为标准 (S1) 层。 可以在创建后更改该计划。 请在[此处](https://azure.microsoft.com/pricing/details/app-service/)查看应用服务定价的详细信息。
    * 选择将部署应用服务的**网站位置**。

        > [!NOTE]
        > “搜索位置”可以与“网站位置”不同。

    * 选择是否要启用 **Application Insights**。 如果启用了 **Application Insights**，则 QnA Maker 会收集有关流量、聊天日志和错误的遥测数据。
    * 选择将部署 Application Insights 资源的 **App insights 位置**。
    * 为了节省成本, 可以[共享](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker)某些但不是所有为 QnA Maker 创建的 Azure 资源。 

1. 验证所有字段后, 可以选择 "**创建**" 以开始在订阅中部署这些服务。 完成此部署可能需要几分钟时间。

1. 部署完成后，你将看到订阅中创建的以下资源。

    ![新建 QnA Maker 服务资源](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>管理服务的区域

仅在美国西部提供 QnA Maker 的管理服务, 该服务仅用于门户 & 用于初始数据处理。 此美国西部服务中未存储任何客户数据。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建并发布知识库](../Quickstarts/create-publish-knowledge-base.md)
