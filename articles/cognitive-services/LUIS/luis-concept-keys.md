---
title: 创作密钥和运行时密钥 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 使用两种密钥，其中的创作密钥用于创建模型，运行时密钥用于通过用户话语查询预测终结点。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 973a8dd56437506d907159f212164ff147ba975c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487498"
---
# <a name="authoring-and-runtime-keys"></a>创作和运行时密钥

语言理解（LUIS）具有两个服务和 API 集： 

* 创作（以前称为_编程_）
* 预测运行时

有几种密钥类型，具体取决于要使用的服务，以及如何使用它。

## <a name="non-azure-resources-for-luis"></a>LUIS 的非 Azure 资源

### <a name="starter-key"></a>初学者密钥

首次开始使用 LUIS 时，将为你创建一个**starter 密钥**。 此资源提供：

* 通过 LUIS 门户或 Api （包括 Sdk）免费创作服务请求
* 每月免费1000预测终结点请求，通过浏览器、API 或 Sdk

## <a name="azure-resources-for-luis"></a>用于 LUIS 的 Azure 资源

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS 允许三种类型的 Azure 资源： 
 
|键|目的|认知服务 `kind`|认知服务 `type`|
|--|--|--|--|
|[创作密钥](#programmatic-key)|通过创作、培训、发布和测试访问和管理应用程序的数据。 如果打算以编程方式创作 LUIS 应用，请创建 LUIS 创作密钥。<br><br>`LUIS.Authoring` 项的目的是让你：<br>* 以编程方式管理语言理解应用和模型，包括培训和发布<br> * 通过将用户分配到["参与者" 角色](#contributions-from-other-authors)来控制创作资源的权限。|`LUIS.Authoring`|`Cognitive Services`|
|[预测密钥](#prediction-endpoint-runtime-key)| 查询预测终结点请求。 请先创建 LUIS 预测密钥，然后客户端应用请求的预测超出了 starter 资源提供的1000请求。 |`LUIS`|`Cognitive Services`|
|[认知服务多服务资源密钥](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|与 LUIS 和其他受支持认知服务共享的查询预测终结点请求。|`CognitiveServices`|`Cognitive Services`|

资源创建过程完成后，[将密钥分配](luis-how-to-azure-subscription.md)到 LUIS 门户中的应用。

请务必在想要进行发布和查询的[区域](luis-reference-regions.md#publishing-regions)创作 LUIS 应用。

> [!CAUTION]
> 为了方便起见，很多示例使用[初学者密钥](#starter-key)，因为该密钥在[配额](luis-boundaries.md#key-limits)中提供了几个免费的预测终结点调用。  


### <a name="query-prediction-resources"></a>查询预测资源

* 运行时密钥可用于所有 LUIS 应用或特定 LUIS 应用。 
* 请勿将运行时密钥用于创作 LUIS 应用。 

LUIS 运行时终结点接受两种样式的查询，这两种查询都使用预测终结点运行时密钥，但使用的位置不同。

用于访问运行时的终结点使用一个特定于资源所在区域的子域，该区域在下表中使用 `{region}` 表示。 

## <a name="assignment-of-the-key"></a>密钥的分配

可以通过 [LUIS 门户](luis-how-to-azure-subscription.md)或相应的 API [分配](https://www.luis.ai)运行时密钥。 

## <a name="key-limits"></a>密钥限制

可以按订阅为每个区域创建最多 10 个创作密钥。 

请参阅[密钥限制](luis-boundaries.md#key-limits)和 [Azure 区域](luis-reference-regions.md)。 

发布区域不同于创作区域。 请确保在对应于发布区域的创作区域中创建应用，你需要将客户端应用程序置于该发布区域中。

## <a name="key-limit-errors"></a>密钥限制错误
如果超过了每秒事务数 (TPS) 配额，则会出现 HTTP 429 错误。 如果超过了每月事务数 (TPM) 配额，则会出现 HTTP 403 错误。 

## <a name="contributions-from-other-authors"></a>其他作者的贡献

**对于[创作资源迁移](luis-migration-authoring.md)的应用**：使用**访问控制（IAM）** 页在创作资源的 Azure 门户中管理_参与者_。 了解如何使用合作者的电子邮件地址和_参与者_角色[来添加用户](luis-how-to-collaborate.md)。 

**对于尚未迁移的应用**：所有协作者都在 LUIS 门户中通过“管理 -> 协作者”页面进行管理。

## <a name="move-transfer-or-change-ownership"></a>移动、传输或更改所有权

应用通过其 Azure 资源进行定义，这取决于所有者的订阅。 

你可以移动自己的 LUIS 应用。 在 Azure 门户或 Azure CLI 中，请参阅以下文档资源：

* [在 LUIS 创作资源之间移动应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [将资源移到新的资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)
* [在同一订阅内移动资源或跨订阅移动资源](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)

若要转让订阅的[所有权](../../billing/billing-subscription-transfer.md)： 

**对于已迁移的[创作资源](luis-migration-authoring.md)的用户**：作为资源的所有者，你可以添加 `contributor`。

**对于尚未迁移的用户**：将应用导出为 JSON 文件。 其他 LUIS 用户可以导入应用程序，从而成为应用所有者。 新应用程序将具有不同的应用程序 ID。  

## <a name="access-for-private-and-public-apps"></a>专用和公共应用的访问权限

对于**专用**应用，所有者和参与者可以使用运行时访问权限。 对于**公共**应用，任何具有自己的 Azure [认知服务](../cognitive-services-apis-create-account.md)或 [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 运行时资源和公共应用 ID 的人员均可使用运行时访问权限。 

目前没有公共应用的目录。

### <a name="authoring-access"></a>创作访问
从 [LUIS](luis-reference-regions.md#luis-website) 门户或[创作 API](https://go.microsoft.com/fwlink/?linkid=2092087) 访问应用的权限由 Azure 创作资源控制。 

所有者和所有参与者具有创作应用所需的访问权限。 

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

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>预测终结点运行时访问权限

查询预测终结点所需的访问权限由“应用程序信息”页上“管理”部分的设置进行控制。 

|[专用终结点](#runtime-security-for-private-apps)|[公共终结点](#runtime-security-for-public-apps)|
|:--|:--|
|可供所有者和参与者使用|可供所有者、参与者以及知道应用 ID 的任何其他人使用|

可以通过在服务器到服务器环境中调用 LUIS 运行时密钥来控制谁可以查看该密钥。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/functions/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/active-directory/)）。 如果调用并验证服务器端 API，则在确认授权后将调用传递到 LUIS。 尽管此策略不会阻止中间人攻击，但它进行模糊处理用户的密钥和终结点 URL，使你可以跟踪访问，并允许你添加终结点响应日志记录（例如[Application Insights](https://azure.microsoft.com/services/application-insights/)）。

#### <a name="runtime-security-for-private-apps"></a>专用应用的运行时安全性

专用应用的运行时仅适用于：

|密钥和用户|说明|
|--|--|
|所有者的创作密钥| 最多 1000 个终结点命中数|
|协作者/参与者的创作密钥| 最多 1000 个终结点命中数|
|由作者或协作者/参与者分配给 LUIS 的任何密钥|基于密钥用法层|

#### <a name="runtime-security-for-public-apps"></a>公共应用的运行时安全性

应用配置为公共后，任何有效的 LUIS 创作密钥或 LUIS 终结点密钥都可以查询应用，只要该密钥未使用整个终结点配额。

不是所有者或参与者的用户只有在获得应用 ID 时才能访问公共应用的运行时。 LUIS 不提供公共市场或其他搜索公共应用的方式。  

公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与资源密钥关联的任何区域中访问该应用。

## <a name="transfer-of-ownership"></a>转让所有权

LUIS 不具有转移资源所有权的概念。 

## <a name="securing-the-endpoint"></a>保护终结点安全 

可以通过在服务器到服务器环境中调用 LUIS 预测运行时终结点密钥来控制谁可以查看该密钥。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/functions/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/active-directory/)）。 如果调用服务器端 API 并且身份验证和授权得到验证，则将调用传递到 LUIS。 尽管此策略不会防止中间人攻击，但它针对用户模糊化处理终结点，允许跟踪访问，并允许添加终结点响应日志记录（如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。  

## <a name="next-steps"></a>后续步骤

* 了解[版本控制](luis-concept-version.md)概念。 
* 了解[如何创建密钥](luis-how-to-azure-subscription.md)。
