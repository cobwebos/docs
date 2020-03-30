---
title: Azure AD 连接云预配故障排除
description: 本文介绍如何解决云预配代理可能出现的问题。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549479"
---
# <a name="cloud-provisioning-troubleshooting"></a>云配置故障排除

云资源调配涉及许多不同的事物，并且具有许多不同的依赖关系。 这种广泛的范围会引起各种问题。 本文可帮助您解决这些问题。 它介绍了您需要关注的典型领域、如何收集其他信息以及可用于跟踪问题的各种技术。


## <a name="common-troubleshooting-areas"></a>常见故障排除区域

|“属性”|描述|
|-----|-----|
|[代理问题](#agent-problems)|验证代理安装正确，以及代理是否与 Azure 活动目录 （Azure AD） 通信。|
|[对象同步问题](#object-synchronization-problems)|使用预配日志解决对象同步问题。|
|[预配隔离问题](#provisioning-quarantined-problems)|了解预配隔离问题以及如何解决这些问题。|


## <a name="agent-problems"></a>代理问题
要与代理验证的一些第一件事是：

-  是否已安装？
-  代理是否在本地运行？
-  代理是否位于门户中？
-  代理是否标记为正常？

这些项目可以在 Azure 门户和运行代理的本地服务器上进行验证。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证

要验证 Azure 是否看到代理并处于正常状态，请按照以下步骤操作。

1. 登录到 Azure 门户。  
1. 在左侧，选择**Azure 活动目录** > **Azure AD 连接**。 在中心中，选择 **"管理预配（预览）"。**
1. 在**Azure AD 预配（预览）** 屏幕上，选择 **"查看所有代理**"。

   ![查看所有代理](media/how-to-install/install7.png)</br>
 
1. 在**本地预配代理**屏幕上，可以看到已安装的代理。 验证有问题的代理是否在那里，并且标记为 *"正常*"。

   ![本地预配代理屏幕](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>验证端口

要验证 Azure 正在侦听端口 443，并且代理是否可以与它通信，请使用以下工具：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试验证代理是否可以通过端口 443 与 Azure 通信。 打开浏览器，然后从安装代理的服务器转到以前的 URL。

![端口可到达性验证](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本地服务器上

要验证代理是否正在运行，请按照以下步骤操作。

1. 在安装了代理的服务器上，通过导航到**它**或访问**启动** > **运行** > **服务.msc**打开服务。
1. 在 **"服务**"下，请确保**Microsoft Azure AD 连接代理更新程序**以及**Microsoft Azure AD 连接预配代理**已存在，并且其状态正在*运行*。

   ![服务屏幕](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>常见的代理安装问题

以下各节介绍一些常见的代理安装问题和典型解决方法。

#### <a name="agent-failed-to-start"></a>代理无法启动

您可能会收到一条错误消息，指出：

**服务"微软 Azure AD 连接预配代理"无法启动。验证您是否有足够的权限来启动系统服务。** 

此问题通常是由组策略引起的，该策略阻止将权限应用于安装程序创建的本地 NT 服务登录帐户（NT SERVICE_AADConnect预配代理）。 启动服务需要这些权限。

要解决此问题，请按照以下步骤操作。

1. 使用管理员帐户登录到服务器。
1. 通过导航到服务或访问**启动** > **运行** > **服务.msc**来打开**服务**。
1. 在 **"服务**"下，双击**微软 Azure AD 连接预配代理**。
1. 在"**登录**"选项卡上，**将此帐户**更改为域管理员。然后重新启动服务。 

   ![登录选项卡](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理超时或证书无效

当您尝试注册代理时，您可能会收到以下错误消息。

![超时错误消息](media/how-to-troubleshoot/troubleshoot4.png)

此问题通常是由于代理无法连接到混合标识服务而引起的，需要您配置 HTTP 代理。 要解决此问题，请配置出站代理。 

预配代理支持使用出站代理。 您可以通过编辑代理配置文件*C：\程序文件\微软 Azure AD 连接预配代理\AADConnect预配代理.exe.config*来配置它。在文件的结尾端向结束`</configuration>`标记之前添加以下行。
将变量`[proxy-server]`替换为`[proxy-port]`代理服务器名称和端口值。

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>代理注册失败，出现安全错误

安装云预配代理时可能会收到错误消息。

此问题通常是由于代理由于本地 PowerShell 执行策略而无法执行 PowerShell 注册脚本引起的。

要解决此问题，更改服务器上的 PowerShell 执行策略。 您需要将计算机和用户策略设置为 *"未定义*"或 *"远程签名*"。 如果它们设置为 *"无限制"，* 您将看到此错误。 有关详细信息，请参阅[PowerShell 执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)。 

### <a name="log-files"></a>日志文件

默认情况下，代理会发出最少的错误消息和堆栈跟踪信息。 您可以在文件夹*C：\程序数据\微软_Azure AD 连接预配代理_跟踪*中找到这些跟踪日志。

要收集有关排除代理相关问题的其他详细信息，请按照以下步骤操作。

1. 停止**服务微软 Azure AD 连接预配代理**。
1. 创建原始配置文件的副本 *：C：\程序文件\微软 Azure AD 连接预配代理_AADConnect预配代理.exe.config*。
1. 将现有`<system.diagnostics>`部分替换为以下内容，所有跟踪消息都将转到文件*ProvAgentTrace.log*。

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. 启动**服务微软 Azure AD 连接预配代理**。
1. 使用以下命令尾随文件和调试问题。 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>对象同步问题

以下部分包含有关对对象同步进行故障排除的信息。

### <a name="provisioning-logs"></a>“预配”日志

在 Azure 门户中，预配日志可用于帮助跟踪和排除对象同步问题。 要查看日志，请选择 **"日志**"。

![“日志”按钮](media/how-to-troubleshoot/log1.png)

预配日志提供有关本地活动目录环境和 Azure 之间正在同步的对象的状态的大量信息。

![预配日志屏幕](media/how-to-troubleshoot/log2.png)

您可以使用页面顶部的下拉框将视图筛选为对特定问题（如日期）的零。 双击单个事件以查看其他信息。

![预配日志下拉框信息](media/how-to-troubleshoot/log3.png)

此信息提供了详细的步骤以及同步问题发生的位置。 通过这种方式，您可以精确定位问题的确切位置。


## <a name="provisioning-quarantined-problems"></a>预配隔离问题

云预配监视配置的运行状况，并将不正常的对象置于隔离状态。 如果针对目标系统所做的大多数或所有调用始终由于错误（例如无效的管理员凭据）而失败，则预配作业将标记为隔离区。

![隔离状态](media/how-to-troubleshoot/quarantine1.png)

通过选择状态，您可以看到有关隔离的其他信息。 您还可以获取错误代码和消息。

![隔离状态信息](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>解决隔离

- 使用 Azure 门户重新启动预配作业。 在代理配置页上，选择 **"重新启动预配**"。

  ![重新启动预配](media/how-to-troubleshoot/quarantine3.png)

- 使用 Microsoft 图形[重新启动预配作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 您将完全控制重新启动的内容。 您可以选择清除：
  - 埃斯库，重新启动代管柜台，累积到隔离状态。
  - 隔离，从隔离区中删除应用程序。
  - 水印。 
  
  使用以下请求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)



