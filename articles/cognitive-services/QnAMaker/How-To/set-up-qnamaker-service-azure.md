---
title: 如何设置 QnA Maker 服务 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何设置 QnA Maker 服务
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366290"
---
# <a name="create-a-qna-maker-service"></a>创建 QnA Maker 服务

在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。

此设置将部署一些 Azure 资源。 这些资源共同管理知识库内容，并通过终结点提供问答功能。

1. 登录到 [Azure 门户](<https://portal.azure.com>)。

2.  单击“添加新资源”，在搜索中键入“qna maker”，然后选择 QnA Maker 资源

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  阅读条款和条件后，单击“创建”。

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. 在 **QnA Maker** 中，选择适当的层和区域。

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 使用唯一名称填写**名称**以标识此 QnA Maker 服务。 此名称还标识知识库将关联到的 QnA Maker 终结点。
    * 选择将部署 QnA Maker 资源的**订阅**。
    * 为 QnA Maker 管理服务（门户和管理 API）选择**管理定价层**。 有关 SKU 定价的详细信息，请参见[此处](https://aka.ms/qnamaker-pricing)。
    * 创建新的**资源组**（推荐）或使用现有的资源组来在其中部署此 QnA Maker 资源。
    * 选择 Azure 搜索服务的**搜索定价层**。 如果看到“免费层”选项显示为灰色，则表示你已在订阅中部署了免费 Azure 搜索层。 在这种情况下，将需要从基本 Azure 搜索层开始。 在[此处](https://azure.microsoft.com/en-us/pricing/details/search/)查看 Azure 搜索定价的详细信息。
    * 选择要部署 Azure 搜索数据的**搜索位置**。 必须存储客户数据的限制将通知你为 Azure 搜索选择的位置。
    * 在**应用名称**中为应用服务命名。
    * 默认情况下，应用服务默认为标准 (S1) 层。 可以在创建后更改该计划。 请在[此处](https://azure.microsoft.com/en-in/pricing/details/app-service/)查看应用服务定价的详细信息。
    * 选择将部署应用服务的**网站位置**。

        > [!NOTE]
        > “搜索位置”可以与“网站位置”不同。

    * 选择是否要启用 **Application Insights**。 如果启用了 **Application Insights**，则 QnA Maker 会收集有关流量、聊天记录和错误的遥测数据。
    * 选择将部署 Application Insights 资源的 **App insights 位置**。

5. 验证完所有字段后，便可以单击“创建”以开始在订阅中部署这些服务。 完成此部署可能需要几分钟时间。

6.  部署完成后，你将看到订阅中创建的以下资源。

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建并发布知识库](../Quickstarts/create-publish-knowledge-base.md)