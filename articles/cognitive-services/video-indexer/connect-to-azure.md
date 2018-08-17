---
title: 创建连接到 Azure 的视频索引器帐户 | Microsoft Docs
description: 本文介绍如何创建连接到 Azure 的视频索引器帐户。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/05/2018
ms.author: juliako
ms.openlocfilehash: ce8ba5b192b857b10b5c1e6327fc330744e8fa83
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2018
ms.locfileid: "40187361"
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

        In the Azure portal, go to **Subscriptions** > [subscription] > **ResourceProviders** > **Microsoft.EventGrid**. If not in the "Registered" state, click **Register**. It takes a couple of minutes to register. 

        ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>连接到 Azure

1. 以该用户的身份登录，然后单击“连接到 Azure”按钮：

    ![连接到 Azure](./media/create-account/connect-to-azure.png)

2. 订阅列表出现时，请选择要使用的订阅。 

    ![将视频索引器连接到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. 从支持的位置选择一个 Azure 区域：“美国西部 2”、“北欧”或“东亚”。
4. 在“Azure 媒体服务帐户”下，选择以下选项之一：

    * 若要创建新的媒体服务帐户，请选择“创建新资源组”。 提供资源组的名称。

        Azure 会在订阅中创建新帐户，包括新的 Azure 存储帐户。 新媒体服务帐户的默认初始配置使用一个流式处理终结点和 10 个 S3 预留单位。
    * 若要使用现有的媒体服务帐户，请选择“使用现有资源”。 从帐户列表中选择自己的帐户。

        媒体服务帐户必须与视频索引器帐户位于同一区域。 为了尽量减少索引持续时间和降低吞吐量，请在媒体服务帐户中将预留单位的类型和数量调整为“10 个 S3 预留单位”。
    * 若要手动配置连接，请单击“切换到手动配置”链接，并提供所需的信息：

    ![将视频索引器连接到 Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. 完成后，选择“连接”。 此操作可能需要长达数分钟时间才能完成。 

    连接到 Azure 后，新的视频索引器帐户会显示在帐户列表中：

    ![新帐户](./media/create-account/new-account.png)

6. 浏览到新帐户： 

    ![视频索引器帐户](./media/create-account/vi-account.png)

## <a name="considerations"></a>注意事项

请注意以下 Azure 媒体服务相关的事项：

* 如果已连接到新的媒体服务帐户，Azure 订阅中会显示新的资源组、媒体服务帐户和存储帐户。
* 如果已连接到新的媒体服务帐户，视频索引器会将媒体**预留单位**设置为 10 个 S3 单位：

    ![媒体服务预留单位](./media/create-account/ams-reserved-units.png)

* 如果已连接到现有的媒体服务帐户，视频索引器不会更改现有的媒体**预留单位**配置。

    可能需要根据计划的负载调整媒体**预留单位**的类型和数量。 请记住，如果负载较高，并且没有足够的单位或速度，则视频处理可能导致超时失败。

* 如果已连接到新的媒体服务帐户，视频索引器会自动在其中启动**流式处理终结点**：

    ![媒体服务流式处理终结点](./media/create-account/ams-streaming-endpoint.png)

* 如果已连接到现有的媒体服务帐户，视频索引器不会更改流式处理终结点配置。 如果未运行任何**流式处理终结点**，将无法从此媒体服务帐户或者在视频索引器中观看视频。

## <a name="use-video-indexer-apis-v2"></a>使用视频索引器 API v2

可以遵照[使用 API](video-indexer-use-apis.md) 中的说明，以编程方式来与试用帐户和/或连接到 Azure 的视频索引器帐户进行交互。

应使用连接到 Azure 时所用的同一 Azure AD 用户。

## <a name="next-steps"></a>后续步骤

[检查输出 JSON 的详细信息](video-indexer-output-json-v2.md)。

