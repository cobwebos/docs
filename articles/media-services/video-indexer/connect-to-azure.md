---
title: 在 Azure 门户中创建视频索引器帐户
titlesuffix: Azure Media Services
description: 本文介绍如何在 Azure 门户中创建视频索引器帐户。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: affa6f9a808543401b7d57812c7d2bef4324a83c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796539"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>创建连接到 Azure 的视频索引器帐户

创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。 需要为编制索引的分钟数付费，此外还需要支付媒体帐户相关的费用。 

本文介绍如何创建链接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。 本主题提供使用自动（默认）流连接 Azure 的步骤。 它还演示如何手动连接到 Azure（高级）。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。

    如果没有 Azure 订阅，请注册 [Azure 免费试用](https://azure.microsoft.com/free/)帐户。

* Azure Active Directory (AD) 域。

    如果没有 Azure AD 域，请使用 Azure 订阅创建此域。 有关详细信息，请参阅[管理 Azure Active Directory 中的自定义域名](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD 域中的用户和成员。 将视频索引器帐户连接到 Azure 时，将要使用此成员。

    此用户应为使用工作或学校帐户而非个人帐户（例如 outlook.com、live.com 或 hotmail.com）的 Azure AD 用户。

    ![所有 AAD 用户](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自动流的其他先决条件

Azure AD 域中的用户和成员。 将视频索引器帐户连接到 Azure 时，将要使用此成员。

此用户应当是 Azure 订阅中具有“所有者”角色，或者兼具“参与者”和“用户访问管理员”两个角色的成员。 可以添加某个用户两次并分配 2 个角色。 一次分配“参与者”角色，另一次分配“用户访问管理员”角色。

![访问控制](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手动流的其他先决条件

使用 Azure 门户注册 EventGrid 资源提供程序。

在 [Azure 门户](https://portal.azure.com/)中，转到“订阅”->[订阅]->“资源提供程序”。 

搜索“Microsoft.Media”和“Microsoft.EventGrid”。 如果该提供程序不是处于“已注册”状态，请单击“注册”。 注册需要花费几分钟时间。

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>连接到 Azure

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。

2. 单击**创建新帐户**按钮：

    ![连接到 Azure](./media/create-account/connect-to-azure.png)

3. 订阅列表出现时，请选择要使用的订阅。

    ![将视频索引器连接到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 从支持的位置中选择一个 Azure 区域：美国西部 2、北欧或东亚。
5. 在“Azure 媒体服务帐户”下，选择以下选项之一：

    * 若要创建新的媒体服务帐户，请选择“创建新资源组”。 提供资源组的名称。

        Azure 会在订阅中创建新帐户，包括新的 Azure 存储帐户。 新媒体服务帐户的默认初始配置使用一个流式处理终结点和 10 个 S3 预留单位。
    * 若要使用现有的媒体服务帐户，请选择“使用现有资源”。 从帐户列表中选择自己的帐户。

        媒体服务帐户必须与视频索引器帐户位于同一区域。 

        > [!NOTE]
        > 为了尽量缩短索引持续时间和降低吞吐量，强烈建议在媒体服务帐户中将[预留单位](../previous/media-services-scale-media-processing-overview.md )的类型和数量调整为“10 个 S3 预留单位”。 请参阅[使用门户更改预留单位](../previous/media-services-portal-scale-media-processing.md)。

    * 若要手动配置连接，请单击“切换到手动配置”链接。

        有关详细信息，请参阅下文[手动连接到 Azure](#connect-to-azure-manually-advanced-option)（高级选项）部分。
6. 完成后，选择“连接”。 此操作可能需要长达数分钟时间才能完成。 

    连接到 Azure 后，新的视频索引器帐户会显示在帐户列表中：

    ![新帐户](./media/create-account/new-account.png)

7. 浏览到新帐户

## <a name="connect-to-azure-manually-advanced-option"></a>手动连接到 Azure（高级选项）

如果与 Azure 的连接失败，你可以尝试通过手动连接来排除并解决问题。

> [!NOTE]
> 强烈建议在同一区域中拥有以下三个帐户：与媒体服务帐户连接的视频索引器帐户，以及连接到同一媒体服务帐户的 Azure 存储帐户。

### <a name="create-and-configure-a-media-services-account"></a>创建和配置媒体服务帐户

1. 使用 [Azure](https://portal.azure.com/) 门户创建 Azure 媒体服务帐户，如[创建帐户](../previous/media-services-portal-create-account.md)中所述。

    创建媒体服务帐户的存储帐户时，选择“StorageV2”作为帐户类型并在复制字段中选择“异地冗余(RGS)”。

    ![新建 AMS 帐户](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > 请务必记下媒体服务资源和帐户名。 下一部分的步骤中将用到它们。

2. 在创建的媒体服务帐户中将[预留单位](../previous/media-services-scale-media-processing-overview.md )类型和数量调整为“10 个 S3 预留单位”。 请参阅[使用门户更改预留单位](../previous/media-services-portal-scale-media-processing.md)。
3. 必须先启动新媒体服务帐户的默认“流式处理终结点”，才能在视频索引器 Web 应用程序中播放视频。

    在新的媒体服务帐户中，单击“流式处理终结点”。 选择流式处理终结点，然后按“启动”。

    ![新建 AMS 帐户](./media/create-account/create-ams-account2.png)

4. 对于要使用媒体服务 API 执行身份验证的视频索引器，需要创建一个 AD 应用程序。 以下步骤引导你完成[通过 Azure 门户开始使用 Azure AD 身份验证](../previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 身份验证过程：

    1. 在新的媒体服务帐户中，选择“API 访问”。
    2. 选择[服务主体身份验证方法](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)。
    3. 按[获取客户端 ID 和客户端密码](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret)部分所述，获取客户端 ID 和客户端密码。

        在选择“设置”->“密钥”后，添加“说明”并按“保存”，系统会填充密钥值。

        如果密钥到期，帐户所有者需联系视频索引器支持人员更新密钥。

        > [!NOTE]
        > 务必记下密钥值和应用程序 ID。 下一部分的步骤中将用到它们。

### <a name="connect-manually"></a>手动连接

在[视频索引器](https://www.videoindexer.ai/)页的“将视频索引器连接到 Azure 订阅”对话框中，选择“切换为手动配置”链接。

在对话框中提供以下信息：

|设置|描述|
|---|---|
|视频索引器帐户区域|视频索引器帐户区域的名称。 为了提高性能和降低成本，强烈建议指定 Azure 媒体服务资源和 Azure 存储帐户所在区域的名称。 |
|Azure Active Directory (AAD) 租户|Azure AD 租户的名称，例如“contoso.onmicrosoft.com”。 可以在 Azure 门户中检索租户信息。 将光标悬停在右上角的登录用户名上。 找到“域”右侧的名称。|
|订阅 ID|应在其下创建此连接的 Azure 订阅。 可以从 Azure 门户中检索订阅 ID。 单击左侧面板中的“所有服务”，然后搜索“订阅”。 选择“订阅”并从订阅列表中选择所需的 ID。|
|Azure 媒体服务资源组名称|资源组（你在其中创建了媒体服务帐户）的名称。|
|媒体服务资源名称|你在上一节中创建的 Azure 媒体服务帐户的名称。|
|应用程序 ID|你在上一节中创建的 Azure AD 应用程序 ID（具有指定的媒体服务帐户的权限）。|
|应用程序密钥|你在上一节中创建的 Azure AD 应用程序密钥。 |

## <a name="considerations"></a>注意事项

请注意以下 Azure 媒体服务相关的事项：

* 如果自动连接，Azure 订阅中会显示新的资源组、媒体服务帐户和存储帐户。
* 如果自动连接，视频索引器会将媒体“预留单位”设置为 10 个 S3 单位：

    ![媒体服务预留单位](./media/create-account/ams-reserved-units.png)

* 如果连接到现有的媒体服务帐户，视频索引器不会更改现有的媒体“预留单位”配置。

   可能需要根据计划的负载调整媒体预留单位的类型和数量。 请记住，如果负载较高，并且没有足够的单位或速度，则视频处理可能导致超时失败。

* 如果连接到新的媒体服务帐户，视频索引器会自动在其中启动默认的流式处理终结点：

    ![媒体服务流式处理终结点](./media/create-account/ams-streaming-endpoint.png)

    流式处理终结点的启动时间很长。 从将帐户连接到 Azure 到可以在视频索引器 Web 应用程序中流式传输和观看视频的这段过程可能需要几分钟时间。

* 如果连接到现有的媒体服务帐户，视频索引器不会更改默认流式处理终结点配置。 如果未运行任何**流式处理终结点**，将无法从此媒体服务帐户或者在视频索引器中观看视频。

## <a name="next-steps"></a>后续步骤

可以遵照如下说明，以编程方式来与试用帐户和/或连接到 Azure 的视频索引器帐户进行交互：[使用 API](video-indexer-use-apis.md)。

应使用连接到 Azure 时所用的同一 Azure AD 用户。


