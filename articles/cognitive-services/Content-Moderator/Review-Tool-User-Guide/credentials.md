---
title: Azure 内容审查器中的凭据 | Microsoft Docs
description: 管理要与 API 一起使用的内容审查器凭据。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365440"
---
# <a name="manage-credentials"></a>管理凭据

内容审查器凭据的创建位置如下：

- [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [内容审查器“审阅”工具](http://contentmoderator.cognitive.microsoft.com/)

本文介绍了凭据所在的位置，以及凭据之间的关系。

## <a name="the-azure-portal"></a>Azure 门户

在 Azure 门户仪表板上，选择内容审查器帐户。 选择“资源管理”下的“密钥”。 若要复制密钥，请选择密钥右侧的图标。

![Azure 门户中的内容审查器密钥](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>如何结合使用 Azure 帐户和“审阅”工具
若要结合使用 Azure 密钥和审阅 API，请复制下面屏幕截图中“属性”屏幕上列出的“资源 ID”，并在“审阅”工具的“凭据”屏幕上的“列入白名单的一个或多个资源 ID”字段中输入此资源 ID，如下面的“资源 ID”部分所述。 

若要对内容审查器中的可用工作流使用 Azure 密钥，请在“工作流设置”部分的“Ocp-Apim-Subscription-Key”字段中输入此密钥，如下面的“工作流”部分所述。

> [!NOTE]
> 使用 Azure 订阅中的密钥和资源 ID 替换“审阅”工具中的两个位置后，将不再使用“凭据”屏幕上显示的“Trial Ocp-Apim-Subscription-Key”，但它始终可用。
> 试用版密钥限制为，每月最多 5,000 个事务，1 个请求/秒 (RPS)。

![Azure 门户中的内容审查器资源 ID](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>“审阅”工具

在“审阅”工具仪表板上，选择“设置”选项卡中的“凭据”。

![“审阅”工具中的内容审查器凭据](images/credentials-trial-resource-workflow.PNG)

下面的部分更详细地介绍了上图：


### <a name="api"></a>API

第一部分列出了在创建审阅团队过程中生成的“审阅 API 终结点”、“团队 ID”和“Ocp-Apim-Subscription-Key”（内容审查器试用版密钥）。 它们可用于调用所有内容审查器 API（包括审阅 API）。

另请注意，API 终结点的区域标识符。 例如，“westus”是“https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0”中的区域

![“审阅”工具中的内容审查器密钥](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>资源 ID

第二部分起初是空的，不含任何资源 ID。 **若要结合使用 Azure 订阅密钥和审阅 API，请转到“资源 ID”屏幕（如前所示），并将它复制到所显示的字段中**。 点击“+”，以保存资源 ID。

> [!NOTE]
> 内容审查器订阅区域应与审阅团队的区域一致，才能识别团队并访问团队数据。 例如，在本页面上的图中，“美国西部”区域 (4) 包含内容审查器 Azure 订阅和审阅团队。

![“审阅”工具中的内容审查器资源 ID](images/credentials-resourceids.PNG)


### <a name="workflows"></a>工作流

第三部分显示用于运行工作流的信息。 默认情况下，它起初显示的是自动生成的试用版密钥。 

**获取 Azure 订阅时，使用 Azure 密钥更新它**。 借助其他两个字段，可以分别在“筛查文本”和“评估图像”操作中使用术语列表和图像列表。

![“审阅”工具中的内容审查器工作流凭据](images/credentials-workflow.PNG)


## <a name="next-steps"></a>后续步骤

* 了解如何在[工作流](workflows.md)中使用内容审查器凭据。
