---
title: 在 Azure 数据工厂中排查自承载集成运行时问题
description: 了解如何在 Azure 数据工厂中排查自承载集成运行时问题。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/19/2020
ms.author: abnarain
ms.openlocfilehash: 521756081db938e749849e6f3630dbd60700d24f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023804"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>排查自承载集成运行时问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨 Azure 数据工厂中的自承载集成运行时的常用故障排除方法。

## <a name="gather-self-hosted-integration-runtime-logs-from-azure-data-factory"></a>从 Azure 数据工厂收集自承载集成运行时日志

对于在自承载 IR/共享 IR 上运行的失败的活动，Azure 数据工厂支持查看和上载错误日志。 你可以执行以下步骤来获取错误报告 ID，然后输入报表 ID 以查找相关的已知问题。

1. "中转到**活动运行**" 页。

1. 在 "**错误**" 列下，单击 "下一按钮"。

    !["活动运行" 页](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 你会看到失败的活动运行的相关日志。 单击 "**发送日志**" 按钮以获得进一步帮助。

    ![发送日志](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 您可以选择要发送的日志。 对于*自承载 ir*，你可以上载与失败的活动或自承载 ir 节点上的所有日志相关的日志。 对于*共享 IR*，只能上载与失败的活动相关的日志。

    ![选择日志](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 当上载日志时，如果需要进一步的帮助来解决此问题，请保留报表 ID 记录。

    ![上传日志](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 将对所有联机的自承载 IR 实例执行日志查看和上载请求。 请确保所有自承载 IR 实例都处于联机状态，以防丢失任何日志。 


## <a name="common-errors-and-resolutions"></a>常见错误和解决方案

### <a name="error-message"></a>错误消息： 

`Self-hosted integration runtime can't connect to cloud service`

![自承载 IR 连接问题](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自承载集成运行时无法连接到数据工厂服务（后端）。 此问题通常是防火墙中的网络设置导致的。

#### <a name="resolution"></a>解决方法

1. 检查集成运行时服务是否正在运行。
    
   ![自承载 IR 服务运行状态](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果服务正在运行，请转到步骤 3。

1. 如果自承载集成运行时上没有配置代理（这是默认设置），请在安装了自承载集成运行时的计算机上运行以下 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 服务 URL 可能会有所不同，具体取决于数据工厂位置。 可以在“ADF UI” > “连接” > “集成运行时” > “编辑自承载 IR” > “节点” > “查看服务 URL”下找到服务 URL。
            
    下面是预期的响应：
            
    ![PowerShell 命令响应](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 如果未收到预期的响应，请根据情况使用以下方法之一：
            
    * 如果收到“无法解析远程名称”消息，则表明存在域名系统 (DNS) 问题。 请与网络团队联系以解决此问题。
    * 如果收到“ssl/tls 证书不受信任”消息，请检查 https://wu2.frontend.clouddatahub.net/ 的证书在计算机上是否受信任，然后使用证书管理器安装公共证书。 此操作应该会缓解此问题。
    * 转到“Windows” > “事件查看器(日志)” > “应用程序和服务日志” > “Integration Runtime”，检查是否存在由 DNS、防火墙规则或公司网络设置导致的故障。 （如果发现此类故障，请强行关闭连接。）由于每个公司都有自定义的网络设置，因此请与网络小组联系以排查这些问题。

1. 如果在自承载集成运行时上配置了“代理”，请验证代理服务器是否可以访问服务终结点。 有关示例命令，请参阅 [PowerShell, web requests, and proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)（PowerShell、Web 请求和代理）。    
                
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
            
![PowerShell 命令响应 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 代理注意事项：
> *    检查是否需要将代理服务器放在“安全接收方”列表中。 如果是，请确保“安全接收方”列表中包含[这些域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)。
> *    检查 TLS/SSL 证书“wu2.frontend.clouddatahub.net/”在代理服务器上是否受信任。
> *    如果在代理上使用 Active Directory 身份验证，请将服务帐户更改为能够以“Integration Runtime 服务”身份访问代理的用户帐户。

### <a name="error-message"></a>错误消息： 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>原因 

自承载集成运行时节点的状态可能为“非活动”，如以下屏幕截图所示：

![非活动的自承载 IR 节点](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

当节点无法相互通信时，会出现此行为。

#### <a name="resolution"></a>解决方法

1. 登录到节点托管的 VM。 在“应用程序和服务日志” > “Integration Runtime”下，打开事件查看器并筛选所有错误日志。

1. 检查错误日志是否包含以下错误： 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 如果看到此错误，请在命令行上运行以下命令： 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 如果收到以下错误，请与 IT 部门联系以获得解决此问题的帮助。 成功 telnet 后，如果集成运行时节点状态仍存在问题，请联系 Microsoft 支持部门。
        
   ![命令行错误](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 检查错误日志是否包含以下错误：

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 若要解决此问题，请尝试下面的一种或两种方法：
    - 将所有节点置于同一域中。
    - 在所有托管 VM 的主机文件中添加 IP 到主机映射。


## <a name="troubleshoot-connectivity-issue"></a>排查连接问题

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>排查自承载 IR 与数据工厂或自承载 IR 与数据源/接收器之间的连接问题

若要解决网络连接问题，您应该知道如何[收集网络跟踪](#how-to-collect-netmon-trace)，了解如何使用它，并在从自承载 IR 在真实情况下应用 netmon 工具之前[分析 netmon 跟踪](#how-to-analyze-netmon-trace)。

有时，在自承载 IR 和数据工厂之间排查连接问题（例如下面的问题）时： 

![HTTP 请求失败](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

或者，在自承载 IR 与数据源/接收器之间，会遇到以下错误：

**错误消息：**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**错误消息：**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**解决方法：** 遇到以上问题时，请参考以下说明进一步解决问题：

获取 netmon 跟踪并进行进一步分析。
- 首先，可以将筛选器设置为查看从服务器到客户端的任何重置。 在下面的示例中，可以看到服务器端为数据工厂服务器。

    ![数据工厂服务器](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- 当你获取重置包时，你可以通过以下 TCP 查找此会话。

    ![查找对话](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 然后，可以通过删除筛选器来获取客户端和数据工厂服务器之间的转换。

    ![获取会话](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 根据收集的 netmon 跟踪，我们可以判断 TTL （TimeToLive）总数为64。 根据[本文](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)中提到的**默认 TTL 和跃点限制值**（如下所示），我们可以看到它是用于重置包并导致断开连接的 Linux 系统。

    默认 TTL 和跃点限制值在不同的操作系统之间有所不同，以下是一些默认值：
    - Linux 内核2.4 （大约2001）： 255 TCP、UDP 和 ICMP
    - Linux 内核4.10 （2015）： 64 TCP、UDP 和 ICMP
    - Windows XP （2001）：128（针对 TCP、UDP 和 ICMP）
    - Windows 10 （2015）：128（针对 TCP、UDP 和 ICMP）
    - Windows Server 2008：128（针对 TCP、UDP 和 ICMP）
    - Windows Server 2019 （2018）： 128 TCP、UDP 和 ICMP
    - macOS （2001）：64（针对 TCP、UDP 和 ICMP）

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    但是，在上面的示例中，它显示为61，而不是64，因为当网络包到达目标时，它需要通过不同的跃点，如路由器/网络设备。 路由器/网络设备的数量将在最终 TTL 中扣减。
    在这种情况下，我们可以看到，可以从 Linux 系统通过 TTL 64 发送重置。

- 需要检查自托管 IR 的第四个跃点，确认重置设备可能来自何处。
 
    *带有 TTL 64 的 Linux 系统 A 中的网络包-> B TTL 64 减 1 = 63-> C TTL 63 减 1 = 62-> TTL 62 减 1 = 61 自承载 IR*

- 在理想情况下，TTL 将为128，这意味着 Windows 系统正在运行我们的数据工厂。 如以下示例中所示， *128 – 107 = 21 个跃点*，这意味着在 TCP 3 握手期间，将包的21个跃点从数据工厂发送到自承载 IR。
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    因此，你需要与网络团队合作，以检查第四个跃点是自承载 IR 的跃点。 如果它是作为 Linux 系统的防火墙，请在 TCP 3 握手后查看有关设备重置包的原因的任何日志。 但是，如果不确定进行调查的位置，请尝试在有问题的时间内从自承载 IR 和防火墙一起获取 netmon 跟踪，找出可能会重置此包并导致断开连接的设备。 在这种情况下，还需要让网络团队继续发展。

### <a name="how-to-collect-netmon-trace"></a>如何收集 netmon 跟踪

1.  从此[网站](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)下载 Netmon 工具，并将其安装在服务器计算机（有问题的任何服务器）和客户端（如自承载 IR）上。

2.  在以下路径中创建一个文件夹，例如： *D:\netmon*。 请确保其具有足够的空间来保存日志。

3.  捕获 IP 和端口信息。 
    1. 启动 CMD 提示符。
    2. 选择 "以管理员身份运行" 并运行以下命令：
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  捕获 Netmon 跟踪（网络包）。
    1. 启动 CMD 提示符。
    2. 选择 "以管理员身份运行" 并运行以下命令：
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. 可以使用三个不同的命令来捕获网络页面：
        - 选项 A： RoundRobin File 命令（这将只捕获一个文件，并将覆盖旧日志）。

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - 选项 B：链式文件命令（如果达到 200 MB，将创建新文件）。
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - 选项 C：计划的文件命令。

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  按**Ctrl + C**停止捕获 Netmon 跟踪。
 
> [!NOTE]
> 如果只能在客户端计算机上收集 netmon 跟踪，请获取服务器 ip 地址以帮助分析跟踪。

### <a name="how-to-analyze-netmon-trace"></a>如何分析 netmon 跟踪

当你尝试收集上述 netmon 跟踪的 telnet **8.8.8.8 888**时，你将看到下面的跟踪：

![netmon 跟踪1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon 跟踪2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

这意味着你不能基于端口**888**与**8.8.8.8**服务器端建立 TCP 连接，因此你可以在其中看到两个**SynReTransmit**附加包。 由于源**HOST2**无法与第一个包的**8.8.8.8**建立连接，因此它将继续进行连接。

> [!TIP]
> - 你可以单击 "**加载筛选器**  ->  **标准筛选器**" 来  ->  **寻址**  ->  **IPv4 地址**。
> - 输入**Address = = 8.8.8.8**作为筛选器，然后单击 "**应用**"。 之后，你将只能看到从本地计算机到目标**8.8.8.8**的通信。

![筛选地址1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![筛选器地址2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

下面的示例演示了一个很好的方案。 

- 如果 Telnet **8.8.8.8 53**在没有任何问题的情况下正常工作，则可以看到 tcp 3 握手发生，然后会话结束时将使用 TCP 4 握手。

    ![典型方案示例1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![典型方案示例2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 根据上述 TCP 3 握手，可以看到以下工作流：

    ![TCP 3 握手工作流](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 完成会话及其工作流的 TCP 4 握手将如下所示：

    ![TCP 4 握手](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 握手工作流](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 问题页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [映射数据流性能指南](concepts-data-flow-performance.md)
