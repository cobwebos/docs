---

title: "使用 Azure 门户管理流式处理终结点 | Microsoft Docs"
description: "本主题说明如何使用 Azure 门户管理流式处理终结点。"
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 80ba024cd219b10bce8d88c9f11fd2d14d4ed34f
ms.openlocfilehash: 223fee8930b1aebff94eddaeb82b5c0e364e067c


---


# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>使用 Azure 门户管理流式处理终结点

本主题说明如何使用 Azure 门户管理流式处理终结点。 

>[!NOTE]
>请确保查看[概述](media-services-streaming-endpoints-overview.md)主题。 

有关如何缩放流式处理终结点的信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)主题。

## <a name="start-managing-streaming-endpoints"></a>开始管理流式处理终结点 

要开始管理帐户的流式处理终结点，请执行以下操作。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”边栏选项卡中，选择“流式处理终结点”。
   
    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> 仅当流式处理终结点处于运行状态时才进行计费。

## <a name="adddelete-a-streaming-endpoint"></a>添加/删除流式处理终结点

>[!NOTE]
>无法删除默认流式处理终结点。

若要使用 Azure 门户添加/删除流式处理终结点，请执行以下操作：

1. 若要添加流式处理终结点，请单击页面顶部的“+ 终结点”。 

    如果想要使用不同的 CDN 并直接访问，可能需要多个流式处理终结点。

2. 若要删除流式处理终结点，请按“删除”按钮。      
3. 单击“启动”按钮以启动流式处理终结点。
   
    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="a-idconfigurestreamingendpointsaconfiguring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>配置流式处理终结点
使用流式处理终结点可以配置以下属性：

* 访问控制
* 缓存控制
* 跨网站访问策略

有关这些属性的详细信息，请参阅 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)。

可通过执行以下操作配置流式处理终结点：

1. 选择要配置的流式处理终结点。
2. 单击“设置”。

后面提供了字段的简要说明。

![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. 最大缓存策略：用于为资产配置通过此流式处理终结点提供的缓存生存期。 如果未设置任何值，则使用默认值。 也可直接在 Azure 存储中定义默认值。 如果为流式处理终结点启用了 Azure CDN，则不应设置小于 600 秒的缓存策略值。  
2. 允许的 IP 地址：用于指定将允许连接到发布的流式处理终结点的 IP 地址。 如果未指定 IP 地址，则任何 IP 地址都可以连接。 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.255.0)”）。
3. Akamai 签名标头身份验证的配置：用于指定如何配置 Akamai 服务器发出的签名标头身份验证请求。 到期日期以 UTC 时间表示。

## <a name="scale-your-premium-streaming-endpoint"></a>缩放高级流式处理终结点

有关详细信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)主题。

## <a name="a-idenablecdnaenable-azure-cdn-integration"></a><a id="enable_cdn"></a>启用 Azure CDN 集成

创建新帐户时，默认情况下，默认流式处理终结点 Azure CDN 集成已启用。

如果以后想要禁用/启用 CDN，流式处理终结点必须处于“已停止”状态。 可能需要长达 2 小时才能启用 Azure CDN 集成并使更改在所有 CDN POP 中生效。 但是，可以启动流式处理终结点和流，而不会被流式处理终结点中断，集成完成后，将从 CDN 传送流。 在预配期间，流式处理终结点将处于“正在启动”状态，你可能会发现性能降低。

将在所有 Azure 数据中心启用 CDN 集成，但中国区域和联邦政府区域除外。

启用 CDN 集成后，“访问控制”、“自定义主机名”和“Akamai 签名身份验证”配置将处于禁用状态。
 
> [!IMPORTANT]
> 标准流式处理终结点可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。 可以使用所有 **Azure CDN 定价层和提供程序**配置高级流式处理终结点。 有关 Azure CDN 功能的详细信息，请参阅 [CDN 概述](../cdn/cdn-overview.md)。
 
### <a name="additional-considerations"></a>其他注意事项

* 为流式处理终结点启用 CDN 时，客户端不能从原点直接请求内容。 如果需要能够分别使用或不使用 CDN 测试内容，则可以创建另一个不启用 CDN 的流式处理终结点。
* 流式处理终结点主机名在启用 CDN 后仍保持不变。 启用 CDN 后，不需要对媒体服务工作流进行任何更改。 例如，如果流式处理终结点主机名是 strasbourg.streaming.mediaservices.windows.net，则启用 CDN 后使用完全相同的主机名。
* 对于新的流式处理终结点，只需通过创建新的终结点即可启用 CDN；对于现有流式处理终结点，则需要首先停止该终结点，然后再启用/禁用 CDN。
* 只能通过 Azure 管理门户使用 **Verizon 标准 CDN 提供程序**配置标准流式处理终结点。 但是，可以使用 REST API 启用其他 Azure CDN 提供程序。

## <a name="configure-cdn-profile"></a>配置 CDN 配置文件

可以通过选择顶部的“管理 CDN”按钮配置 CDN 配置文件。

![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


