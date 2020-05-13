---
title: 故障排除指南 - Azure 事件中心 | Microsoft Docs
description: 本文提供 Azure 事件中心消息传送异常和建议的操作列表。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124663"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure 事件中心-故障排除指南
本文提供了有关使用 Azure EventHubs 时可能会遇到的一些问题的疑难解答提示和建议。

## <a name="connectivity-certificate-or-timeout-issues"></a>连接性、证书或超时问题
以下步骤可帮助排除 *.servicebus.windows.net 下所有服务的连接性/证书/超时问题。 

- 浏览至 `https://<yournamespacename>.servicebus.windows.net/` 或使用 [wget](https://www.gnu.org/software/wget/)。 这可帮助检查是否存在 IP 筛选或虚拟网络或证书链问题（使用 java SDK 时最常见）。

    成功消息的示例：
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失败错误消息的示例：

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 运行以下命令，检查防火墙是否阻止了任何端口。 使用的端口为 443 (HTTPS)、5671 (AMQP) 和 9093 (Kafka)。 根据使用的库，还会使用其他端口。 下面是用于检查 5671 端口是否被阻止的示例命令。

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    在 Linux 上：

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 出现间歇性连接问题时，请运行以下命令，检查是否存在任何丢弃的数据包。 此命令会尝试通过服务每隔 1 秒建立 25 个不同的 TCP 连接。 然后，可以检查其中有多少成功/失败，还可以查看 TCP 连接延迟。 可从[此处](/sysinternals/downloads/psping)下载 `psping` 工具。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    如果使用的是 `tnc`、`ping` 等其他工具，可以使用等效的命令。 
- 如果上述步骤没有帮助，请获取网络跟踪，并使用 [Wireshark](https://www.wireshark.org/) 之类的工具对其进行分析。 如果需要，请联系 [Microsoft 支持部门](https://support.microsoft.com/)。 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>服务升级/重新启动时可能出现的问题
后端服务升级和重新启动可能会对应用程序造成以下影响：

- 可能会暂时限制请求。
- 传入消息/请求中可能有删除项。
- 日志文件可能包含错误消息。
- 应用程序可能会在几秒钟后断开与服务的连接。

如果应用程序代码使用 SDK，则重试策略已内置并处于活动状态。 应用程序将重新连接，而不会对应用程序/工作流产生严重影响。


## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)
