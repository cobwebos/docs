---
title: Azure 通信服务中的呼叫流
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务中的呼叫流。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9fe5cb13ee352b2c49ab6ae57cabd6116cdfa720
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91667667"
---
# <a name="call-flows"></a>呼叫流

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

以下部分提供 Azure 通信服务中的呼叫流概述。 信号和媒体流取决于用户所进行的呼叫的类型。 呼叫类型的示例包括一对一 VoIP、一对一 PSTN 以及包含 VoIP 和 PSTN 连接的参与者组合的群呼。 查看[呼叫类型](./voice-video-calling/about-call-types.md)。

## <a name="about-signaling-and-media-protocols"></a>关于信号和媒体协议

建立对等呼叫或群呼时，会在后台使用两种协议：HTTP (REST) 用于信号，SRTP 用于媒体。 

客户端库之间或客户端库与通信服务信号控制器之间的信号使用 HTTP REST (TLS) 进行处理。 对于实时媒体流量 (RTP)，首选用户数据报协议 (UDP)。 如果防火墙阻止使用 UDP，则客户端库将对媒体使用传输控制协议 (TCP)。 

让我们来查看各种方案中的信号和媒体协议。 

## <a name="call-flow-cases"></a>呼叫流事例

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>事例 1：可在两个设备之间实现直接连接的 VoIP

在一对一 VoIP 或视频呼叫中，流量首选最直接的路径。 “直接路径”是指如果两个客户端库可以直接相互联系，则它们会建立直接连接。 当两个客户端库处于同一个子网中（例如，在子网 192.168.1.0/24 中）时或是当两个设备各自处于可以相互看到的子网中（子网 10.10.0.0/16 和 192.168.1.0/24 中的客户端库可以相互联系）时，这通常可以实现。

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="显示用户与通信服务之间的直接 VOIP 呼叫的关系图。":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>事例 2：无法在设备之间实现直接连接，但是可以在 NAT 设备之间实现连接的 VoIP

如果两个设备位于无法相互联系的子网中（例如，Alice 在咖啡店中工作，而 Bob 在家庭办公室中工作），但是可以在 NAT 设备之间实现连接，则客户端的客户端库会通过 NAT 设备建立连接。 

对于 Alice，它会作为咖啡店的 NAT，而对于 Bob，它会作为家庭办公室的 NAT。 Alice 的设备会发送其 NAT 的外部地址，Bob 的设备会执行相同操作。 客户端库从 Azure 通信服务免费提供的 STUN（适用于 NAT 的会话遍历实用工具）服务了解外部地址。 处理 Alice 与 Bob 之间的握手的逻辑嵌入在 Azure 通信服务提供的客户端库中。 （无需任何其他配置）

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="显示用户与通信服务之间的直接 VOIP 呼叫的关系图。":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>事例 3：无法实现直接和 NAT 连接的 VoIP

如果一个或两个客户端设备处于对称 NAT 后面，则需要用于在两个客户端库之间中继媒体的单独云服务。 此服务称为 TURN（围绕 NAT 使用中继进行遍历），也由通信服务提供。 通信服务呼叫客户端库会基于检测到的网络条件自动使用 TURN 服务。 使用 Microsoft 的 TURN 服务会单独计费。

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="显示用户与通信服务之间的直接 VOIP 呼叫的关系图。":::
 
### <a name="case-4-group-calls-with-pstn"></a>事例 4：使用 PSTN 的群呼

PSTN 呼叫的信号和媒体都使用 Azure 通信服务电话服务资源。 此资源与其他运营商互连。

PSTN 媒体流量会通过一个称为媒体处理器的组件进行流动。

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="显示用户与通信服务之间的直接 VOIP 呼叫的关系图。":::

> [!NOTE]
> 对于熟悉媒体处理的用户，我们的媒体处理器还是背靠背用户代理（在 [RFC 3261 SIP：会话初始协议](https://tools.ietf.org/html/rfc3261)中定义），这意味着它可以在处理 Microsoft 与运营商网络之间的呼叫时转换编解码器。 Azure 通信服务信号控制器是 Microsoft 根据相同 RFC 实现的 SIP 代理。

对于群呼，媒体和信号始终通过 Azure 通信服务后端进行流动。 来自所有参与者的音频和/或视频都在媒体处理器组件中混合。 群呼的所有成员会将其音频和/或视频流发送到媒体处理器，后者会返回混合媒体流。

群呼的默认实时协议 (RTP) 是用户数据报协议 (UDP)。

> [!NOTE]
> 媒体处理器可以充当多点控制单元 (MCU) 或选择性转发单元 (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="显示用户与通信服务之间的直接 VOIP 呼叫的关系图。":::

如果由于防火墙限制，客户端库无法对媒体使用 UDP，则会尝试使用传输控制协议 (TCP)。 请注意，媒体处理器组件需要 UDP，因此当发生这种情况时，通信服务 TURN 服务会添加到群呼中，用于将 TCP 转换为 UDP。 在这种情况下会产生 TURN 费用，除非手动禁用 TURN 功能。

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="显示用户与通信服务之间的直接 VOIP 呼叫的关系图。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [呼叫入门](../quickstarts/voice-video-calling/getting-started-with-calling.md)

你可能会对下列文档感兴趣：

- 详细了解[呼叫类型](../concepts/voice-video-calling/about-call-types.md)
- 了解[客户端-服务器体系结构](./client-and-server-architecture.md)
