---
title: 管理视频索引器帐户
titlesuffix: Azure Media Services
description: 本文介绍如何管理已连接到 Azure 的视频索引器帐户。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 1515a026ae297a960f220a97449d2258c0b75e58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004531"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>管理已连接到 Azure 的视频索引器帐户

本文演示如何管理已连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户。

> [!NOTE]
> 只有视频索引器帐户所有者才能执行本主题中所述的帐户配置调整。

## <a name="prerequisites"></a>先决条件

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

可以遵照如下说明，以编程方式来与试用帐户和/或连接到 Azure 的视频索引器帐户进行交互：[使用 API](video-indexer-use-apis.md)。

应使用连接到 Azure 时所用的同一 Azure AD 用户。
