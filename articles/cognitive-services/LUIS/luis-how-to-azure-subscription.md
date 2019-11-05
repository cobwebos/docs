---
title: 如何使用创作密钥和运行时密钥 - LUIS
titleSuffix: Azure Cognitive Services
description: 首次使用语言理解 (LUIS) 时，不需要创建创作密钥。 如果需要发布应用，然后使用运行时终结点，则需创建运行时密钥并将其分配给应用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 957b12ad00f53a5aed7ff2a1ecd4afd21e58eb93
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467453"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>使用创作和运行时资源密钥

创作和运行时资源为 LUIS 应用和预测终结点提供身份验证。

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

登录 LUIS 门户时，可以选择使用以下项继续操作：

* 免费的[试用密钥](#trial-key) - 提供创作和一些预测终结点查询。
* Azure [LUIS 创作](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)资源。 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>登录 LUIS 门户并开始创作

1. 登录 [LUIS 门户](https://www.luis.ai)并同意使用条款。
1. 开始使用 LUIS 应用时，请选择要使用的 LUIS 创作密钥的类型：免费试用密钥或新的 Azure LUIS 创作密钥。 

    ![选择语言理解创作资源的类型](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 完成资源选择过程以后，请[创建新应用](luis-how-to-start-new-app.md#create-new-app-in-luis)。 

## <a name="trial-key"></a>试用密钥

试用（初学者）密钥是为你提供的。 它用作身份验证密钥，用来查询预测终结点运行时，每月最多可以进行 1000 次查询。 

它在 LUIS 门户中的“用户设置”页和“管理 -> Azure 资源”页上均可见。 

准备好发布预测终结点后，请创建并分配创作密钥和预测运行时密钥，替换初学者密钥功能。 

## <a name="create-resources-in-the-azure-portal"></a>在 Azure 门户中创建资源

1. 使用[此链接](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)打开用于创建资源的 Azure 门户。
1. 选择“两者”，以便创建创作密钥和预测终结点运行时密钥。 
1. 输入创建资源所需的信息，然后选择“创建”以完成此过程。

    ![创建语言理解资源](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |名称|目的|
    |--|--|
    |资源名称| 你选择的自定义名称，用作创作和预测终结点查询的 URL 的一部分。|
    |订阅名称| 将对其收取资源费用的订阅。|
    |资源组| 你选择或创建的自定义资源组名称。 可以使用资源组将 Azure 资源分组，以便在同一区域进行访问和管理。|
    |创作位置|与模型关联的区域。|
    |创作定价层|定价层决定了每秒和每月的最大事务数。|
    |运行时位置|与发布的预测终结点运行时相关联的区域。|
    |运行时定价层|定价层决定了每秒和每月的最大事务数。|

    创建这两项资源后，请在 LUIS 门户中分配资源。

## <a name="create-resources-in-azure-cli"></a>在 Azure CLI 中创建资源

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 单独创建每项资源。 

资源 `kind`：

* 创作：`LUIS.Authoring`
* 预测：`LUIS` 

1. 登录 Azure CLI：

    ```console
    az login
    ```

    此时会打开一个浏览器，让你可以选择正确的帐户并提供身份验证。

1. 在名为 **的现有资源组中为** 区域创建一个类型为 `LUIS.Authoring`、名称为 `my-luis-authoring-resource` 的 _LUIS 创作资源_。`my-resource-group``westus` 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 在名为 **的现有资源组中为** 区域创建一个类型为 `LUIS`、名称为 `my-luis-prediction-resource` 的 _LUIS 预测终结点资源_。`my-resource-group``westus` 如果需要比免费层更高的吞吐量，请将 `F0` 更改为 `S0`。 详细了解[定价层和吞吐量](luis-boundaries.md#key-limits)。

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > 在 LUIS 门户的“管理 -> Azure 资源”上分配此密钥之前，该门户不能使用此密钥。

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>在 LUIS 门户中为所有应用分配创作资源

可以在 LUIS 中为单个应用或所有应用分配创作资源。 以下过程将所有应用分配给单个创作资源。

1. 登录到 [LUIS 门户](https://www.luis.ai)。
1. 在右上角的导航栏中选择用户帐户，然后选择“设置”。
1. 在“用户设置”页上选择“添加创作资源”，然后选择现有的创作资源。 选择“保存”。 

## <a name="assign-a-resource-to-an-app"></a>将资源分配给应用

可以通过以下过程将单个资源、创作或预测终结点运行时分配给应用。

1. 登录 [LUIS 门户](https://www.luis.ai)，然后从“我的应用”列表中选择一个应用。
1. 导航到“管理 -> Azure 资源”页。

    ![在 LUIS 门户中选择“管理 -> Azure 资源”，以便为应用分配资源。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 选择“预测或创作资源”选项卡，然后选择“添加预测资源”或“添加创作资源”按钮。 
1. 选择窗体中的字段以查找正确的资源，然后选择“保存”。  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>在不使用 LUIS 门户的情况下分配运行时资源

出于 CI/CD 管道等自动化目的，可能需要将 LUIS 运行时资源自动分配给 LUIS 应用。 为此，需要执行以下步骤：

1. 从此[网站](https://resources.azure.com/api/token?plaintext=true)获取 Azure 资源管理器令牌。 此令牌即将过期，请立即使用。 该请求将返回 Azure 资源管理器令牌。

    ![请求 Azure 资源管理器令牌和接收 Azure 资源管理器令牌](./media/luis-manage-keys/get-arm-token.png)

1. 使用该令牌从用户帐户有权访问的[获取 LUIS azure 帐户 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) 跨订阅请求 LUIS 运行时资源。 

    此 POST API 需要以下设置：

    |标头|值|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。| 
    |`Ocp-Apim-Subscription-Key`|你的创作密钥。|

    此 API 将返回 LUIS 订阅的 JSON 对象的数组，包括订阅 ID、资源组和资源名称（作为帐户名称返回）。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。 

1. 使用[将 LUIS azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，将令牌分配给 LUIS 资源。 

    此 POST API 需要以下设置：

    |类型|设置|值|
    |--|--|--|
    |标头|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。|
    |标头|`Ocp-Apim-Subscription-Key`|你的创作密钥。|
    |标头|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。 
    |正文||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功时，将返回“201 - 已创建状态”。 

## <a name="unassign-resource"></a>取消分配资源

1. 登录 [LUIS 门户](https://www.luis.ai)，然后从“我的应用”列表中选择一个应用。
1. 导航到“管理 -> Azure 资源”页。
1. 对于此资源，选择“预测或创作资源”选项卡，然后选择“取消分配资源”按钮。 

取消分配资源时，不会将其从 Azure 中删除。 只会将其从 LUIS 取消链接。 

## <a name="reset-authoring-key"></a>重置创作密钥

**对于[创作资源已迁移](luis-migration-authoring.md)的应用程序**：如果你的创作密钥已泄露，请重置该创作资源的 "**密钥**" 页上的 "Azure 门户中的密钥。 

**对于尚未迁移的应用**：可在 LUIS 门户的所有应用中重置此密钥。 如果通过创作 API 创作应用，则需将 Ocp-Apim-Subscription-Key 的值更改为新密钥。

## <a name="regenerate-azure-key"></a>重新生成 Azure 密钥

在 Azure 门户的“密钥”页上重新生成 Azure 密钥。

## <a name="delete-account"></a>删除帐户

要了解删除帐户时一并删除了哪些数据，请参阅[数据存储和删除](luis-concept-data-storage.md#accounts)。

## <a name="change-pricing-tier"></a>更改定价层

1.  在 [Azure](https://portal.azure.com) 中查找你的 LUIS 订阅。 选择该 LUIS 订阅。
    ![查找 LUIS 订阅](./media/luis-usage-tiers/find.png)
1.  选择“定价层”以查看可用的定价层。 
    ![查看定价层](./media/luis-usage-tiers/subscription.png)
1.  选择定价层，然后选择“选择”以保存更改。 
    ![更改 LUIS 支付层](./media/luis-usage-tiers/plans.png)
1.  定价更改完成后，页面将出现一个供于验证新定价层的弹出窗口。 
    ![验证 LUIS 支付层](./media/luis-usage-tiers/updated.png)
1. 请记住在“发布”页[分配此终结点密钥](#assign-a-resource-to-an-app)，并将其用于所有终结点查询。 

## <a name="viewing-azure-resource-metrics"></a>查看 Azure 资源指标

### <a name="viewing-azure-resource-summary-usage"></a>查看 Azure 资源使用摘要
可在 Azure 中查看 LUIS 使用情况信息。 “概述”页显示包含调用和错误在内的最新摘要信息。 如果发出 LUIS 终结点请求并立即查看“概述”页，则最多需要五分钟才会显示使用情况。

![查看使用概况](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>自定义 Azure 资源使用图表
通过指标可更加详细地了解数据。

![默认指标](./media/luis-usage-tiers/metrics-default.png)

可针对时间期限和指标类型配置度量值图表。 

![自定义指标](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果希望在达到特定事务阈值（例如 10,000 个事务）时收到通知，则可以创建警报。 

![默认警报](./media/luis-usage-tiers/alert-default.png)

添加针对特定时间段内“总调用数”指标的指标警报。 添加应接收该警报的所有人员的电子邮件地址。 添加应接收该警报的所有系统的 Webhook。 还可在触发警报时运行逻辑应用。 

## <a name="next-steps"></a>后续步骤

* 了解[如何使用版本](luis-how-to-manage-versions.md)来控制应用生命周期。
* 了解包括[创作资源](luis-concept-keys.md#authoring-key)和资源[参与者](luis-concept-keys.md#contributions-from-other-authors)的概念。
* 了解[如何创建](luis-how-to-azure-subscription.md)创作资源和运行时资源
* 迁移到新的[创作资源](luis-migration-authoring.md) 
