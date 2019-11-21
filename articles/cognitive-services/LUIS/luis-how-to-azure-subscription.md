---
title: How to use authoring and runtime keys - LUIS
titleSuffix: Azure Cognitive Services
description: When you first use Language Understanding (LUIS), you do not need to create an authoring key. When you intend to publish the app, then use your runtime endpoint, you need to create and assign the runtime key to the app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 876026b5399631728331c4a9e67482a34f9d0b2d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225555"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Using authoring and runtime resource keys

Authoring and runtime resources provide authentication to your LUIS app and prediction endpoint.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

When you sign in to the LUIS portal, you can choose to continue with:

* a free [trial key](#trial-key) - providing authoring and a few prediction endpoint queries.
* an Azure [LUIS authoring](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) resource. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Sign in to LUIS portal and begin authoring

1. Sign in to [LUIS portal](https://www.luis.ai) and agree to the terms of use.
1. Begin your LUIS app by choosing which type of LUIS authoring key you would like to use: free trial key, or new Azure LUIS authoring key. 

    ![选择语言理解创作资源的类型](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. When you are done with your resource selection process, [create a new app](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Trial key

The trial (starter) key is provided for you. It is used as your authentication key to query the prediction endpoint runtime, up to 1000 queries a month. 

It is visible on both the **User Settings** page and the **Manage -> Azure resources** pages in the LUIS portal. 

When you are ready to publish your prediction endpoint, create and assign authoring and prediction runtime keys, to replace the starter key functionality. 

## <a name="create-resources-in-the-azure-portal"></a>Create resources in the Azure portal

1. Use [this link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) to open the Azure portal at for resource creation.
1. Select **Both** to create an authoring and a prediction endpoint runtime key. 
1. Enter the information required to create the resource then select **Create** to finish the process.

    ![Create the language understanding resource](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |名称|用途|
    |--|--|
    |资源名称| A custom name you choose, used as part of the URL for your authoring and prediction endpoint queries.|
    |订阅名称| the subscription that will be billed for the resource.|
    |Resource group| A custom resource group name you choose or create. Resource groups allow you to group Azure resources for access and management in the same region.|
    |创作位置|The region associated with your model.|
    |创作定价层|The pricing tier determines the maximum transaction per second and month.|
    |运行时位置|The region associated with your published prediction endpoint runtime.|
    |运行时定价层|The pricing tier determines the maximum transaction per second and month.|

    Once both resources are created, assign the resources in the LUIS portal.

## <a name="create-resources-in-azure-cli"></a>Create resources in Azure CLI

Use the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) to create each resource individually. 

Resource `kind`:

* Authoring: `LUIS.Authoring`
* Prediction: `LUIS` 

1. Sign in to the Azure CLI:

    ```console
    az login
    ```

    This opens a browser to allow you to select the correct account and provide authentication.

1. Create a **LUIS authoring resource**, of kind `LUIS.Authoring`, named `my-luis-authoring-resource` in the _existing_ resource group named `my-resource-group` for the `westus` region. 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Create a **LUIS prediction endpoint resource**, of kind `LUIS`, named `my-luis-prediction-resource` in the _existing_ resource group named `my-resource-group` for the `westus` region. If you want a higher throughput than the free tier, change `F0` to `S0`. Learn more about [pricing tiers and throughput](luis-boundaries.md#key-limits).

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > This keys are **not** used by the LUIS portal until they are assigned in the LUIS portal on the **Manage -> Azure resources**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Assign an authoring resource in the LUIS portal for all apps

You can assign an authoring resource for a single app or for all apps in LUIS. The following procedure assigns all apps to a single authoring resource.

1. Sign in to the [LUIS portal](https://www.luis.ai).
1. At the top navigation bar, to the far right, select your user account, then select **Settings**.
1. On the **User Settings** page, select **Add authoring resource** then select an existing authoring resource. 选择“保存”。 

## <a name="assign-a-resource-to-an-app"></a>Assign a resource to an app

You can assign a single resource, authoring or prediction endpoint runtime, to an app with the following procedure.

1. Sign in to the [LUIS portal](https://www.luis.ai), then select an app from the **My apps** list.
1. Navigate to the **Manage -> Azure resources** page.

    ![Select the Manage -> Azure resources in the LUIS portal to assign a resource to the app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Select the Prediction or Authoring resource tab then select the **Add prediction resource** or **Add authoring resource** button. 
1. Select the fields in the form to find the correct resource, then select **Save**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Assign runtime resource without using LUIS portal

For automation purposes such as a CI/CD pipeline, you may want to automate the assignment of a LUIS runtime resource to a LUIS app. 为此，需要执行以下步骤：

1. 从此[网站](https://resources.azure.com/api/token?plaintext=true)获取 Azure 资源管理器令牌。 此令牌即将过期，请立即使用。 该请求将返回 Azure 资源管理器令牌。

    ![请求 Azure 资源管理器令牌和接收 Azure 资源管理器令牌](./media/luis-manage-keys/get-arm-token.png)

1. Use the token to request the LUIS runtime resources across subscriptions, from the [Get LUIS azure accounts API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), which your user account has access to. 

    此 POST API 需要以下设置：

    |标头|Value|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。| 
    |`Ocp-Apim-Subscription-Key`|Your authoring key.|

    此 API 将返回 LUIS 订阅的 JSON 对象的数组，包括订阅 ID、资源组和资源名称（作为帐户名称返回）。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。 

1. 使用[将 LUIS azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，将令牌分配给 LUIS 资源。 

    此 POST API 需要以下设置：

    |Type|设置|Value|
    |--|--|--|
    |标头|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。|
    |标头|`Ocp-Apim-Subscription-Key`|Your authoring key.|
    |标头|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功时，将返回“201 - 已创建状态”。 

## <a name="unassign-resource"></a>取消分配资源

1. Sign in to the [LUIS portal](https://www.luis.ai), then select an app from the **My apps** list.
1. Navigate to the **Manage -> Azure resources** page.
1. Select the Prediction or Authoring resource tab then select the **Unassign resource** button for the resource. 

When you unassign a resource, it is not deleted from Azure. 只会将其从 LUIS 取消链接。 

## <a name="reset-authoring-key"></a>重置创作密钥

**For [authoring resource migrated](luis-migration-authoring.md) apps**: if your authoring key is compromised, reset the key in the Azure portal on the **Keys** page for that authoring resource. 

**For apps that have not migrated yet**: the key is reset on all your apps in the LUIS portal. If you author your apps via the authoring APIs, you need to change the value of Ocp-Apim-Subscription-Key to the new key.

## <a name="regenerate-azure-key"></a>Regenerate Azure key

Regenerate the Azure keys from the Azure portal, on the **Keys** page.

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

## <a name="viewing-azure-resource-metrics"></a>Viewing Azure resource metrics

### <a name="viewing-azure-resource-summary-usage"></a>Viewing Azure resource summary usage
可在 Azure 中查看 LUIS 使用情况信息。 “概述”页显示包含调用和错误在内的最新摘要信息。 如果发出 LUIS 终结点请求并立即查看“概述”页，则最多需要五分钟才会显示使用情况。

![查看使用概况](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Customizing Azure resource usage charts
通过指标可更加详细地了解数据。

![默认指标](./media/luis-usage-tiers/metrics-default.png)

可针对时间期限和指标类型配置度量值图表。 

![自定义指标](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果希望在达到特定事务阈值（例如 10,000 个事务）时收到通知，则可以创建警报。 

![默认警报](./media/luis-usage-tiers/alert-default.png)

添加针对特定时间段内“总调用数”指标的指标警报。 添加应接收该警报的所有人员的电子邮件地址。 添加应接收该警报的所有系统的 Webhook。 还可在触发警报时运行逻辑应用。 

## <a name="next-steps"></a>后续步骤

* Learn [how to use versions](luis-how-to-manage-versions.md) to control your app life cycle.
* Understand the concepts including the [authoring resource](luis-concept-keys.md#authoring-key) and [contributors](luis-concept-keys.md#contributions-from-other-authors) on that resource.
* Learn [how to create](luis-how-to-azure-subscription.md) authoring and runtime resources
* Migrate to the new [authoring resource](luis-migration-authoring.md) 
