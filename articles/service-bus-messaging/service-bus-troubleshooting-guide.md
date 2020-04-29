---
title: Azure 服务总线故障排除指南 | Microsoft Docs
description: 本文提供了 Azure 服务总线消息传送异常以及发生异常时建议采取的措施的列表。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887770"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure 服务总线故障排除指南
本文提供了有关使用 Azure 服务总线时可能会遇到的一些问题的疑难解答提示和建议。 

## <a name="connectivity-certificate-or-timeout-issues"></a>连接性、证书或超时问题
以下步骤可帮助排除 *.servicebus.windows.net 下所有服务的连接性/证书/超时问题。 

- 浏览到或[wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`。 这可帮助检查是否存在 IP 筛选或虚拟网络或证书链问题（使用 java SDK 时最常见）。

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
- 运行以下命令，检查防火墙是否阻止了任何端口。 所用的端口为 443 (HTTPS)、5671 (AMQP) 和 9354 (Net Messaging/SBMP)。 根据使用的库，还会使用其他端口。 下面是用于检查 5671 端口是否被阻止的示例命令。 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    在 Linux 上：

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 出现间歇性连接问题时，请运行以下命令，检查是否存在任何丢弃的数据包。 此命令会尝试通过服务每隔 1 秒建立 25 个不同的 TCP 连接。 然后，可以检查其中有多少成功/失败，还可以查看 TCP 连接延迟。 你可以从`psping` [此处](/sysinternals/downloads/psping)下载该工具。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
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

## <a name="unauthorized-access-send-claims-are-required"></a>未经授权的访问：需要发送声明
尝试使用具有 send 权限的用户分配的托管标识从本地计算机上的 Visual Studio 访问服务总线主题时，可能会看到此错误。

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

若要解决此错误，请安装[microsoft.azure.services.appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)库。  有关详细信息，请参阅[本地开发身份验证](..\key-vault\service-to-service-authentication.md#local-development-authentication)。 

若要了解如何向角色分配权限，请参阅[使用 Azure Active Directory 对托管标识进行身份验证，以访问 Azure 服务总线资源](service-bus-managed-service-identity.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [Azure 资源管理器例外](service-bus-resource-manager-exceptions.md)。 它列出了使用 Azure 资源管理器（通过模板或直接调用）与 Azure 服务总线交互时生成的异常。
- [消息传送异常](service-bus-messaging-exceptions.md)。 它提供 Azure 服务总线 .NET Framework 生成的异常列表。 

