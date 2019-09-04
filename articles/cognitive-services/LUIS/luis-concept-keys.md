---
title: 创作和运行时密钥-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 使用两个密钥（创作密钥）创建模型，使用运行时键通过用户最谈话查询预测终结点。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256957"
---
# <a name="authoring-and-runtime-keys"></a>创作和运行时密钥


>[!NOTE]
>请在继续操作之前[迁移](luis-migration-authoring.md)任何不使用 Azure 创作资源的应用。

LUIS 使用两种类型的 Azure 资源，每种类型都有密钥： 
 
* [创作](#programmatic-key)来创建意向、实体和标签最谈话、定型和发布。 准备好发布 LUIS 应用时，需要为分配到应用的[运行时提供一个预测终结点键](luis-how-to-azure-subscription.md)。
* [运行时的预测终结点键](#prediction-endpoint-runtime-key)。 客户端应用程序（如聊天机器人）需要通过此密钥访问运行时的**查询预测终结点**。 

|Key|用途|认知服务`kind`|认知服务`type`|
|--|--|--|--|
|[创作密钥](#programmatic-key)|创作、培训、发布和测试。|`LUIS.Authoring`|`Cognitive Services`|
|[预测终结点运行时密钥](#prediction-endpoint-runtime-key)| 使用用户查询文本查询预测终结点运行时，以确定意向和实体。|`LUIS`|`Cognitive Services`|

LUIS 还提供具有每月1000事务的[starter 密钥](luis-how-to-azure-subscription.md#starter-key)预测终结点配额。 

尽管不需要同时创建这两个密钥，但如果这样做，则会容易得多。

在要发布和查询的[区域](luis-reference-regions.md#publishing-regions)中创建 LUIS 应用很重要。

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>创作密钥

创作密钥是在创建 LUIS 帐户时自动创建的，并且是免费的。 当你开始使用 LUIS 时，每个创作[区域](luis-reference-regions.md)的所有 LUIS 应用中都有一个 starter key。 创作密钥的目的是提供身份验证，以管理 LUIS 应用或测试预测终结点查询。 

在 Azure 门户中创建创作密钥使你可以通过将用户分配到["参与者" 角色](#contributions-from-other-authors)来控制对创作资源的权限。 需要 Azure 订阅级别的权限才能添加参与者。 

若要找到创作密钥，请登录 [LUIS](luis-reference-regions.md#luis-website) 并单击右上方导航栏中的帐户名称，打开“帐户设置”。

![创作密钥](./media/luis-concept-keys/authoring-key.png)

若要进行**运行时查询**，请创建 Azure [LUIS 资源](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)。 

> [!CAUTION]
> 为方便起见，许多示例使用[Starter 密钥](#starter-prediction-endpoint-runtime-key)，因为它在其[配额](luis-boundaries.md#key-limits)中提供了几个免费预测终结点调用。  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>预测终结点运行时密钥 

需要**运行时终结点查询**时，创建语言理解（LUIS）资源，并将其分配给 LUIS 应用。 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

资源创建过程完成后，[将密钥分配](luis-how-to-azure-subscription.md)到应用。 

* 运行时（查询预测终结点）键允许基于创建运行时密钥时指定的使用计划对终结点进行配额。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h)了解定价信息。

* 运行时密钥可用于所有 LUIS 应用或特定 LUIS 应用。 
* 请勿使用运行时密钥创作 LUIS 应用。 

### <a name="starter-prediction-endpoint-runtime-key"></a>Starter 预测终结点运行时密钥

**Starter**预测终结点密钥免费提供，并包括1000预测终结点查询。 使用这些查询后，应为语言理解创建自己的预测终结点资源。  

这是为你创建的特殊资源。 它不会显示在 Azure 资源列表中，因为它是一个临时的开始密钥。 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>在查询中使用运行时键
LUIS 运行时终结点接受两种查询样式，两者都使用预测终结点运行时键，但在不同的位置。

用于访问运行时的终结点使用资源所在区域独有的子域， `{region}`如下表所示。 

|Verb|示例 URL 和密钥位置|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>`runtime-key` 的查询字符串值<br><br>将 `runtime-key` 的终结点查询值从创作（初学者）密钥更改为新的终结点密钥，以便使用 LUIS 终结点密钥配额率。 如果创建并分配了该密钥，但是没有更改 `runtime-key` 的终结点查询值，则不会使用终结点密钥配额。|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> `Ocp-Apim-Subscription-Key` 的标头值<br>如果创建运行时键，并分配运行时键，但不更改的终结点查询值`Ocp-Apim-Subscription-Key`，则不会使用运行时键。|

在以前的 URL 中使用的应用 ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 是用于[互动演示](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)的公共 IoT 应用。 

## <a name="assignment-of-the-runtime-key"></a>分配运行时密钥

可以在[LUIS 门户](https://www.luis.ai)中或通过相应的 api[分配](luis-how-to-azure-subscription.md)运行时密钥。 

## <a name="key-limits"></a>密钥限制

每个订阅的每个区域最多可创建10个创作密钥。 

请参阅[密钥限制](luis-boundaries.md#key-limits)和[Azure 区域](luis-reference-regions.md)。 

发布区域不同于创作区域。 请确保在创作区域中创建与你希望客户端应用程序所在的发布区域相对应的应用。

## <a name="key-limit-errors"></a>密钥限制错误
如果超出每秒事务数（TPS）配额，会收到 HTTP 429 错误。 如果超出每个月的事务数（TPS）配额，会收到 HTTP 403 错误。 

## <a name="contributions-from-other-authors"></a>其他作者的贡献



从合作者管理发布内容取决于应用程序的当前状态。

**对于[创作资源迁移](luis-migration-authoring.md)的应用**：使用**访问控制（IAM）** 页在创作资源的 Azure 门户中管理_参与者_。 了解如何使用合作者的电子邮件地址和_参与者_角色[来添加用户](luis-how-to-collaborate.md)。 

**对于尚未迁移的应用**：所有_协作_者都在 LUIS 门户中的 "**管理-> 协作**者" 页上进行管理。

### <a name="contributor-roles-vs-entity-roles"></a>参与者角色与实体角色

[实体角色](luis-concept-roles.md)适用于 LUIS 应用的数据模型。 协作者/参与者角色适用于创作访问级别。 

## <a name="moving-or-changing-ownership"></a>移动或更改所有权

应用是由其 Azure 资源定义的，由所有者的订阅决定。 

可以移动 LUIS 应用。 使用 Azure 门户或 Azure CLI 中的以下文档资源：

* [在 LUIS 创作资源之间移动应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [将资源移到新的资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)
* [在同一订阅中或跨订阅移动资源](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [转让](../../billing/billing-subscription-transfer.md)订阅的所有权 

## <a name="access-for-private-and-public-apps"></a>私有和公共应用的访问权限

对于**私有**应用程序，运行时访问权限适用于所有者和参与者。 对于**公共**应用程序，具有其自己的 Azure[认知服务](../cognitive-services-apis-create-account.md)或[LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)运行时资源的每个人都可以使用运行时访问权限，并且拥有公共应用的 ID。 

目前没有公共应用的目录。

### <a name="authoring-access"></a>创作访问权限
从[LUIS](luis-reference-regions.md#luis-website)门户或[创作 api](https://go.microsoft.com/fwlink/?linkid=2092087)访问应用程序由 Azure 创作资源来控制。 

所有者和所有参与者都有权创作应用程序。 

|创作访问权限包括|说明|
|--|--|
|添加或删除终结点密钥||
|导出版本||
|导出终结点日志||
|导入版本||
|公开应用|如果应用公开，任何拥有创作或终结点密钥的人员都可以查询应用。|
|修改模型|
|发布|
|查看用于[主动学习](luis-how-to-review-endpoint-utterances.md)的终结点陈述|
|训练|

### <a name="prediction-endpoint-runtime-access"></a>预测终结点运行时访问

查询的访问权限是通过 "**管理**" 部分中的 "**应用程序信息**" 页上的设置控制的。 

![将应用设置为公共](./media/luis-concept-security/set-application-as-public.png)

|[专用终结点](#runtime-security-for-private-apps)|[公共终结点](#runtime-security-for-public-apps)|
|:--|:--|
|适用于所有者和参与者|适用于所有者、参与者和知道应用 ID 的任何其他人|

可以通过在服务器到服务器环境中调用来控制 LUIS 运行时密钥的查看者。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/active-directory/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用了服务器端 API 并对其进行了身份验证和授权，请将对的调用传递给 LUIS。 尽管此策略不会阻止中间人攻击，但它进行模糊处理用户的密钥和终结点 URL，使你可以跟踪访问，并允许你添加终结点响应日志记录（例如[Application Insights](https://azure.microsoft.com/services/application-insights/)）。

#### <a name="runtime-security-for-private-apps"></a>私有应用的运行时安全性

专用应用的运行时仅适用于以下各项：

|密钥和用户|说明|
|--|--|
|所有者的创作密钥| 最多 1000 个终结点命中数|
|协作者/参与者创作密钥| 最多 1000 个终结点命中数|
|作者或协作者/参与者分配给 LUIS 的任何密钥|基于密钥用法层|

#### <a name="runtime-security-for-public-apps"></a>公共应用的运行时安全性

应用配置为公共后，任何有效的 LUIS 创作密钥或 LUIS 终结点密钥都可以查询应用，只要该密钥未使用整个终结点配额。

如果给定了应用 ID，则不是所有者或参与者的用户只能访问公共应用的运行时。 LUIS 不提供公共市场或其他搜索公共应用的方式。  

公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与资源密钥关联的任何区域中访问该应用。

## <a name="transfer-of-ownership"></a>转让所有权

**用于[创作资源迁移](luis-migration-authoring.md)的应用**： 

**对于尚未迁移的应用**：将应用导出为 JSON 文件。 其他 LUIS 用户可以导入应用程序，从而成为应用所有者。 新应用程序将具有不同的应用程序 ID。  

## <a name="securing-the-endpoint"></a>保护终结点安全 

可以通过在服务器到服务器环境中调用，来控制可以查看 LUIS 预测运行时终结点密钥的人员。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/active-directory/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用服务器端 API 并且身份验证和授权得到验证，则将调用传递到 LUIS。 尽管此策略不会防止中间人攻击，但它针对用户模糊化处理终结点，允许跟踪访问，并允许添加终结点响应日志记录（如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。  

## <a name="next-steps"></a>后续步骤

* 了解[版本控制](luis-concept-version.md)概念。 
* 了解[如何创建密钥](luis-how-to-azure-subscription.md)。
