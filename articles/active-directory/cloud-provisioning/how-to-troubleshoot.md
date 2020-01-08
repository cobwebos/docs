---
title: Azure AD Connect 云预配故障排除
description: 本文介绍如何排查云设置代理可能出现的问题。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549479"
---
# <a name="cloud-provisioning-troubleshooting"></a>云预配故障排除

云预配涉及许多不同的功能，并具有许多不同的依赖关系。 这种广泛的作用域可以带来各种问题。 本文将帮助你解决这些问题。 它为你提供了重点关注的典型领域、如何收集其他信息，以及可用于跟踪问题的各种技术。


## <a name="common-troubleshooting-areas"></a>常见疑难解答区域

|名称|Description|
|-----|-----|
|[代理问题](#agent-problems)|验证是否正确安装了代理，以及该代理是否与 Azure Active Directory （Azure AD）通信。|
|[对象同步问题](#object-synchronization-problems)|使用预配日志排查对象同步问题。|
|[预配隔离问题](#provisioning-quarantined-problems)|了解预配隔离问题及其修复方法。|


## <a name="agent-problems"></a>代理问题
你需要在代理中验证的一些首要事项是：

-  是否安装了？
-  代理是否在本地运行？
-  代理在门户中吗？
-  代理是否标记为正常？

可以在运行代理的 Azure 门户和本地服务器上验证这些项。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证

若要验证代理是否已由 Azure 查看并且运行正常，请执行以下步骤。

1. 登录到 Azure 门户。
1. 在左侧，选择 " **Azure Active Directory** > **Azure AD Connect**"。 在中心选择 "**管理预配（预览版）** "。
1. 在 " **Azure AD 预配（预览版）** " 屏幕上，选择 "**查看所有代理**"。

   ![查看所有代理](media/how-to-install/install7.png)</br>
 
1. 在 "**本地预配代理**" 屏幕上，可以看到已安装的代理。 验证相关代理是否存在并标记为 "*正常*"。

   ![本地设置代理屏幕](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>验证端口

若要验证 Azure 是否正在侦听端口443，以及代理是否可以与其通信，请使用以下工具：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试验证代理是否可以通过端口443与 Azure 通信。 打开浏览器，并从安装了代理的服务器中转到上一个 URL。

![验证端口可访问性](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本地服务器上

若要验证代理是否正在运行，请执行以下步骤。

1. 在安装了代理的服务器上，通过导航到该服务器或通过转到**开始** > **运行** > **services.msc**来打开**服务**。
1. 在 "**服务**" 下，确保**Microsoft Azure AD 连接代理更新**程序和**Microsoft Azure AD 连接设置代理**存在并且其状态为 "*正在运行*"。

   ![服务屏幕](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>常见代理安装问题

以下部分介绍了一些常见的代理安装问题和典型解决方案。

#### <a name="agent-failed-to-start"></a>代理启动失败

你可能会收到一条错误消息，指出：

**无法启动服务 "Microsoft Azure AD 连接设置代理"。验证您是否有足够的权限来启动系统服务。** 

此问题通常由阻止将权限应用于安装程序创建的本地 NT 服务登录帐户的组策略（NT SERVICE\AADConnectProvisioningAgent）引起的。 这些权限是启动服务所必需的。

若要解决此问题，请执行以下步骤。

1. 使用管理员帐户登录到服务器。
1. 通过导航到服务或通过转到**开始** > **运行** > **services.msc**来打开**服务**。
1. 在 "**服务**" 下，双击 " **Microsoft Azure AD 连接设置代理**"。
1. 在 "**登录**" 选项卡上，将**此帐户**更改为域管理员。然后重新启动该服务。 

   ![登录选项卡](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理超时或证书无效

尝试注册代理时，可能会收到以下错误消息。

![超时错误消息](media/how-to-troubleshoot/troubleshoot4.png)

此问题通常是由于代理无法连接到混合标识服务并且需要你配置 HTTP 代理所致。 若要解决此问题，请配置出站代理。 

预配代理支持使用出站代理。 可以通过编辑*C:\Program Files\Microsoft Azure AD Connect 预配 Agent\AADConnectProvisioningAgent.exe.config*代理配置文件来配置该配置文件。将以下行添加到文件末尾，紧靠在结束 `</configuration>` 标记之前。
将变量 `[proxy-server]` 和 `[proxy-port]` 替换为代理服务器名称和端口值。

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

安装云设置代理时，可能会收到一条错误消息。

此问题通常是由于本地 PowerShell 执行策略无法执行 PowerShell 注册脚本导致的。

若要解决此问题，请更改服务器上的 PowerShell 执行策略。 需要将计算机和用户策略设置为*未定义*或*RemoteSigned*。 如果将其设置为 "*无限制*"，则会看到此错误。 有关详细信息，请参阅[PowerShell 执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)。 

### <a name="log-files"></a>日志文件

默认情况下，代理发出最少的错误消息和堆栈跟踪信息。 可以在*C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect 预配 Agent\Trace*文件夹中找到这些跟踪日志。

若要收集用于排查代理相关问题的其他详细信息，请执行以下步骤。

1. 停止服务**Microsoft Azure AD 连接设置代理**。
1. 创建原始配置文件的副本： *C:\Program Files\Microsoft Azure AD Connect 预配 Agent\AADConnectProvisioningAgent.exe.config*。
1. 将现有 `<system.diagnostics>` 部分替换为以下内容，并将所有跟踪消息都发送到文件*ProvAgentTrace*。

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
1. 启动服务**Microsoft Azure AD 连接设置代理**。
1. 使用以下命令来结尾文件并调试问题。 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>对象同步问题

以下部分包含有关如何对对象同步进行故障排除的信息。

### <a name="provisioning-logs"></a>“预配”日志

在 Azure 门户中，设置日志可用于帮助跟踪对象同步问题并排除故障。 若要查看日志，请选择 "**日志**"。

![“日志”按钮](media/how-to-troubleshoot/log1.png)

预配日志提供了大量有关在本地 Active Directory 环境与 Azure 之间同步的对象状态的信息。

![预配日志屏幕](media/how-to-troubleshoot/log2.png)

您可以使用页面顶部的下拉框在特定问题（如日期）中将视图筛选为零。 双击单个事件可查看其他信息。

!["设置日志" 下拉框信息](media/how-to-troubleshoot/log3.png)

此信息提供了详细步骤以及出现同步问题的位置。 通过这种方式，您可以查明问题的确切位置。


## <a name="provisioning-quarantined-problems"></a>预配隔离问题

云预配监视配置的运行状况，并将不正常的对象置于隔离状态。 如果对目标系统进行的大部分或全部调用由于错误（例如，无效的管理员凭据）而失败，则设置作业会标记为隔离。

![隔离状态](media/how-to-troubleshoot/quarantine1.png)

通过选择状态，可以看到有关隔离的其他信息。 你还可以获取错误代码和消息。

![隔离状态信息](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>解析隔离

- 使用 Azure 门户重启预配作业。 在 "代理配置" 页上，选择 "**重新启动设置**"。

  ![重新启动预配](media/how-to-troubleshoot/quarantine3.png)

- 使用 Microsoft Graph[重启预配作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 你可以完全控制重新启动的内容。 你可以选择清除以下内容：
  - Escrows，用于重新启动向隔离状态累算的保管计数器。
  - 隔离：从隔离区中删除应用程序。
  - 水印. 
  
  使用以下请求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)



