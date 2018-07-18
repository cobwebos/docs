---
title: 使用 Azure 内容审查器中的连接器访问其他 API | Microsoft Docs
description: 了解如何使用连接器访问内容审查器工作流的其他 API。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: d8114457e7079ca8772cab830bd011dcddf372f5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365468"
---
# <a name="connectors"></a>连接器

除了内容审查器 API 之外，Azure 内容审查器工作流还可以使用其他 API。 可以使用内容审查器中的连接器访问其他 API。 连接器可链接到其他 API。

内容审查器包括下面这些默认连接器：

* 情感 API
* 人脸 API
* PhotoDNA 云服务

![内容审查器可用的连接器](images/connectors-1.png)

## <a name="verify-your-credentials"></a>验证凭据 

定义工作流前，请先确保拥有要使用的连接器 API 的有效凭据：

1.  在“审阅”工具仪表板上，依次选择“设置” > “连接器”。

  ![在内容审查器中选择“连接器”](images/connectors-2.png)

2.  选择要验证其凭据的连接器旁边的“编辑”符号。

  ![在内容审查器中选择“编辑”符号](images/connectors-3.png)

3.  此时，订阅密钥显示。 如果执行任何编辑，请在完成后选择“保存”。

  ![内容审查器中的“编辑连接器”页](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>添加连接器

1.  必须有订阅密钥，才能添加连接器。 在“审阅”工具仪表板上，依次选择“设置” > “凭据”。 选择并复制“Ocp-Admin-Subscription-Key”框中的值。

2.  选择“连接器”。 选择“审阅”工具仪表板上显示的可用连接器之一。 然后，选择“连接”。 

  ![内容审查器中的“添加连接器”页](images/connectors-5.png)

3.  在“Ocp-Admin-Subscription-Key”框中，粘贴所复制的密钥。 然后选择“保存”。

## <a name="next-steps"></a>后续步骤

* 了解如何使用连接器[定义自定义工作流](workflows.md)。
