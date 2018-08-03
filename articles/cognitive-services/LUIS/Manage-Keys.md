---
title: 在 LUIS 中管理密钥| Microsoft Docs
description: 使用语言理解 (LUIS) 管理编程 API、终结点和外部密钥。
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 1fdf0f21697e48fd5cb9eca4212596059a9519cb
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127111"
---
# <a name="manage-your-luis-keys"></a>管理 LUIS 密钥
借助密钥，可创作并发布 LUIS 应用或查询终结点。 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>关键概念
请参阅 [LUIS 中的密钥](luis-concept-keys.md)，了解 LUIS 创作和终结点密钥概念。

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>分配终结点密钥
“发布应用”页面上的“资源和密钥”表中已有一个密钥。 这是创作（初学者）密钥。 

1. 在 [Azure 门户](https://portal.azure.com)上创建一个 LUIS 密钥。 有关更多说明，请参阅[使用 Azure 创建终结点密钥](luis-how-to-azure-subscription.md)。
 
2. 若要添加在之前的步骤中创建的 LUIS 密钥，请单击“添加密钥”按钮，打开“将密钥分配至应用”对话框。 

    ![将密钥分配至应用](./media/luis-manage-keys/assign-key.png)
3. 在对话框中选择一个租户。 
 
    > [!Note]
    > 在 Azure 中，租户代表与服务关联的客户或组织的 Azure Active Directory ID。 如果之前使用个人 Microsoft 帐户注册过 Azure 订阅，则已有一个租户！ 登录到 Azure 门户时，会自动登录到[默认租户](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)。 可以任意使用此租户，不过可能会需要创建一个组织管理员帐户。

4. 选择与想添加的 Azure LUIS 密钥关联的 Azure 订阅。

5. 选择 Azure LUIS 帐户。 帐户的区域显示在括号中。 

    ![选择密钥](./media/luis-manage-keys/assign-key-filled-out.png)

6. 分配终结点密钥后，在所有终结点查询中使用它。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>发布区域
详细了解发布[区域](luis-reference-regions.md)，包括[欧洲](luis-reference-regions.md#publishing-to-europe)和[澳大利亚](luis-reference-regions.md#publishing-to-australia)的发布。 发布区域和创作区域不同。 请确保在对应于所需发布区域的创作区域中创建应用。

## <a name="unassign-key"></a>取消分配密钥

* 在“资源和密钥”列表中，单击想取消分配的实体旁边的回收站图标。 然后单击确认信息中的“确定”以确认删除。
 
    ![取消分配实体](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> 取消分配 LUIS 密钥不会将其从 Azure 订阅中删除。

## <a name="next-steps"></a>后续步骤

使用密钥，在“发布应用”页面中发布应用。 若要查看发布的相关说明，请参阅[发布应用](PublishApp.md)。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website