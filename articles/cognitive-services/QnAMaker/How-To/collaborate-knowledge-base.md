---
title: 与知识库协作-QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过 QnA Maker，多名人员可针对知识库展开协作。 此功能通过 Azure 基于角色的访问控制提供。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660743"
---
# <a name="collaborate-on-your-knowledge-base"></a>针对知识库展开协作

QnA Maker 允许多人在同一 QnA Maker 资源中协作处理所有知识库。 此功能通过 Azure [基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)提供。

执行以下步骤，与他人共享 QnA Maker 服务：

1. 登录到 Azure 门户，并中转到 QnA Maker 资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. 转到“访问控制 (IAM)”选项卡。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. 选择 **添加** 。

    ![QnA Maker IAM 添加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. 选择“所有者”或“参与者”角色。 不能通过基于角色的访问控制授予只读访问权限。 所有者和参与者角色具有对 QnA Maker 服务的读写访问权限。

    ![QnA Maker IAM 添加角色](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. 输入用户的电子邮件地址，并按 "**保存**"。

    ![QnA Maker IAM 添加电子邮件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

当用户与共享 QnA Maker 服务时，会登录到[QnA Maker 门户](https://qnamaker.ai)，他们可以看到该服务中的所有知识库。

请记住，无法共享 QnA Maker 服务中的某个特定知识库。 如果想要实现更精细的访问控制，可考虑将知识库分布在不同 QnA Maker 服务中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [测试知识库](./test-knowledge-base.md)
