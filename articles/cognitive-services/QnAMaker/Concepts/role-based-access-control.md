---
title: 与他人协作-QnA Maker
description: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 080e6549579675e27486e6173d5907d92bbaad70
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724915"
---
# <a name="collaborate-with-other-authors-and-editors"></a>与其他作者和编辑人员协作

使用基于角色的访问控制（RBAC）在 QnA Maker 资源上与其他作者和编辑器进行协作。

## <a name="access-is-provided-on-the-qna-maker-resource"></a>QnA Maker 资源上提供了访问权限

所有权限都受 QnA Maker 资源上的权限控制。 这些权限与读取、写入、发布和完全访问权限一致。

此 RBAC 功能包括：
* Azure Active Directory （AAD）与所有者和参与者的基于密钥的身份验证向后兼容100%。 客户可在其请求中使用基于密钥的身份验证或基于 RBAC 的身份验证。
* 快速将作者和编辑器添加到资源中的所有知识库，因为控件位于资源级别，而不是在知识库级别。

## <a name="access-is-provided-by-a-defined-role"></a>访问由定义的角色提供

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>身份验证流

下图显示了从作者的角度来登录到 QnA Maker 门户并使用创作 Api 的流。

> [!div class="mx-imgBorder"]
> ![下图显示了从作者的角度来登录到 QnA Maker 门户并使用创作 Api 的流。](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|步骤|说明|
|--|--|
|1|门户获取 QnA Maker 资源的标记。|
|2|门户会调用适当的 QnA Maker 创作 API （APIM），而不是通过密钥传递令牌。|
|3|QnA Maker API 验证令牌。|
|4 |QnA Maker API 调用 QnAMaker 服务。|

如果要调用[创作 api](../How-To/collaborate-knowledge-base.md)，请详细了解如何设置身份验证。

## <a name="authenticate-by-qna-maker-portal"></a>QnA Maker 门户进行身份验证

如果使用 QnA Maker 门户创建和协作，则在[将适当的角色添加到协作者的资源](../How-To/collaborate-knowledge-base.md)之后，QnA Maker 门户将管理所有访问权限。

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>QnA Maker Api 和 Sdk 进行身份验证

如果通过 REST 或 Sdk 使用 Api 创作和协作，则需要[创建服务主体](../../authentication.md#assign-a-role-to-a-service-principal)来管理身份验证。

## <a name="next-step"></a>后续步骤

* 为[语言](design-language-culture.md)和[客户端应用程序](integration-with-other-applications.md)设计知识库
