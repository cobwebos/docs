---
title: 如何在 LUIS 中使用创作和运行时密钥
titleSuffix: Azure Cognitive Services
description: LUIS 使用两个密钥（创作密钥）创建模型，使用运行时键通过用户最谈话查询预测终结点。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220959"
---
# <a name="authoring-and-runtime-keys"></a>创作和运行时密钥

语言理解（LUIS）具有两个服务和 API 集： 

* 创作（以前称为_编程_）
* 预测运行时

有几种密钥类型，具体取决于要使用的服务，以及如何使用它。

## <a name="non-azure-resources-for-luis"></a>LUIS 的非 Azure 资源

### <a name="starter-key"></a>Starter 密钥

首次开始使用 LUIS 时，将为你创建一个**starter 密钥**。 此资源提供：

* 通过 LUIS 门户或 Api （包括 Sdk）免费创作服务请求
* 每月免费1000预测终结点请求，通过浏览器、API 或 Sdk

## <a name="azure-resources-for-luis"></a>用于 LUIS 的 Azure 资源

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS 允许三种类型的 Azure 资源： 
 
|密钥|目的|认知服务 `kind`|认知服务 `type`|
|--|--|--|--|
|[创作密钥](#programmatic-key)|通过创作、培训、发布和测试访问和管理应用程序的数据。 如果打算以编程方式创作 LUIS 应用，请创建 LUIS 创作密钥。<br><br>`LUIS.Authoring` 项的目的是让你：<br>* 以编程方式管理语言理解应用和模型，包括培训和发布<br> * 通过将用户分配到["参与者" 角色](#contributions-from-other-authors)来控制创作资源的权限。|`LUIS.Authoring`|`Cognitive Services`|
|[预测密钥](#prediction-endpoint-runtime-key)| 查询预测终结点请求。 请先创建 LUIS 预测密钥，然后客户端应用请求的预测超出了 starter 资源提供的1000请求。 |`LUIS`|`Cognitive Services`|
|[认知服务多服务资源密钥](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|与 LUIS 和其他受支持认知服务共享的查询预测终结点请求。|`CognitiveServices`|`Cognitive Services`|

资源创建过程完成后，[将密钥分配](luis-how-to-azure-subscription.md)到 LUIS 门户中的应用。

在要发布和查询的[区域](luis-reference-regions.md#publishing-regions)中创建 LUIS 应用很重要。

> [!CAUTION]
> 为方便起见，许多示例使用[Starter 密钥](#starter-key)，因为它在其[配额](luis-boundaries.md#key-limits)中提供了几个免费预测终结点调用。  


### <a name="query-prediction-resources"></a>查询预测资源

* 运行时密钥可用于所有 LUIS 应用或特定 LUIS 应用。 
* 请勿使用运行时密钥创作 LUIS 应用。 

LUIS 运行时终结点接受两种查询样式，两者都使用预测终结点运行时键，但在不同的位置。

用于访问运行时的终结点使用由下表中的 `{region}` 表示的资源区域独有的子域。 

## <a name="assignment-of-the-key"></a>密钥的分配

可以在[LUIS 门户](https://www.luis.ai)中或通过相应的 api[分配](luis-how-to-azure-subscription.md)运行时密钥。 

## <a name="key-limits"></a>密钥限制

每个订阅的每个区域最多可创建10个创作密钥。 

请参阅[密钥限制](luis-boundaries.md#key-limits)和[Azure 区域](luis-reference-regions.md)。 

发布区域不同于创作区域。 请确保在创作区域中创建与你希望客户端应用程序所在的发布区域相对应的应用。

## <a name="key-limit-errors"></a>密钥限制错误
如果超出每秒事务数（TPS）配额，会收到 HTTP 429 错误。 如果超出每个月的事务数（TPS）配额，会收到 HTTP 403 错误。 

## <a name="contributions-from-other-authors"></a>其他作者的贡献

**对于[创作资源迁移](luis-migration-authoring.md)的应用**：使用**访问控制（IAM）** 页在创作资源的 Azure 门户中管理_参与者_。 了解如何使用合作者的电子邮件地址和_参与者_角色[来添加用户](luis-how-to-collaborate.md)。 

**对于尚未迁移的应用**：所有_协作_者都在 LUIS 门户中的 "**管理-> 协作**者" 页上进行管理。

## <a name="move-transfer-or-change-ownership"></a>移动、传输或更改所有权

应用是由其 Azure 资源定义的，由所有者的订阅决定。 

可以移动 LUIS 应用。 使用 Azure 门户或 Azure CLI 中的以下文档资源：

* [在 LUIS 创作资源之间移动应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [在同一订阅中或跨订阅移动资源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

若要转让订阅的[所有权](../../cost-management-billing/manage/billing-subscription-transfer.md)： 

**对于已迁移的[创作资源](luis-migration-authoring.md)的用户**：作为资源的所有者，你可以添加 `contributor`。

**对于尚未迁移的用户**：将应用导出为 JSON 文件。 其他 LUIS 用户可以导入应用程序，从而成为应用所有者。 新应用程序将具有不同的应用程序 ID。  

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
|定型|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>预测终结点运行时访问

查询的访问权限是通过 "**管理**" 部分中的 "**应用程序信息**" 页上的设置控制的。 

|[专用终结点](#runtime-security-for-private-apps)|[公共终结点](#runtime-security-for-public-apps)|
|:--|:--|
|适用于所有者和参与者|适用于所有者、参与者和知道应用 ID 的任何其他人|

可以通过在服务器到服务器环境中调用来控制 LUIS 运行时密钥的查看者。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/functions/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/active-directory/)）。 如果调用了服务器端 API 并对其进行了身份验证和授权，请将对的调用传递给 LUIS。 尽管此策略不会阻止中间人攻击，但它进行模糊处理用户的密钥和终结点 URL，使你可以跟踪访问，并允许你添加终结点响应日志记录（例如[Application Insights](https://azure.microsoft.com/services/application-insights/)）。

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

LUIS 不具有转移资源所有权的概念。 

## <a name="securing-the-endpoint"></a>保护终结点安全 

可以通过在服务器到服务器环境中调用，来控制可以查看 LUIS 预测运行时终结点密钥的人员。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/functions/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/active-directory/)）。 如果调用服务器端 API 并且身份验证和授权得到验证，则将调用传递到 LUIS。 尽管此策略不会防止中间人攻击，但它针对用户模糊化处理终结点，允许跟踪访问，并允许添加终结点响应日志记录（如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。  

## <a name="next-steps"></a>后续步骤

* 了解[版本控制](luis-concept-version.md)概念。 
* 了解[如何创建密钥](luis-how-to-azure-subscription.md)。
