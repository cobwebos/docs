---
title: Azure AD Connect 云预配故障排除
description: 本文档介绍如何排查云预配代理可能会出现的问题。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794232"
---
# <a name="cloud-provisioning-troubleshooting"></a>云预配故障排除

云预配涉及许多不同的功能，并具有许多不同的依赖关系。  当然，这可能会导致各种问题。  本文档旨在帮助你开始排查这些问题。  本文档将介绍你应重点关注的典型领域、如何收集其他信息以及可用于跟踪问题的各种技术。  


## <a name="common-troubleshooting-areas"></a>常见疑难解答区域

|名称|描述|
|-----|-----|
|[代理问题](#agent-issues)|验证代理是否已正确安装，并且是否正在与 Azure AD 通信。|
|[对象同步问题](#object-synchronization-issues)|使用预配日志来排查对象同步问题。|
|[预配隔离问题](#provisioning-quarantined-issues)|了解预配隔离问题及其修复方法。|


## <a name="agent-issues"></a>代理问题
你需要在代理中验证的一些首要事项是：


-  是否安装了？
-  代理是否在本地运行？
-  代理在门户中吗？
-  代理是否标记为正常？  

可以在运行代理的 Azure 门户和本地服务器上验证这些项。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证

若要验证代理是否正在由 Azure 查看并且运行正常，请执行以下步骤：

1. 登录到 Azure 门户。
2. 在左侧选择 " **Azure Active Directory**"，单击 " **Azure AD Connect** "，然后在中心选择 "**管理预配（预览版）** "。
3.  在 " **Azure AD 预配（预览版）** " 屏幕上，单击 "**查看所有代理**"。
 ![Azure AD 预配](media/how-to-install/install7.png)</br>
 
4. 在 "**本地预配代理" 屏幕**上，你将看到已安装的代理。  验证相关代理是否存在并标记为 "**正常**"。
 ![预配代理](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>验证端口

若要验证 Azure 是否正在侦听端口443，以及代理是否可以与其通信，可以使用以下工具：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试将验证代理是否能够通过端口443与 Azure 通信。  打开浏览器，并从安装了代理的服务器导航到上述 URL。
 ![服务](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本地服务器上

若要验证代理是否正在运行，请执行以下步骤：

1.  在安装了代理的服务器上，通过导航到该服务器或通过转到 "开始/运行"/"服务" 打开**服务**。
2.  在 "**服务**" 下，确保**Microsoft Azure AD 连接代理更新**程序和**Microsoft Azure AD 连接设置代理**存在并且状态为 "**正在运行**"。
 ![服务](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>常见代理安装问题

下面是一些常见的代理安装问题以及典型的解决方法。

#### <a name="agent-failed-to-start"></a>代理启动失败

如果收到一条错误消息，指出：

**无法启动服务 "Microsfoft Azure AD Connect 预配代理"。 验证您是否有足够的权限来启动系统服务。** 

这通常是由阻止权限应用于安装程序创建的本地 NT 服务 "登录帐户" 的组策略（NT SERVICE\AADConnectProvisioningAgent）引起的，这些权限是启动服务所必需的。

若要解决此问题，请使用以下步骤：

1.  使用管理员帐户登录到服务器
2.  通过导航到服务或转到 "开始/运行"/"服务" 打开**服务**。
3.  双击 "**服务**" **Microsoft Azure AD 连接设置代理**
4. 在 "" 选项卡上，将 "登录帐户" 更改为域管理员并重新启动该服务。 

 ![服务](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理超时或证书无效

如果尝试注册代理，可能会收到以下错误。

 ![服务](media/how-to-troubleshoot/troubleshoot4.png)

这通常是由于代理无法连接到混合标识服务并且需要配置 HTTP 代理而导致的。  若要解决此问题，请配置出站代理。 

预配代理支持使用出站代理。 可以通过编辑**C:\Program Files\Microsoft Azure AD Connect 预配 Agent\AADConnectProvisioningAgent.exe.config**代理配置文件来配置该配置文件。将以下行添加到该文件的末尾，紧靠在结束 `</configuration>` 标记之前。
将变量 [proxy-server] 和 [proxy-port] 替换为代理服务器名称和端口值。

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

安装云设置代理时，可能会看到以下错误。

这通常是因为本地 PowerShell 执行策略导致代理无法执行 PowerShell 注册脚本。

若要解决此问题，请更改服务器上的 PowerShell 执行策略。 需要将计算机和用户策略设置为 "未定义" 或 "RemoteSigned"。 如果为 "不受限制"，则会看到此错误。  有关详细信息，请参阅[PowerShell 执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)。 

### <a name="log-files"></a>日志文件

默认情况下，代理发出非常少的错误消息和堆栈跟踪信息。 可以在以下文件夹中找到这些跟踪日志： **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect 预配 Agent\Trace**

使用以下步骤来收集用于排查代理相关问题的其他详细信息。

1. 停止服务**Microsoft Azure AD 连接设置代理**
2. 创建原始配置文件的副本： **C:\Program Files\Microsoft Azure AD Connect 预配 Agent\AADConnectProvisioningAgent.exe.config**
3. 将现有 < "诊断 >" 部分替换为以下内容，并将所有跟踪消息都发送到**ProvAgentTrace 文件。**

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
4. 启动服务**Microsoft Azure AD 连接设置代理**
5. 您可以使用以下命令来结尾文件和调试问题： 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>对象同步问题

以下部分包含有关如何对对象同步进行故障排除的信息。

### <a name="provisioning-logs"></a>“预配”日志

在 Azure 门户中，设置日志可用于帮助跟踪对象同步问题并排除故障。  若要查看日志，请选择 "**日志**"。
 ![预配日志](media/how-to-troubleshoot/log1.png)

预配日志提供了大量有关在本地 AD 环境与 Azure 之间同步的对象状态的信息。

 ![“预配”日志](media/how-to-troubleshoot/log2.png)

您可以使用页面顶部的下拉顺序在特定问题、日期等中将视图筛选为零。 双击单个事件将提供其他详细信息。

 ![“预配”日志](media/how-to-troubleshoot/log3.png)

此信息将提供详细的步骤和发生同步问题的位置。  这样，你就可以从零开始，找出问题的确切位置。


## <a name="provisioning-quarantined-issues"></a>预配隔离问题

云预配监视配置的运行状况，并将不正常的对象置于 "隔离" 状态。 如果对目标系统进行的大部分或全部调用由于错误（例如无效的管理员凭据）而失败，则设置作业会标记为隔离。

 ![隔离](media/how-to-troubleshoot/quarantine1.png)

单击状态即可查看有关隔离的其他信息。  你还可以获取错误代码和消息。

 ![隔离](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>解析隔离

- 使用 Azure 门户重启预配作业。 在 "代理配置" 页上，选择 "**重新启动设置**"。

  ![隔离](media/how-to-troubleshoot/quarantine3.png)

- 使用 Microsoft Graph[重启预配作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 你可以完全控制重新启动的内容。 你可以选择清除 escrows （以重新启动向隔离状态累算的保管计数器）、清除隔离（从隔离区删除应用程序）或清除水印。 使用以下请求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)



