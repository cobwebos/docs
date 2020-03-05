---
title: 导出 & 删除数据-LUIS
titleSuffix: Azure Cognitive Services
description: 您可以完全控制查看、导出和删除其数据。 删除客户数据以确保隐私和符合性。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273359"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>在认知服务中的语言理解 (LUIS) 中导出和删除客户数据

删除客户数据以确保隐私和符合性。

## <a name="summary-of-customer-data-request-features"></a>客户数据请求功能摘要
语言理解智能服务 (LUIS) 保存客户内容来使服务运转，但 LUIS 用户拥有完全控制权限来查看、导出以及删除其数据。 可通过 LUIS web[门户](luis-reference-regions.md)或[LUIS 创作（也称为编程） api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)完成此操作。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

客户内容以加密方式存储在 Microsoft 区域性 Azure 存储中，并且包括：

- 在注册时收集的用户帐户内容
- 生成模型所需的定型数据
- [活动学习](luis-concept-review-endpoint-utterances.md)使用的已记录用户查询，以帮助改进模型
  - 用户可以通过将 `&log=false` 追加到请求来关闭查询日志记录，[此处](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)提供了详细信息

## <a name="deleting-customer-data"></a>删除客户数据
LUIS 用户具有 "完全控制"，可通过 LUIS web 门户或 LUIS 创作（也称为编程） Api 来删除任何用户内容。 下表显示了对这两种方式有帮助的链接：

| | **用户帐户** | **应用程序** | **示例查询文本** | **最终用户查询** |
| --- | --- | --- | --- | --- |
| **门户** | [链接](luis-concept-data-storage.md#delete-an-account) | [链接](luis-how-to-start-new-app.md#delete-app) | [链接](luis-concept-data-storage.md#utterances-in-an-intent) | [主动学习最谈话](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[记录的最谈话](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>导出客户数据
LUIS 用户具有 "完全控制"，可以在门户中查看数据，但必须通过 LUIS 创作（也称为编程） Api 来导出数据。 下表显示了通过 LUIS 创作（也称为编程） Api 帮助进行数据导出的链接：

| | **用户帐户** | **应用程序** | **陈述** | **最终用户查询** |
| --- | --- | --- | --- | --- |
| **API** | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [链接](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>活动学习的位置

若要启用[活动学习](luis-how-to-review-endpoint-utterances.md#enable-active-learning)，在已发布的 LUIS 终结点上收到的用户的已记录最谈话存储在以下 Azure 地理区域中：

* [欧洲](#europe)
* [澳大利亚](#australia)
* [美国](#united-states)

除了活动学习数据（如下所述）之外，LUIS 遵循[区域服务的数据存储实践](https://azuredatacentermap.azurewebsites.net/)。

### <a name="europe"></a>欧洲

[Eu.luis.ai](https://eu.luis.ai)门户和欧洲创作（也称为编程式 api）托管在 Azure 欧洲地区。 Eu.luis.ai 门户和欧洲创作（也称为编程 Api）支持将终结点部署到以下 Azure 地域：

* 欧洲
* 法国
* United Kingdom

部署到这些 Azure 地理位置时，终结点从应用的最终用户那里收到的最谈话将存储在 Azure 欧洲地区，以便进行主动学习。 你可以禁用活动学习，请参阅[禁用活动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理存储的最谈话，请参阅[删除查询文本](luis-how-to-review-endpoint-utterances.md#delete-utterance)。

### <a name="australia"></a>澳大利亚

[Au.luis.ai](https://au.luis.ai)门户和澳大利亚创作（也称为编程式 api）托管在 Azure 的澳大利亚地区。 Au.luis.ai 门户和澳大利亚创作（也称为编程 Api）支持将终结点部署到以下 Azure 地域：

* 澳大利亚

部署到这些 Azure 地理位置时，终结点从应用的最终用户那里收到的最谈话将存储在 Azure 的澳大利亚地区，以便进行主动学习。 你可以禁用活动学习，请参阅[禁用活动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理存储的最谈话，请参阅[删除查询文本](luis-how-to-review-endpoint-utterances.md#delete-utterance)。

### <a name="united-states"></a>United States

[Luis.ai](https://www.luis.ai)门户和美国创作（也称为编程式 api）托管在 Azure 美国地理位置。 Luis.ai 门户和美国创作（也称为编程 Api）支持将终结点部署到以下 Azure 地域：

* 欧洲或澳大利亚的创作区域不支持 Azure 地理位置

部署到这些 Azure 地理位置时，终结点从应用的最终用户那里收到的最谈话将存储在 Azure 美国地理位置，以便进行主动学习。 你可以禁用活动学习，请参阅[禁用活动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理存储的最谈话，请参阅[删除查询文本](luis-how-to-review-endpoint-utterances.md#delete-utterance)。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [LUIS 区域参考](./luis-reference-regions.md)
