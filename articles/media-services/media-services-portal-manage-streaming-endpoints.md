---
title: "使用 Azure 门户管理流式处理终结点 | Microsoft Docs"
description: "本主题说明如何使用 Azure 门户管理流式处理终结点。"
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0351e286ae4f36c6c6fba25e5eaf4c135b21549


---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>使用 Azure 门户管理流式处理终结点
## <a name="overview"></a>概述
> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

在 Microsoft Azure 媒体服务中，**流式处理终结点**表示一个流服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容支付网络 (CDN) 以进一步分发。 媒体服务还提供无缝 Azure CDN 集成。 StreamingEndpoint 服务的出站流可以是实时流，也可以是媒体服务帐户中的视频点播资产。

此外，还可以通过调整流式处理单元来控制流式处理终结点服务处理不断增长的带宽需求的能力。 建议为生产环境中的应用程序分配一个或多个扩展单元。 流式处理单元提供了可按照 200 Mbps 的增量购买的专用出口容量和包括[动态打包](media-services-dynamic-packaging-overview.md)、CDN 集成和高级配置在内的其他功能。

> [!NOTE]
> 仅当流式处理终结点处于运行状态时才进行计费。
> 
> 

本主题概述了流式处理终结点提供的主要功能。 本主题还演示如何使用 Azure 门户管理流式处理终结点。 有关如何缩放流式处理终结点的信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)主题。

## <a name="start-managing-streaming-endpoints"></a>开始管理流式处理终结点
要开始管理帐户的流式处理终结点，请执行以下操作。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“流式处理终结点”。
   
    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

## <a name="adddelete-a-streaming-endpoint"></a>添加/删除流式处理终结点
若要使用 Azure 门户添加/删除流式处理终结点，请执行以下操作：

1. 若要添加流式处理终结点，请单击页面顶部的“+ 终结点”。 
2. 若要删除流式处理终结点，请按“删除”按钮。 
   
    无法删除默认流式处理终结点。
3. 单击“启动”按钮以启动流式处理终结点。
   
    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

默认情况下可以具有最多两个流式处理终结点。 如果需要请求详细信息，请参阅[配额和限制](media-services-quotas-and-limitations.md)。

## <a name="a-idconfigurestreamingendpointsaconfiguring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>配置流式处理终结点
当你拥有至少 1 个缩放单位时，通过流式处理终结点可以配置以下属性： 

* 访问控制
* 缓存控制
* 跨网站访问策略

有关这些属性的详细信息，请参阅 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)。

可通过执行以下操作配置流式处理终结点：

1. 选择要配置的流式处理终结点。
2. 单击“设置”。

后面提供了字段的简要说明。

![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. 最大缓存策略：用于为资产配置通过此流式处理终结点提供的缓存生存期。 如果未设置任何值，则使用默认值。 也可直接在 Azure 存储中定义默认值。 如果为流式处理终结点启用了 Azure CDN，则不应设置小于 600 秒的缓存策略值。  
2. 允许的 IP 地址：用于指定将允许连接到发布的流式处理终结点的 IP 地址。 如果未指定 IP 地址，则任何 IP 地址都可以连接。 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.255.0)”）。
3. Akamai 签名标头身份验证的配置：用于指定如何配置 Akamai 服务器发出的签名标头身份验证请求。 到期日期以 UTC 时间表示。

## <a name="a-idenablecdnaenable-azure-cdn-integration"></a><a id="enable_cdn"></a>启用 Azure CDN 集成
可以指定为流式处理终结点启用 Azure CDN 集成（默认禁用）。

若要将 Azure CDN 集成设置为 true：

* 流式处理终结点必须具有至少一个流式处理单元。 如果稍后想要将缩放单位设置为 0，必须首先禁用 CDN 集成。 默认情况下，创建新的流式处理终结点时会自动设置一个流式处理单元。
* 流式处理终结点必须处于停止状态。 一旦启用 CDN，即可启动流式处理终结点。 

启用 Azure CDN 集成可能需要花费最多 90 分钟。  在所有 CDN POP 中激活更改将花费最多两个小时。

在下列所有 Azure 数据中心中启用了 CDN 集成：美国西部、美国东部、北欧、西欧、日本西部、日本东部、东南亚和东亚。

启用后，会禁用“访问控制”配置。

![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

> [!IMPORTANT]
> 可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。  如果希望将 **Akamai 提供的 Azure CDN** 用于 Azure 媒体服务，则必须 [手动配置终结点](../cdn/cdn-create-new-endpoint.md)。  有关 Azure CDN 功能的详细信息，请参阅 [CDN 概述](../cdn/cdn-overview.md)。
> 
> 

### <a name="additional-considerations"></a>其他注意事项
* 为流式处理终结点启用 CDN 时，客户端不能从原点直接请求内容。 如果需要能够分别使用或不使用 CDN 测试内容，则可以创建另一个不启用 CDN 的流式处理终结点。
* 流式处理终结点主机名在启用 CDN 后仍保持不变。 启用 CDN 后，不需要对媒体服务工作流进行任何更改。 例如，如果流式处理终结点主机名是 strasbourg.streaming.mediaservices.windows.net，则启用 CDN 后使用完全相同的主机名。
* 对于新的流式处理终结点，只需通过创建新的终结点即可启用 CDN；对于现有流式处理终结点，则需要首先停止该终结点，然后再启用 CDN。

有关详细信息，请参阅[通过 Azure CDN（内容交付网络）宣布 Azure 媒体服务集成](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/)。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


