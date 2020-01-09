---
title: 管理视频索引器帐户
titleSuffix: Azure Media Services
description: 本文演示如何管理连接到 Azure 的视频索引器帐户。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: f3825f6c9186c5e04807dd3890a14fcc6d370989
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454665"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>管理已连接到 Azure 的视频索引器帐户

本文演示如何管理已连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。

> [!NOTE]
> 只有视频索引器帐户所有者才能执行本主题中所述的帐户配置调整。

## <a name="prerequisites"></a>必备组件

根据[连接到 Azure](connect-to-azure.md) 中所述，将自己的视频索引器帐户连接到 Azure。 

请务必遵循[先决条件](connect-to-azure.md#prerequisites)，并查看上述文章中的[注意事项](connect-to-azure.md#considerations)。

## <a name="examine-account-settings"></a>帐户设置说明

本部分介绍视频索引器帐户的设置。

查看设置：

1. 单击右上角中的用户图标，并选择“设置”。

    ![设置](./media/manage-account-connected-to-azure/select-settings.png)

2. 在“设置”页上，选择“帐户”选项卡。

如果视频索引器帐户已连接到 Azure，则会看到以下信息：

* 基础 Azure 媒体服务帐户的名称。
* 正在运行和已排队的索引作业的数目。
* 已分配的预留单位的数目和类型。

如果帐户需要做出某些调整，“设置”页上会显示与帐户配置相关的错误和警告。 消息中包含链接，单击这些链接会转到 Azure 门户中用于做出更改的确切位置。 有关详细信息，请参阅后面的[错误和警告](#errors-and-warnings)部分。

## <a name="repair-the-connection-to-azure"></a>修复与 Azure 的连接

在 "[视频索引器](https://www.videoindexer.ai/)" 页的 "**更新与 Azure 媒体服务的连接**" 对话框中，系统会要求你提供以下设置的值： 

|设置|Description|
|---|---|
|Azure 订阅 ID|可以从 Azure 门户中检索订阅 ID。 单击左侧面板中的“所有服务”，然后搜索“订阅”。 选择“订阅”并从订阅列表中选择所需的 ID。|
|Azure 媒体服务资源组名称|资源组（你在其中创建了媒体服务帐户）的名称。|
|应用程序 ID|为此视频索引器帐户创建的 Azure AD 应用程序 ID （具有指定媒体服务帐户的权限）。 <br/><br/>若要获取应用 ID，请导航到 Azure 门户。 在 Media Services 帐户下，选择帐户并**访问 "API 访问**"。 单击 "**通过服务主体 -> Azure AD 应用连接到媒体服务 API** "。 复制相关的参数。|
|应用程序密钥|与上面指定的媒体服务帐户关联的 Azure AD 应用程序密钥。 <br/><br/>若要获取应用程序密钥，请导航到 Azure 门户。 在 Media Services 帐户下，选择帐户并**访问 "API 访问**"。 单击 "**通过服务主体连接到媒体服务 API** " -> "**管理应用程序** -> **证书" & 机密**。 复制相关的参数。|

## <a name="auto-scale-reserved-units"></a>自动缩放预留单位

在“设置”页中，可以设置媒体预留单位 (RU) 的自动缩放。 如果该选项设置为“打开”，则你可以分配最多的 RU，并可以确保视频索引器自动停止/启动 RU。 使用此选项无需为空闲时间支付额外的费用，而且在索引负载较高时，无需等待索引作业花费很长的时间来完成。

自动缩放不会缩减到 1 个 RU 以下，或者扩展到超过媒体服务帐户的默认限制。 若要提高此限制，请创建服务请求。 有关配额和限制以及如何开具支持票证的信息，请参阅[配额和限制](../../media-services/previous/media-services-quotas-and-limitations.md)。

![注册](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>错误和警告

如果帐户需要做出某些调整，“设置”页上会显示与帐户配置相关的错误和警告。 消息中包含链接，单击这些链接会转到 Azure 门户中用于做出更改的确切位置。 本部分提供有关错误和警告消息的更多详细信息。

* 事件网格

    必须使用 Azure 门户注册事件网格资源提供程序。 在 [Azure 门户](https://portal.azure.com/)中，转到“订阅”>“[订阅]”>“ResourceProviders” > “Microsoft.EventGrid”。 如果该提供程序不是处于“已注册”状态，请单击“注册”。 注册需要花费几分钟时间。 

* 流式处理终结点

    确保基础媒体服务帐户中包含处于已启动状态的默认**流式处理终结点**。 否则，无法通过此媒体服务帐户或视频索引器观看视频。

* 媒体预留单位 

    必须在媒体服务资源中分配媒体预留单位才能为视频编制索引。 为获得最佳索引性能，我们建议至少分配 10 个 S3 预留单位。 有关定价信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题解答”部分。   

## <a name="next-steps"></a>后续步骤

可以遵照[使用 API](video-indexer-use-apis.md) 中的说明，以编程方式来与试用帐户和/或连接到 Azure 的视频索引器帐户进行交互。

应使用连接到 Azure 时所用的同一 Azure AD 用户。
