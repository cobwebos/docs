---
title: 设置 QnA 制造商服务 - QnA 制造商
description: 在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 8ec57f441ba58227e45398c35c7931dc75fa658f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131702"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA 制造商资源

在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。

在创建资源之前，深入了解以下概念很有帮助：

* [QnA Maker 资源](../Concepts/azure-resources.md)
* [创作和发布密钥](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>新建 QnA Maker 服务

此过程创建管理知识库内容所需的 Azure 资源。 完成这些步骤后，您将在 Azure 门户中的"**密钥"** 页上找到资源的_订阅_密钥。

1. 登录到 Azure 门户并[创建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)资源。

1. 在阅读条款和条件后选择 **"创建**"：

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在**QnA 制造商中**，选择相应的层和区域：

    ![新建 QnA Maker 服务 - 定价层和区域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 **"名称"** 字段中，输入唯一名称以标识此 QnA Maker 服务。 此名称还标识知识库将关联的 QnA Maker 终结点。
    * 选择将部署 QnA Maker 资源的**订阅**。
    * 选择 QnA 制造商管理服务（门户和管理 API）的**定价层**。 查看[有关 SKU 定价的更多详细信息](https://aka.ms/qnamaker-pricing)。
    * 创建新的资源组（建议）或使用现有**资源组**来部署此 QnA Maker 资源。 QnA Maker 创建多个 Azure 资源。 创建资源组以保存这些资源时，可以轻松地通过资源组名称查找、管理和删除这些资源。
    * 选择**资源组位置**。
    * 选择 Azure 认知搜索服务的**搜索定价层**。 如果"免费套餐"选项不可用（显示为灰色），则意味着您已经通过订阅部署了免费服务。 在这种情况下，您需要从基本层开始。 请参阅[Azure 认知搜索定价详细信息](https://azure.microsoft.com/pricing/details/search/)。
    * 选择要部署 Azure 认知搜索索引的**搜索位置**。 对客户数据的存储位置的限制将有助于确定为 Azure 认知搜索选择的位置。
    * 在 **"应用名称**"字段中，输入 Azure 应用服务实例的名称。
    * 默认情况下，应用服务默认为标准 （S1） 层。 可以在创建后更改该计划。 了解有关[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)的更多。
    * 选择将部署应用服务**的网站位置**。

        > [!NOTE]
        > **搜索位置**可能与**网站位置**不同。

    * 选择是否要启用**应用程序见解**。 如果启用了 **Application Insights**，则 QnA Maker 会收集有关流量、聊天日志和错误的遥测数据。
    * 选择应用**见解位置**，其中将部署应用见解资源。
    * 为了节省成本，可以[共享](#configure-qna-maker-to-use-different-cognitive-search-resource)为 QnA Maker 创建的某些 Azure 资源，但不是所有。

1. 验证所有字段后，选择 **"创建**"。 此过程可能需要几分钟才能完成。

1. 部署完成后，您将看到订阅中创建的以下资源：

   ![新建 QnA Maker 服务资源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有_认知服务_类型的资源具有_订阅_密钥。

## <a name="find-subscription-keys-in-the-azure-portal"></a>在 Azure 门户中查找订阅密钥

您可以在 Azure 门户中查看和重置订阅密钥，并创建 QnA Maker 资源。

1. 转到 Azure 门户中的 QnA Maker 资源，然后选择具有_认知服务_类型的资源：

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 转到**密钥**：

    ![订阅密钥](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA 制造商门户中查找终结点键

终结点与资源位于同一区域中，因为终结点键用于调用知识库。

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录到[QnA Maker 门户](https://qnamaker.ai)，转到您的个人资料，然后选择 **"服务设置**"：

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置密钥：

    > [!div class="mx-imgBorder"]
    > ![端点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果您认为密钥已泄露，请刷新您的密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

### <a name="upgrade-qna-maker-sku"></a>升级 QnA 制造商 SKU

当您希望在知识库中有更多的问题和答案时，除了当前层之外，请升级 QnA Maker 服务定价层。

升级 QnA Maker 管理 SKU：

1. 转到 Azure 门户中的 QnA Maker 资源，然后选择“定价层”****。

    ![QnA Maker 资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 选择合适的 SKU 并按“选择”****。

    ![QnA Maker 定价](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>升级应用服务

 当您的知识库需要从客户端应用提供更多请求时，请升级应用服务定价层。

您可以[扩展](https://docs.microsoft.com/azure/app-service/manage-scale-up)或扩展应用服务。

转到 Azure 门户中的应用服务资源，然后根据需要选择 **"向上缩放**"或 **"横向扩展**"选项。

![QnA 制造商应用服务规模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>升级 Azure 认知搜索服务

如果计划拥有许多知识库，请升级 Azure 认知搜索服务定价层。

目前，无法对 Azure 搜索 SKU 执行就地升级。 但是，你可以使用所需的 SKU 创建新的 Azure 搜索资源、将数据还原到新资源，然后将其链接到 QnA Maker 堆栈。 为此，请按照下列步骤进行操作：

1. 在 Azure 门户中创建新的 Azure 搜索资源，然后选择所需的 SKU。

    ![QnA Maker Azure 搜索资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 将索引从原始 Azure 搜索资源还原到新资源。 请参阅[备份还原示例代码](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 还原数据后，转到新的 Azure 搜索资源，选择**键**，然后记下**名称****和管理密钥**：

    ![QnA Maker Azure 搜索密钥](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 要将新的 Azure 搜索资源链接到 QnA Maker 堆栈，请访问 QnA Maker 应用服务实例。

    ![QnA 制造商应用服务实例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 选择**应用程序设置**并修改步骤 3 中的**Azure 搜索名称**和**Azure 搜索管理键**字段中的设置。

    ![QnA 制造商应用服务设置](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重新启动应用服务实例。

    ![重新启动 QnA 制造商应用服务实例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>获取最新的运行时更新

QnAMaker 运行时是在 Azure 门户中[创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md)时部署的 Azure 应用服务实例的一部分。 对运行时的更新定期进行。 QnA Maker 应用服务实例在 2019 年 4 月网站扩展版本（版本 5+）后处于自动更新模式。 此更新旨在处理升级期间的零停机时间。

您可以在 上https://www.qnamaker.ai/UserSettings检查当前版本。 如果版本早于版本 5.x，则必须重新启动应用服务以应用最新更新：

1. 转到[Azure 门户](https://portal.azure.com)中的 QnAMaker 服务（资源组）。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择应用服务实例并打开 **"概述"** 部分。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker 应用服务实例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重新启动应用服务。 更新过程应在几秒钟后完成。 在此重新启动期间，使用此 QnAMaker 服务的任何从属应用程序或自动程序都将对最终用户不可用。

    ![重新启动 QnAMaker 应用服务实例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>配置 QnA 制造商以使用不同的认知搜索资源

如果通过门户创建 QnA 服务及其依赖项（如搜索），则为您创建搜索服务并链接到 QnA Maker 服务。 创建这些资源后，可以更新应用服务设置以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

QnA Maker**的应用服务**资源使用认知搜索资源。 为了更改 QnA Maker 使用的认知搜索资源，您需要更改 Azure 门户中的设置。

1. 获取您希望 QnA Maker 使用的认知搜索资源的**管理密钥**和**名称**。

1. 登录到 Azure[门户](https://portal.azure.com)并找到与您的 QnA Maker 资源关联的**应用服务**。 两者具有相同的名称。

1. 选择 **"设置"，** 然后**选择配置**。 这将显示 QnA 制造商应用服务的所有现有设置。

    > [!div class="mx-imgBorder"]
    > ![显示应用服务配置设置的 Azure 门户屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 更改以下键的值：

    * **Azure 搜索管理密钥**
    * **Azure 搜索名称**

1. 要使用新设置，您需要重新启动应用服务。 选择 **"概览**"，然后选择 **"重新启动**"。

    > [!div class="mx-imgBorder"]
    > ![配置设置更改后 Azure 门户重新启动应用服务的屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果通过 Azure 资源管理器模板创建 QnA 服务，则可以创建所有资源并控制应用服务创建以使用现有的搜索服务。

详细了解如何配置应用服务[应用程序设置](../../../app-service/configure-common.md#configure-app-settings)。

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>配置应用服务空闲设置以避免超时

应用服务为已发布的知识库的 QnA Maker 预测运行时提供服务，具有空闲超时配置，如果服务处于空闲状态，则默认为自动超时。 对于 QnA Maker，这意味着预测运行时在无流量期间后偶尔会超时应答 API。

为了即使在没有流量的情况下也能加载预测终结点应用，请将空闲设置为始终打开。

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 搜索并选择 QnA Maker 资源的应用服务。 它将具有与 QnA Maker 资源相同的名称，但它将具有**不同类型的**应用服务。
1. 查找**设置**，然后选择 **"配置**"。
1. 在"配置"窗格中，选择 **"常规设置**"，然后查找 **"始终打开**"，然后选择 **"作为**值"。

    > [!div class="mx-imgBorder"]
    > ![在"配置"窗格中，选择[常规设置]，然后查找 [始终打开]，然后选择 [打开] 作为值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 选择“保存”**** 以保存配置。
1. 系统会询问您是否要重新启动应用以使用新设置。 选择“继续”****。

详细了解如何配置应用服务[常规设置](../../../app-service/configure-common.md#configure-general-settings)。

## <a name="delete-azure-resources"></a>删除 Azure 资源

如果你删除了用于 QnA Maker 知识库的任何 Azure 资源，则知识库将不再工作。 在删除任何资源之前，请务必从“设置”**** 页导出知识库。

## <a name="next-steps"></a>后续步骤

了解有关[应用](../../../app-service/index.yml)服务和[搜索服务的更多。](../../../search/index.yml)

> [!div class="nextstepaction"]
> [创建并发布知识库](../Quickstarts/create-publish-knowledge-base.md)