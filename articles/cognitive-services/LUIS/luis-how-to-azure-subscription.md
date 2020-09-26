---
title: 如何使用创作密钥和运行时密钥 - LUIS
description: 首次使用语言理解 (LUIS) 时，不需要创建创作密钥。 如果需要发布应用，然后使用运行时终结点，则需创建运行时密钥并将其分配给应用。
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 949ad4176cc7bf65e07e40323fc72a0a144b53b6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327215"
---
# <a name="create-luis-resources"></a>创建 LUIS 资源

创作和查询预测运行时资源为 LUIS 应用和预测终结点提供身份验证。

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS 资源

LUIS 允许三类 Azure 资源和一类非 Azure 资源：

|资源|目的|认知服务 `kind`|认知服务 `type`|
|--|--|--|--|
|创作资源|允许您创建、管理、定型、测试和发布您的应用程序。 如果打算在 programtically 或 LUIS 门户中创作 LUIS apps，请[创建 LUIS 创作资源](luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)。 需要首先 [迁移 LUIS 帐户](luis-migration-authoring.md#what-is-migration) ，才能将 Azure authroring 资源链接到应用程序。 可以通过将用户分配到 ["参与者" 角色](#contributions-from-other-authors)来控制对创作资源的权限。 <br><br> LUIS 创作资源有一个层在：<br> * **免费 F0 创作资源** ，可提供1m 免费创作事务，每月免费测试预测终结点请求1000。 |`LUIS.Authoring`|`Cognitive Services`|
|预测资源| 发布 LUIS 应用程序后，使用预测资源/键查询预测终结点请求。 在客户端应用程序请求除由创作或 starter 资源提供的1000请求之外的预测之前，请创建 LUIS 预测资源。 <br><br> 预测资源有两个层可用：<br> * **免费 F0 预测资源** ，每月提供10000免费预测终结点请求<br> * **标准 S0 预测资源** ，是付费层。 [了解有关定价详细信息的详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Starter/试用资源|允许您创建、管理、定型、测试和发布您的应用程序。 如果在第一次注册 tp LUIS 时选择了 "starter 资源" 选项，则会通过默认创建此项。 但是，此入门密钥最终将被弃用，并且所有 LUIS 用户将需要 [迁移其帐户](luis-migration-authoring.md#what-is-migration) 并将其 LUIS 应用程序链接到创作资源。 此资源不提供对基于角色的访问控制（如创作资源）的权限。 <br><br> 就像创作资源一样，starter 资源可提供1M 免费创作事务和1000免费测试预测终结点请求。|-|不是 Azure 资源|
|[认知服务多服务资源密钥](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|与 LUIS 和其他受支持的认知服务共享的查询预测终结点请求。|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS 提供了两种类型的 F0 (免费层) 资源。 一个用于创作事务，另一个用于预测交易。 如果预测交易的可用配额不足，请确保使用 F0 预测资源，每月提供10000免费交易，而不是每月提供1000预测交易的创作资源。

Azure 资源创建过程完成后， [将资源分配](#assign-a-resource-to-an-app) 给 LUIS 门户中的应用。

请务必在想要进行发布和查询的[区域](luis-reference-regions.md#publishing-regions)创作 LUIS 应用。

## <a name="resource-ownership"></a>资源所有权

Azure 资源（如 LUIS）由包含资源的订阅所有。

若要转让资源的所有权，可以执行以下任一操作：
* 转让订阅的[所有权](../../cost-management-billing/manage/billing-subscription-transfer.md)
* 将 LUIS 应用导出为文件，然后在其他订阅上导入应用。 可以从 LUIS 门户中的“我的应用”页面进行导出。


## <a name="resource-limits"></a>资源限制

### <a name="authoring-key-creation-limits"></a>创作密钥创建限制

可以按订阅为每个区域创建最多 10 个创作密钥。

请参阅[密钥限制](luis-limits.md#key-limits)和 [Azure 区域](luis-reference-regions.md)。

发布区域不同于创作区域。 请确保在对应于发布区域的创作区域中创建应用，你需要将客户端应用程序置于该发布区域中。

### <a name="key-usage-limit-errors"></a>密钥使用限制错误

使用限制取决于定价层。

如果超过了每秒事务数 (TPS) 配额，则会出现 HTTP 429 错误。 如果超过了每月事务数 (TPM) 配额，则会出现 HTTP 403 错误。


### <a name="reset-authoring-key"></a>重置创作密钥

对于 [创作资源已迁移](luis-migration-authoring.md) 的应用程序：如果你的创作密钥已泄露，请重置该创作资源的 " **密钥** " 页上的 "Azure 门户中的密钥。

对于尚未迁移的应用：可在 LUIS 门户的所有应用中重置此密钥。 如果通过创作 API 创作应用，则需将 Ocp-Apim-Subscription-Key 的值更改为新密钥。

### <a name="regenerate-azure-key"></a>重新生成 Azure 密钥

在 Azure 门户的“密钥”页上重新生成 Azure 密钥。


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>应用所有权、访问权限和安全性

应用通过其 Azure 资源进行定义，这取决于所有者的订阅。

你可以移动自己的 LUIS 应用。 在 Azure 门户或 Azure CLI 中，请参阅以下文档资源：

* [在 LUIS 创作资源之间移动应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [在同一订阅内移动资源或跨订阅移动资源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>其他作者的贡献

对于[创作资源迁移](luis-migration-authoring.md)的应用：使用**访问控制 (IAM) **页，在创作资源的 Azure 门户中管理_参与者_。 了解如何使用协作者的电子邮件地址和_参与者_角色[添加用户](luis-how-to-collaborate.md)。

对于尚未迁移的应用：所有协作者都在 LUIS 门户中通过“管理 -> 协作者”页面进行管理。

### <a name="query-prediction-access-for-private-and-public-apps"></a>专用和公共应用的查询预测访问权限

对于“专用”应用，所有者和参与者可以使用查询预测运行时访问权限。 对于**公共**应用，任何具有自己的 Azure [认知服务](../cognitive-services-apis-create-account.md)或 [LUIS](#create-resources-in-the-azure-portal) 运行时资源和公共应用 ID 的人员均可使用运行时访问权限。

目前没有公共应用的目录。

### <a name="authoring-permissions-and-access"></a>创作权限和访问权限
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
|定型|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>预测终结点运行时访问权限

查询预测终结点所需的访问权限由“应用程序信息”**** 页上“管理”**** 部分的设置进行控制。

|[专用终结点](#runtime-security-for-private-apps)|[公共终结点](#runtime-security-for-public-apps)|
|:--|:--|
|可供所有者和参与者使用|可供所有者、参与者以及知道应用 ID 的任何其他人使用|

可以通过在服务器到服务器环境中调用 LUIS 运行时密钥来控制谁可以查看该密钥。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/active-directory/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用并验证服务器端 API，则在确认授权后将调用传递到 LUIS。 尽管此策略不能防范中间人攻击，但会模糊化处理用户的密钥和终结点 URL，允许跟踪访问，并允许添加终结点响应日志记录（如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。

### <a name="runtime-security-for-private-apps"></a>专用应用的运行时安全性

专用应用的运行时仅适用于：

|密钥和用户|说明|
|--|--|
|所有者的创作密钥| 最多 1000 个终结点命中数|
|协作者/参与者的创作密钥| 最多 1000 个终结点命中数|
|由作者或协作者/参与者分配给 LUIS 的任何密钥|基于密钥用法层|

### <a name="runtime-security-for-public-apps"></a>公共应用的运行时安全性

应用配置为公共后，任何有效的 LUIS 创作密钥或 LUIS 终结点密钥都可以查询应用，只要该密钥未使用整个终结点配额__。

不是所有者或参与者的用户只有在获得应用 ID 时才能访问公共应用的运行时。 LUIS 不提供公共市场或其他搜索公共应用的方式__。

公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与资源密钥关联的任何区域中访问该应用。


### <a name="securing-the-query-prediction-endpoint"></a>保护查询预测终结点

可以通过在服务器到服务器环境中调用 LUIS 预测运行时终结点密钥来控制谁可以查看该密钥。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/active-directory/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用服务器端 API 并且身份验证和授权得到验证，则将调用传递到 LUIS。 尽管此策略不会防止中间人攻击，但会模糊化处理用户的终结点，允许跟踪访问，并允许添加终结点响应日志记录（如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>登录 LUIS 门户并开始创作

1. 登录 [LUIS 门户](https://www.luis.ai)并同意使用条款。
1. 通过选择 Azure LUIS 创作密钥，开始使用 LUIS 应用。

   ![选择语言理解创作资源的类型](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 完成资源选择过程以后，请[创建新应用](luis-how-to-start-new-app.md#create-new-app-in-luis)。


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>在 Azure CLI 中创建资源

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 单独创建每项资源。

资源 `kind`：

* 创作：`LUIS.Authoring`
* 预测：`LUIS`

1. 登录 Azure CLI：

    ```azurecli
    az login
    ```

    此时会打开一个浏览器，让你可以选择正确的帐户并提供身份验证。

1. 在名为 `my-resource-group` 的现有资源组中为 `westus` 区域创建一个类型为 `LUIS.Authoring`、名称为 `my-luis-authoring-resource` 的 **LUIS 创作资源**。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 在名为 `my-resource-group` 的现有资源组中为 `westus` 区域创建一个类型为 `LUIS`、名称为 `my-luis-prediction-resource` 的 **LUIS 预测终结点资源**。 如果需要比免费层更高的吞吐量，请将 `F0` 更改为 `S0`。 详细了解[定价层和吞吐量](luis-limits.md#key-limits)。

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > 在 LUIS 门户的“管理 -> Azure 资源”上分配此密钥之前，该门户不能使用此密钥。 

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>在 LUIS 门户中分配资源

可以在 LUIS 中为单个应用或所有应用分配创作资源。 以下过程将所有应用分配给单个创作资源。

1. 登录到 [LUIS 门户](https://www.luis.ai)。
1. 在右上角的导航栏中选择用户帐户，然后选择“设置”。
1. 在“用户设置”页上选择“添加创作资源”，然后选择现有的创作资源。  选择“保存” 。

## <a name="assign-a-resource-to-an-app"></a>将资源分配给应用

请注意，如果你没有 Azure 订阅，你将无法分配或创建新资源。 必须先开始创建 [Azure 免费试用版](https://azure.microsoft.com/en-us/free/) ，然后再返回到 LUIS，以便通过门户创建新的资源。

您可以使用以下过程为应用程序分配或创建创作或预测资源：

1. 登录到 [LUIS 门户](https://www.luis.ai)，然后从 " **我的应用** " 列表中选择一个应用
1. 导航到 " **管理-> Azure 资源** " 页

    ![在 LUIS 门户中选择“管理 -> Azure 资源”，以便为应用分配资源。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 选择 "预测或创作资源" 选项卡，然后选择 " **添加预测资源** " 或 " **添加创作资源** " 按钮
1. 选择窗体中的字段以查找正确的资源，然后选择 "**保存**"
1. 如果没有现有资源，可以选择 "创建新的 LUIS 资源？" 来创建一个资源。 从窗口底部


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>在不使用 LUIS 门户的情况下分配查询预测运行时资源

出于 CI/CD 管道等自动化目的，可能需要将 LUIS 运行时资源自动分配给 LUIS 应用。 为此，需要执行以下步骤：

1. 从此[网站](https://resources.azure.com/api/token?plaintext=true)获取 Azure 资源管理器令牌。 此令牌即将过期，请立即使用。 该请求将返回 Azure 资源管理器令牌。

    ![请求 Azure 资源管理器令牌和接收 Azure 资源管理器令牌](./media/luis-manage-keys/get-arm-token.png)

1. 使用令牌，通过用户帐户有权访问的 [GET LUIS Azure 帐户 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)，在订阅之间请求 LUIS 运行时资源。

    此 POST API 需要以下设置：

    |标头|Value|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。|
    |`Ocp-Apim-Subscription-Key`|你的创作密钥。|

    此 API 将返回 LUIS 订阅的 JSON 对象的数组，包括订阅 ID、资源组和资源名称（作为帐户名称返回）。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。

1. 将令牌分配给 LUIS 资源，并将 [LUIS Azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API。

    此 POST API 需要以下设置：

    |类型|设置|Value|
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
* 迁移到新的[创作资源](luis-migration-authoring.md)
