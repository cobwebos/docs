---
title: Azure 数据工厂中的自承载集成运行时疑难解答
description: 了解如何对 Azure 数据工厂中的自承载集成运行时问题进行故障排除。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439880"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>自承载集成运行时故障排除

本文探讨了 Azure 数据工厂中自承载集成运行时的常见故障排除方法。

## <a name="common-errors-and-resolutions"></a>常见错误和解决方法

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>错误消息：自承载集成运行时无法连接到云服务

![自承载 IR 连接问题](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自承载集成运行时无法连接到数据工厂服务（后端）。 此问题通常是由于防火墙中的网络设置导致的。

#### <a name="resolution"></a>分辨率

1. 检查集成运行时服务是否正在运行。
    
   ![自承载 IR 服务运行状态](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果服务正在运行，请继续执行步骤3。

1. 如果自承载集成运行时（这是默认设置）上没有配置代理，请在安装了自承载集成运行时的计算机上运行以下 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 服务 URL 可能会有所不同，具体取决于你的数据工厂位置。 可以在 " **ADF UI** > **连接**" > **集成运行时**"中找到服务 URL， > **编辑自托管 IR** > **节点** > **查看服务 url**。
            
    下面是预期的响应：
            
    ![PowerShell 命令响应](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 如果未收到预期的响应，请根据您的情况使用以下方法之一：
            
    * 如果收到 "无法解析远程名称" 消息，则表明存在域名系统（DNS）问题。 请与网络团队联系以解决此问题。
    * 如果收到 "ssl/tls 证书不受信任" 消息，请检查计算机上是否信任 https://wu2.frontend.clouddatahub.net/ 的证书，然后使用证书管理器安装公共证书。 此操作应缓解此问题。
    * 请参阅**Windows** > **事件查看器（日志）**  > **应用程序和服务日志** > **Integration Runtime**并检查由 DNS、防火墙规则或公司网络设置导致的任何故障。 （如果发现此类故障，请强行关闭连接。）由于每个公司都有自定义的网络设置，因此请与网络小组联系以解决这些问题。

1. 如果在自承载集成运行时上配置了 "proxy"，请验证代理服务器是否可以访问服务终结点。 有关示例命令，请参阅[PowerShell、web 请求和代理](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)。    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

下面是预期的响应：
            
![Powershell 命令响应2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 代理注意事项：
> * 检查是否需要将代理服务器放在 "安全收件人" 列表中。 如果是这样，请确保[这些域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)位于 "安全收件人" 列表中。
> * 检查 TLS/SSL 证书 "wu2.frontend.clouddatahub.net/" 是否在代理服务器上受信任。
> * 如果在代理上使用 Active Directory 身份验证，请将服务帐户更改为可作为 "Integration Runtime 服务" 访问该代理的用户帐户。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>错误消息：自承载集成运行时节点/逻辑 SHIR 处于非活动/"正在运行（受限）" 状态

#### <a name="cause"></a>原因 

自承载集成运行时节点可能具有**非活动**状态，如以下屏幕截图所示：

![不活动的自承载 IR 节点](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

当节点无法相互通信时，会出现此行为。

#### <a name="resolution"></a>分辨率

1. 登录到节点托管的 VM。 在 "**应用程序和服务日志** > **Integration Runtime**，打开事件查看器，并筛选所有错误日志。

1. 检查错误日志是否包含以下错误： 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.  Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
