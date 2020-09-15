---
title: gRPC 扩展协议 - Azure
description: 在本文中，你将学习如何使用 gRPC 扩展协议在实时视频分析模块与 AI 或 CV 自定义扩展之间发送消息。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 288dcd1a11c7c42d8796d3b17f2bfd56f562aaf1
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89448039"
---
# <a name="grpc-extension-protocol"></a>gRPC 扩展协议

在本文中，你将学习如何使用 gRPC 扩展协议在实时视频分析模块与 AI 或 CV 自定义扩展之间发送消息。

gRPC 是一种新式的高性能开源 RPC 框架，可在任何环境中运行。 gRPC 传输服务在以下两者之间使用 HTTP/2 双向流式传输：

* gRPC 客户端（IoT Edge 上的实时视频分析模块）与 
* gRPC 服务器（自定义扩展）。

gRPC 会话是通过 TCP/TLS 端口从 gRPC 客户端到 gRPC 服务器的单一连接。 

在单个会话中：客户端通过 gRPC 流会话将媒体流描述符（后跟视频帧）作为 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 消息发送到服务器。 服务器验证流描述符、分析视频帧，并将推理结果作为 protobuf 消息返回。

![gRPC 扩展协定](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>实现 gRPC 协议

### <a name="creating-a-grpc-connection"></a>创建 gRPC 连接

自定义扩展插件必须实现以下 gRPC 服务：

```
service MediaGraphExtension {
  rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
}
```

调用时，将打开双向流，以便消息在 gRPC 扩展和实时视频分析图之间流动。 每个参与方在此流中发送的第一条消息都将包含一个 MediaStreamDescriptor，它定义将在后面的 MediaSample 中发送的具体信息。

例如，图形扩展可发送该消息（此处以 JSON 表示），表示它会将 gRPC 消息中嵌入的 416x416 rgb24 编码的帧发送到自定义扩展。

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": {
        "graph_identifier": {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": {
            "timescale": 90000,
            "video_frame_sample_format": {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

自定义扩展将发送以下消息作为响应，该消息表示它仅返回推理。

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": {
        "extension_identifier": "customExtensionName"    }
}
```

现在双方已交换媒体描述符，接下来实时视频分析将开始将帧传输到该扩展。

### <a name="sequence-numbers"></a>序列号

gRPC 扩展节点和自定义扩展都将保留一组各自分配给其消息的序列号。 这些序列号应从 1 开始单调递增。 如果未确认任何消息，则可忽略 `ack_sequence_number`，发送第一条消息时可能会发生此情况。

完全处理相应的消息后，必须确认请求。 如果是共享内存传输，则此确认表示发送方可释放共享内存，而接收方将不再访问它。 在确认之前，发送方必须保留任何共享内存。

### <a name="reading-embedded-content"></a>读取嵌入式内容

可通过 `content_byte` 字段直接从 `MediaSample` 消息中读取嵌入式内容。 将根据 `MediaDescriptor` 对数据进行编码。

### <a name="reading-content-from-shared-memory"></a>从共享内存读取内容

通过共享内存传输内容时，发送方会在首次建立会话时将 `SharedMemoryBufferTransferProperties` 包含在其 `MediaStreamDescriptor` 中。 此内容如下所示（以 JSON 表示）：

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

然后，接收方打开文件 `/dev/shm/inference_client_share_memory_2146989006636459346`。 发件人将发送 `MediaSample` 消息，其中包含引用此文件中特定位置的 `ContentReference`。

```
{
    "timestamp": 143598615750000,
    "content_reference": {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

然后，接收方从文件中的此位置读取数据。

为了使实时视频分析容器能够通过共享内存通信，必须正确配置容器的 IPC 模式。 可采用多种方式完成此操作，但这里提供了一些建议的配置。

* 与在主机设备上运行的 gRPC 推理引擎通信时，应将 IPC 模式设置为“主机”。
* 与在另一 IoT Edge 模块中运行的 gRPC 服务器通信时，应将 IPC 模式设置为可对实时视频分析模块共享，并为自定义扩展设置 `container:liveVideoAnalytics`，其中 `liveVideoAnalytics` 是实时视频分析模块的名称。

下面是使用上述第一个选项时，设备孪生中可能呈现的内容。

```
"liveVideoAnalytics": {
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": {
        "IpcMode": "host"
      }
    }
  }
}
```

有关 IPC 模式的详细信息，请参阅 https://docs.docker.com/engine/reference/run/#ipc-settings---ipc 。

## <a name="media-graph-grpc-extension-contract-definitions"></a>媒体图 gRPC 扩展协定定义

本部分定义可定义数据流的 gRPC 协定。

### <a name="protocol-messages"></a>协议消息

![协议消息](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>客户端身份验证

自定义扩展的实施者可验证传入的 gRPC 连接的真实性，以确保这些连接来自 gRPC 扩展节点。 该节点将在请求头中提供要验证的条目。

可使用用户名/密码凭据来完成此操作。 创建 gRPC 扩展节点时，将提供如下所示的凭据：

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

发送 gRPC 请求时，以下标头将包含在请求元数据中，模仿 HTTP 基本身份验证。

`x-ms-authentication: Basic (Base64 Encoded username:password)`

## <a name="using-grpc-over-tls"></a>通过 TLS 使用 gRPC

可通过 TLS 为用于推理的 gRPC 连接提供保护。 无法保证实时视频分析和推理引擎之间的网络安全时，这非常有用。 TLS 会对嵌入 gRPC 消息中的任何内容进行加密，导致在以高速率传输帧时产生额外的 CPU 开销。

gRPC 不支持 IgnoreHostname 和 IgnoreSignature 验证选项，因此推理引擎提供的服务器证书必须包含与 gRPC 扩展节点的终结点 URL 中 IP 地址/主机名完全匹配的 CN。

## <a name="next-steps"></a>后续步骤

[了解推理元数据架构](inference-metadata-schema.md)
