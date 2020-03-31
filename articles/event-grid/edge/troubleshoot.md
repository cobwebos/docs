---
title: 故障排除 - Azure 事件网格 IoT 边缘 |微软文档
description: 在 IoT 边缘的事件网格中进行故障排除。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100161"
---
# <a name="common-issues"></a>常见问题

如果在环境中使用 IoT Edge 上的 Azure 事件网格遇到问题，请使用本文作为故障排除和解决指南。

## <a name="view-event-grid-module-logs"></a>查看事件网格模块日志

要进行故障排除，您可能需要访问事件网格模块日志。 为此，在部署模块的 VM 上运行以下命令：

在 Windows 上，

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

在 Linux 上，

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>无法发出 HTTPS 请求

* 首先确保事件网格模块已**入站：服务器Auth：tls策略**设置为**严格**或**已启用**。

* 如果模块到模块通信，请确保在端口**4438**上进行调用，模块的名称与部署的内容匹配。 

  例如，如果事件网格模块使用名称**事件网格模块**部署，则 URL 应为**https://eventgridmodule:4438**。 确保套管和端口号正确。
    
* 如果是来自非 IoT 模块，请确保事件网格端口在部署期间映射到主机，例如，

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

* 首先确保事件网格模块已**入站：服务器Auth：tls策略**设置为**已启用**或**禁用**。

* 如果模块到模块通信，请确保对端口**5888**进行调用，模块的名称与部署的内容匹配。 

  例如，如果事件网格模块使用名称**事件网格模块**部署，则 URL 应为**http://eventgridmodule:5888**。 确保套管和端口号正确。
    
* 如果是来自非 IoT 模块，请确保事件网格端口在部署期间映射到主机，例如，

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

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>证书链由不信任的颁发机构颁发

默认情况下，事件网格模块配置为使用 IoT Edge 安全守护进程颁发的证书对客户端进行身份验证。 确保客户端正在显示根植于此链的证书。

中的[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) **IoTSecurity**类演示如何从 IoT 边缘安全守护进程检索证书，并用它来配置传出调用。

如果是非生产环境，则可以选择关闭客户端身份验证。 有关如何执行此操作的详细信息，请参阅[安全和身份验证](security-authentication.md)。

## <a name="debug-events-not-received-by-subscriber"></a>订阅者未接收的调试事件

造成这种情况的典型原因是：

* 事件从未成功发布。 在将事件发布到事件网格模块时，应收到 200（OK） 的 HTTP 状态代码。

* 检查事件订阅以验证：
    * 终结点 URL 有效
    * 订阅中的任何筛选器不会导致事件"删除"。

* 验证订户模块是否正在运行

* 登录到部署事件网格模块的 VM 并查看其日志。

* 通过设置**代理：logDelivery成功=true**并重新部署事件网格模块并重试请求，打开每个传递日志记录。 打开每次交付的日志记录可能会影响吞吐量和延迟，因此一旦调试完成，我们建议将其转回**代理：logDelivery成功—false**并重新部署事件网格模块。

* 通过设置指标打开指标 **：报告器类型=控制台**和重新部署事件网格模块。 之后的任何操作都将导致指标记录在事件网格模块的控制台上，可用于进一步调试。 我们建议仅打开用于调试的指标，并在完成一次，通过设置**指标：报告类型_none**和重新部署事件网格模块来将其关闭。

## <a name="next-steps"></a>后续步骤

报告任何问题，建议在 IoT 边缘使用事件网格。 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)