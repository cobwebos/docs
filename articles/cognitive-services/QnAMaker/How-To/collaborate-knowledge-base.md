---
title: 针对知识库展开协作 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何针对 QnA Maker 知识库展开协作
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366251"
---
# <a name="collaborate-on-your-knowledge-base"></a>针对知识库展开协作

通过 QnA Maker，多名人员可针对知识库展开协作。 此功能通过 Azure [基于角色的访问控制](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure)提供。 

执行以下步骤，与他人共享 QnA Maker 服务：

1. 登录 Azure 门户，然后转到 QnA Maker 资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. 转到“访问控制 (IAM)”选项卡。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. 选择 **添加** 。

    ![QnA Maker IAM 添加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. 选择“所有者”或“参与者”角色。

    ![QnA Maker IAM 添加角色](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. 输入要与之共享的人员的电子邮件，然后按“保存”。

    ![QnA Maker IAM 添加电子邮件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

现在，要与之共享 QnA Maker 服务的人员登录 [QnA Maker 门户](https://qnamaker.ai)时，可以看到该服务中的所有知识库。

请记住，无法共享 QnA Maker 服务中的某个特定知识库。 如果想要实现更精细的访问控制，可考虑将知识库分布在不同 QnA Maker 服务中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [测试知识库](./test-knowledge-base.md)
