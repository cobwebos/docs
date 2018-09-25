---
title: 在 Azure 门户中创建视频索引器帐户
titlesuffix: Azure Cognitive Services
description: 本文介绍如何在 Azure 门户中创建视频索引器帐户。
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: juliako
ms.openlocfilehash: 8b8aa3e2f7a461c13cc73270863498283f02f740
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983109"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>创建连接到 Azure 的视频索引器帐户

创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。 需要为编制索引的分钟数付费，此外还需要支付媒体帐户相关的费用。 

本文介绍如何创建链接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 

    如果没有 Azure 订阅，请注册 [Azure 免费试用](https://azure.microsoft.com/free/)帐户。

* Azure Active Directory (AD) 域。 

    如果没有 Azure AD 域，请使用 Azure 订阅创建此域。 有关详细信息，请参阅[管理 Azure Active Directory 中的自定义域名](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD 域中的用户和成员。 将视频索引器帐户连接到 Azure 时，将要使用此成员。

    此用户应满足以下条件：

    * 是具有工作或学校帐户而不是 outlook.com、 live.com 或 hotmail.com 等个人帐户的 Azure AD 用户。
        
        ![所有 AAD 用户](./media/create-account/all-aad-users.png)

    *  是 Azure 订阅中具有“所有者”角色，或者同时具有“参与者”和“用户访问管理员”角色的成员。 可以添加某个用户两次并分配 2 个角色。 一次分配“参与者”角色，另一次分配“用户访问管理员”角色。

        ![访问控制](./media/create-account/access-control-iam.png)

* 使用 Azure 门户注册 EventGrid 资源提供程序。

    在 [Azure 门户](https://portal.azure.com/)中，转到“订阅”>“[订阅]”>“ResourceProviders” > “Microsoft.EventGrid”。 如果该提供程序不是处于“已注册”状态，请单击“注册”。 注册需要花费几分钟时间。 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>连接到 Azure

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。

2. 单击“连接到 Azure”按钮：

    ![连接到 Azure](./media/create-account/connect-to-azure.png)

3. 订阅列表出现时，请选择要使用的订阅。 

    ![将视频索引器连接到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 从支持的位置选择一个 Azure 区域：“美国西部 2”、“北欧”或“东亚”。
5. 在“Azure 媒体服务帐户”下，选择以下选项之一：

    * 若要创建新的媒体服务帐户，请选择“创建新资源组”。 提供资源组的名称。

        Azure 会在订阅中创建新帐户，包括新的 Azure 存储帐户。 新媒体服务帐户的默认初始配置使用一个流式处理终结点和 10 个 S3 预留单位。
    * 若要使用现有的媒体服务帐户，请选择“使用现有资源”。 从帐户列表中选择自己的帐户。

        媒体服务帐户必须与视频索引器帐户位于同一区域。 为了尽量减少索引持续时间和降低吞吐量，请在媒体服务帐户中将预留单位的类型和数量调整为“10 个 S3 预留单位”。
    * 若要手动配置连接，请单击“切换到手动配置”。 
    
        如果出于某种原因自动选项无法完成，或如果你的设置和配置不同于常见情况，或者要对设置拥有完全可见性和控制，则可能要手动配置连接。 
        
        在“将视频索引器连接到 Azure 订阅”中，提供以下信息。

        |设置|Description|
        |---|---|
        |视频索引器帐户区域|视频索引器帐户区域的名称。 为了提高性能和降低成本，强烈建议指定 Azure 媒体服务资源和 Azure 存储帐户所在区域的名称。 |
        |Azure Active Directory (AAD) 租户|Azure AD 租户的名称，例如“contoso.onmicrosoft.com”。 可以在 Azure 门户中检索租户信息。 将光标悬停在右上角的登录用户名之上。|
        |订阅 ID|应在其下创建此连接的 Azure 订阅。 可以从 Azure 门户中检索订阅 ID。 单击左侧面板中的“所有服务”，然后搜索“订阅”。 选择“订阅”并从订阅列表中选择所需的 ID。|
        |Azure 媒体服务资源组名称|存在媒体服务帐户的资源组的名称。|
        |媒体服务资源名称|Azure 媒体服务资源的名称。|
        |应用程序 ID|具有指定媒体服务帐户的权限的 Azure AD 应用程序 ID。 有关更多信息，请参阅[使用服务主体身份验证](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)。|
        |应用程序密钥|有关更多信息，请参阅[使用服务主体身份验证](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)。|

6. 完成后，选择“连接”。 此操作可能需要长达数分钟时间才能完成。 

    连接到 Azure 后，新的视频索引器帐户会显示在帐户列表中：

    ![新帐户](./media/create-account/new-account.png)

7. 浏览到新帐户： 

    ![视频索引器帐户](./media/create-account/vi-account.png)

## <a name="considerations"></a>注意事项

请注意以下 Azure 媒体服务相关的事项：

* 如果已连接到新的媒体服务帐户，Azure 订阅中会显示新的资源组、媒体服务帐户和存储帐户。
* 如果已连接到新的媒体服务帐户，视频索引器会将媒体**预留单位**设置为 10 个 S3 单位：

    ![媒体服务预留单位](./media/create-account/ams-reserved-units.png)

* 如果已连接到现有的媒体服务帐户，视频索引器不会更改现有的媒体**预留单位**配置。

    可能需要根据计划的负载调整媒体**预留单位**的类型和数量。 请记住，如果负载较高，并且没有足够的单位或速度，则视频处理可能导致超时失败。

* 如果已连接到新的媒体服务帐户，视频索引器会自动在其中启动默认**流式处理终结点**：

    ![媒体服务流式处理终结点](./media/create-account/ams-streaming-endpoint.png)

* 如果已连接到现有的媒体服务帐户，视频索引器不会更改默认流式处理终结点配置。 如果未运行任何**流式处理终结点**，将无法从此媒体服务帐户或者在视频索引器中观看视频。

## <a name="next-steps"></a>后续步骤

可以遵照[使用 API](video-indexer-use-apis.md) 中的说明，以编程方式来与试用帐户和/或连接到 Azure 的视频索引器帐户进行交互。

应使用连接到 Azure 时所用的同一 Azure AD 用户。


