---
title: 创建连接到 Azure 的视频索引器帐户
titleSuffix: Azure Media Services
description: 了解如何创建连接到 Azure 的视频索引器帐户。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/08/2020
ms.author: juliako
ms.openlocfilehash: 405533aad8247350d45cc53009abe6b58a511264
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005933"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>创建连接到 Azure 的视频索引器帐户

创建视频索引器帐户时，可以选择一个免费试用帐户（在其中获得特定数量的可用索引分钟数）或付费选项（不受配额限制）。 使用免费试用版，视频索引器可向网站用户提供最多600分钟的免费索引，最多可向 API 用户提供2400分钟的免费索引。 使用付费选项，可以创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。 你需要为索引分钟数和与媒体帐户相关的费用付费。

本文介绍如何创建链接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。 本主题提供使用自动（默认）流连接 Azure 的步骤。 它还演示如何手动连接到 Azure（高级）。

如果要从*试用版*移动到*付费*视频索引器帐户，可以选择将所有视频和模型自定义复制到新帐户，如[从试用帐户导入内容](#import-your-content-from-the-trial-account)部分中所述。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。

    如果没有 Azure 订阅，请注册 [Azure 免费试用](https://azure.microsoft.com/free/)帐户。

* Azure Active Directory （Azure AD）域。

    如果没有 Azure AD 域，请使用 Azure 订阅创建此域。 有关详细信息，请参阅[管理 Azure AD 中的自定义域名](../../active-directory/users-groups-roles/domains-manage.md)

* 具有**应用程序管理员**角色的 Azure AD 域中的用户。 将视频索引器帐户连接到 Azure 时，将要使用此成员。

    此用户应该是拥有工作或学校帐户的 Azure AD 用户。 请勿使用个人帐户，如 outlook.com、live.com 或 hotmail.com。

    ![所有 AAD 用户](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自动流的其他先决条件

* Azure AD 域中的用户和成员。

    将视频索引器帐户连接到 Azure 时，将要使用此成员。

    此用户应当是 Azure 订阅中具有“所有者”角色，或者兼具“参与者”和“用户访问管理员”两个角色的成员************。 用户可以添加两次，具有两个角色。 一次分配“参与者”角色，另一次分配“用户访问管理员”角色。

    ![访问控制](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手动流的其他先决条件

* 使用 Azure 门户注册 EventGrid 资源提供程序。

    在 [Azure 门户](https://portal.azure.com/)中，转到“订阅”->[订阅]->“资源提供程序”********。

    搜索“Microsoft.Media”和“Microsoft.EventGrid”********。 如果该提供程序不是处于“已注册”状态，请单击“注册”。**** 注册需要花费几分钟时间。

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>连接到 Azure

> [!NOTE]
> 如果你的 Azure 订阅使用基于证书的多重身份验证，则必须在安装了所需证书的设备上执行以下步骤。

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。

2. 选择 "新建**帐户**" 按钮：

    ![创建新的视频索引器帐户](./media/create-account/connect-to-azure.png)

3. 订阅列表出现时，请选择要使用的订阅。

    ![将视频索引器连接到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 从支持的位置选择一个 Azure 区域：“美国西部 2”、“北欧”或“东亚”。
5. 在“Azure 媒体服务帐户”下，选择以下选项之一：****

    * 若要创建新的媒体服务帐户，请选择“创建新资源组”。**** 提供资源组的名称。

        Azure 会在订阅中创建新帐户，包括新的 Azure 存储帐户。 新的 Media Services 帐户具有默认初始配置，其中包含流式处理终结点和10个 S3 保留单位。
    * 若要使用现有的媒体服务帐户，请选择“使用现有资源”。**** 从帐户列表中选择自己的帐户。

        媒体服务帐户必须与视频索引器帐户位于同一区域。

        > [!NOTE]
        > 为了最大限度地减少索引持续时间和低吞吐量，强烈建议将媒体服务帐户中的[保留单元](../previous/media-services-scale-media-processing-overview.md )类型和数量调整为**10 S3 保留单位**。 请参阅[使用门户更改保留单位](../previous/media-services-portal-scale-media-processing.md)。

    * 若要手动配置连接，请选择 "**切换到手动配置**" 链接。

        有关详细信息，请参阅下文[手动连接到 Azure](#connect-to-azure-manually-advanced-option)（高级选项）部分。
6. 完成后，选择“连接”****。 此操作可能需要长达数分钟时间才能完成。

    连接到 Azure 后，新的视频索引器帐户会显示在帐户列表中：

    ![新帐户](./media/create-account/new-account.png)

7. 浏览到新帐户。

## <a name="connect-to-azure-manually-advanced-option"></a>手动连接到 Azure（高级选项）

如果与 Azure 的连接失败，你可以尝试通过手动连接来排除并解决问题。

> [!NOTE]
> 强烈建议在同一区域中使用以下三个帐户：要与媒体服务帐户连接的视频索引器帐户，以及连接到相同媒体服务帐户的 Azure 存储帐户。

### <a name="create-and-configure-a-media-services-account"></a>创建和配置媒体服务帐户

1. 使用 [Azure](https://portal.azure.com/) 门户创建 Azure 媒体服务帐户，如[创建帐户](../previous/media-services-portal-create-account.md)中所述。

    为媒体服务帐户创建存储帐户时，为 "复制" 字段选择 " **StorageV2** " 作为 "帐户类型" 和 "**异地冗余（GRS）** "。

    ![新 AMS 帐户](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > 请务必记下媒体服务资源和帐户名。 下一节中的步骤需要用到它们。

2. 在创建的媒体服务帐户中，将[保留单位](../previous/media-services-scale-media-processing-overview.md )的类型和数量调整为**10 个 S3 保留单位**。 请参阅[使用门户更改保留单位](../previous/media-services-portal-scale-media-processing.md)。
3. 你必须启动新的媒体服务帐户的默认**流式处理终结点**，然后才能在视频索引器 web 应用中播放视频。

    在新的媒体服务帐户中，选择 "**流式处理终结点**"。 然后选择流式处理终结点，并按 "启动"。

    ![新 AMS 帐户](./media/create-account/create-ams-account2.png)

4. 要使视频索引器使用媒体服务 API 进行身份验证，需要创建 AD 应用。 以下步骤引导你完成[通过 Azure 门户开始使用 Azure AD 身份验证](../previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 身份验证过程：

    1. 在新的媒体服务帐户中，选择“API 访问”****。
    2. 选择[服务主体身份验证方法](../previous/media-services-portal-get-started-with-aad.md)。
    3. 获取客户端 ID 和客户端密码

        选择 "**设置**->**密钥**" 后，添加 "**说明**"，按 "**保存**"，然后填充 "密钥" 值。

        如果密钥过期，则帐户所有者必须联系视频索引器支持来续订密钥。

        > [!NOTE]
        > 务必记下密钥值和应用程序 ID。 下一节中的步骤需要用到它。

### <a name="connect-manually"></a>手动连接

在[视频索引器](https://www.videoindexer.ai/)页的“将视频索引器连接到 Azure 订阅”对话框中，选择“切换为手动配置”链接********。

在对话框中提供以下信息：

|设置|说明|
|---|---|
|视频索引器帐户区域|视频索引器帐户区域的名称。 为了获得更好的性能和更低的成本，强烈建议指定 Azure 媒体服务资源和 Azure 存储帐户所在的区域的名称。 |
|Azure AD 租户|Azure AD 租户的名称，例如“contoso.onmicrosoft.com”。 可以在 Azure 门户中检索租户信息。 将光标悬停在右上角的登录用户名上。 找到“域”右侧的名称****。|
|订阅 ID|应在其下创建此连接的 Azure 订阅。 可以从 Azure 门户中检索订阅 ID。 在左侧面板中选择 "**所有服务**"，然后搜索 "订阅"。 选择“订阅”并从订阅列表中选择所需的 ID****。|
|Azure 媒体服务资源组名称|资源组（你在其中创建了媒体服务帐户）的名称。|
|媒体服务资源名称|你在上一节中创建的 Azure 媒体服务帐户的名称。|
|应用程序 ID|你在上一节中创建的 Azure AD 应用程序 ID（具有指定的媒体服务帐户的权限）。|
|应用程序密钥|你在上一节中创建的 Azure AD 应用程序密钥。 |

## <a name="import-your-content-from-the-trial-account"></a>从*试用*帐户导入内容

[创建新帐户](#connect-to-azure)时，可以选择将*试用*帐户中的内容导入到新帐户。 如果选中 "在**Azure 订阅上创建新帐户**" 对话框中的 "*导入*" 选项，则所有媒体和内容模型自定义都将从*试用*帐户复制到新帐户。

导入内容的功能对于上述自动和手动方法均有效。

> [!NOTE]
> 每个帐户只能导入一次内容。

## <a name="considerations"></a>注意事项

请注意以下 Azure 媒体服务相关的事项：

* 如果自动连接，Azure 订阅中会显示新的资源组、媒体服务帐户和存储帐户。
* 如果自动连接，视频索引器会将媒体“预留单位”设置为 10 个 S3 单位****：

    ![媒体服务预留单位](./media/create-account/ams-reserved-units.png)

* 如果连接到现有的媒体服务帐户，则视频索引器不会更改现有的媒体**保留单位**配置。

   你可能需要根据计划的负载调整媒体保留单位的类型和数量。 请记住，如果负载较高，并且没有足够的单位或速度，则视频处理可能导致超时失败。

* 如果连接到新的媒体服务帐户，视频索引器会自动在其中启动默认的流式处理终结点****：

    ![媒体服务流式处理终结点](./media/create-account/ams-streaming-endpoint.png)

    流式处理终结点具有相当多的启动时间。 因此，将你的帐户连接到 Azure 之前可能需要花费几分钟时间，直到视频可以在视频索引器 web 应用中进行流式处理和监视。

* 如果连接到现有的媒体服务帐户，则视频索引器不会更改默认的流式处理终结点配置。 如果没有正在运行的**流式处理终结点**，则无法通过此媒体服务帐户或视频索引器观看视频。

## <a name="next-steps"></a>后续步骤

可以按照：[使用 api](video-indexer-use-apis.md)中的说明，以编程方式与试用帐户和/或连接到 Azure 的视频索引器帐户交互。

应使用连接到 Azure 时所用的同一 Azure AD 用户。
