---
title: 与知识库协作-QnA Maker
description: 通过 QnA Maker，多名人员可针对知识库展开协作。 此功能通过 Azure 基于角色的访问控制提供。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650782"
---
# <a name="collaboration-with-authors-and-editors"></a>与作者和编辑器协作

在 QnA Maker 资源级别提供协作，使你能够基于合作者角色限制协作者访问权限。 详细了解 QnA Maker 协作者身份验证[概念](../Concepts/role-based-access-control.md)。

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>向 QnA Maker 资源添加基于角色的访问（RBAC）

QnA Maker 允许多人在同一 QnA Maker 资源中协作处理所有知识库。 此功能通过 Azure [基于角色的访问控制](../../../active-directory/role-based-access-control-configure.md)提供。

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker 资源级别的访问权限

不能在 QnA Maker 服务中共享特定的知识库。 如果需要更精细的访问控制，请考虑在不同的 QnA Maker 资源之间分布知识库，并将角色添加到每个资源。

## <a name="add-role-to-resource"></a>将角色添加到资源

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>将用户帐户添加到 QnA Maker 资源

以下步骤使用 "协作者" 角色，但可以使用以下步骤添加任何[角色](../reference-role-based-access-control.md)

1. 登录到[Azure](https://portal.azure.com/)门户，并中转到 QnA Maker 资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. 转到“访问控制 (IAM)”选项卡****。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. 选择 **添加** 。

    ![QnA Maker IAM 添加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. 从以下列表中选择一个角色：

    |角色|
    |--|
    |所有者|
    |参与者|
    |QnA Maker 读取器|
    |QnA Maker 编辑器|
    |认知服务用户|

    ![QnA Maker IAM 添加角色](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. 输入用户的电子邮件地址，并按 "**保存**"。

    ![QnA Maker IAM 添加电子邮件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

当你将 QnA Maker 服务的用户共享到[QnA Maker 门户](https://qnamaker.ai)时，他们可以根据其角色查看该服务中的所有知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [测试知识库](./test-knowledge-base.md)

了解有关协作的详细信息：
* [Azure](../../../active-directory/role-based-access-control-configure.md)基于角色的访问控制
* QnA Maker 基于角色的访问控制[概念](../Concepts/role-based-access-control.md)
