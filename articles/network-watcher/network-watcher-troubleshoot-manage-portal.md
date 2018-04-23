---
title: 对 Azure 虚拟网络网关和连接进行故障排除 - 门户 | Microsoft Docs
description: 此页说明如何使用 Azure 网络观察程序对门户进行故障排除
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: becc4f799e34aabba5c703f09e51a4b7d497c45a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>从 Azure 门户使用网络观察程序对虚拟网络网关和连接进行故障排除

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

网络观察程序提供了许多功能，因为它关系到了解 Azure 中的网络资源。 其中一项功能就是资源故障排除。 可以通过门户、PowerShell、CLI 或 REST API 调用资源故障排除。 调用后，网络观察程序会检查虚拟网络网关或连接的运行状况，并返回调查结果。

## <a name="before-you-begin"></a>开始之前

此方案假定已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

有关支持的网关类型列表，请访问[支持的网关类型](network-watcher-troubleshoot-overview.md#supported-gateway-types)。

## <a name="overview"></a>概述

“资源故障排除”提供对使用虚拟网络网关和连接时发生的问题进行故障排除的功能。 发出资源故障排除请求时，系统将查询并检查日志。 检查完成后，将返回结果。 资源故障排除请求是长时间运行的请求，可能需要好几分钟才能返回结果。 故障排除日志存储在指定的存储帐户上的容器中。

## <a name="troubleshoot-a-gateway-or-connection"></a>对网关或连接进行故障排除

1. 导航到 [Azure 门户](https://portal.azure.com)，然后单击“网络” > “网络观察程序” > “VPN 诊断”
2. 选择“订阅”、“资源组”和“位置”。
3. 资源故障排除会返回有关资源运行状况的数据。 它还会将相关的日志保存到存储帐户以待查看。 单击“存储帐户”，选择一个存储帐户。
4. 在“存储帐户”边栏选项卡中，选择现有存储帐户或单击“+ 存储帐户”新建一个。
5. 在“容器”边栏选项卡上，选择现有容器或单击“+ 容器”创建新容器。 完成后，单击“选择”
6. 选择要进行故障排除的网关和连接资源，然后单击“开始故障排除”

如果选择了多个资源，则针对网关资源同时运行故障排除。 不能同时针对连接及其关联网关运行故障排除。 建议先对网关进行故障排除，因为连接故障排除过程耗时较长。 对资源运行 VPN 诊断时，“故障排除状态”列所显示的状态为“正在运行”

完成后，状态列会更改为“正常”或“不正常”。

![故障排除完成][2]

## <a name="understanding-the-results"></a>了解结果

在窗口的“详细信息”部分中，“状态”选项卡会显示针对所选资源运行的最后一次故障排除的状态。 最新诊断的结果将在最后一次运行 xx 分钟后显示。

|属性  |说明  |
|---------|---------|
|资源     | 指向资源的链接。        |
|存储路径     |  存储帐户和包含日志（运行期间如有生成）的容器的路径。 退出门户后不会保留此设置。        |
|摘要     | 资源运行状况摘要。        |
|详细信息     | 资源运行状况的详细信息。        |
|最后一次运行     | 最后一次运行故障排除的时间。        |


“操作”选项卡提供有关如何解决问题的常规指导。 如果可以对问题采取措施，将提供一个包含更多指导的链接。 如果没有更多指导，响应将提供一个用于建立支持案例的 URL。  有关响应的属性及其包含的内容的详细信息，请访问[网络观察程序故障排除概述](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>后续步骤

如果停止 VPN 连接的设置已更改，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)找到可能有问题的网络安全组和安全规则。


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
