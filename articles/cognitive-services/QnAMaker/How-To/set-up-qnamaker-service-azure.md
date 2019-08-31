---
title: 设置 QnA Maker 服务-QnA Maker
titleSuffix: Azure Cognitive Services
description: 在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195318"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA Maker 资源

在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。

## <a name="types-of-keys-in-qna-maker"></a>QnA Maker 中的密钥类型

QnA Maker 服务处理两种密钥：**订阅密钥**和**终结点密钥**。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

|姓名|Location|用途|
|--|--|--|
|订阅密钥|[Azure 门户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|这些密钥用来访问 [QnA Maker 管理服务 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 通过这些 Api, 您可以编辑知识库中的问题和解答, 并发布您的知识库。 这些密钥是在创建新的 QnA Maker 服务时创建的。<br><br>在**认知服务**资源的 "**密钥**" 页上查找这些密钥。|
|终结点密钥|[QnA Maker 门户](http://www.qnamaker.ai)|这些密钥用于访问已发布的知识库终结点, 以获取用户问题的响应。 通常在聊天机器人或连接到 QnA Maker 服务的客户端应用程序代码中使用此终结点。 这些密钥是在您发布 QnA Maker 知识库时创建的。<br><br>在 "**服务设置**" 页上查找这些密钥。 在下拉菜单中找到从上向右用户的菜单中选择的此页。|

## <a name="create-a-new-qna-maker-service"></a>新建 QnA Maker 服务

此过程创建管理知识库内容所需的 Azure 资源。 完成这些步骤后, 可在 "**密钥**" 页上查找 Azure 门户中的资源的_订阅_密钥。

1. 登录到 Azure 门户并[创建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)资源。

1. 阅读条款和条件后, 选择 "**创建**"。

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，选择适当的层和区域。

    ![新建 QnA Maker 服务 - 定价层和区域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 使用唯一名称填写**名称**以标识此 QnA Maker 服务。 此名称还标识知识库将关联到的 QnA Maker 终结点。
    * 选择将部署 QnA Maker 资源的**订阅**。
    * 为 QnA Maker 管理服务 (门户和管理 Api) 选择**定价层**。 有关 SKU 定价的详细信息，请参见[此处](https://aka.ms/qnamaker-pricing)。
    * 创建新的**资源组**（推荐）或使用现有的资源组来在其中部署此 QnA Maker 资源。 QnA Maker 创建多个 Azure 资源;创建用于保存这些资源的资源组时, 可以通过资源组名称轻松查找、管理和删除这些资源。
    * 选择**资源组位置**。
    * 选择 Azure 搜索服务的**搜索定价层**。 如果看到“免费层”选项显示为灰色，则表示你已在订阅中部署了免费 Azure 搜索层。 在这种情况下，将需要从基本 Azure 搜索层开始。 在[此处](https://azure.microsoft.com/pricing/details/search/)查看 Azure 搜索定价的详细信息。
    * 选择要部署 Azure 搜索数据的**搜索位置**。 必须存储客户数据的限制将通知你为 Azure 搜索选择的位置。
    * 在**应用名称**中为应用服务命名。
    * 默认情况下，应用服务默认为标准 (S1) 层。 可以在创建后更改该计划。 请在[此处](https://azure.microsoft.com/pricing/details/app-service/)查看应用服务定价的详细信息。
    * 选择将部署应用服务的**网站位置**。

        > [!NOTE]
        > “搜索位置”可以与“网站位置”不同。

    * 选择是否要启用 **Application Insights**。 如果启用了 **Application Insights**，则 QnA Maker 会收集有关流量、聊天日志和错误的遥测数据。
    * 选择将部署 Application Insights 资源的 **App insights 位置**。
    * 为了节省成本, 可以[共享](#share-existing-services-with-qna-maker)某些但不是所有为 QnA Maker 创建的 Azure 资源。 

1. 验证所有字段后, 选择 "**创建**"。 可能需要几分钟才能完成。

1. 部署完成后，你将看到订阅中创建的以下资源。

    ![新建 QnA Maker 服务资源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有类型_认知服务_的资源具有_订阅_密钥。

## <a name="find-subscription-keys-in-azure-portal"></a>在 Azure 门户中查找订阅密钥

你可以查看和重置你的订阅密钥, 该密钥在你创建 QnA Maker 资源的 Azure 门户中进行编辑。 

1. 中转到 Azure 门户中的 QnA Maker 资源, 并选择类型_认知服务_的资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 转到“密钥”。

    ![订阅密钥](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>在 QnA Maker 门户中查找终结点密钥

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录 [QnA Maker 门户](https://qnamaker.ai)，转到你的个人资料，然后单击“服务设置”。

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置你的密钥。

    ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为密钥已泄露，请刷新你的密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

## <a name="share-existing-services-with-qna-maker"></a>与 QnA Maker 共享现有服务

QnA Maker 创建多个 Azure 资源。 为了减少成本共享的管理和权益, 请使用下表来了解可以和不能共享的内容:

|服务|共享|Reason|
|--|--|--|
|认知服务|X|不能通过设计|
|应用服务计划|✔|为应用服务计划分配的固定磁盘空间。 如果其他应用程序共享同一个应用服务计划, 则占用大量磁盘空间, QnAMaker 应用服务将遇到问题。|
|应用服务|X|不能通过设计|
|Application Insights|✔|可以共享|
|搜索服务|✔|1. `testkb`是 QnAMaker 服务的保留名称, 其他名称不能使用。<br>2.名称`synonym-map`的同义词映射是为 QnAMaker 服务保留的。<br>3.已发布的 Kb 数受搜索服务层限制。 如果有可用的可用索引, 则其他服务可以使用。|

### <a name="using-a-single-search-service"></a>使用单个搜索服务

如果通过门户创建 QnA 服务及其依赖项 (如搜索), 将为你创建一个搜索服务并将其绑定到 QnA Maker 服务。 创建这些资源后, 你可以更新应用服务设置, 以使用以前存在的搜索服务, 并删除刚刚创建的搜索服务。

如果通过 Azure 资源管理器模板创建 QnA 服务, 则可以创建所有资源, 并控制应用服务创建, 以使用现有搜索服务。 

## <a name="upgrade-qna-maker"></a>升级 QnA Maker

|升级|Reason|
|--|--|
|[升级](#upgrade-qna-maker-sku)QnA Maker 管理 sku|你需要在知识库中获得更多问题和答案。|
|[升级](#upgrade-app-service)应用服务 sku|您的知识库需要提供来自您的客户端应用程序的更多请求, 例如聊天机器人。|
|[升级](#upgrade-azure-search-service)Azure 搜索服务|你计划有多个知识库。|


### <a name="upgrade-qna-maker-sku"></a>升级 QnA Maker sku

当你需要在知识库中有更多问题和解答而超出当前层的能力范围时，请升级 QnA Maker 服务定价层。 

升级 QnA Maker 管理 SKU：

1. 转到 Azure 门户中的 QnA Maker 资源，然后选择“定价层”。

    ![QnA Maker 资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 选择合适的 SKU 并按“选择”。

    ![QnA Maker 定价](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>升级应用服务

 当知识库需要处理来自客户端应用的更多请求时，请升级应用服务定价层。

你可以[扩大](https://docs.microsoft.com/azure/app-service/manage-scale-up)或缩减应用服务。

转到 Azure 门户中的应用服务资源，并根据需要选择“扩大”或“缩减”选项。

![QnA Maker 应用服务缩放](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>升级 Azure 搜索服务

当计划拥有许多知识库时，请升级 Azure 搜索服务定价层。 

当前无法对 Azure 搜索 SKU 执行就地升级。 但是，你可以使用所需的 SKU 创建新的 Azure 搜索资源、将数据还原到新资源，然后将其链接到 QnA Maker 堆栈。

1. 在 Azure 门户中创建新的 Azure 搜索资源，然后选择所需的 SKU。

    ![QnA Maker Azure 搜索资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 将索引从原始 Azure 搜索资源还原到新资源。 有关备份还原示例代码，请参阅[此处](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 还原数据后，转到新的 Azure 搜索资源，选择“密钥”，并记下“名称”和“管理密钥”。

    ![QnA Maker Azure 搜索密钥](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 若要将新的 Azure 搜索资源链接到 QnA Maker 堆栈，请转到 QnA Maker 应用服务。

    ![QnA Maker 应用服务](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 选择“应用程序设置”并替换步骤 3 中的“AzureSearchName”和“AzureSearchAdminKey”字段。

    ![QnA Maker 应用服务设置](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重启应用服务。

    ![QnA Maker 应用服务重启](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>获取最新的运行时更新

QnAMaker 运行时是在 Azure 门户中[创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md)时部署的 Azure App Service 的一部分。 对运行时的更新定期进行。 在2019版站点扩展版本 (版本 5 +) 后, QnA Maker 应用服务处于自动更新模式。 这已设计为在升级过程中注意零停机时间。 

您可以在 https://www.qnamaker.ai/UserSettings 中检查当前版本。 如果你的版本早于版本 1.x, 则必须重启应用服务才能应用最新更新。

1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 单击“应用服务”并打开“概述”部分

     ![QnAMaker 应用服务](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 重启应用服务。 应该会在几秒钟内完成。 在此重新启动期间, 最终用户将无法使用此 QnAMaker 服务的所有从属应用程序或 bot。

    ![QnAMaker 应用服务重启](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>管理服务区域

QnA Maker 的管理服务仅适用于 QnA Maker 门户 & 用于初始数据处理, 只能在美国西部使用。 此美国西部服务中未存储任何客户数据。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建并发布知识库](../Quickstarts/create-publish-knowledge-base.md)
