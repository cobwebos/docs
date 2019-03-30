---
title: Azure IoT 中心设备流（预览版）| Microsoft Docs
description: 概要介绍 IoT 中心设备流。
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 672b06dda41edb18cbf31352188b0fdd2a155782
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649624"
---
# <a name="iot-hub-device-streams-preview"></a>IoT 中心设备流（预览版）

## <a name="overview"></a>概述
Azure IoT 中心*设备流*可促进为各种云到设备通信方案创建安全的双向 TCP 隧道。 设备流由 IoT 中心*流式处理终结点*进行调解，该终结点充当设备和服务终结点之间的代理。 此设置如下图所示，当设备位于网络防火墙之后或驻留在专用网络内部时特别有用。 因此，IoT 中心设备流有助于满足客户以防火墙友好方式访问 IoT 设备的需求，而无需广泛开放传入或传出网络防火墙端口。

![替代文本](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png "IoT 设备流概述")


使用 IoT 中心设备流可让设备保持安全状态，同时只需打开端口 443 上到 IoT 中心流式处理终结点的出站 TCP 连接。 建立设备流后，服务端和设备端应用程序将各自具有针对 WebSocket 客户端对象的编程访问权限，以便相互发送和接收原始字节。 此隧道提供的可靠性和顺序保证与 TCP 提供的可靠性和顺序保证相当。

## <a name="benefits"></a>优点
IoT 中心设备流具有以下优点：
- **防火墙友好的安全连接：** 可以从服务终结点访问 IoT 设备，而无需在设备或网络外围打开入站防火墙端口（仅需要端口 443 上到 IoT 中心的出站连接）。

- **身份验证：** 隧道的设备端和服务端都需要使用相应的凭据对 IoT 中心进行身份验证。

- **加密：** 默认情况下，IoT 中心设备流使用启用了 TLS 的连接。 这可确保始终加密流量，而无论应用程序是否使用加密。

- **简单的连接：** 在许多情况下，使用设备流可消除对虚拟专用网络进行复杂设置以连接到 IoT 设备的需求。

- **与 TCP/IP 堆栈的兼容性：** IoT 中心设备流可以接纳 TCP/IP 应用程序流量。 这意味着各种专有协议和标准协议均可利用此功能。

- **专用网络设置下的易用性：** 服务可以通过引用设备 ID（而不是设备的 IP 地址）与设备通信。 在设备位于专用网络内并拥有专用 IP 地址，或其 IP 地址由系统动态分配且对于服务端未知的情况下，这很有用。

## <a name="device-stream-workflows"></a>设备流工作流
当服务请求通过提供设备 ID 连接到设备时，设备流会启动。 此工作流特别适合客户端/服务器通信模型，其中包括 SSH 和 RDP，即用户打算使用 SSH 或 RDP 客户端程序远程连接到设备上运行的 SSH 或 RDP 服务器。

设备流创建过程涉及设备、服务、IoT 中心的主要终结点和流式处理终结点之间的协商。 IoT 中心的主要终结点协调设备流的创建，流式处理终结点则处理在服务和设备之间流动的流量。

### <a name="device-stream-creation-flow"></a>设备流创建流程
使用 SDK 以编程方式创建设备流涉及以下步骤，如下图所示：

![替代文本](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png "设备流握手过程")


1. 设备应用程序会预先注册回叫，以便在向设备发起新设备流时收到通知。 此步骤通常在设备启动并连接到 IoT 中心时发生。

2. 服务端程序在需要时通过提供设备 ID（_而不是_ IP 地址）来发起设备流。

3. IoT 中心通过调用步骤 1 中注册的回叫来通知设备端程序。 设备可以接受或拒绝流启动请求。 此逻辑可以特定于应用程序方案。 如果设备拒绝了流请求，则 IoT 中心会相应地通知服务；否则，请执行以下步骤。

4. 设备会在端口 443 上创建到流式处理终结点的安全出站 TCP 连接，并将连接升级为 WebSocket。 流式处理终结点的 URL 以及用于进行身份验证的凭据都由 IoT 中心在步骤 3 发送的请求中向设备提供。

5. 服务获得设备接受流这一结果的通知，并继续创建自己的到流式处理终结点的 WebSocket 客户端。 同样，它从 IoT 中心接收流式处理终结点 URL 和身份验证信息。

在上述握手过程中：
- 握手过程必须在 60 秒内完成（步骤 2 到 5），否则握手会因超时而失败且服务将相应获得通知。

- 上述流创建流程完成后，流式处理终结点将充当代理，并通过其各自的 WebSocket 在服务和设备之间传输流量。

- 设备和服务都需要端口 443 上的到 IoT 中心主要终结点和流式处理终结点的出站连接。 IoT 中心门户的“概览”选项卡上提供这些终结点的 URL。

- 已建立的流的可靠性和排序保证与 TCP 的可靠性和排序保证相当。

- 到 IoT 中心和流式处理终结点的所有连接都使用 TLS 并加密。

### <a name="termination-flow"></a>终止流
当到网关的任一 TCP 连接（由服务或设备）断开时，建立的流会终止。 此情况可通过关闭设备或服务程序上的 WebSocket 来自愿发生，也可能在出现网络连接超时或进程失败的情况下非自愿发生。 当设备或服务与流式处理终结点的连接终止时，另一个 TCP 连接也将被（强制）终止，服务和设备负责重新创建流（如果需要）。


## <a name="connectivity-requirements"></a>连接要求

设备流的设备端和服务端都必须能够建立到 IoT 中心及其流式处理终结点的启用 TLS 的连接。 这需要在端口 443 上建立到这些终结点的出站连接。 可以在 IoT 中心的“概述”选项卡上找到与这些终结点关联的主机名，如下图所示：![替代文本](./media/iot-hub-device-streams-overview/device-stream-portal.png "设备流终结点")

或者，可以使用中心“属性”部分下的 Azure CLI 检索终结点信息，具体而言是 `property.hostname` 和 `property.deviceStreams` 键。

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

输出是中心的设备和服务可能需要连接的所有终结点的 JSON 对象，以便建立设备流。

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> 确保已安装 Azure CLI 2.0.57 或更高版本。 可以在 [此处](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)下载最新版本。
> 

## <a name="whitelist-device-streaming-endpoints"></a>将设备流式处理终结点加入允许列表

如[之前](#overview)所述，设备在设备流启动过程中创建到 IoT 中心流式处理终结点的出站连接。 设备或其网络上的防火墙必须允许端口 443 上到流式传输网关的出站连接（请注意，通信发生在使用 TLS 加密的 WebSocket 连接上）。

可以在 Azure IoT 中心门户的“概述”选项卡下找到设备流式处理终结点的主机名。![替代文本](./media/iot-hub-device-streams-overview/device-stream-portal.PNG "设备流终结点")

或者，可以使用 Azure CLI 找到此信息：

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> 确保已安装 Azure CLI 2.0.57 或更高版本。 可以在 [此处](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)下载最新版本。
> 

## <a name="troubleshoot-via-device-streams-activity-logs"></a>通过设备流活动日志排除故障

可以设置 Azure Monitor 日志收集在 IoT 中心设备流的活动日志。 这在故障排除方案中非常有用。

请执行以下步骤来配置 Azure Monitor 的 IoT 中心的设备流活动的日志：

1. 导航到 IoT 中心的“诊断设置”选项卡，然后单击“打开诊断”链接。

   ![替代文本](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings.PNG "启用诊断日志")


2. 提供诊断设置的名称，然后选择“发送到 Log Analytics”选项。 将指导你选择现有的 Log Analytics 工作区资源或创建一个新。 此外，在列表中选中“DeviceStreams”。

    ![替代文本](./media/iot-hub-device-streams-overview/device-streams-diagnostics.PNG "启用设备流日志")

3. 现在可以在 IoT 中心门户的“日志”选项卡下访问设备流日志。 设备流活动日志将在 `AzureDiagnostics` 表中显示并具有 `Category=DeviceStreams`。

    <p>
如下所示，日志中还提供目标设备的标识和操作的结果。
    ![替代文本](./media/iot-hub-device-streams-overview/device-streams-log-analytics.PNG "访问设备流日志")


## <a name="regional-availability"></a>区域可用性

在公开预览版发布期间，IoT 中心设备流在“美国中部”和“美国中部 EUAP”区域提供。 请确保在这其中的一个区域创建中心。 


## <a name="sdk-availability"></a>SDK 可用性

每个流的两端（设备端和服务端）使用 IoT 中心 SDK 建立隧道。 在公开预览版中，客户可以选择以下 SDK 语言：
- C 和 C# SDK 支持设备端上的设备流。

- NodeJS 和 C# SDK 支持服务端上的设备流。


## <a name="iot-hub-device-stream-samples"></a>IoT 中心设备流示例

我们发布了两个[快速入门示例](/azure/iot-hub)，演示应用程序如何使用设备流。
* *Echo*示例演示 （通过直接调用 SDK API） 以编程方式使用的设备流。
* 本地代理示例演示如何通过设备流以隧道方式连接现成的客户端/服务器应用程序流（例如 SSH、RDP 或 Web）。

这些示例在下面进行更详细的介绍。

### <a name="echo-sample"></a>回显示例
回显示例演示如何以编程方式使用设备流在服务和设备应用程序之间发送和接收字节。 使用以下链接访问快速入门指南。 请注意，可以通过不同的语言使用服务和设备程序，例如，C 设备程序可与 C# 服务程序配合使用。

| SDK 中 IsInRole 中的声明    | 服务程序                                          | 设备程序                                           |
|--------|----------------------------------------------------------|----------------------------------------------------------|
| C#     | [链接](quickstart-device-streams-echo-csharp.md) | [链接](quickstart-device-streams-echo-csharp.md) |
| Node.js | [链接](quickstart-device-streams-echo-nodejs.md) | -                                                        |
| C      | -                                                        | [链接](quickstart-device-streams-echo-c.md)      |

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>本地代理示例（适用于 SSH 或 RDP）
本地代理示例演示了启用现有应用程序流量隧道的方法，该方法涉及客户端和服务器程序之间的通信。 此设置适用于 SSH 和 RDP 等客户端/服务器协议，其中服务端充当客户端（运行 SSH 或 RDP 客户端程序），设备端则充当服务器（运行 SSH 守护程序或 RDP 服务器程序）。 

本部分介绍如何使用设备流，以便用户使用 SSH 通过设备流来连接到设备（RDP 或其他客户端/服务器应用程序的情况类似，方法是使用协议的相应端口）。

该设置利用下图所示的两个*本地代理*程序，即*设备本地代理*和*服务本地代理*。 本地代理程序负责使用 IoT 中心执行[设备流启动握手](#device-stream-creation-flow)，并使用常规客户端/服务器套接字与 SSH 客户端和 SSH 守护进程交互。

![替代文本](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png "SSH/RDP 的设备流代理设置")

1. 用户运行服务本地代理以向设备发起设备流。

2. 设备-本地代理接受流发起请求，并建立到 IoT 中心的流式处理终结点的隧道（如上所述）。

3. 设备本地代理连接到在设备上侦听端口 22 的 SSH 守护程序终结点。

4. 服务本地代理侦听指定端口，该端口等待来自用户的新 SSH 连接（示例中使用端口 2222，但可以将该端口配置为任何其他的可用端口）。 用户将 SSH 客户端指向 localhost 上的服务本地代理端口。

### <a name="notes"></a>说明
- 上述步骤完成了 SSH 客户端（右侧）与 SSH 守护程序（左侧）之间的端到端隧道连接。 此端到端连接的一部分涉及通过设备流将流量发送到 IoT 中心。

- 图中的箭头表示终结点之间建立的连接的方向。 具体而言，请注意，没有到设备的入站连接（这通常被防火墙拦截）。

- 在服务本地代理上使用端口 2222 的选择是任意选择。 可以将代理配置为使用任何其他可用端口。

- 在这种情况下，端口 22 的选择取决于协议并特定于 SSH。 对于 RDP，必须使用端口 3389。 这可以在提供的示例程序中配置。

使用以下链接获取有关如何以选择的语言运行本地代理程序的说明。 与[回显示例](#echo-sample)类似，可以使用不同的语言运行设备本地代理程序和服务本地代理程序，因为它们完全可互操作。

| SDK 中 IsInRole 中的声明    | 服务本地代理                                       | 设备本地代理                                |
|--------|-----------------------------------------------------------|---------------------------------------------------|
| C#     | [链接](quickstart-device-streams-proxy-csharp.md)  | [链接](quickstart-device-streams-proxy-csharp.md) |
| NodeJS | [链接](quickstart-device-streams-proxy-nodejs.md)         | -                                                 |
| C      | -                                                         | [链接](quickstart-device-streams-proxy-c.md)      |

## <a name="next-steps"></a>后续步骤

使用以下链接详细了解设备流：

> [!div class="nextstepaction"]
> [IoT Show 中的设备流 (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
> [尝试使用设备流快速入门指南](/azure/iot-hub)
