---
title: 升级 QnA Maker 服务 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何升级 QnA Maker 服务
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e8d3713d6729c4e30da9a64a382e9d5a647dfefd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366282"
---
# <a name="upgrade-your-qna-maker-service"></a>升级 QnA Maker 服务
你可以选择在初始创建后升级 QnA Maker 堆栈的各个组件。 有关从属组件和 SKU 选择的详细信息，请参阅[此处](https://aka.ms/qnamaker-docs-capacity)。

## <a name="upgrade-qna-maker-management-sku"></a>升级 QnA Maker 管理 SKU
升级 QnA Maker 管理 SKU：
1. 转到 Azure 门户中的 QnA Maker 资源，然后选择“定价层”。

    ![QnA Maker 资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. 选择合适的 SKU 并按“选择”。

    ![QnA Maker 定价](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>升级应用服务
你可以[扩大](https://docs.microsoft.com/azure/app-service/web-sites-scale)或缩减应用服务。

1. 转到 Azure 门户中的应用服务资源，并根据需要选择“扩大”或“缩减”选项。

    ![QnA Maker 应用服务缩放](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>升级 Azure 搜索服务
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
