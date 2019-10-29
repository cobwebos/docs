---
title: 故障排除-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的故障排除。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 59b98ebbaade95bfcc613f3ae83b20a3e0a9e15a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992127"
---
# <a name="common-issues"></a>常见问题

如果你在环境中的 IoT Edge 上使用 Azure 事件网格时遇到问题，请使用本文作为疑难解答和解决方法。

## <a name="view-event-grid-module-logs"></a>查看事件网格模块日志

若要解决此问题，你可能需要访问事件网格模块日志。 为此，请在部署该模块的 VM 上运行以下命令：

在 Windows 上，

```sh
docker -H npipe:////./pipe/notedly_moby_engine container logs eventgridmodule
```

在 Linux 上，

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>无法发出 HTTPS 请求

* 首先确保事件网格模块的**入站： serverAuth： tlsPolicy**设置为**strict**或**enabled**。

* 如果其模块到模块的通信，请确保在端口**4438**上进行调用，并且模块的名称与部署的名称相匹配。 

  例如，如果事件网格模块是用名称**eventgridmodule**部署的，则 URL 应 **https://eventgridmodule:4438** 。 请确保大小写和端口号正确。
    
* 如果它来自非 IoT 模块，请确保在部署期间将事件网格端口映射到主机计算机，例如

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>无法发出 HTTP 请求

* 首先确保事件网格模块的**入站： serverAuth： tlsPolicy**设置为**enabled**或**disabled**。

* 如果其模块到模块的通信，请确保在端口**5888**上进行调用，并且模块的名称与部署的名称相匹配。 

  例如，如果事件网格模块是用名称**eventgridmodule**部署的，则 URL 应 **http://eventgridmodule:5888** 。 请确保大小写和端口号正确。
    
* 如果它来自非 IoT 模块，请确保在部署期间将事件网格端口映射到主机计算机，例如

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>证书链由不受信任的颁发机构颁发

默认情况下，事件网格模块配置为使用 IoT Edge security daemon 颁发的证书对客户端进行身份验证。 请确保客户端正在提供一个根为此链的证书。

[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge)中的**IoTSecurity**类演示如何从 IoT Edge 安全守护程序检索证书，并使用该证书来配置传出呼叫。

如果它是非生产环境，则可以选择禁用客户端身份验证。 有关如何执行此操作的详细信息，请参阅[安全性和身份验证](security-authentication.md)。

## <a name="debug-events-not-received-by-subscriber"></a>订阅服务器未收到调试事件

这种情况的典型原因如下：

* 此事件从未成功发布。 将事件发布到事件网格模块时，应收到200（正常）的 HTTP 状态。

* 检查事件订阅以验证：
    * 终结点 URL 有效
    * 订阅中的任何筛选器都不会导致事件 "删除"。

* 验证订阅服务器模块是否正在运行

* 登录到部署了事件网格模块的 VM，并查看其日志。

* 通过设置**broker： logDeliverySuccess = true**并重新部署事件网格模块并重试请求，打开每个传递日志记录。 启用每个传递的日志记录可能会影响吞吐量和延迟，因此一旦调试完成，我们的建议是将此转换回**broker： logDeliverySuccess = false**并重新部署事件网格模块。

* 通过设置指标来启用指标 **： reportertype = console**和重新部署事件网格模块。 此后的任何操作都会导致度量值记录在事件网格模块的控制台上，该模块可用于进一步调试。 建议仅打开用于调试的度量值，并在完成后通过设置度量值将其关闭 **： reportertype = 无**和重新部署事件网格模块。

## <a name="next-steps"></a>后续步骤

使用[https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)的 IoT Edge 上的事件网格报告所有问题。