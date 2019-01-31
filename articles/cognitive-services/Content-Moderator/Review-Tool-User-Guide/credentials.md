---
title: 在 Azure 内容审查器中管理凭据 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 管理需要用于使用 API 的内容审查器凭据。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f3138a9d0fe201a9486c34c08b8cdb3cca47e1d7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207541"
---
# <a name="manage-content-moderator-service-credentials"></a>管理内容审查器服务凭据

内容审查器凭据的创建位置如下：

- [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [内容审查器“审阅”工具](https://contentmoderator.cognitive.microsoft.com/)

本文介绍了凭据所在的位置，以及凭据之间的关系。

## <a name="the-azure-portal"></a>Azure 门户

在 Azure 门户仪表板上，选择内容审查器帐户。 选择“资源管理”下的“密钥”。 若要复制密钥，请选择密钥右侧的图标。

![Azure 门户中的内容审查器密钥](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>将 Azure 帐户与审阅工具和审阅 API 配合使用
若要结合使用 Azure 密钥和审阅 API，请复制下面屏幕截图中“属性”屏幕上列出的“资源 ID”，并在“审阅”工具的“凭据”屏幕上的“列入允许列表的一个或多个资源 ID”字段中输入此资源 ID，如下面的“资源 ID”部分所述。 

> [!NOTE]
> 内容审查器订阅区域应与审阅团队的区域一致，才能识别团队并访问团队数据。 例如，在本页面上的图中，“美国西部”区域 (4) 包含内容审查器 Azure 订阅和审阅团队。
>
> 使用 Azure 订阅中的密钥和资源 ID 替换“审阅”工具中的两个位置后，将不再使用“凭据”屏幕上显示的“Trial Ocp-Apim-Subscription-Key”，但它始终可用。
> 试用版密钥限制为，每月最多 5,000 个事务，1 个请求/秒 (RPS)。

![Azure 门户中的内容审查器资源 ID](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>将 Azure 帐户与审阅工具中的工作流配合使用

若要对内容审查器中的可用工作流使用 Azure 密钥，请在“工作流设置”部分的“Ocp-Apim-Subscription-Key”字段中输入此密钥，如下面的“工作流”部分所述。 点击“+”，以保存资源 ID。

![“审阅”工具中的内容审查器工作流凭据](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>“审阅”工具

在“审阅”工具仪表板上，选择“设置”选项卡中的“凭据”。

![“审阅”工具中的内容审查器凭据](images/credentials-trial-resource-workflow.PNG)

下面的部分更详细地介绍了上图：

### <a name="api"></a>API

第一部分列出了在创建审阅团队过程中生成的“审阅 API 终结点”、“团队 ID”和“Ocp-Apim-Subscription-Key”（内容审查器试用版密钥）。 它们可用于调用所有内容审查器 API（包括审阅 API）。

另请注意，API 终结点的区域标识符。 例如，“westus”是“https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0”中的区域

![“审阅”工具中的内容审查器密钥](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>资源 ID

上一节[将 Azure 帐户与审阅工具和 API 配合使用](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api)中已对该组字段进行了介绍。 此字段通常为空，除非你将 Azure 资源 Id 添加到此字段，如上一部分中所述。

### <a name="workflows"></a>工作流

上一节[将 Azure 帐户与审阅工具中的工作流配合使用](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool)中已对该组字段进行了介绍。 默认情况下，审阅工具使用其自动生成的试用版密钥来运行工作流，并且这就是上一部分开头出现的内容。 借助其他两个字段，可以分别在“筛查文本”和“评估图像”操作中使用术语列表和图像列表。

## <a name="next-steps"></a>后续步骤

* 了解如何在[工作流](workflows.md)中使用内容审查器凭据。
