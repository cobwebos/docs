---
title: Azure 数据工厂中的自承载集成运行时疑难解答
description: 了解如何对 Azure 数据工厂中的自承载集成运行时问题进行故障排除。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907277"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>自承载集成运行时故障排除

本文探讨了 Azure 数据工厂中自承载集成运行时的常见故障排除方法。

## <a name="common-errors-and-resolutions"></a>常见错误和解决方法

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>错误消息：自承载集成运行时无法连接到云服务。

- **症状**： 

    ![自承载 IR 连接问题](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **原因**：自承载集成运行时无法连接到数据工厂服务（后端）。 通常是由于防火墙中的网络设置导致的。

- **解决方法**： 

    1. 检查 windows 服务 "Integration Runtime 服务" 是否正在运行。
    
        ![自承载 IR 服务运行状态](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. 如果 [1] 中所示的 windows 服务正在运行，请根据需要执行以下说明：

        1. 如果未在自承载集成运行时上配置 "proxy" （默认设置为 "无代理配置"），请在安装了自承载集成运行时的计算机上运行以下 PowerShell 命令： 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > 服务 URL 根据数据工厂位置的不同而异。 可以在 ADF UI > 连接-> Integration runtime-> 编辑自托管 IR > 节点-> 查看服务 Url 下找到服务 URL。
            
            下面是预期的响应：
            
            ![Powershell 命令响应](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            如果响应不同，请根据需要执行以下说明：
            
            * 如果收到错误消息 "无法解析远程名称"，则 DNS 出现问题。 请与网络团队联系，以解决 DNS 解析问题！ 
            * 如果收到错误 "ssl/tls 证书不受信任"，请检查计算机上是否信任 "https://wu2.frontend.clouddatahub.net/" 的证书，使用证书管理器安装公共证书，该证书应缓解此问题。
            * 检查 Windows > 事件查看器（日志）-> 的应用程序和服务日志-> Integration Runtime 以了解是否存在任何故障，这通常是由 DNS、防火墙规则和公司的网络设置（Forcedly 关闭连接）引起的。 对于此问题，请与网络小组合作进行进一步的故障排除，因为每个公司都自定义了网络设置。

        2. 如果在自承载集成运行时上配置了 "proxy"，请验证代理服务器是否能够访问我们的服务终结点。 有关示例命令，请参阅[此](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)示例。    
                
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
            > * 检查代理服务器是否需要允许列表。 如果是这样，请将[这些域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)列入白名单。
            > * 在代理服务器上检查 "wu2.frontend.clouddatahub.net/" 的 TLS/SSL 证书是否受信任。
            > * 如果你使用的是代理中的 active directory 身份验证，则将服务帐户更改为可访问代理的用户帐户 "Integration Runtime 服务"。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>错误消息：自承载集成运行时节点/逻辑 SHIR 处于非活动/"正在运行（受限）" 状态

- **原因**：你可能会看到处于非活动状态的自承载 IR 节点，如以下屏幕截图中所示：

    ![不活动的自承载 IR 节点](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    如果节点不能相互通信，则会发生这种情况。 

- **解决方法**： 

    登录到节点托管 VM，并在 "应用程序和服务日志->" Integration Runtime 下打开 "事件视图"，筛选所有错误日志。 

     1. 如果错误日志包含： 
    
        **错误日志**： System.servicemodel.endpointnotfoundexception：无法连接到 net.tcp：//xxxxxxx.bwld.com： 8060/ExternalService/WorkerManager。 连接尝试持续时间为00：00：00.9940994。 TCP 错误代码10061：无法建立连接，因为目标计算机主动拒绝了它10.2.4.10：8060。  ---> SocketException：无法建立连接，因为目标计算机主动拒绝10.2.4.10：8060
    
           在系统 DoConnect （终结点 endPointSnapshot，SocketAddress socketAddress）
           
           在系统 .Net. Socket （终结点 remoteEP）上
           
           在 SocketConnectionInitiator （Uri uri，TimeSpan 超时）
    
        **解决方案：** 启动命令行： telnet 10.2.4.10 8060
        
        如果出现以下错误，请与 IT 专家联系，以获取解决此问题的帮助。 成功登录后，如果你仍遇到 IR 节点状态问题，请联系 Microsoft 支持部门。
        
        ![命令行错误](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. 如果错误日志包含：
     
        **错误日志：** 无法连接到 worker 管理器： net.tcp：//xxxxxx： 8060/ExternalService/对于主机 azranlcir01r1，不存在 DNS 条目。 此主机不存在已知异常详细信息： System.servicemodel.endpointnotfoundexception：主机 xxxxx 不存在 DNS 条目。 ---> SocketException：在系统 GetAddrInfo （字符串名称）处，在系统（string）上找不到此类主机。 System.net.dns.gethostentry 上的系统 includeIPv6 （string hostNameOrAddress）在 DnsCache （Uri uri）---内部异常堆栈跟踪的末尾---服务器堆栈跟踪：在 DnsCache （Uri uri）上进行分析。 
    
        **解决方案：** 以下两项操作之一可帮助解决此问题：
         1. 将所有节点置于同一域中。
         2. 在所有托管 VM 的 hosts 文件中，将 IP 添加到主机映射。


## <a name="next-steps"></a>后续步骤

有关故障排除的详细信息，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)
