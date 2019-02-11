---
title: 升级 QnA Maker 服务 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 你可以选择在初始创建后升级 QnA Maker 堆栈的各个组件。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 5cef98fedc0e2e29582c8cde8c75837880f1cf58
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216058"
---
# <a name="upgrade-your-qna-maker-service"></a>升级 QnA Maker 服务
你可以选择在初始创建后升级 QnA Maker 堆栈的各个组件。 有关从属组件和 SKU 选择的详细信息，请参阅[此处](https://aka.ms/qnamaker-docs-capacity)。

## <a name="upgrade-qna-maker-management-sku"></a>升级 QnA Maker 管理 SKU

当你需要在知识库中有更多问题和解答而超出当前层的能力范围时，请升级 QnA Maker 服务定价层。 

升级 QnA Maker 管理 SKU：

1. 转到 Azure 门户中的 QnA Maker 资源，然后选择“定价层”。

    ![QnA Maker 资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. 选择合适的 SKU 并按“选择”。

    ![QnA Maker 定价](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>升级应用服务

 当知识库需要处理来自客户端应用的更多请求时，请升级应用服务定价层。

你可以[扩大](https://docs.microsoft.com/azure/app-service/web-sites-scale)或缩减应用服务。

1. 转到 Azure 门户中的应用服务资源，并根据需要选择“扩大”或“缩减”选项。

    ![QnA Maker 应用服务缩放](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>升级 Azure 搜索服务

当计划拥有许多知识库时，请升级 Azure 搜索服务定价层。 

当前无法对 Azure 搜索 SKU 执行就地升级。 但是，你可以使用所需的 SKU 创建新的 Azure 搜索资源、将数据还原到新资源，然后将其链接到 QnA Maker 堆栈。

1. 在 Azure 门户中创建新的 Azure 搜索资源，然后选择所需的 SKU。

    ![QnA Maker Azure 搜索资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. 将索引从原始 Azure 搜索资源还原到新资源。 有关备份还原示例代码，请参阅[此处](https://github.com/pchoudhari/QnAMakerBackupRestore)。

3. 还原数据后，转到新的 Azure 搜索资源，选择“密钥”，并记下“名称”和“管理密钥”。

    ![QnA Maker Azure 搜索密钥](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. 若要将新的 Azure 搜索资源链接到 QnA Maker 堆栈，请转到 QnA Maker 应用服务。

    ![QnA Maker 应用服务](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. 选择“应用程序设置”并替换步骤 3 中的“AzureSearchName”和“AzureSearchAdminKey”字段。

    ![QnA Maker 应用服务设置](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. 重启应用服务。

    ![QnA Maker 应用服务重启](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 QnA Maker API](../Quickstarts/csharp.md)
