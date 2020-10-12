---
title: 在 Azure 数据工厂中排查自承载集成运行时问题
description: 了解如何在 Azure 数据工厂中排查自承载集成运行时问题。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: abnarain
ms.openlocfilehash: 1a68263598cb2cba8cc0853f5dd1be7c62dc062e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069469"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>排查自承载集成运行时问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨 Azure 数据工厂中的自承载集成运行时的常用故障排除方法。

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>从 Azure 数据工厂收集自承载 IR 日志

对于在自承载 IR/共享 IR 上运行的失败的活动，Azure 数据工厂支持查看和上载错误日志。 你可以执行以下步骤来获取错误报告 ID，然后输入报表 ID 以查找相关的已知问题。

1. "中转到 **活动运行** " 页。

1. 在 " **错误** " 列下，单击 "下一按钮"。

    !["活动运行" 页](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 你会看到失败的活动运行的相关日志。 单击 " **发送日志** " 按钮以获得进一步帮助。

    ![发送日志](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 您可以选择要发送的日志。 对于 *自承载 ir*，你可以上载与失败的活动或自承载 ir 节点上的所有日志相关的日志。 对于 *共享 IR*，只能上载与失败的活动相关的日志。

    ![选择日志](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 当上载日志时，如果需要进一步的帮助来解决此问题，请保留报表 ID 记录。

    ![上传日志](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 将对所有联机的自承载 IR 实例执行日志查看和上载请求。 请确保所有自承载 IR 实例都处于联机状态，以防丢失任何日志。 


## <a name="self-hosted-ir-general-failure-or-error"></a>自承载 IR 一般性故障或错误

### <a name="tlsssl-certificate-issue"></a>TLS/SSL 证书问题

#### <a name="symptoms"></a>症状

尝试从“自承载 IR Configuration Manager” -> “从 intranet 执行远程访问”启用 TLS/SSL 证书（高级）时，选择 TLS/SSL 证书后将显示以下错误 ：

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

在上述情况下，用户使用的是以“microsoft.com”为最后一项的证书。

#### <a name="cause"></a>原因

这是 WCF 中的一个已知问题：WCF TLS/SSL 验证仅检查 SAN 中的最后一个 DNSName。 

#### <a name="resolution"></a>解决方法

Azure 数据工厂 v2 自承载 IR 支持通配符证书。 发生此问题的原因通常是 SSL 证书不正确。 SAN 中的最后一个 DNSName 应该为有效。 请按照以下步骤进行验证。 
1.  打开管理控制台，再次检查“证书详细信息”中的“使用者”和“使用者可选名称” 。 例如，在上面的示例中，“使用者可选名称”中的最后一项“DNS Name = microsoft.com.com”不合法。
2.  请联系证书颁发公司以删除错误的 DNS 名称。

### <a name="concurrent-jobs-limit-issue"></a>并发作业限制问题

#### <a name="symptoms"></a>症状

尝试从 Azure 数据工厂 UI 增加并发作业限制时，它会永久卡在“正在更新”。
并发作业的最大值已设置为 24，而你希望增加该值，使作业运行速度更快。 可输入的最小值为 3，可输入的最大值为 32。 你将该值从 24 增加到了 32，然后点击了“更新”按钮，在 UI 中，它在更新时卡住了，如下所示 。 刷新后，客户会看到值仍为 24，并且永远不会更新到 32。

![正在更新状态](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>原因

此设置存在限制，因为该值取决于计算机 logicCore 和内存，你只能将其调整为较小的值（如 24），然后查看结果。

> [!TIP] 
> - 若要详细了解逻辑核心计数是多少以及如何查找计算机的逻辑核心计数，请参阅[本文](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)。
> - 若要详细了解如何计算 math.log，请参阅[本文](https://www.rapidtables.com/calc/math/Log_Calculator.html)。


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>自承载 IR HA SSL 证书问题

#### <a name="symptoms"></a>症状

自承载 IR 工作节点已报告以下错误：

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>原因

当我们处理与 SSL/TLS 握手相关的事例时，可能会遇到一些与证书链验证相关的问题。 

#### <a name="resolution"></a>解决方法

- 下面是对 X.509 证书链生成失败进行故障排除的快速且直观的方法。
 
    1. 导出需要验证的证书。 转到“管理计算机证书”并找到要检查的证书，然后右键单击“所有任务” -> “导出” 。
    
        ![导出任务](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. 将导出的证书复制到客户端计算机。 
    3. 在客户端，使用 CMD 运行以下命令。 请确保已将下面的 \<certificate path> 和 \<output txt file path> 占位符替换为相关路径 。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        例如：

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 检查输出 txt 文件中是否有任何错误。 可在 txt 文件末尾找到错误摘要。

        例如： 

        ![错误摘要](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        如果如下所示，日志文件的末尾未出现任何错误，可认为证书链已在客户端计算机中成功生成。
        
        ![日志文件中没有错误](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- 如果在证书文件中配置了 AIA、CDP 和 OCSP， 则可通过更直观的方式对其进行检查。
 
    1. 可通过检查证书的详细信息来获取此信息。
    
        ![证书详细信息](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. 运行下列命令。 请确保已将 \<certificate path> 占位符替换为证书的相关路径。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. 然后 URL 检索工具将会打开。 可通过单击“检索”按钮来验证 AIA、CDP 和 OCSP 中的证书。

        ![检索按钮](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        如果来自 AIA 的证书为“已验证”，并且来自 CDP 或 OCSP 的证书为“已验证”，则证书链可以成功构建。

        如果在检索 AIA、CDP 时出现故障，请与网络团队合作，使客户端计算机可以连接到目标 URL。 如果能验证 http 路径或 ldap 路径，则足够了。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>自承载 IR 未能加载文件或程序集

#### <a name="symptoms"></a>症状

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
例如： 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>原因

如果使用进程监视器，可以看到以下结果：

[![进程监视器](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 可以设置筛选器，如以下屏幕截图所示。
> 它告诉我们 dll System.ValueTuple 不位于 GAC 相关文件夹中，不位于 C:\Program Files\Microsoft Integration Runtime\4.0\Gateway 中，也不位于 C:\Program Files\Microsoft Integration Runtime\4.0\Shared 文件夹中 。
> 基本上，它将依次从 GAC 文件夹、Shared 文件夹和 Gateway 文件夹加载 dll  。 因此可将 dll 放到任何有帮助的路径中。

![设置筛选器](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>解决方法

可以发现，System.ValueTuple.dll 位于 C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan 文件夹中。 将 System.ValueTuple.dll 复制到 C:\Program Files\Microsoft Integration Runtime\4.0\Gateway 文件夹中即可解决该问题。

你可以使用相同方法解决其他文件或程序集丢失问题。

#### <a name="more-information"></a>更多信息

System.ValueTuple.dll 是 .NET 行为，因此它位于 %windir%\Microsoft.NET\assembly 和 %windir%\assembly 下 。 

在下方的错误中，可以清楚地看到并不存在程序集 System.ValueTuple。 因此，应用程序尝试检查程序集 System.ValueTuple.dll 时会发生此类问题。
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
有关 GAC 的详细信息，请参阅[本文](https://docs.microsoft.com/dotnet/framework/app-domains/gac)。


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>如何审核自承载 IR 密钥丢失

#### <a name="symptoms"></a>症状

自承载集成运行时在没有密钥时突然进入脱机状态，事件日志中显示以下错误消息：`Authentication Key is not assigned yet`

![缺少身份验证密钥](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>原因

- 在门户中删除了自承载 IR 节点或逻辑自承载 IR。
- 完全卸载已完成。

#### <a name="resolution"></a>解决方法

如果上述两个原因均不适用，你可前往文件夹 %programdata%\Microsoft\Data Transfer\DataManagementGateway，并检查是否删除了名为 Configurations 的文件。 如果该文件已删除，请按照[此处](https://www.netwrix.com/how_to_detect_who_deleted_file.html)的说明审核谁删除了文件。

![检查 Configurations 文件](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>无法使用自承载 IR 桥接两个本地数据存储

#### <a name="symptoms"></a>症状

为源数据存储和目标数据存储创建自承载 IR 后，需要将这两个 IR 连接在一起以完成复制。 如果这两个数据存储是在不同的 VNET 中配置的，或者它们无法理解网关机制，你会遇到如下错误：在目标 IR 中找不到源驱动程序；源无法通过目标 IR 访问 。
 
#### <a name="cause"></a>原因

自承载 IR 设计为复制活动的中心节点，而不是需要为每个数据存储安装的客户端代理。
 
在上述情况下，应使用相同的 IR 为每个数据存储创建链接服务，并且 IR 应该能够通过网络访问这两个数据存储。 无论 IR 是否与源数据存储、目标数据存储或第三方计算机一起安装，如果两个链接服务是使用不同 IR 创建的，但用于同一复制活动，则将使用目标 IR，并且需要在目标 IR 计算机上为这两个数据存储安装驱动程序。

#### <a name="resolution"></a>解决方法

在目标 IR 上安装源和目标安装驱动程序，并确保它可访问源数据存储。
 
如果流量无法通过网络在两个数据存储间传递（例如它们在两个 VNET 中配置），即使安装了 IR，也不能在一个活动中完成复制。 在这种情况下，你可以使用两个 IR 创建两个复制活动，每个活动都在一个 VENT 中：1 个 IR 从数据存储 1 复制到 Azure Blob 存储，另一个从 Azure Blob 存储复制到数据存储 2。 这可以模拟以下要求：使用 IR 创建一个网桥来连接两个已断开连接的数据存储。


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>凭据同步问题导致凭据从 HA 丢失

#### <a name="symptoms"></a>症状

从当前 Integration Runtime 节点删除了数据源凭据“XXXXXXXXXX”，其有效负载为“在 Azure 门户上删除链接服务或任务具有错误的有效负载时，请使用凭据重新创建新的链接服务”。

#### <a name="cause"></a>原因

你的自承载 IR 是在 HA 模式下构建的，其中包含两个节点，但它们不处于凭据同步状态，这意味着存储在调度程序节点中的凭据不会同步到其他工作器节点。 如果从调度程序节点到工作器节点发生了故障转移，但凭据仅存在于以前的调度程序节点中，则任务将在尝试访问凭据时失败，并且你将会遇到上述错误。

#### <a name="resolution"></a>解决方法

避免此问题的唯一方法是确保两个节点处于凭据同步状态。 否则必须为新的调度程序重新输入凭据。


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>由于缺少私钥，无法选择证书

#### <a name="symptoms"></a>症状

1.  将 PFX 文件导入到证书存储中。
2.  通过 IR Configuration Manager UI 选择证书时，遇到以下错误：

    ![缺少私钥](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>原因

- 用户帐户的权限较低，无法访问私钥。
- 此证书生成为签名，而不是密钥交换。

#### <a name="resolution"></a>解决方法

1.  使用可访问私钥以操作 UI 的特权帐户。
2.  运行以下命令来导入证书：
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>自承载 IR 设置

### <a name="the-integration-runtime-registration-error"></a>Integration Runtime 注册错误 

#### <a name="symptoms"></a>症状

有时我们希望在另一个帐户中运行自承载 IR，原因如下：
- 公司策略不允许使用服务帐户。
- 需要进行一些身份验证。

在服务面板中更改服务帐户后，你可能会发现 Integration Runtime 停止工作。

![IR 注册错误](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>原因

许多资源只能通过服务帐户访问。 将服务帐户更改为另一个帐户时，所有从属资源的权限保持不变。

#### <a name="resolution"></a>解决方法

查看 Integration Runtime 事件日志以检查错误。

![IR 事件日志](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

如果错误为上方的 UnauthorizedAccessException，请按以下说明进行操作：


1. 在 Windows 服务面板中，勾选 DIAHostService 登录服务帐户。

    ![登录服务帐户](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. 检查登录服务帐户是否具有对 %programdata%\Microsoft\DataTransfer\DataManagementGateway 文件夹的 R/W 权限：。

    - 默认情况下，如果未更改服务登录帐户，则它应具有 R/W 权限。

        ![服务权限](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - 如果更改了服务登录帐户，请按以下步骤进行操作以缓解该问题：
        1. 彻底卸载当前的自承载 IR。
        1. 安装自承载 IR 位。
        1. 按以下说明进行操作以更改服务帐户： 
            1. 转到自承载 IR 的安装文件夹，切换到以下文件夹：Microsoft Integration Runtime\4.0\Shared。
            1. 使用提升的权限启动命令行。 将 \<user> 和 \<password> 替换为自己的用户名和密码，然后运行以下命令 ：
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. 如果要更改为 LocalSystem 帐户，请确保为此帐户使用正确的格式。 以下是正确格式示例：

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                请勿使用如下所示的格式：

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. 或者，由于 LocalSystem 具有高于管理员的权限，因此你也可以直接在“服务”中进行更改。
            1. 你可以在 IR 服务登录帐户中使用本地/域用户。            
        1. 注册 Integration Runtime。

如果显示如下错误：Integration Runtime 服务 (DIAHostService) 未能启动。请按照以下说明验证你是否有足够的权限启动系统服务：

1. 在 Windows 服务面板中，勾选 DIAHostService 登录服务帐户。
   
    ![登录服务帐户](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. 检查登录服务帐户是否具有用于启动 Windows 服务的“以服务形式登录”权限：

    ![以服务形式登录](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>更多信息

如果以上两种解决方法均不适用，请尝试收集以下 Windows 事件日志： 
- 应用程序和服务日志 -> Integration Runtime
- Windows 日志 -> 应用程序

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>找不到“注册”按钮来注册自承载 IR    

#### <a name="symptoms"></a>症状

注册自承载 IR 时，在 Configuration Manager UI 上找不到“注册”按钮。

![没有“注册”按钮](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>原因

自 Integration Runtime 3.0 发行以来，为了实现更干净、更安全的环境，删除了现有 Integration Runtime 节点上的“注册”按钮。 如果某个节点已注册到某个 Integration Runtime（无论是否联机），则必须卸载之前的节点，然后安装并注册该节点，才能将该节点重新注册到另一个 Integration Runtime。

#### <a name="resolution"></a>解决方法

1. 转到控制面板以卸载现有的 Integration Runtime。

    > [!IMPORTANT] 
    > 在以下过程中，选择“是”。 在卸载过程中请勿保留数据。

    ![删除数据](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. 如果没有 Integration Runtime 安装程序 MSI，请转到[下载中心](https://www.microsoft.com/en-sg/download/details.aspx?id=39717)以下载最新的 Integration Runtime。
1. 安装 MSI 并注册 Integration Runtime。


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>由于 localhost，无法注册自承载 IR     

#### <a name="symptoms"></a>症状

出现 get_LoopbackIpOrName 时，无法在新计算机上注册自承载 IR。

**调试：** 出现运行时错误。
“Microsoft.DataTransfer.DIAgentHost.DataSourceCache”的类型初始化表达式引发了异常。
数据库查找期间发生不可恢复的错误。
 
**异常详细信息：** System.TypeInitializationException：“Microsoft.DataTransfer.DIAgentHost.DataSourceCache”的类型初始化表达式引发了异常。 ---> System.Net.Sockets.SocketException：数据库查找 System.Net.Dns.GetAddrInfo(String name) 期间发生不可恢复的错误。

#### <a name="cause"></a>原因

解析 localhost 时通常会发生此问题。

#### <a name="resolution"></a>解决方法

在主机文件上使用 Localhost 127.0.0.1 并解决此类问题。


### <a name="self-hosted-setup-failed"></a>自承载设置失败    

#### <a name="symptoms"></a>症状

无法卸载现有 IR，或无法安装新 IR，或无法将现有 IR 升级为新 IR。

#### <a name="cause"></a>原因

安装取决于 Windows Installer 服务。 多种原因可导致安装问题：
- 磁盘空间不足
- 权限不足
- NT 服务由于某种原因处于锁定状态
- CPU 使用率太高
- MSI 文件托管位置的网络慢
- 意外改动了某些系统文件或注册表


## <a name="self-hosted-ir-connectivity-issues"></a>自承载 IR 连接问题

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>自承载集成运行时无法连接到云服务

#### <a name="symptoms"></a>症状

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

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>错误消息：自承载集成运行时节点/逻辑 SHIR 处于“非活动”/“正在运行(受限)”状态

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

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>自承载 IR 与数据工厂或自承载 IR 与数据源/接收器之间的连接问题

若要排查网络连接问题，应知道如何收集网络跟踪，了解如何使用它，并在通过自承载 IR 在实际案例中应用 Netmon 工具之前[分析 netmon 跟踪](#how-to-analyze-netmon-trace)。

#### <a name="symptoms"></a>症状

有时，在排查自承载 IR 与数据工厂之间的连接问题（例如下面的问题）时，会出现以下情况： 

![HTTP 请求失败](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

或者，在排查自承载 IR 与数据源/接收器之间的连接问题时，会遇到以下错误：

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>解决方法：

遇到以上问题时，请参考以下说明来进一步进行排查：

获取 netmon 跟踪并进一步进行分析。
- 首先，你可以设置筛选器来查看从服务器到客户端的任何重置。 在下面的示例中，你可以看到服务器端是数据工厂服务器。

    ![数据工厂服务器](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- 当你获得重置包时，可以顺着 TCP 来查找对话。

    ![查找对话](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 然后，你可以通过删除筛选器来获取客户端与数据工厂服务器之间的对话。

    ![获取对话](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 根据收集的 netmon 跟踪，我们可以判断 TTL (TimeToLive) 总计为 64。 根据[此文](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)中提到的**默认 TTL 和跃点限制值**（摘录如下），我们可以确定是 Linux 系统重置了包并导致连接断开。

    默认 TTL 和跃点限制值在不同的操作系统中有所不同，下面是一些操作系统的默认值：
    - Linux 内核 2.4 (circa 2001)：对于 TCP、UDP 和 ICMP，该值为 255
    - Linux 内核 4.10 (2015)：对于 TCP、UDP 和 ICMP，该值为 64
    - Windows XP (2001)：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows 10 (2015)：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows Server 2008：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows Server 2019 (2018)：对于 TCP、UDP 和 ICMP，该值为 128
    - macOS (2001)：对于 TCP、UDP 和 ICMP，该值为 64

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    但在上面的示例中，它显示为 61 而不是 64，因为当网络包到达目标时，它需要通过不同的跃点，例如路由器/网络设备。 将在最终 TTL 中扣减路由器/网络设备的数目。
    在本例中，我们可以看到，可以从 Linux 系统以 TTL 64 发送重置。

- 我们需要检查从自承载 IR 算起的第四个跃点，以确认重置设备可能来自何处。
 
    *来自 Linux 系统 A 的网络包的 TTL 64 -> B 的 TTL (64 - 1 = 63) -> C 的 TTL (63 - 1 = 62) -> 自承载 IR 的 TTL (62 - 1 = 61)*

- 在理想情况下，TTL 将为 128，这意味着 Windows 系统正在运行我们的数据工厂。 如以下示例中所示，128 – 107 = 21 个跃点，这意味着在 TCP 3 握手期间，包从数据工厂发送到自承载 IR 共经过了 21 个跃点。
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    因此，你需要与网络团队合作，以检查从自承载 IR 算起的第四个跃点是什么。 如果它是作为 Linux 系统存在的防火墙，请检查日志来确认设备为何在 TCP 3 握手后重置了包。 但是，如果不确定要在何处进行调查，请尝试一起获取自承载 IR 和防火墙在有问题的时间内的 netmon 跟踪，以找出可能重置此包并导致连接断开的设备。 在这种情况下，你还需要与网络团队合作才能继续进行调查。

### <a name="how-to-analyze-netmon-trace"></a>如何分析 netmon 跟踪

> [!NOTE] 
> 以下说明适用于 netmon 跟踪。 由于目前不支持 netmon 跟踪，因此可以使用 wireshark。

如果收集到 netmon 跟踪，则在尝试 telnet 8.8.8.8 888 时会看到以下跟踪：

![netmon 跟踪 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon 跟踪 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

这意味着你无法基于端口 **888** 与服务器端 **8.8.8.8** 建立 TCP 连接，因此你会看到两个额外的 **SynReTransmit** 包。 由于源 **SELF-HOST2** 在发送第一个包时无法建立到 **8.8.8.8** 的连接，因此它将继续建立连接。

> [!TIP]
> - 你可以单击“加载筛选器” -> “标准筛选器” -> “地址” -> “IPv4 地址”。   
> - 输入 **IPv4.Address == 8.8.8.8** 作为筛选器，然后单击“应用”。 之后，你将只能看到从本地计算机到目标 **8.8.8.8** 的通信。

![筛选器地址 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![筛选器地址 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

下面的示例演示了一个优秀方案。 

- 如果 Telnet **8.8.8.8 53** 正常运行且没有任何问题，则可以看到发生了 TCP 3 握手，然后会话完成了 TCP 4 握手。

    ![优秀方案示例 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![优秀方案示例 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 基于上面的 TCP 3 握手，你可以看到以下工作流：

    ![TCP 3 握手工作流](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 完成会话所需的 TCP 4 握手及其工作流将如下所示：

    ![TCP 4 握手](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 握手工作流](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="self-hosted-ir-sharing"></a>自承载 IR 共享

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>不支持从其他租户共享自承载 IR 

#### <a name="symptoms"></a>症状

尝试从 Azure 数据工厂 UI 共享自承载 IR 时，你可能会注意到其他数据工厂（位于不同租户），但你无法在位于不同租户的数据工厂之间共享自承载 IR。

#### <a name="cause"></a>原因

不能跨租户共享自承载 IR。


## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [映射数据流性能指南](concepts-data-flow-performance.md)
