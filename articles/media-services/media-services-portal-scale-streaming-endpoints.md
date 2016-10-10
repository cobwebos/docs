<properties
	pageTitle=" 使用 Azure 门户缩放流式处理终结点 | Azure"
	description="本教程逐步演示如何使用 Azure 门户缩放流式处理终结点。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	wacn.date=""
	ms.author="juliako"/>


# 使用 Azure 门户缩放流式处理终结点

##概述

> [AZURE.NOTE] 若要完成本教程，你需要一个 Azure 帐户。有关详细信息，请参阅 [Azure 试用](/pricing/1rmb-trial/)。

使用 Azure 媒体服务时，最常见的场景之一是通过自适应比特率流式处理将视频传送至你的客户端。媒体服务支持以下自适应比特率流式处理技术：HTTP 实时流式处理 (HLS)、平滑流式处理、MPEG DASH 和 HDS（仅适用于 Adobe PrimeTime/Access 许可证持有人）。

媒体服务所提供的动态打包可让你以媒体服务支持的流格式（MPEG DASH、HLS、Smooth Streaming、HDS）及时传送自适应比特率 MP4 编码内容，而无须按这些流格式逐个存储预先打包的版本。

若要使用动态打包，必须执行下列操作：

- 将夹层（源）文件编码成一组自适应比特率 MP4 文件（本教程稍后将演示编码步骤）。
- 针对你要从其传送内容的“流式处理终结点”，创建至少一个流式处理单元。以下步骤显示如何更改流式处理单元数。

通过动态打包，你只需要存储及支付一种存储格式的文件，媒体服务将会根据客户端的要求创建并提供适当的响应。

此外，还可以通过调整流式处理单元来控制流式处理终结点服务处理不断增长的带宽需求的能力。建议为生产环境中的应用程序分配一个或多个扩展单元。流式处理单元提供可按照 200 Mbps 的增量购买的专用出口容量和包括[动态打包](/documentation/articles/media-services-dynamic-packaging-overview/)、CDN 集成和高级配置在内的其他功能。有关详细信息，请参阅[使用 Azure 门户管理流式处理终结点](/documentation/articles/media-services-portal-manage-streaming-endpoints/)。

## 缩放流式处理终结点

若要创建和更改流式处理保留单元数，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn/)登录。
2. 在“设置”窗口中，选择“流式处理终结点”。
3. 单击要缩放的流式处理终结点。
4. 滑动滑块，指定流式处理单元数
 
![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

请注意以下事项：

- 大约需要 20 分钟才能完成所有新流式处理单元的分配。
- 当前，将流式处理单位的任何正值设置回“无”可将按需流式处理功能禁用最多 1 小时。
- 为 24 小时期间指定的最大单位数将用于计算成本。有关定价详细信息，请参阅 [媒体服务定价详细信息](/pricing/details/media-services/)。

<!---HONumber=Mooncake_0926_2016-->