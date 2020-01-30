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
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dc3bb6882963205e17e37f52ec9dcdffecdf9e21
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843080"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA Maker 资源

在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。

## <a name="types-of-keys-in-qna-maker"></a>QnA Maker 中的密钥类型

QnA Maker 服务处理两种类型的密钥：**订阅密钥**和**终结点密钥**。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

|名称|位置|用途|
|--|--|--|
|订阅密钥|[Azure 门户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|这些密钥用于访问[QnA Maker 管理服务 api](https://go.microsoft.com/fwlink/?linkid=2092179)。 通过这些 Api，您可以编辑知识库中的问题和解答，并发布您的知识库。 这些密钥是在创建新的 QnA Maker 服务时创建的。<br><br>在 "**密钥**" 页上的 "**认知服务**" 资源上查找这些密钥。|
|终结点密钥|[QnA Maker 门户](https://www.qnamaker.ai)|这些密钥用于访问已发布的知识库终结点，以获取用户问题的响应。 通常在聊天机器人中或连接到 QnA Maker 服务的客户端应用程序代码中使用此终结点。 这些密钥是在您发布 QnA Maker 知识库时创建的。<br><br>在 "**服务设置**" 页中查找这些密钥。 在下拉菜单上页面右上角的用户菜单中找到此页。|

## <a name="create-a-new-qna-maker-service"></a>新建 QnA Maker 服务

此过程创建管理知识库内容所需的 Azure 资源。 完成这些步骤后，可以在 "**密钥**" 页上找到 Azure 门户资源的_订阅_密钥。

1. 登录到 Azure 门户并[创建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)资源。

1. 阅读条款和条件后，选择 "**创建**"：

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在**QnA Maker**中，选择适当的层和区域：

    ![新建 QnA Maker 服务 - 定价层和区域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 "**名称**" 字段中，输入唯一的名称来标识此 QnA Maker 服务。 此名称还标识您的知识库将与之关联的 QnA Maker 终结点。
    * 选择将在其下部署 QnA Maker 资源的**订阅**。
    * 为 QnA Maker 管理服务（门户和管理 Api）选择**定价层**。 查看[有关 SKU 定价的更多详细信息](https://aka.ms/qnamaker-pricing)。
    * 创建新的**资源组**（推荐），或使用现有资源组部署此 QnA Maker 资源。 QnA Maker 创建多个 Azure 资源。 创建用于保存这些资源的资源组时，可以通过资源组名称轻松查找、管理和删除这些资源。
    * 选择**资源组位置**。
    * 选择 "搜索" Azure 认知搜索服务的**定价层**。 如果 "免费层" 选项不可用（显示为灰色），则表示已通过订阅部署了免费服务。 在这种情况下，你将需要从 "基本" 层开始。 请参阅[Azure 认知搜索定价详细信息](https://azure.microsoft.com/pricing/details/search/)。
    * 选择要在其中部署 Azure 认知搜索索引的**搜索位置**。 对客户数据的存储位置的限制有助于确定为 Azure 认知搜索选择的位置。
    * 在 "**应用名称**" 字段中，输入 Azure App Service 实例的名称。
    * 默认情况下，应用服务默认为标准（S1）层。 可以在创建后更改该计划。 了解有关[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)的详细信息。
    * 选择将在其中部署应用服务的**网站位置**。

        > [!NOTE]
        > **搜索位置**可能不同于**网站位置**。

    * 选择是否要启用**Application Insights**。 如果启用了 **Application Insights**，则 QnA Maker 会收集有关流量、聊天日志和错误的遥测数据。
    * 选择将在其中部署 Application Insights 资源的**App insights 位置**。
    * 为了节省成本，可以[共享](#share-existing-services-with-qna-maker)为 QnA Maker 创建的某些 Azure 资源，但不是所有。

1. 验证所有字段后，选择 "**创建**"。 此过程可能需要几分钟才能完成。

1. 部署完成后，你将看到在订阅中创建的以下资源：

   ![新建 QnA Maker 服务资源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有_认知服务_类型的资源具有您的_订阅_密钥。

## <a name="find-subscription-keys-in-the-azure-portal"></a>在 Azure 门户中查找订阅密钥

你可以从创建 QnA Maker 资源的 Azure 门户查看和重置你的订阅密钥。

1. 中转到 Azure 门户中的 QnA Maker 资源，并选择具有_认知服务_类型的资源：

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 中转到**密钥**：

    ![订阅密钥](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA Maker 门户中查找终结点密钥

终结点与资源位于同一区域，因为终结点键用于调用知识库。

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录到[QnA Maker 门户](https://qnamaker.ai)，中转到你的配置文件，然后选择 "**服务设置**"：

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置密钥：

    ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为密钥已泄露，请刷新你的密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

## <a name="share-existing-services-with-qna-maker"></a>与 QnA Maker 共享现有服务

QnA Maker 创建多个 Azure 资源。 若要减少成本共享的管理和权益，请使用下表来了解可以和不能共享的内容：

|服务|共享|原因|
|--|--|--|
|认知服务|X|不能通过设计|
|应用服务计划|✔|为应用服务计划分配的固定磁盘空间。 如果共享同一应用服务计划的其他应用使用了大量磁盘空间，QnAMaker 应用服务实例将会遇到问题。|
|应用服务|X|不能通过设计|
|Application Insights|✔|可以共享|
|搜索服务|✔|1. `testkb` 是 QnAMaker 服务的保留名称;其他用户不能使用它。<br>2. `synonym-map` 的名称是为 QnAMaker 服务保留的同义词映射。<br>3. 已发布的知识库数受搜索服务层限制。 如果有可用的可用索引，则其他服务可以使用这些索引。|

了解有关[应用服务](../../../app-service/index.yml)和[搜索服务](../../../search/index.yml)的详细信息。

## <a name="using-a-single-search-service"></a>使用单个搜索服务

如果通过门户创建 QnA 服务及其依赖项（如搜索），将为你创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

如果通过 Azure 资源管理器模板创建 QnA 服务，则可以创建所有资源，并控制应用服务创建，以使用现有搜索服务。


## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>将 QnA Maker 配置为使用不同的认知搜索资源

如果通过门户创建 QnA 服务及其依赖项（如搜索），将为你创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

QnA Maker 的**应用服务**资源使用认知搜索资源。 若要更改 QnA Maker 使用的认知搜索资源，需要更改 Azure 门户中的设置。

1. 获取要 QnA Maker 使用的认知搜索资源的**管理员密钥**和**名称**。

1. 登录到[Azure 门户](https://portal.azure.com)并找到与 QnA Maker 资源关联的**应用服务**。 具有相同名称的。

1. 依次选择 "**设置**"、"**配置**"。 这将显示 QnA Maker 的应用服务的所有现有设置。

    > [!div class="mx-imgBorder"]
    > 显示应用服务配置设置 Azure 门户 ![屏幕快照](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 更改以下项的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新设置，需要重新启动应用服务。 选择 "**概述**"，然后选择 "**重新启动**"。

    > [!div class="mx-imgBorder"]
    > 更改配置设置后 Azure 门户重新启动应用服务的 ![屏幕快照](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果通过 Azure 资源管理器模板创建 QnA 服务，则可以创建所有资源，并控制应用服务创建，以使用现有搜索服务。

## <a name="upgrade-qna-maker"></a>升级 QnA Maker

|升级|原因|
|--|--|
|[升级](#upgrade-qna-maker-sku)QnA Maker 管理 SKU|你需要在知识库中获得更多问题和答案。|
|[升级](#upgrade-app-service)应用服务 SKU|您的知识库需要提供客户端应用程序的更多请求，例如聊天机器人。|
|[升级](#upgrade-the-azure-cognitive-search-service)Azure 认知搜索服务|你计划有多个知识库。|


### <a name="upgrade-qna-maker-sku"></a>升级 QnA Maker SKU

如果你想要在你的知识库中获得更多问题和答案，请将 QnA Maker 服务定价层升级。

升级 QnA Maker 管理 SKU：

1. 转到 Azure 门户中的 QnA Maker 资源，然后选择“定价层”。

    ![QnA Maker 资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 选择合适的 SKU 并按“选择”。

    ![QnA Maker 定价](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>升级应用服务

 当您的知识库需要提供客户端应用程序的更多请求时，请升级您的应用服务定价层。

你可以[纵向扩展](https://docs.microsoft.com/azure/app-service/manage-scale-up)或横向扩展应用服务。

中转到 Azure 门户中的 "应用服务" 资源，并根据需要选择 "**纵向扩展**" 或 "**横向扩展**" 选项。

![QnA Maker 应用服务规模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>升级 Azure 认知搜索服务

如果计划有很多知识库，请升级 Azure 认知搜索服务定价层。

目前不能执行 Azure 搜索 SKU 的就地升级。 但是，你可以使用所需的 SKU 创建新的 Azure 搜索资源、将数据还原到新资源，然后将其链接到 QnA Maker 堆栈。 为此，请按照下列步骤进行操作：

1. 在 Azure 门户中创建新的 Azure 搜索资源，并选择所需的 SKU。

    ![QnA Maker Azure 搜索资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 将索引从原始 Azure 搜索资源还原到新资源。 请参阅[备份还原示例代码](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 还原数据后，请切换到新的 Azure 搜索资源，选择 "**密钥**"，并记下**名称**和**管理密钥**：

    ![QnA Maker Azure 搜索密钥](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 若要将新的 Azure 搜索资源链接到 QnA Maker 堆栈，请中转到 QnA Maker 应用服务实例。

    ![QnA Maker 应用服务实例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 选择 "**应用程序设置**"，并修改步骤3中 " **AzureSearchName** " 和 " **AzureSearchAdminKey** " 字段中的设置。

    ![QnA Maker 应用服务设置](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重新启动应用服务实例。

    ![QnA Maker 应用服务实例的重新启动](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>获取最新的运行时更新

QnAMaker 运行时是在 Azure 门户中[创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md)时部署的 Azure App Service 实例的一部分。 对运行时的更新定期进行。 2019年4月版（版本 5 +）之后，QnA Maker 应用服务实例处于自动更新模式。 此更新旨在在升级过程中处理零停机时间。

你可以在 https://www.qnamaker.ai/UserSettings 上检查当前版本。 如果版本低于版本1.x，则必须重启应用服务才能应用最新更新：

1. 在[Azure 门户](https://portal.azure.com)中转到你的 QnAMaker 服务（资源组）。

    ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择应用服务实例，并打开 "**概述**" 部分。

    ![QnAMaker 应用服务实例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 重新启动应用服务。 更新过程只需几秒钟即可完成。 在此重新启动期间，最终用户将无法使用此 QnAMaker 服务的所有从属应用程序或 bot。

    ![QnAMaker 应用服务实例的重新启动](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>管理服务区域

QnA Maker 的管理服务仅用于 QnA Maker 门户和初始数据处理。 此服务仅在美国西部区域提供。 此美国西部服务中未存储任何客户数据。

## <a name="next-steps"></a>后续步骤

了解有关[应用服务](../../../app-service/index.yml)和[搜索服务](../../../search/index.yml)的详细信息。

> [!div class="nextstepaction"]
> [创建并发布知识库](../Quickstarts/create-publish-knowledge-base.md)
