---
title: 配置 API 协议 - Azure 事件网格 IoT 边缘 |微软文档
description: 配置 IoT 边缘上事件网格公开的 API 协议。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841804"
---
# <a name="configure-event-grid-api-protocols"></a>配置事件网格 API 协议

本指南提供了事件网格模块的可能协议配置的示例。 事件网格模块公开 API 以进行管理和运行时操作。 下表捕获协议和端口。

| 协议 | 端口 | 描述 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 默认情况下关闭。 仅在测试期间有用。 不适合生产工作负载。
| HTTPS | 4438 | 默认

有关所有可能的配置，请参阅[安全和身份验证](security-authentication.md)指南。

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>将 HTTPS 公开给同一边缘网络上的 IoT 模块

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>将 HTTPS 启用到其他 IoT 模块和非 IoT 工作负载

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> **PortBindings**部分允许您将内部端口映射到容器主机的端口。 此功能使得如果 IoT 边缘设备可以公开访问，则可以从 IoT 边缘容器网络外部访问事件网格模块。

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>在同一边缘网络上的 IoT 模块中公开 HTTP 和 HTTPS

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>将 HTTP 和 HTTPS 启用到其他 IoT 模块和非 IoT 工作负载

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> 默认情况下，每个 IoT 模块都是网桥网络创建的 IoT Edge 运行时的一部分。 它使同一网络上的不同 IoT 模块能够相互通信。 **PortBindings**允许您将容器内部端口映射到主机上，从而允许任何人能够从外部访问事件网格模块的端口。

>[!IMPORTANT]
> 虽然可以在 IoT Edge 网络之外访问这些端口，但客户端身份验证强制实际允许谁向模块进行调用。
